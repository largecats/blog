---
layout: post
title:  "Setting up PySpark with Jupyter Notebook"
date:   2019-07-31 15:12
categories: life-saver work
tags: spark pyspark
---

* content
{:toc}

I want to use PySpark form Jupyter Notebook for covenient view of program output.



## Preparation

This post assumes configurations in [this earlier post](https://largecats.github.io/2019/07/31/set-up-spark-on-windows/). I read [this article](https://blog.sicara.com/get-started-pyspark-jupyter-guide-tutorial-ae2fe84f594f).

## Method

There are two ways to set up PySpark with Jupyter Notebook. They are explained in detail in the article above. I would like to supplement the article by providing a summary and highlighting some caveats.

### Option 1: Open notebook directly from PySpark

1. Create environment variables `PYSPARK_DRIVER_PYTHON` and `PYSPARK_DRIVER_PYTHON_OPTS` and set them to be `jupyter` and `'notebook'`, respectively.
2. Open `cmd` and type `pyspark`, this should open Jupyter Notebook in the browser.
3. Run the following code in the notebook. (`sample.txt` is taken from the [wikipedia page of Apache Spark](https://en.wikipedia.org/wiki/Apache_Spark).)
    ```python
    import random
    num_samples = 100000000
    def inside(p):     
    x, y = random.random(), random.random()
    return x*x + y*y < 1
    count = sc.parallelize(range(0, num_samples)).filter(inside).count()
    pi = 4 * count / num_samples
    print(pi)

    lines = sc.textFile("sample.txt")
    print(lines.count())
    print(lines.first())
    ```
    The output should look like this. 
    ![](/images/direct-open-tryout.png){:width="800px"}

### Option 2: Invoke Spark environment in notebook on the fly

1. Install `findspark` module by typing `pip install findspark`.
2. Create environment variable `SPARK_HOME` and set it to the path of Spark installation.
3. Launch Jupyter Notebook.
4. Paste the following code at the start of the notebook.
    ```python
    import findspark
    findspark.init()
    import pyspark
    ```
5. Run the following code.
    ```python
    import random
    sc = pyspark.SparkContext()
    num_samples = 100000000
    def inside(p):     
    x, y = random.random(), random.random()
    return x*x + y*y < 1
    count = sc.parallelize(range(0, num_samples)).filter(inside).count()
    pi = 4 * count / num_samples
    print(pi)
    sc.stop()

    sc = pyspark.SparkContext()
    lines = sc.textFile("sample.txt")
    print(lines.count())
    print(lines.first())
    sc.stop()
    ```
    The output should look like this. 
    ![](/images/findspark-tryout.png){:width="800px"}


Note that option 1 does not require manually creating a `SparkContext` object, while option 2 does. As a result, if the notebook created in option 1 is not opened from PySpark but from a regular Jupyter Notebook, the `sc` variable would not be recognized. Vice versa, if the notebook created in option 2 is opened from PySpark, the line `sc = pyspark.SparkContext()` would be redundant, and the program would raise an error saying that only one `SparkContext` can be run at once.