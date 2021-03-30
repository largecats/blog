---
layout: post
title:  "Spark Data Pipeline Framework Management"
date:   2021-03-30
categories: work
tags: spark python scala
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

Data pipelines are widely used to support business needs. As the business expands, both the volumn and diversity of its data increase. This calls for a system of data pipelines, each channelling data for a specific area of the business that may interact in various ways with other business areas. How to manage this system of data pipelines? If they share a common structure, is it worth it to maintain this structure in a separate framework package? How to manage the framework package together with the data pipelines that use it?



## Framework package

Data pipelines likely share similar structures. They all need to read data from source, process them, and write them out to some storage system or database. These stages can be extracted into a common package with unified interface for all pipelines, while providing functionalities that allow flexibility in the mode of reading input and writing output.

Batch pipelines and streaming pipelines likely need separate frameworks, as they have different requirements of SparkSession that may lead to different structures (e.g., streaming jobs require new SparkSession for restarted queries).

## Managing framework package with data pipelines

The framework package and each of the data pipeline can be maintained in git repositories.

### Submodule

We can add framework package as a submodule to each of the pipeline repos. However, this may require extra work in managing commit/push access, as we may want different groups of people to maintain the framework package and the pipeline repos. E.g., we may want to avoid accidental commits to the framework package from a pipeline repo. This can be done using [git hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks).

### Separate package

Alternatively, we can maintain the framework package separately. This means separate development, build, and unit tests.

**Python**

Dev environment: `pip install` framework package locally to allow IDE references when developing in pipeline repos.

Live environment: The framework package needs to be zipped and added to `--py-files` in the `spark-submit` command of each job launched from the pipeline repos.

**Scala (sbt)**

Dev environment: Include the framework package as dependency in the build definition. See [this post](https://largecats.github.io/blog/2021/03/30/sbt-multiproject-dependency/).

Live environment: Build framework package, then build pipeline repos using the framework package's assembled jar file. `spark-submit` launches job using the pipeline repos' jar files.

## CI/CD

**Python**

There is no build dependency. Once the framework package is updated and zipped, the pipeline jobs will automatically be using the updated framework that is added via `--py-files`. The CI/CD pipeline of the framework package does not need to trigger any downstream CI/CD pipelines in build stage.

**Scala (sbt)**

The pipeline repos need to include the framework package in their build. And so each time the framework package is built, the pipelien repos that depend on it also need to be re-built. This can be done using Gitlab CI/CD's [multi-project pipeline](https://docs.gitlab.com/ee/ci/multi_project_pipelines.html) feature.

