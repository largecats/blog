---
layout: post
title:  "Setting up Jupyter Notebook kernel for Scala, Python to use Spark"
date:   2019-08-17
categories: life-saver work
tags: spark scala python
---

* content
{:toc}

I want to use spark via both python and scala with Jupyter notebook without having to import all those modules associated with pyspark. I'm using Windows.



## Preparation

This post assumes configurations [here](https://largecats.github.io/2019/07/31/install-spark-on-windows/) and [here](https://largecats.github.io/2019/07/31/set-up-pyspark-with-jupyter-notebook/).


## Method

I tried a number of kernels in [this list](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels), only `spylon-spark` seems to work.

Follow the instructions [here](https://github.com/Valassis-Digital-Media/spylon-kernel).

## Result

![](/images/spylon.png){:width="800px"}
