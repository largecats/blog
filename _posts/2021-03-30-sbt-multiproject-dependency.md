---
layout: post
title:  "sbt Multi-Project Dependency"
date:   2021-03-30
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

If a project depends on an external project, how to manage the dependency in sbt?



## Via git repo link

Add the external project's git repo link to the build.sbt file of the project. E.g.:
```scala
lazy val someLibrary =
  ProjectRef(uri("https://xxx.git#v0.0.1"), "someLibrary") // directly load package from git
```
After importing sbt changes, the external project will be installed in 
```
C:\Users\<username>\.sbt\1.0\staging\273fb42ed4714bd36627\some-library
```
or
```
/home/<username>/.sbt/1.0/staging/273fb42ed4714bd36627/some-library
```

### Caveats

1. If need to build the project on a server, may need to check whether the server has access to the git repository of the external project.
2. Each time when the main project is built/assembled, the external project needs to be built also. This can be wasteful if the external project is used by multiple projects.

## Via source code

Add the external project's source root directory to the build.sbt file of the project. E.g.,:
```scala
lazy val someLibrary = ProjectRef(file("../some-library"), "someLibrary")
```
See [here](https://www.scala-sbt.org/1.x/api/sbt/ProjectRef.html) for details on `ProjectRef`.

After importing sbt changes, the external project will be included in the IntelliJ IDE's project explorer view:
```
> my_project
> some_library
> External Libraries
> Scratches and Consoles
```

### Caveats

Same as the previous method: The external project needs to be built each time any of its dependencies is built.

## Via jar file

Add the path of the external project's assembly jar file to the build.sbt file of the project as a dependency. E.g.:
```scala
val frameworkJarPath = "/.../some_library/target/scala-2.11/some_library-assembly-1.0.0.jar"
lazy val customDependencySettings = Seq(
  libraryDependencies += "scala_data_processing_framework" %% "scala_data_processing_framework" % "1.0.0" from frameworkJarPath
)
```
If the relative positions of the main project and the external project are fixed, the jar file path can be determined relatively from the directory of the main project.

After importing sbt changes, the external project will be included in the "External Libraries" in the IntelliJ IDE's project explorer view:
```
> my_project
> External Libraries
    > sbt:some_library:core_2.11:1.0.0.jar
> Scratches and Consoles
```

With this method, the external project only needs to be built/assembled once, and then any project that depends on it can use its jar file when building.

## References

[sbt Documentation](https://www.scala-sbt.org/1.x/docs/index.html)