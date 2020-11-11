---
layout: post
title:  "Installing Hadoop, Spark, and Hive in Windows Subsystem for Linux (WSL)"
date:   2019-08-19
categories: life-saver work
tags: hadoop spark hive wsl
---

* content
{:toc}

I want to set up Hadoop, Spark, and Hive on my personal laptop.



## Method

### Installation

1. Set up WSL following [this guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
2. Install Hadoop following [this guide](https://kontext.tech/docs/DataAndBusinessIntelligence/p/install-hadoop-320-on-windows-10-using-windows-subsystem-for-linux-wsl).
3. Install Hive following [this guide](https://kontext.tech/docs/DataAndBusinessIntelligence/p/apache-hive-311-installation-on-windows-10-using-windows-subsystem-for-linux).
4. Install Spark following [this guide](https://kontext.tech/docs/DataAndBusinessIntelligence/p/apache-spark-243-installation-on-windows-10-using-windows-subsystem-for-linux).

### Start

1. Open WSL terminal.
2. Type
```sh
sudo service ssh restart
cd $HADOOP_HOME
sbin/start-all.sh
```
3. Open `http://localhost:8088/cluster` in browser to view resource manager. Note that only spark jobs submitted to `yarn` will show up here.