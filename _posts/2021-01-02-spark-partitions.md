---
layout: post
title:  "Spark Partitions"
date:   2021-01-02
categories: work
tags: spark
---
<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>
* content
{:toc}

Spark partitions are important for parallelism.



Sample data of student ids and their nationality:
```
id	country
1	Russia
2	America
3	China
4	China
5	China
6	Russia
7   America
8   Russia
9   China
10  Russia
```
Creating sample DataFrame:
```scala
import spark.implicits._

val df = Seq((1, "Russia"), (2, "America"), (3, "China"), (4, "China"), (5, "China"), (6, "Russia"), (7, "America"), (8, "Russia"), (9, "China"), (10, "Russia")).toDF("id", "country")
```
```python
values = [(1, "Russia"), (2, "America"), (3, "China"), (4, "China"), (5, "China"), (6, "Russia"), (7, "America"), (8, "Russia"), (9, "China"), (10, "Russia")]
columns = ["id", "country"]
df = spark.createDataFrame(values, columns)
```
Without explicit parallelizing, the initial number of partitions of `df` depends on the number of executors allocated.

This example was launched with `spark-shell --num-executors 4`:
```shell
scala> df.rdd.getNumPartitions
res0: Int = 4

scala> df.rdd.glom.collect
res1: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array([1,Russia], [2,America]), 
    Array([3,China], [4,China], [5,China]), 
    Array([6,Russia], [7,America]), 
    Array([8,Russia], [9,China], [10,Russia])
    )
```

But if we launch `spark-shell` with `spark-shell --num-executors 1`:
```shell
scala> df.rdd.getNumPartitions
res7: Int = 2

scala> df.rdd.glom.collect
res0: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array([1,Russia], [2,America], [3,China], [4,China], [5,China]), 
    Array([6,Russia], [7,America], [8,Russia], [9,China], [10,Russia])
    )
```

## Memory Partitions

These are partitions of data in memory used by computation in Spark.

### Coalesce

`df.coalesce(n)` decreases the number of partitions to `n` by collapsing partitions.

```shell
scala> val df1 = df.coalesce(2)
df1: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, country: string]

scala> df1.rdd.glom.collect
res2: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array([1,Russia], [2,America], [3,China], [4,China], [5,China]), 
    Array([6,Russia], [7,America], [8,Russia], [9,China], [10,Russia])
    )
```

If `n` is larger than the original number of partitions, `df.coalese(n)` won't change the partitions:
```shell
scala> val df1 = df.coalesce(6)
df1: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, country: string]

scala> df1.rdd.glom.collect
res3: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array([1,Russia], [2,America]), 
    Array([3,China], [4,China], [5,China]), 
    Array([6,Russia], [7,America]), 
    Array([8,Russia], [9,China], [10,Russia])
    )
```

### Repartition

`df.repartition(n)` decreases or increases the number of partitions by doing a full shuffle (expensive) and splitting the data as equally as possible (up to the hashing algorithm) into `n` partitions.
```shell
scala> val df1 = df.repartition(3)
df1: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, country: string]

scala> df1.rdd.glom.collect
res4: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array([6,Russia], [3,China], [8,Russia]), 
    Array([2,America], [10,Russia], [4,China]), 
    Array([5,China], [1,Russia], [9,China], [7,America])
    )
```
```shell
scala> val df1 = df.repartition(5)
df1: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, country: string]

scala> df1.rdd.glom.collect
res5: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array([6,Russia], [8,Russia]),
    Array([4,China], [10,Russia], [2,America]), 
    Array([1,Russia], [9,China], [5,China]), 
    Array([3,China]), 
    Array([7,America])
    )
```
If the total number of records < n, some partitions will be empty.
```shell
scala> val df1 = df.repartition(20)
df1: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, country: string]

scala> df1.rdd.glom.collect
res9: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array(), 
    Array([2,America]), 
    Array([1,Russia]), 
    Array(), 
    Array(), 
    Array(), 
    Array([4,China]), 
    Array([5,China]), 
    Array([3,China]), 
    Array([7,America]), 
    Array([6,Russia]), 
    Array(), 
    Array(), 
    Array(), 
    Array(), 
    Array([8,Russia]), 
    Array([10,Russia]),
    Array([9,China]), 
    Array(), 
    Array()
    )
```

`df.repartition(n, $"country")` splits data into `n` partitions based on column `country`. `n` defaults to 200, i.e., `df.repartition($"country")` will split data into 200 partitions.

Spark uses hashing on the parition key to determine which partition to put each record in (hash partitioning). See [here](https://stackoverflow.com/questions/31424396/how-does-hashpartitioner-work?noredirect=1&lq=1).
```shell
scala> import org.apache.spark.unsafe.types.UTF8String
import org.apache.spark.unsafe.types.UTF8String
scala> import org.apache.spark.unsafe.hash.Murmur3_x86_32.hashUnsafeBytes
import org.apache.spark.unsafe.hash.Murmur3_x86_32.hashUnsafeBytes
scala> val countries = Seq("America", "Russia", "China").map(UTF8String.fromString(_))
countries: Seq[org.apache.spark.unsafe.types.UTF8String] = List(America, Russia, China)
```
For `numPartitions = 5`:
```shell
scala> val numPartitions = 5
numPartitions: Int = 5
scala> val partitionIndices = countries.map(utf8 => hashUnsafeBytes(utf8.getBaseObject, utf8.getBaseOffset, utf8.numBytes, 42) % numPartitions).map(x => if (x < 0) x + numPartitions else x)
partitionIndices: Seq[Int] = List(1, 2, 4)
```
Accordingly, records with "America", "Russia", and "China" are put in partitions 1, 2, and 4, repsectively (indexed with 0).
```shell
scala> val df1 = df.repartition(5, $"country")
df1: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, country: string]

scala> df1.rdd.glom.collect
res13: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array(), 
    Array([2,America], [7,America]), 
    Array([6,Russia], [8,Russia], [10,Russia], [1,Russia]), 
    Array(), 
    Array([3,China], [4,China], [5,China], [9,China])
    )
```
For `numPartitions = 6`:
```shell
scala> val numPartitions = 6
numPartitions: Int = 6

scala> val partitionIndices = countries.map(utf8 => hashUnsafeBytes(utf8.getBaseObject, utf8.getBaseOffset, utf8.numBytes, 42) % numPartitions).map(x => if (x < 0) x + numPartitions else x)
partitionIndices: Seq[Int] = List(4, 4, 3)
```
Accordingly, "America" and "Russia" records are both put in partition 4, while "China" records are put in partition 3:
```shell
scala> val df1 = df.repartition(6, $"country")
df1: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [id: int, country: string]

scala> df1.rdd.glom.collect
res21: Array[Array[org.apache.spark.sql.Row]] = Array(
    Array(), 
    Array(), 
    Array(), 
    Array([9,China], [3,China], [4,China], [5,China]), 
    Array([6,Russia], [7,America], [1,Russia], [2,America], [8,Russia], [10,Russia]), 
    Array()
    )
```
## Disk Partitions

These are separate folders stored in HDFS.

`df.repartition(n).write.parquet("...")` will generate `max(n. number of records in df + 1)` parquet files under "/user/xxx/example/repartition_n". E.g., `df.repartition(200)` generates 11 parquet files, with the first one empty, and the rest containing 1 record each:
```shell
/.../_SUCCESS
/.../part-00000-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00109-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00110-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00135-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00136-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00137-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00161-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00162-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00186-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00187-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
/.../part-00188-e630013a-6cb6-4e4f-984c-a49e371794db-c000.snappy.parquet
```

`df.write.partitionBy("country").parquet("...")` generates 1 folder for each country, and under each country folder, there would be 1 parquet file from each partition that contains that country.
Recall the original partition of `df`:
```
partition 0     Array([1,Russia], [2,America]), 
partition 1     Array([3,China], [4,China], [5,China]), 
partition 2     Array([6,Russia], [7,America]), 
partition 3     Array([8,Russia], [9,China], [10,Russia])
```
```shell
/.../country=America/part-00000-9a040405-7fb3-47cb-b43c-ce765730770c.c000.snappy.parquet    from partition 0
/../country=America/part-00002-9a040405-7fb3-47cb-b43c-ce765730770c.c000.snappy.parquet    from partition 2

/.../country=China/part-00001-9a040405-7fb3-47cb-b43c-ce765730770c.c000.snappy.parquet      from partition 1
/.../country=China/part-00003-9a040405-7fb3-47cb-b43c-ce765730770c.c000.snappy.parquet      from partition 3

/.../country=Russia/part-00000-9a040405-7fb3-47cb-b43c-ce765730770c.c000.snappy.parquet     from partition 0
/.../country=Russia/part-00002-9a040405-7fb3-47cb-b43c-ce765730770c.c000.snappy.parquet     from partition 2
/.../country=Russia/part-00003-9a040405-7fb3-47cb-b43c-ce765730770c.c000.snappy.parquet     from partition 3
```
This generates many small files, which could be problematic for HDFS performance.

`df.repartition($"country").write.partitionBy("country").parquet("...")` generates 1 folder for each country, and 1 parquet file under each folder containing all of that country's data.
```shell
/.../country=America/part-00166-b37bdc10-a8f3-42c0-9fcf-aa580701f753.c000.snappy.parquet
/.../country=China/part-00059-b37bdc10-a8f3-42c0-9fcf-aa580701f753.c000.snappy.parquet
/.../country=Russia/part-00002-b37bdc10-a8f3-42c0-9fcf-aa580701f753.c000.snappy.parquet
```
This reduces the number of parquet files, but would be skewed if the data is not evenly distributed among different countries (e.g., if China has much more records than the other two, it would be infeasible to write all its data into a single parquet file).

`df.repartition($"country").write.option("maxRecordsPerFile", n).partitionBy("country").parquet("...")` will create 1 folder of parquet files for each country, and each parquet files has <= n records. E.g., with `n = 3`:
```shell
/.../country=America/part-00166-ea943b36-4d09-4675-99b5-0d0bbf4c346e.c000.snappy.parquet

/.../country=China/part-00059-ea943b36-4d09-4675-99b5-0d0bbf4c346e.c000.snappy.parquet
/.../country=China/part-00059-ea943b36-4d09-4675-99b5-0d0bbf4c346e.c001.snappy.parquet

/.../country=Russia/part-00002-ea943b36-4d09-4675-99b5-0d0bbf4c346e.c000.snappy.parquet
/.../country=Russia/part-00002-ea943b36-4d09-4675-99b5-0d0bbf4c346e.c001.snappy.parquet
```
Recall that both China and Russia have 4 records, so their data are split into 2 partitions each. E.g., the two partitions under `country=China`:
```shell
scala> spark.read.parquet("/.../country=China/part-00059-ea943b36-4d09-4675-99b5-0d0bbf4c346e.c000.snappy.parquet").show()
+---+
| id|
+---+
|  9|
|  3|
|  4|
+---+
scala> spark.read.parquet("/.../country=China/part-00059-ea943b36-4d09-4675-99b5-0d0bbf4c346e.c001.snappy.parquet").show()
+---+
| id|
+---+
|  5|
+---+
```

## References

* [https://mungingdata.com/apache-spark/partitionby/](https://mungingdata.com/apache-spark/partitionby/)
* [https://www.codenong.com/42283766/](https://www.codenong.com/42283766/)