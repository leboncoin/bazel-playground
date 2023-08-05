workspace(name = "bazel-playground")

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

#
# Override java_tools to use v11.12 (last release < v12.0)
#

# http_archive(
#     name = "remote_java_tools",
#     sha256 = "af20366f926b1dadf8c084a51936116ef2f0db90e73e94b406c4ad8180f0788d",
#     urls = [
#         "https://mirror.bazel.build/bazel_java_tools/releases/java/v11.12/java_tools-v11.12.zip",
#         "https://github.com/bazelbuild/java_tools/releases/download/java_v11.12/java_tools-v11.12.zip",
#     ],
# )

#
# Java linting setup
#

APPLE_RULES_LINT_VERSION = "0.3.2"

APPLE_RULES_LINT_SHA256 = "7c3cc45a95e3ef6fbc484a4234789a027e11519f454df63cbb963ac499f103f9"

http_archive(
    name = "apple_rules_lint",
    sha256 = APPLE_RULES_LINT_SHA256,
    strip_prefix = "apple_rules_lint-%s" % APPLE_RULES_LINT_VERSION,
    url = "https://github.com/apple/apple_rules_lint/archive/refs/tags/%s.tar.gz" % APPLE_RULES_LINT_VERSION,
)

load("@apple_rules_lint//lint:repositories.bzl", "lint_deps")

lint_deps()

load("@apple_rules_lint//lint:setup.bzl", "lint_setup")

# Register all linters to be executed in the `java_test_suite` rule.
lint_setup({
    "java-spotbugs": "@contrib_rules_jvm//java:spotbugs-default-config",
})

#
# Java dependencies
#

RULES_JVM_VERSION = "5.3"

RULES_JVM_SHA256 = "6cc8444b20307113a62b676846c29ff018402fd4c7097fcd6d0a0fd5f2e86429"

http_archive(
    name = "rules_jvm_external",
    sha256 = RULES_JVM_SHA256,
    strip_prefix = "rules_jvm_external-%s" % RULES_JVM_VERSION,
    url = "https://github.com/bazelbuild/rules_jvm_external/archive/%s.zip" % RULES_JVM_VERSION,
)

load("@rules_jvm_external//:repositories.bzl", "rules_jvm_external_deps")

rules_jvm_external_deps()

load("@rules_jvm_external//:setup.bzl", "rules_jvm_external_setup")

rules_jvm_external_setup()

#
# JVM contributions, such as junit5 support, linting etc
#

CONTRIB_RULES_JVM_VERSION = "0.17.0"

CONTRIB_RULES_JVM_SHA256 = "9082d3fb20724c683cd7f909cff2711a5d40fd8529a40247f879e14aa917a8b4"

http_archive(
    name = "contrib_rules_jvm",
    sha256 = CONTRIB_RULES_JVM_SHA256,
    strip_prefix = "rules_jvm-%s" % CONTRIB_RULES_JVM_VERSION,
    url = "https://github.com/bazel-contrib/rules_jvm/archive/refs/tags/v%s.tar.gz" % CONTRIB_RULES_JVM_VERSION,
)

load("@contrib_rules_jvm//:repositories.bzl", "contrib_rules_jvm_deps")

contrib_rules_jvm_deps()

load("@contrib_rules_jvm//:setup.bzl", "contrib_rules_jvm_setup")

contrib_rules_jvm_setup()
