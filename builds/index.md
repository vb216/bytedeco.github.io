---
layout: default
title: Builds
---

Builds
======

We now have reliable automated builds using [Travis CI](https://travis-ci.org) (to cover all Android, Linux, and Mac OS X builds) and [AppVeyor](https://ci.appveyor.com) (providing Windows builds). Thanks to both initiatives for providing such a great service to open source projects! Every pull request is built for all targets, and each commit generates a new snapshot build.

Using snapshot builds
---------------------

These builds can be used outside of the main release cycles to test and use latest features with only two changes required.

Firstly, in your [Maven settings file](https://maven.apache.org/settings.html), add a profile like the one below to enable snapshots:

```xml
<profile>
  <id>allow-snapshots</id>
  <activation><activeByDefault>true</activeByDefault></activation>
  <repositories>
    <repository>
      <id>sonatype-nexus-snapshots</id>
      <url>https://oss.sonatype.org/content/repositories/snapshots</url>
      <releases><enabled>false</enabled></releases>
      <snapshots><enabled>true</enabled></snapshots>
    </repository>
  </repositories>
</profile>
```

Secondly, update your `pom.xml` file to use the latest snapshot version. Taking the [OpenCV presets example](https://github.com/bytedeco/javacpp-presets/tree/master/opencv#sample-usage), the dependency in the `pom.xml` file would change to this:

```xml
<dependency>
  <groupId>org.bytedeco.javacpp-presets</groupId>
  <artifactId>opencv-platform</artifactId>
  <version>3.3.1-1.3.4-SNAPSHOT</version>
</dependency>
```

It is also advisable to specify your platform with the `javacpp.platform` system property and use the `--update-snapshots` option, for example, `mvn -Djavacpp.platform=linux-x86_64 --update-snapshots [...]`, as binaries for all platforms may not be available at all times.

Current build status
---------------------

Builds information and history is available on

* [Travis CI at bytedeco/javacpp-presets](https://travis-ci.org/bytedeco/javacpp-presets) for Android, Linux, and Mac OS X, and on
* [AppVeyor at bytedeco/javacpp-presets](https://ci.appveyor.com/project/Bytedeco/javacpp-presets) for Windows.

