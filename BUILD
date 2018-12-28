# Bazel (http://bazel.io/) BUILD file for libuv.

# TODO(Erich) Missing Windows and Linux support. Android is incomplete.

config_setting(
    name = "android",
    values = {
        "crosstool_top": "//external:android/crosstool",
    },
)

config_setting(
    name = "darwin",
    values = {
        "cpu": "darwin",
    }
)

config_setting(
    name = "k8",
    values = {
        "cpu": "amd64",
    }
)

config_setting(
    name = "ios_armv7",
    values = {
        "ios_cpu": "armv7",
    },
)

config_setting(
    name = "ios_armv7s",
    values = {
        "ios_cpu": "armv7s",
    },
)

config_setting(
    name = "ios_arm64",
    values = {
        "ios_cpu": "arm64",
    },
)

COPTS = [
    "--std=gnu89",
    "-pedantic",
    "-Wall",
    "-Wextra",
    "-Wno-unused-parameter",
    "-D_LARGEFILE_SOURCE",
    "-D_FILE_OFFSET_BITS=64",
    "-Ilibuv/include",
    "-Ilibuv/src",
]

UNIX_COPTS = COPTS + [
    "-Ilibuv/src/unix",
]

DARWIN_COPTS = UNIX_COPTS + [
    "-D_DARWIN_USE_64_BIT_INODE=1",
    "-D_DARWIN_UNLIMITED_SELECT=1",
]

LINUX_COPTS = UNIX_COPTS + [
    "-D_GNU_SOURCE",
]

IOS_ARM_COPTS = DARWIN_COPTS + [
    "-DOS_IOS",
    "-miphoneos-version-min=7.0",
    "-arch armv7",
    "-arch armv7s",
    "-arch arm64",
    "-D__thread=",
    "-isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS9.3.sdk/",
]

# Android builds do not need to link in a separate pthread library.
LINK_OPTS = select({
    ":android": [],
    "//conditions:default": ["-lpthread"],
})

CLEANHDRS = [
  "include/uv.h",
  "include/uv-errno.h",
  "include/uv-threadpool.h",
  "include/uv-version.h",
]

CLEANSRCS = [
    "src/fs-poll.c",
    "src/heap-inl.h",
    "src/inet.c",
    "src/queue.h",
    "src/threadpool.c",
    "src/uv-common.c",
    "src/uv-common.h",
    "src/version.c",
]

WINNT_SRCS = CLEANSRCS + [
]

UNIX_HDRS = CLEANHDRS + [
  "include/uv-unix.h",
  "include/tree.h",
]

UNIX_SRCS = CLEANSRCS + [
    "src/unix/async.c",
    "src/unix/atomic-ops.h",
    "src/unix/core.c",
    "src/unix/dl.c",
    "src/unix/fs.c",
    "src/unix/getaddrinfo.c",
    "src/unix/getnameinfo.c",
    "src/unix/internal.h",
    "src/unix/loop.c",
    "src/unix/loop-watcher.c",
    "src/unix/pipe.c",
    "src/unix/poll.c",
    "src/unix/process.c",
    "src/unix/signal.c",
    "src/unix/spinlock.h",
    "src/unix/stream.c",
    "src/unix/tcp.c",
    "src/unix/thread.c",
    "src/unix/timer.c",
    "src/unix/tty.c",
    "src/unix/udp.c",
]

ANDROID_HDRS = UNIX_HDRS + [
    "include/android-ifaddrs.h",
    "include/pthread-barrier.h",
]

ANDROID_SRCS = UNIX_SRCS + [
    "src/unix/android-ifaddrs.c",
    "src/unix/pthread-fixes.c",
    "src/unix/pthread-barrier.c",
]

DARWIN_HDRS = UNIX_HDRS + [
    "include/uv-darwin.h",
    "include/pthread-barrier.h",
]

DARWIN_SRCS = UNIX_SRCS + [
    "src/unix/darwin.c",
    "src/unix/darwin-proctitle.c",
    "src/unix/fsevents.c",
    "src/unix/kqueue.c",
    "src/unix/proctitle.c",
    "src/unix/pthread-barrier.c",
]

LINUX_HDRS = UNIX_HDRS + [
    "include/uv-linux.h",
]

LINUX_SRCS = UNIX_SRCS + [
    "src/unix/linux-core.c",
    "src/unix/linux-inotify.c",
    "src/unix/linux-syscalls.c",
    "src/unix/linux-syscalls.h",
    "src/unix/proctitle.c",
]

# You can use this library as: #include "libuv/uv.h"
cc_library(
    name = "libuv",
    hdrs = glob(["include/*.h"]),
    include_prefix = "include",
    deps = [":libuv_impl"],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "libuv_impl",
    hdrs = select({
        ":darwin": DARWIN_HDRS,
        ":ios_armv7": DARWIN_HDRS,
        ":ios_armv7s": DARWIN_HDRS,
        ":ios_arm64": DARWIN_HDRS,
        "//conditions:default": LINUX_HDRS,
    }),
    srcs = select({
        ":darwin": DARWIN_SRCS,
        ":ios_armv7": DARWIN_SRCS,
        ":ios_armv7s": DARWIN_SRCS,
        ":ios_arm64": DARWIN_SRCS,
        "//conditions:default": LINUX_SRCS,
    }),
    copts = select({
        ":darwin": DARWIN_COPTS,
        ":ios_armv7": IOS_ARM_COPTS,
        ":ios_armv7s": IOS_ARM_COPTS,
        ":ios_arm64": IOS_ARM_COPTS,
        "//conditions:default": LINUX_COPTS,
    }),
    linkopts = LINK_OPTS,
    visibility = ["//visibility:private"],
)
