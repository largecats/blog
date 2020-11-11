---
layout: post
title:  "Building Scala project with and without sbt"
date:   2019-07-31 23:29
categories: work
tags: scala
---

* content
{:toc}

I want to build `.scala` file.



## Preparation

This post assumes configurations in [this earlier post](https://largecats.github.io/2019/07/31/install-scala-on-windows/).

## Method

### With sbt

#### Installing sbt

`sbt` stands for Simple Build Tool and is Scala's de facto build tool.

1. Install sbt from [here](https://piccolo.link/sbt-1.2.8.msi).
2. Create system variable `SBT_HOME` and set it to the path of the sbt installation.
3. Add `%SBT_HOME%\bin` to path.
4. Open `cmd` and type `sbt`. Wait for everything to install and the interactive mode with `sbt:scala>` to pop up.

#### Building project

1. Create a `.scala` file containing, say, the following code.
    ```scala
    object Hi{
    def main(args: Array[String]) = println("Hello world!")
    }
    ```
2. Open `cmd`, `cd` to the directory containing the `.scala` file, and type `sbt`.
3. Type `run`. The output should look something like this:
    ```
    C:\Users\steve-rogers\scala>sbt
    [warn] Neither build.sbt nor a 'project' directory in the current directory: C:\Users\steve-rogers\scala
    c) continue
    q) quit
    ? c
    Java HotSpot(TM) Client VM warning: ignoring option MaxPermSize=256m; support was removed in 8.0
    [warn] No sbt.version set in project/build.properties, base directory: C:\Users\steve-rogers\scala
    [info] Set current project to scala (in build file:/C:/Users/steve-rogers/scala/)
    [info] sbt server started at local:sbt-server-62d2810ebd461b523e48
    sbt:scala> run
    [info] Updating ...
    [info] Done updating.
    [info] Compiling 1 Scala source to C:\Users\steve-rogers\scala\target\scala-2.12\classes ...
    [info] Done compiling.
    [info] Packaging C:\Users\steve-rogers\scala\target\scala-2.12\scala_2.12-0.1.0-SNAPSHOT.jar ...
    [info] Done packaging.
    [info] Running Hi
    Hello world!
    [success] Total time: 5 s, completed Jul 31, 2019 11:28:17 PM
    sbt:scala>
    ```

### Without sbt

1. Create a `.scala` file containing, say, the following code.
    ```scala
    object Hi{
    def main(args: Array[String]) = println("Hello world!")
    }
    ```
2. Open `cmd`, `cd` to the directory containing the `.scala` file, and type `scalac hello.scala`.
3. Observe that the files `Hi$.class` and `Hi.class` are created. The latter is what we need to execute. Type `scala Hi`. The output should look something like this:
    ```
    C:\Users\steve-rogers\scala>scala Hi
    Hello world!
    ```