---
layout: post
title:  "Installing Spark on Windows"
date:   2019-07-31 14:11
categories: work
tags: spark pyspark scala
---

* content
{:toc}

I want to install Spark on my PC.



## Preparation

I read chapter 2 of O'Reilly's [Learning Spark](https://www.oreilly.com/library/view/learning-spark/9781449359034/).

## Method

1. Download Java from [here](https://java.com/en/download/win10.jsp) and install it to a directory with no spaces in the path, e.g., `C:\Java`.

    If the directory contains space, the Spark shell may fail with an error described [here](https://stackoverflow.com/questions/44027151/why-does-spark-shell-fail-with-was-unexpected-at-this-time).
2. Create system variable `JAVA_HOME` and set it to the path in which Java is just installed.
3. Download Spark from [here](http://spark.apache.org/downloads.html) and unzip it to a directory with no spaces in the path, e.g., `C:\Spark`.
4. Add the `bin` folder in the Spark installation to environment variables.
5. Open `cmd` and type `pyspark` to open the Python version of Spark shell or type `spark-shell` to open the Scala version.

## Result

![](/images/pyspark.png){:width="800px"}
<div align="center">
<sup>Python version of the Spark shell.</sup>
</div>

![](/images/scala_spark.png){:width="800px"}
<div align="center">
<sup>Scala version of the Spark shell.</sup>
</div>