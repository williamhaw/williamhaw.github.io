---
layout: post
title: SBT Tricks
category: SBT
tags:
- Scala
- SBT

---
I was recently upgrading a library at work from using Scala 2.11 to 2.12. Here are some sbt tricks that I picked up while trying to perform the migration.

<!--excerpt-->

# Build with different library versions for different Scala versions

I followed steps 1 and 2 [here](http://rosslawley.co.uk/how-to-handle-multiple-scala-versions/ "http://rosslawley.co.uk/how-to-handle-multiple-scala-versions/")

Basically you can define a function that takes the Scala version string and return the right version of the library for that Scala version. This is useful especially for protobuf libraries compiled with scalapb since we had a version compiled with scalapb 0.4.9 for 2.11 and a version compiled with scalapb 0.6.0 for 2.12.

# Conflicting cross-version suffixes

If you fix the version of your library to a certain Scala version like so:

```scala
    val myDependency = "org.scala-lang.modules" % "scala-xml_2.11" % "0.1.0"
```

then shame on you!

This will make upgrading Scala versions difficult in the future. Instead, you should let sbt resolve the version suffix of the library like so:

```scala
    val myDependency = "org.scala-lang.modules" %% "scala-xml" % "0.1.0"
```

This is even worse when the import is inside a dependency of a dependency, so that it doesn't appear in the build.sbt of the original project! I used the sbt-dependency-graph plugin to look for places where the conflicting version was being brought in (for e.g scala-xml_2.11) and then went to those projects to publish new versions without the forced suffixes.

# Change Scala version to new version to get IDE hints

After solving some of the dependency resolution issues, I was able to get the compilation started. However, it was failing for the new Scala version because of some field changes in the protobufs.

# Compile some classes differently between Scala versions

Since sbt 0.13.8, you can just put your version specific code in version specific directories (for e.g. `src/main/scala-2.12` will only be compiled when the current Scala version is 2.12).

# Exclude libraries properly

`exclude()` and `excludeAll()` do not understand Scala versions; you have to specify the Scala version suffix i.e. `exclude("com.example", "my_library_2.12")`

The reason, as noted [here](https://stackoverflow.com/questions/25179314/why-is-sbt-not-excluding-these-libraries-despite-using-excludes), is that the underlying Ivy dependency resolution doesn't understand sbt conventions (for e.g. the version suffix in the library name).