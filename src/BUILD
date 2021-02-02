filegroup(
    name = "lj_hdrs",
    srcs = glob([
        "lj_*.h",
        "lib_*.h",
    ]),
    visibility = ["//visibility:public"],
)

LJ_COPTS = [
    "-O3",
    "-fPIC",
    "-fomit-frame-pointer",
    "-D_FILE_OFFSET_BITS=64",
    "-D_LARGEFILE_SOURCE",
    "-U_FORTIFY_SOURCE",
    "-DLUA_MULTILIB=\"lib\"",
    "-fno-stack-protector",
]

genrule(
    name = "buildvm_arch",
    srcs = [
        "vm_x64.dasc",
        "//dynasm",
        "//dynasm:dasm_lua",
    ],
    outs = [
        "buildvm_arch.h",
    ],
    cmd = "$(location //src/host:minilua) $(location //dynasm:dynasm) -D ENDIAN_LE -D P64 -D JIT -D FFI -D FPU -D HFABI -D VER= -o $(location buildvm_arch.h) $(location vm_x64.dasc)",
    tools = [
        "//src/host:minilua",
    ],
    visibility = ["//visibility:public"],
)

genrule(
    name = "lj_vm",
    outs = [
        "lj_vm.s",
    ],
    cmd = "$(location //src:buildvm) -m elfasm -o $(location lj_vm.s)",
    tools = [
        "//src:buildvm",
    ],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "luajit",
    srcs = glob(
        [
            "lj_*.c",
            "lib_*.c",
            "lua.h",
            "luaconf.h",
            "lauxlib.h",
            "lualib.h",
            "luajit.h",
        ],
        exclude = [
            "ljamalg.c",
            "luajit.c",
        ],
    ) + [
        "//src:lj_ffdef",
        "//src:lj_libdef",
        "//src:lj_folddef",
        "//src:lj_bcdef",
        "//src:lj_recdef",
        "//src:lj_vm",
    ],
    hdrs = ["//src:lj_hdrs"],
    copts = LJ_COPTS,
    includes = [
        ".",
    ],
    visibility = ["//visibility:public"],
)

cc_binary(
    name = "lua",
    srcs = ["luajit.c"],
    copts = LJ_COPTS,
    linkopts = [
        "-ldl",
        "-lm",
    ],
    deps = [
        "//src:luajit",
    ],
)

BUILDVM_COPTS = [
    "-O3",
    "-fomit-frame-pointer",
    "-DLUAJIT_TARGET=LUAJIT_ARCH_x64",
    "-DLJ_ARCH_HASFPU=1",
    "-DLJ_ABI_SOFTFP=0",
]

cc_binary(
    name = "buildvm",
    srcs = [
        "//dynasm:dasm_h",
        "//src:buildvm_arch",
        "//src:lua.h",
        "//src:luaconf.h",
        "//src/host:buildvm_srcs",
        "//src:luajit.h",
    ] + glob(
        ["lj_*.h"],
        exclude = [
            "lj_recdef.h",
            "lj_bcdef.h",
            "lj_bcdef.h",
            "lj_folddef.h",
            "lj_libdef.h",
            "lj_ffdef.h",
        ],
    ),
    copts = BUILDVM_COPTS,
    includes = [
        ".",
    ],
    visibility = ["//visibility:public"],
)

filegroup(
    name = "lib_c",
    srcs = glob(["lib_*.c"]),
)

genrule(
    name = "lj_ffdef",
    srcs = ["//src:lib_c"],
    outs = [
        "lj_ffdef.h",
    ],
    cmd = "$(location //src:buildvm) -m ffdef -o $(location lj_ffdef.h) $(locations //src:lib_c)",
    tools = [
        "//src:buildvm",
    ],
    visibility = ["//visibility:public"],
)

genrule(
    name = "lj_libdef",
    srcs = ["//src:lib_c"],
    outs = [
        "lj_libdef.h",
    ],
    cmd = "$(location //src:buildvm) -m libdef -o $(location lj_libdef.h) $(locations //src:lib_c)",
    tools = [
        "//src:buildvm",
    ],
    visibility = ["//visibility:public"],
)

genrule(
    name = "lj_folddef",
    srcs = ["lj_opt_fold.c"],
    outs = [
        "lj_folddef.h",
    ],
    cmd = "$(location //src:buildvm) -m folddef -o $(location lj_folddef.h) $(SRCS)",
    tools = [
        "//src:buildvm",
    ],
    visibility = ["//visibility:public"],
)

genrule(
    name = "lj_bcdef",
    srcs = ["//src:lib_c"],
    outs = [
        "lj_bcdef.h",
    ],
    cmd = "$(location //src:buildvm) -m bcdef -o $(location lj_bcdef.h) $(SRCS)",
    tools = [
        "//src:buildvm",
    ],
    visibility = ["//visibility:public"],
)

genrule(
    name = "lj_recdef",
    srcs = ["//src:lib_c"],
    outs = [
        "lj_recdef.h",
    ],
    cmd = "$(location //src:buildvm) -m recdef -o $(location lj_recdef.h) $(SRCS)",
    tools = [
        "//src:buildvm",
    ],
    visibility = ["//visibility:public"],
)

genrule(
    name = "vmdef",
    srcs = ["//src:lib_c"],
    outs = [
        "jit/vmdef.lua",
    ],
    cmd = "$(location //src:buildvm) -m vmdef -o $(location jit/vmdef.lua) $(SRCS)",
    tools = [
        "//src:buildvm",
    ],
    visibility = ["//visibility:public"],
)
