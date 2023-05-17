![Bazel logo](docs/img/bazel-logo.png)

# bazel-playground - Intellij / go_source

![build: Bazel](https://img.shields.io/badge/build-bazel-43a047)

The goal of this environment is to test [Bazel Intellij plugin] for a [Go] project
using a [go_test] and a [go_library] with embedded [go_source] rule containing:

- Source files
- Generated source files
- Go dependencies.

[Bazel Intellij plugin]: https://ij.bazel.build/
[Go]: https://go.dev/

[go_source]: https://github.com/bazelbuild/rules_go/blob/master/docs/go/core/rules.md#go_source
[go_test]: https://github.com/bazelbuild/rules_go/blob/master/docs/go/core/rules.md#go_test
[go_library]: https://github.com/bazelbuild/rules_go/blob/master/docs/go/core/rules.md#go_library
