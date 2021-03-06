# The WORKSPACE file tells Bazel that this directory is a "workspace", which is like a project root.
# The content of this file specifies all the external dependencies Bazel needs to perform a build.

####################################
# ESModule imports (and TypeScript imports) can be absolute starting with the workspace name.
# The name of the workspace should match the npm package where we publish, so that these
# imports also make sense when referencing the published package.
workspace(name = "angular_bazel_example")

####################################
# Fetch external repositories containing Bazel build toolchain support.
# Bazel doesn't support transitive WORKSPACE deps, so we must install those too.

# Allows Bazel to run tooling in Node.js
http_archive(
    name = "build_bazel_rules_nodejs",
    url = "https://github.com/gregmagolan/rules_nodejs/archive/519b15b7bd5e0177e6f4ba0f6e58e9b18c17dd18.zip",
    strip_prefix = "rules_nodejs-519b15b7bd5e0177e6f4ba0f6e58e9b18c17dd18",
    sha256 = "c7f67af525e5b4540dd83f98960a05943c1924928e56eb681477a615f2ae3afd",
)

http_archive(
    name = "bazel_skylib",
    url = "https://github.com/bazelbuild/bazel-skylib/archive/0.3.1.zip",
    strip_prefix = "bazel-skylib-0.3.1",
    sha256 = "95518adafc9a2b656667bbf517a952e54ce7f350779d0dd95133db4eb5c27fb1",
)

# The Bazel buildtools repo contains tools like the BUILD file formatter, buildifier
# This commit matches the version of buildifier in angular/ngcontainer
# If you change this, also check if it matches the version in the angular/ngcontainer
# version in /.circleci/config.yml
BAZEL_BUILDTOOLS_VERSION = "82b21607e00913b16fe1c51bec80232d9d6de31c"

http_archive(
    name = "com_github_bazelbuild_buildtools",
    url = "https://github.com/bazelbuild/buildtools/archive/%s.zip" % BAZEL_BUILDTOOLS_VERSION,
    strip_prefix = "buildtools-%s" % BAZEL_BUILDTOOLS_VERSION,
    sha256 = "edb24c2f9c55b10a820ec74db0564415c0cf553fa55e9fc709a6332fb6685eff",
)

# Runs the TypeScript compiler
http_archive(
    name = "build_bazel_rules_typescript",
    url = "https://github.com/bazelbuild/rules_typescript/archive/0.15.3.zip",
    strip_prefix = "rules_typescript-0.15.3",
    sha256 = "a2b26ac3fc13036011196063db1bf7f1eae81334449201dc28087ebfa3708c99",
)

# Used by the ts_web_test_suite rule to provision browsers
http_archive(
    name = "io_bazel_rules_webtesting",
    # Use a commit SHA because we need a release
    # https://github.com/bazelbuild/rules_webtesting/issues/273
    url = "https://github.com/bazelbuild/rules_webtesting/archive/bbfc846d98dacb0fb40dd9173acfe4070e3e0f62.zip",
    strip_prefix = "rules_webtesting-bbfc846d98dacb0fb40dd9173acfe4070e3e0f62",
    sha256 = "a79e2d681b7c9ddc51e7974ddb385b9ee2b389cdc823dd3e78e18936337e4c5a",
)

# Runs the Sass CSS preprocessor
http_archive(
    name = "io_bazel_rules_sass",
    url = "https://github.com/bazelbuild/rules_sass/archive/0.1.0.zip",
    strip_prefix = "rules_sass-0.1.0",
    sha256 = "b243c4d64f054c174051785862ab079050d90b37a1cef7da93821c6981cb9ad4",
)

# Some of the TypeScript tooling is written in Go.
http_archive(
    name = "io_bazel_rules_go",
    url = "https://github.com/bazelbuild/rules_go/releases/download/0.13.0/rules_go-0.13.0.tar.gz",
    sha256 = "ba79c532ac400cefd1859cbc8a9829346aa69e3b99482cd5a54432092cbc3933",
)

####################################
# Tell Bazel about some workspaces that were installed from npm.

# The @angular repo contains rule for building Angular applications
local_repository(
    name = "angular",
    path = "node_modules/@angular/bazel",
)

# The @rxjs repo contains targets for building rxjs with bazel
local_repository(
    name = "rxjs",
    path = "node_modules/rxjs/src",
)

####################################
# Load and install our dependencies downloaded above.

load("@build_bazel_rules_nodejs//:defs.bzl", "check_bazel_version", "node_repositories", "yarn_install")

node_repositories(package_json = ["//:package.json"])

load("@io_bazel_rules_go//go:def.bzl", "go_rules_dependencies", "go_register_toolchains")

go_rules_dependencies()
go_register_toolchains()

load("@io_bazel_rules_webtesting//web:repositories.bzl", "browser_repositories", "web_test_repositories")

web_test_repositories()
browser_repositories(
    chromium = True,
    firefox = True,
)

load("@build_bazel_rules_typescript//:defs.bzl", "ts_setup_workspace")

ts_setup_workspace()

load("@io_bazel_rules_sass//sass:sass_repositories.bzl", "sass_repositories")

sass_repositories()

####################################
# Setup our local toolchain

yarn_install(
    name = "history-server_runtime_deps",
    package_json = "//tools/history-server:package.json",
    yarn_lock = "//tools/history-server:yarn.lock",
)
