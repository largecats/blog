---
layout: post
title:  "Spark Magnet: Push-based Shuffle"
date:   2021-03-07
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

Recently, our data infrastructure team deployed a new version of Spark, called Spark Magnet. It is said to offer 30% to 50% improvement in performance, compared to the original Spark 3.0.

Spark Magnet is a patch to Spark 3 that improves shuffle efficiency:

<div style="text-align: center"><img src="{{ site.baseurl }}/images/spark_magnet_jira_ticket.png" width="800px" /></div>
<div align="center">
<sup>Spark Magnet's JIRA ticket.</sup>
</div>

Provided by LinkedIn's data infrastructure team, it makes use of the Magnet shuffle service, which is a novel shuffle mechanism built on top of Spark's native shuffle service. It improves shuffle efficiency by addressing several major bottlenecks with reasonable trade-offs.



## Background

Magnet is about shuffle, and shuffle is about MapReduce.

### MapReduce

TBD

### Shuffle

TBD

#### Shuffle bottlenecks

TBD

| Input data size | Number of mappers | Number of reducers | Number of shuffle blocks | Shuffle block size  |
| --------------- | ----------------- | ------------------ | ------------------------ | ------------------- |
| $D$             | $M$               | $R$                | $M*R$                    | $\frac{D}{M*R}$     |
| $2D$            | $2M$              | $2R$               | $4(M*R)$                 | $\frac{D}{2(M*R)}$  |
| $10D$           | $10M$             | $10R$              | $100(M*R)$               | $\frac{D}{10(M*R)}$ |