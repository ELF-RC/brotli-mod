# Brotli MOD

English | 中文

Original project: https://github.com/google/brotli

## Command tree

```
brotli
├── compress                     压缩（默认行为）
│   ├── -# / -q N                压缩级别（-# 等价 -q #，0-9；-q 0-11）
│   ├── -w N / --lgwin=N         LZ77 窗口大小，2^N - 16（0 自动；10-24）
│   ├── --large_window=N         不兼容大窗口位流（0, 10-30），非 RFC 7932
│   ├── -T N / --threads=N       多线程并行压缩（0=按核数自动；默认串行）
│   ├── -o FILE                  指定输出文件（仅限单输入）
│   ├── -S SUF / --suffix=SUF    输出后缀（默认 .br）
│   ├── -D FILE / --dictionary   使用 FILE 作为 raw（LZ77）字典
│   ├── -C B64 / --comment=B64   嵌入/校验 base64 注释（≤80 字节）
│   ├── -Z / --best              等价 -q 11（默认）
│   ├── -k / -j / -s             保留源文件 / 删除源文件 / 输出更大时丢弃
│   ├── -f / -n                  强制覆盖 / 不拷贝源文件属性
│   ├── -c                       输出到 stdout
│   └── -v                       显示进度
├── -d / --decompress            解压
│   ├── -K / --concatenated      允许拼接的多流作为输入
│   └── -t / --test              校验压缩文件完整性（不解压）
└── -V / --version               显示版本
```

## What the release contains

The build workflow compiles a single native binary on each architecture (amd64 + arm64) and ships it as one artifact:

```
brotli   # CMake-built native CLI: encoder + decoder + integrity test
```

No runtime dependencies beyond the system C library; POSIX links `pthread`, Windows uses `CreateThread`, Emscripten builds have the parallel path compiled out.

## How it differs from upstream

- **`-T N` / `--threads N`** — multi-threaded compression for large regular files (>16 MiB). Output is a single standard Brotli stream, decodable by any existing decoder. `-T 0` picks the core count automatically; files ≤16 MiB or `N=1` fall back to the serial path, so existing scripts are unchanged.
- All other flags, defaults, and behaviour match upstream `brotli 1.2.0`.

## Build

CMake + GitHub Actions only; no Python packaging, Bazel publish, or research tooling.

```sh
cmake -S . -B out -DCMAKE_BUILD_TYPE=Release -DBROTLI_BUILD_TOOLS=ON -DBROTLI_DISABLE_TESTS=ON
cmake --build out --parallel
```

CI triggers on push to `master` and `edge`, plus PRs and manual dispatch.

## License

MIT. See `LICENSE`.
