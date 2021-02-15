cc_binary(
    name = "minilua",
    srcs = ["minilua.c"],
    copts = [
        "-O3",
        "-Wall",
        "-DLUAJIT_TARGET=LUAJIT_ARCH_x64",
        "-DLJ_ARCH_HASFPU=1",
        "-DLJ_ABI_SOFTFP=0",
    ],
    includes = [
        ".",
    ],
    linkopts = [
        "-lm",
    ],
    visibility = ["//visibility:public"],
)

filegroup(
    name = "buildvm_srcs",
    srcs = glob([
        "buildvm*.h",
        "buildvm*.c",
    ]),
    visibility = ["//visibility:public"],
)
