---
layout: post
title:  "Caching and Unpersisting Pyspark RDD"
date:   2019-12-06
categories: work
tags: pyspark
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

A cached RDD can only be unpersisted through a variable referencing it.




```sh
>>> l = [('Alice', 1)]
>>> df = spark.createDataFrame(l) # create sample dataframe
>>> df
DataFrame[_1: string, _2: bigint]
>>> for (id, rdd) in spark.sparkContext._jsc.getPersistentRDDs().items():
...  print id
...
>>> df.cache() # cache dataframe
DataFrame[_1: string, _2: bigint]
>>> for (id, rdd) in spark.sparkContext._jsc.getPersistentRDDs().items():
...  print id
...
481 # id of the cached dataframe

>>> df_dict = {'df': df}
>>> ref = df_dict['df'] # create reference
>>> ref.unpersist() # unpersist dataframe through reference variable
DataFrame[_1: string, _2: bigint]
>>> for (id, rdd) in spark.sparkContext._jsc.getPersistentRDDs().items():
...  print id
... 
# no id, successfully unpersisted
```
Variable created via `registerTempTable()` does not reference the original dataframe:
```bash
>>> df.cache() # cache dataframe again
DataFrame[_1: string, _2: bigint]
>>> for (id, rdd) in spark.sparkContext._jsc.getPersistentRDDs().items():
...  print id
...
483
>>> df.registerTempTable('df')
>>> ref = spark.sql('select * from df') # create variable that does not reference df
>>> ref.unpersist()
DataFrame[_1: string, _2: bigint]
>>> for (id, rdd) in spark.sparkContext._jsc.getPersistentRDDs().items():
...  print id
...
483 # df is not unpersisted, id is still there
```