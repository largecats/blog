---
layout: post
title:  "Installing Scala on Windows"
date:   2019-07-31 22:54
categories: work
tags: scala
---

* content
{:toc}

I want to install Scala on my PC.



## Preparation

I read [Scala's documentation](https://docs.scala-lang.org/).

## Method

1. Install Java as explained in [this post](https://largecats.github.io/2019/07/31/install-spark-on-windows/).
2. Install Scala (without sbt) from [here](https://downloads.lightbend.com/scala/2.13.0/scala-2.13.0.msi).
3. Create a system variable `SCALA_HOME` and set it to the path of the Scala installation.
4. Add `%SCALA_HOME%\jre\bin` to path.
5. Open `cmd` and type `scala`. The output should look something like this.
    ```
    C:\Users\steve-rogers>scala
    Welcome to Scala 2.13.0 (Java HotSpot(TM) Client VM, Java 1.8.0_221).
    Type in expressions for evaluation. Or try :help.

    scala>
    ```