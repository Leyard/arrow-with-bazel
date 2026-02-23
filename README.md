# Build Arrow with Bazel

Build [Apache Arrow](https://arrow.apache.org/) C++ (v21.0.0) with Bazel using bzlmod.

## Available Targets

| Alias | Actual Target | Description |
|-------|--------------|-------------|
| `@arrow//:arrow` | `//cpp/src/arrow:core` | Core Arrow library |
| `@arrow//:parquet` | `//cpp/src/parquet:parquet` | Parquet file format |
| `@arrow//:acero` | `//cpp/src/arrow/acero:acero` | Query execution engine |
| `@arrow//:csv` | `//cpp/src/arrow/csv:csv` | CSV reader/writer |
| `@arrow//:dataset` | `//cpp/src/arrow/dataset:dataset` | Dataset API |
| `@arrow//:filesystem` | `//cpp/src/arrow/filesystem:filesystem` | Filesystem abstraction |
| `@arrow//:flight` | `//cpp/src/arrow/flight:flight` | Arrow Flight RPC |
| `@arrow//:flight_sql` | `//cpp/src/arrow/flight/sql:flight_sql` | Flight SQL |
| `@arrow//:ipc` | `//cpp/src/arrow/ipc:ipc` | IPC / Feather |
| `@arrow//:engine` | `//cpp/src/arrow/engine:engine` | Substrait engine |

## Platform Support

- Linux (x86_64)
- macOS (x86_64, arm64)

## Usage

Add to your `MODULE.bazel`:

```starlark
bazel_dep(name = "arrow", version = "21.0.0")
archive_override(
    module_name = "arrow",
    patch_args = ["-p1", "-N"],
    patches = [
        "//patches:arrow_fix_includes.patch",
        "//patches:arrow_add_bazel.patch",
    ],
    sha256 = "5d3f8db7e72fb9f65f4785b7a1634522e8d8e9657a445af53d4a34a3849857b5",
    strip_prefix = "apache-arrow-21.0.0",
    url = "https://github.com/apache/arrow/releases/download/apache-arrow-21.0.0/apache-arrow-21.0.0.tar.gz",
)
```

Then depend on any target in your `BUILD.bazel`:

```starlark
cc_binary(
    name = "main",
    srcs = ["main.cc"],
    deps = ["@arrow//:arrow"],
)
```

## SIMD Support

AVX2 is enabled automatically on x86_64 platforms. To also enable AVX-512:

```shell
bazel build --@arrow//cpp/src/arrow:simd_avx512=True //:main
```

## Example

Check `BUILD.bazel` and `main.cc` for a working demo:

```shell
bazel run //:main
```
