load("//:ts_library.bzl", "ts_library")
load("@io_bazel_rules_sass//:defs.bzl", "multi_sass_binary")
load("@npm//webpack-dev-server:index.bzl", "webpack_dev_server")
load("@npm//webpack:index.bzl", "webpack")
load("@build_bazel_rules_nodejs//:index.bzl", "npm_package_bin")
load("//:css_typings.bzl", "css_typings")
load("//testing:index.bzl", "ts_test_suite")

package(default_visibility = ["//visibility:public"])

filegroup(
    name = "assets",
    srcs = glob(["public/**/*.*"]) + [":index.html"],
)

multi_sass_binary(
    name = "sass",
    srcs = glob(["**/*.scss"]),
)

css_typings(
    name = "css_typings",
    srcs = [":sass"],
)

ts_library(
    name = "client",
    srcs = glob(
        [
            "**/*.ts",
            "**/*.tsx",
        ],
        exclude = ["**/*.spec.ts"],
    ),
    deps = [
        ":css_typings",
        "@npm//@material-ui/core",
        "@npm//@material-ui/icons",
        "@npm//@types/js-base64",
        "@npm//@types/node",
        "@npm//@types/pako",
        "@npm//@types/react",
        "@npm//@types/react-dom",
        "@npm//@types/react-router",
        "@npm//@types/react-router-dom",
        "@npm//@types/uuid",
        "@npm//js-base64",
        "@npm//mobx",
        "@npm//mobx-react",
        "@npm//pako",
        "@npm//react",
        "@npm//react-dom",
        "@npm//react-router",
        "@npm//react-router-dom",
        "@npm//uuid",
    ],
)

ts_test_suite(
    name = "tests",
    srcs = glob(["**/*.spec.ts"]),
    data = [
        "@npm//source-map-support",
    ],
    templated_args = ["--node_options=--require=source-map-support/register"],
    deps = [
        ":client",
        "//testing",
        "@npm//@types/chai",
        "@npm//@types/node",
        "@npm//chai",
    ],
)

webpack_dev_server(
    name = "devserver",
    args = [
        "--config=client/webpack.config.js",
        "--output=./app.bundle.js",
    ],
    data = [
        ":assets",
        ":client",
        ":index.html",
        ":sass",
        ":webpack.config.js",
        "@npm//css-loader",
        "@npm//style-loader",
        "@npm//webpack-cli",
    ],
    tags = [
        "ibazel_notify_changes",
    ],
)

webpack(
    name = "bundler",
    data = [
        ":client",
        ":sass",
        ":webpack.config.js",
        "@npm//css-loader",
        "@npm//style-loader",
    ],
)

npm_package_bin(
    name = "bundle",
    outs = [
        "app.bundle.js",
    ],
    args = [
        "--config=$(location :webpack.config.js)",
        "--output=$(location app.bundle.js)",
        "--mode=production",
    ],
    data = [
        ":webpack.config.js",
        "@npm//webpack-cli",
    ],
    tool = ":bundler",
)

filegroup(
    name = "site",
    srcs = [
        ":app.bundle.js",
        ":assets",
    ],
)
