---
layout: post
title:  "Solving Spark timeout errors"
date:   2020-10-09
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
  
Timeout errors may occur while the Spark application is running or even after the Spark application has finished. Below are some common timeout errors and their solutions.



## Errors and solutions

### spark.rpc.RpcTimeoutException

<div style="text-align: center"><img src="/images/spark_rpc_askTimeout.png" width="800px" /></div>
<div align="center">
</div>

As suggested [here](https://stackoverflow.com/questions/39354909/how-to-tune-spark-rpc-asktimeout) and [here](https://stackoverflow.com/questions/37260230/spark-cluster-full-of-heartbeat-timeouts-executors-exiting-on-their-own), it is recommended to set `spark.network.timeout` to a higher value than the default 120s (we set it to 10000000). Alternatively, one may consider switching to [later versions of Spark](https://github.com/apache/spark/blob/9fcf0ea71820f7331504073045c38820e50141c7/python/pyspark/rdd.py), where certain relevant timeout values are set to `None`.

### java.util.concurrent.TimeoutException

We observed that this error usually occurs while the query is running or just before the Spark application finishes.

<div style="text-align: center"><img src="/images/java_util_concurrent_TimeoutException.png" width="800px" /></div>
<div align="center">
</div>

As suggested [here](http://mail-archives.apache.org/mod_mbox/spark-issues/201807.mbox/%3CJIRA.13175917.1533061309000.129934.1533062580707@Atlassian.JIRA%3E), this error may appear if the user does not stop the Spark context after the Spark program finishes and ShutdownHookManger would have to stop the Spark context in 10s instead. A simple solution is to call `sc.stop()` at the end of the Spark application.

<div style="text-align: center"><img src="/images/java_util_concurrent_TimeoutException2.png" width="800px" /></div>
<div align="center">
</div>

As suggested [here](https://stackoverflow.com/questions/41123846/why-does-join-fail-with-java-util-concurrent-timeoutexception-futures-timed-ou), join operations on large datasets may fail with `spark.sql.broadcastTimeout`. Assuming that the joins have been optimized to a reasonable extent, a simple solution is to set a higher value than the default 300s (we set it to 36000).