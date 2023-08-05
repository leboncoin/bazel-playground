![Bazel logo](docs/img/bazel-logo.png)

# bazel-playground - Java class file with code coverage

![build: Bazel](https://img.shields.io/badge/build-bazel-43a047)

[Bazel 6.2.0] seems to have introduced a change impacting the generated
Java class file with code coverage.

The tool `spotbugs` provided by [contrib_rules_jvm] is no longer able to parse
the generated class file.

## Issue

### Using Bazel 6.1.2

```shell
$ bazel build //... # It's OK
...

$ bazel test //... # It's OK
...

$ bazel coverage //... # It's OK
...
```

### Using Bazel 6.2.0

```shell
$ bazel build //... # It's OK
...

$ bazel test //... # It's OK
...

$ bazel coverage //...
...
spotbugs exited with unexpected code 1
The following errors occurred during analysis:
  Invalid class resource foo/Foo.class in .../bazel-out/k8-fastbuild/bin/libfoo.jar:foo/Foo.class
    edu.umd.cs.findbugs.classfile.InvalidClassFileFormatException: Invalid classfile format .../bazel-out/k8-fastbuild/bin/libfoo.jar:foo/Foo.class
      At edu.umd.cs.findbugs.classfile.engine.ClassParser.readConstant(ClassParser.java:250)
      At edu.umd.cs.findbugs.classfile.engine.ClassParser.parse(ClassParser.java:101)
      At edu.umd.cs.findbugs.classfile.impl.ClassPathBuilder.parseClassName(ClassPathBuilder.java:732)
      At edu.umd.cs.findbugs.classfile.impl.ClassPathBuilder.scanCodebase(ClassPathBuilder.java:695)
      At edu.umd.cs.findbugs.classfile.impl.ClassPathBuilder.processWorkList(ClassPathBuilder.java:634)
      At edu.umd.cs.findbugs.classfile.impl.ClassPathBuilder.build(ClassPathBuilder.java:230)
      At edu.umd.cs.findbugs.FindBugs2.buildClassPath(FindBugs2.java:708)
      At edu.umd.cs.findbugs.FindBugs2.execute(FindBugs2.java:245)
      At edu.umd.cs.findbugs.FindBugs.runMain(FindBugs.java:395)
      At edu.umd.cs.findbugs.FindBugs2.main(FindBugs2.java:1231)
      At edu.umd.cs.findbugs.LaunchAppropriateUI.launch(LaunchAppropriateUI.java:106)
      At edu.umd.cs.findbugs.LaunchAppropriateUI.main(LaunchAppropriateUI.java:198)
  Error scanning foo/Foo for referenced classes
    java.lang.IllegalStateException: Unexpected tag of 17 at offset 905 while parsing foo/Foo from .../bazel-out/k8-fastbuild/bin/libfoo.jar:foo/Foo.class
      At edu.umd.cs.findbugs.classfile.engine.ClassParserUsingASM.parse(ClassParserUsingASM.java:723)
      At edu.umd.cs.findbugs.classfile.engine.ClassParserUsingASM.parse(ClassParserUsingASM.java:736)
      At edu.umd.cs.findbugs.classfile.engine.ClassInfoAnalysisEngine.analyze(ClassInfoAnalysisEngine.java:79)
      At edu.umd.cs.findbugs.classfile.engine.ClassInfoAnalysisEngine.analyze(ClassInfoAnalysisEngine.java:38)
      At edu.umd.cs.findbugs.classfile.impl.AnalysisCache.getClassAnalysis(AnalysisCache.java:261)
      At edu.umd.cs.findbugs.FindBugs2.buildReferencedClassSet(FindBugs2.java:806)
      At edu.umd.cs.findbugs.FindBugs2.execute(FindBugs2.java:249)
      At edu.umd.cs.findbugs.FindBugs.runMain(FindBugs.java:395)
      At edu.umd.cs.findbugs.FindBugs2.main(FindBugs2.java:1231)
      At edu.umd.cs.findbugs.LaunchAppropriateUI.launch(LaunchAppropriateUI.java:106)
      At edu.umd.cs.findbugs.LaunchAppropriateUI.main(LaunchAppropriateUI.java:198)

Exception in thread "main" edu.umd.cs.findbugs.NoClassesFoundToAnalyzeException: No classes found to analyze in .../bazel-out/k8-fastbuild/bin/libfoo.jar *JrtfsCodeBase [file=.../external/remotejdk17_linux/lib/jrt-fs.jar] 
	at edu.umd.cs.findbugs.FindBugs2.execute(FindBugs2.java:304)
	at edu.umd.cs.findbugs.FindBugs.runMain(FindBugs.java:395)
	at edu.umd.cs.findbugs.FindBugs2.main(FindBugs2.java:1231)
	at edu.umd.cs.findbugs.LaunchAppropriateUI.launch(LaunchAppropriateUI.java:106)
	at edu.umd.cs.findbugs.LaunchAppropriateUI.main(LaunchAppropriateUI.java:198)
...
//:foo-spotbugs                                                          FAILED in 0.6s
  .../bazel-out/k8-fastbuild/testlogs/foo-spotbugs/test.log

Executed 1 out of 1 test: 1 fails locally.
```

## Cause

```shell
$ bazelisk --bisect=6.1.2..6.2.0 coverage //...
...
--- Bisect Result

first bad commit is https://github.com/bazelbuild/bazel/commit/e4682f6c6fa5d8d67d667aa6593a34370e689cf5
```

```text
commit e4682f6c6fa5d8d67d667aa6593a34370e689cf5 (github/release-6.2.0rc1)
Author: xxx <xxx@xxx>
Date:   Mon Apr 24 19:00:22 2023 +0000

    [6.2.0] Update java_tools v12.1 (#18197)

    * Separate prebuilt darwin java_tools for x86_64 and arm64

    This is step 1 of https://github.com/bazelbuild/bazel/issues/17780

    Closes #17767.

    PiperOrigin-RevId: 517115807
    Change-Id: If2bb85a8a15fd126ab52e5738e3d9558da813090

    * Update rules_java and remove references to "remote_java_tools_darwin"

    This is the last step of https://github.com/bazelbuild/bazel/issues/17780

    This updates rules_java to v5.5.0 for bazel and bazel_tools and cleans up any remaining references to remote_java_tools_darwin,  remote_java_tools_test_darwin, and remote_java_tools_darwin_for_testing

    Closes #17843.

    PiperOrigin-RevId: 519059189
    Change-Id: Ic04f3a2e5cc12e6b7731f6d8a3551553e172e000

    * Update java_tools v12.1

    https://github.com/bazelbuild/java_tools/issues/69

    Closes #18097.

    PiperOrigin-RevId: 526116409
    Change-Id: I4f31046d7e5b149c5035b0ede0699d74685a1eb8

    ---------

    Co-authored-by: xxx <xxx@xxx>
```

We no longer have any problems if we downgrade the version of `bazel_tools` used to **v11.12**
(last release before **v12.0**), by inserting these lines below in the _WORKSPACE_ file:

```starlark
http_archive(
    name = "remote_java_tools",
    sha256 = "af20366f926b1dadf8c084a51936116ef2f0db90e73e94b406c4ad8180f0788d",
    urls = [
        "https://mirror.bazel.build/bazel_java_tools/releases/java/v11.12/java_tools-v11.12.zip",
        "https://github.com/bazelbuild/java_tools/releases/download/java_v11.12/java_tools-v11.12.zip",
    ],
)
```

[contrib_rules_jvm]: https://github.com/bazel-contrib/rules_jvm
[Bazel 6.2.0]: https://github.com/bazelbuild/bazel/releases/tag/6.2.0
