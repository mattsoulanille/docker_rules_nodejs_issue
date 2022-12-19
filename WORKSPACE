workspace(
    name = "docker_rules_nodejs_issue",
    managed_directories = {"@npm": ["node_modules"]},
)

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

##################################################
#                                                #
# To see this working, use rules_nodejs <= 5.5.4 #
#                                                #
##################################################
# bazel run :index_bin --toolchain_resolution_debug='nodejs'
http_archive(
    name = "build_bazel_rules_nodejs",
    sha256 = "dcc55f810142b6cf46a44d0180a5a7fb923c04a5061e2e8d8eb05ccccc60864b",  # Toolchain Error
    urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.8.0/rules_nodejs-5.8.0.tar.gz"],

    # sha256 = "0e8a818724c0d5dcc10c31f9452ebd54b2ab94c452d4dcbb0d45a6636d2d5a44", # Toolchain Error
    # urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.7.2/rules_nodejs-5.7.2.tar.gz"],

    # sha256 = "5aae76dced38f784b58d9776e4ab12278bc156a9ed2b1d9fcd3e39921dc88fda", # Toolchain Error
    # urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.7.1/rules_nodejs-5.7.1.tar.gz"],

    # sha256 = "c29944ba9b0b430aadcaf3bf2570fece6fc5ebfb76df145c6cdad40d65c20811", # Toolchain Error
    # urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.7.0/rules_nodejs-5.7.0.tar.gz"],

    # 5.6.0 is the first version that has a toolchain error when used with rules_docker
    # sha256 = "b011d6206e4e76696eda8287618a2b6375ff862317847cdbe38f8d0cd206e9ce",  # Toolchain Error
    # urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.6.0/rules_nodejs-5.6.0.tar.gz"],

    # 5.5.4 is the latest working version
    # sha256 = "493bb318d98bb7492cb30e534ad33df2fc5539b43d4dcc4e294a5cc60a126902",  # Works
    # urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.5.4/rules_nodejs-5.5.4.tar.gz"],

    # sha256 = "f10a3a12894fc3c9bf578ee5a5691769f6805c4be84359681a785a0c12e8d2b6", # Works
    # urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.5.3/rules_nodejs-5.5.3.tar.gz"],
)

load("@build_bazel_rules_nodejs//:repositories.bzl", "build_bazel_rules_nodejs_dependencies")

build_bazel_rules_nodejs_dependencies()

load("@rules_nodejs//nodejs:repositories.bzl", "nodejs_register_toolchains")

# Use node 18
nodejs_register_toolchains(
    name = "nodejs",
    node_version = "16.14.0",
)

load("@rules_nodejs//nodejs:yarn_repositories.bzl", "yarn_repositories")

yarn_repositories(
    name = "yarn",
    node_repository = "nodejs",
)

# The yarn_install rule runs yarn anytime the package.json or yarn.lock file changes.
# It also extracts and installs any Bazel rules distributed in an npm package.
load("@build_bazel_rules_nodejs//:index.bzl", "yarn_install")

yarn_install(
    # Name this npm so that Bazel Label references look like @npm//package
    name = "npm",
    exports_directories_only = False,
    package_json = "//:package.json",
    package_path = "/",
    symlink_node_modules = True,
    yarn = "@yarn//:bin/yarn",
    yarn_lock = "//:yarn.lock",
)

load("@bazel_tools//tools/build_defs/repo:git.bzl", "git_repository")

git_repository(
    name = "io_bazel_rules_docker",
    commit = "48ad6d6df43d1e4b9feeec961995aef01dd72080",
    remote = "https://github.com/bazelbuild/rules_docker.git",
)

load(
    "@io_bazel_rules_docker//repositories:repositories.bzl",
    container_repositories = "repositories",
)

container_repositories()

#########################################################################
#                                                                       #
# Uncomment this region to reproduce the error in rules_nodejs >= 5.6.0 #
#                                                                       #
#########################################################################

# 'Fixed' by using rules_go version 0.33.0.
http_archive(
    name = "io_bazel_rules_go",
    sha256 = "685052b498b6ddfe562ca7a97736741d87916fe536623afb7da2824c0211c369",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_go/releases/download/v0.33.0/rules_go-v0.33.0.zip",
        "https://github.com/bazelbuild/rules_go/releases/download/v0.33.0/rules_go-v0.33.0.zip",
    ],
)

load("@io_bazel_rules_go//go:deps.bzl", "go_register_toolchains", "go_rules_dependencies")

go_rules_dependencies()

go_register_toolchains(version = "1.18.3")

load("@io_bazel_rules_docker//repositories:deps.bzl", container_deps = "deps")

container_deps()

########################
#                      #
# End uncomment region #
#                      #
########################

# Extra stuff I'm also hoping to get working.

# load(
#     "@io_bazel_rules_docker//nodejs:image.bzl",
#     _nodejs_image_repos = "repositories",
# )

# _nodejs_image_repos()

# load("@io_bazel_rules_docker//container:pull.bzl", "container_pull")

# # Load base bazel container for building the CI container
# # https://console.cloud.google.com/gcr/images/cloud-builders/global/bazel
# container_pull(
#     name = "bazel_image",
#     digest = "sha256:ffae30fa83005dcdba4359f4aba4dfdd26c43f572665657ad1fccf1d30de48f4",
#     registry = "gcr.io",
#     repository = "cloud-builders/bazel",
# )
