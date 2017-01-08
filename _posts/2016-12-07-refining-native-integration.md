---
layout: post
title: Refining native integration
---

Over the past few months, I have been busy integrating JavaCPP to [Deeplearning4j](https://github.com/deeplearning4j/deeplearning4j), mainly as part of [ND4J](https://github.com/deeplearning4j/nd4j) and [DataVec](https://github.com/deeplearning4j/datavec). Many bugs had to be fixed, we also needed to enhance native memory management, a few presets were created, but I actually spent most of my time trying to run [Skymind](https://skymind.io/) in Japan. In any case, thanks to all the team, Deeplearning4j has come a long way since last year, so if you have not given it a try recently, [make sure you do](https://deeplearning4j.org/)!

However, a release at Bytedeco was long overdue, so I am proud to announce the availability of version 1.3! The source code and the binaries can be obtained as usual from GitHub and the Maven Central Repository for [JavaCPP](https://github.com/bytedeco/javacpp), [JavaCPP Presets](https://github.com/bytedeco/javacpp-presets), [JavaCV](https://github.com/bytedeco/javacv), [ProCamCalib](https://github.com/bytedeco/procamcalib), and [ProCamTracker](https://github.com/bytedeco/procamtracker). This release comes with binaries for `linux-armhf` (for devices such as [Raspberry Pi](https://www.raspberrypi.org/)), thanks to Vince Baines for his continuous effort, as well as `linux-ppc64le`, built on [SuperVessel Cloud](https://ny1.ptopenlab.com/dashboard/), which features virtual machines that IBM offers for free to the community. Lloyd Chan has also been maintaining [sbt-javacpp](https://github.com/bytedeco/sbt-javacpp) and [sbt-javacv](https://github.com/bytedeco/sbt-javacv), while Andreas Eberle has generously provided [Android builds for TensorFlow](https://github.com/bytedeco/javacpp-presets/tree/master/tensorflow).

To manage native memory more automatically, JavaCPP now monitors physical memory usage, also known as "resident set size" on Linux, Mac OS X, etc or "working set size" on Windows, as reported by the kernel. The maximum value defaults to 2 times `Runtime.maxMemory()`, which can be specified with the usual `-Xmx` option on the command line, but we can also set it independently via the "org.bytedeco.javacpp.maxphysicalbytes" system property. When the whole process uses more physical memory than that amount, `System.gc()` followed by `Thread.sleep(100)` are called a few times in a row, an amount adjustable via the "org.bytedeco.javacpp.maxretries" system property, in an attempt to free memory. With this strategy, we are able to tame memory usage more accurately no matter how it is being allocated.

The presets that were created for this release are [librealsense](https://github.com/bytedeco/javacpp-presets/tree/master/librealsense) (a great contribution from Jeremy Laviole), [HDF5](https://github.com/bytedeco/javacpp-presets/tree/master/hdf5) (that Deeplearning4j uses to import models from Keras, Theano, and TensorFlow), and [OpenBLAS](https://github.com/bytedeco/javacpp-presets/tree/master/openblas) (which also dynamically binds to MKL if found on the system or in the class path). Additions planned for the near future include [MAGMA](http://icl.cs.utk.edu/magma/software/) to supplement functionality missing from cuSOLVER. To simplify further the user experience, we also plan to offer bundles containing all the binaries for [CUDA](https://developer.nvidia.com/cuda-downloads) and [MKL](https://registrationcenter.intel.com/en/forms/?productid=2558&licensetype=2), if it is determined that we are allowed to do so, which appears likely according to the EULAs that accompany their free downloads ([CUDA](http://docs.nvidia.com/cuda/eula/index.html#attachment-a) and
[MKL](https://software.intel.com/sites/default/files/managed/96/0a/Master_EULA_for_Intel_Sw_Development_Products.pdf)). Traditionally, we have to spend time either installing them manually or figuring out a way to automate the process on a case-by-case basis, using when possible platform-specific package managers or containers such as [Docker](https://www.docker.com/), probably along with some scripts. Having such bundles available on the Maven Central Repository would relieve developers and operators from this burden.

Other important changes include a [`HalfIndexer`](http://bytedeco.org/javacpp/apidocs/org/bytedeco/javacpp/indexer/HalfIndexer.html) to process in Java 16-bit half-precision floating-point data from CUDA or other libraries, the adoption of a user defined directory (defaults to `~/.javacpp/cache/`) where JavaCPP now caches native library files, instead of extracting them into a temporary directory, and the introduction of "platform artifacts" for the JavaCPP Presets and JavaCV. Each entry in the cache is a directory with the same name as the JAR file from which the files are extracted, including the subdirectories, for example, `opencv-3.1.0-1.3-linux-x86_64.jar/org/bytedeco/javacpp/linux-x86_64/libopencv_core.so.3.1`. This way, the files are given a (most of the time) unique, predetermined, but easy to remember path, preventing not only the build up of messy temporary files, but also allowing for faster startup times as well as easier integration with native tools, outside the scope of JavaCPP. The technique also works for files other than libraries. Right now, the implementation does not support the extraction of whole directories, but when that becomes possible, one will be able to bundle header files, among other native resources, and have them available for immediate consumption with such a simple call as `Loader.cacheResource(opencv_core.class, "include")`. As a matter of course, the cache functions just as smoothly with uber JARs. "Platform artifacts" can also come in handy. Users are invited to add dependencies on those artifacts suffixed with "-platform", for example, `javacv-platform`, `opencv-platform`, `ffmpeg-platform`, etc, which in turn depend on binaries for all supported platforms. This new strategy was designed to work well with build systems other than Maven (sbt, Gradle, M2Eclipse, etc).

On a final note, before long, we hope to have build servers running allowing us to make releases available in a more timely fashion. Stay tuned for updates, but in the meantime, do not hesitate to contact us through the [mailing list from Google Groups](http://groups.google.com/group/javacpp-project), [issues on GitHub](https://github.com/bytedeco/javacpp/issues), or [the chat room at Gitter](https://gitter.im/bytedeco/javacpp), for any questions that you may have. Contributions are also very welcome!