load("@rules_cc//cc:defs.bzl", "cc_binary", "cc_library")
load("@rules_foreign_cc//tools/build_defs:cmake.bzl", "cmake_external")
load("@rules_proto//proto:defs.bzl", "proto_library")
load("@rules_proto_grpc//go:defs.bzl", "go_grpc_library")
load("@rules_proto_grpc//cpp:defs.bzl", "cpp_grpc_library")
load("@io_bazel_rules_go//go:def.bzl", "go_binary", "go_library")


proto_library(
    name = "drone_proto",
    srcs = ["drone.proto"],
    visibility = ["//visibility:public"],
)

cpp_grpc_library(
    name = "drone_proto_cc",
    deps = [
        ":drone_proto"
    ],
    visibility = ["//visibility:public"],
)

go_grpc_library(
    name = "drone_proto_go",
    compilers = ["@io_bazel_rules_go//proto:go_grpc"],
    importpath = "github.com/lobarr/drone/core",
    deps = [":drone_proto"],
    visibility = ["//visibility:public"],
)

cmake_external(
    name = "leveldb",
    cache_entries = {
        "CMAKE_BUILD_TYPE": "Release"
    },
    lib_source = "@com_google_leveldb//:all",
    headers_only = True,
    # out_include_dir = "include/leveldb",
    visibility = ["//visibility:__pkg__"],
)
cc_library(
    name = "drone_daemon_library",
    srcs = [
        "droneService.hpp",
        "fileContainer.cc",
        "fileController.cc",
        "main.cc",
        "serializeUtil.cc",
    ],
    hdrs = [
        "constants.hpp",
        "dbService.hpp",
        "fileContainer.hpp",
        "fileController.hpp",
        "serializeUtil.hpp",
    ],
    deps = [
        ":drone_proto_cc",
        ":drone_db_service.cc",
        ":leveldb",
        "@com_google_protobuf//:protobuf_lite",
        "@boost//:uuid",
        "@boost//:thread",
        "@boost//:filesystem",
    ],
    visibility = ["//visibility:__pkg__"],
)

cc_binary(
    name = "drone_daemon",
    deps = [
        ":drone_daemon_library",
    ],
)

go_binary(
    name = "drone_db_service",
    embed = [":drone_db_service_library"],
    linkmode = "c-archive",
    visibility = ["//visibility:__pkg__"],
)

go_library(
    name = "drone_db_service_library",
    srcs = ["dbService.go"],
    cgo = True,
    importpath = "github.com/Lobarr/drone/core/",
    deps = ["@com_github_syndtr_goleveldb//leveldb:go_default_library"],
    visibility = ["//visibility:__pkg__"],
)
