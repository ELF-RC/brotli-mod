# Brotli MOD

[English](README.md) | 中文

原项目：https://github.com/google/brotli

## 命令树

```
brotli
├── 压缩（默认行为）
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

## 产物内容

构建工作流在每个架构（amd64 + arm64）上编译单个原生二进制，以单一 artifact 发布：

```text
brotli   # CMake 构建的原生 CLI：编码器 + 解码器 + 完整性测试
```

除系统 C 库外无运行依赖；POSIX 链接 `pthread`，Windows 使用 `CreateThread`，Emscripten 构建在编译期禁用并行路径。

## 与上游的区别

- **`-T N` / `--threads N`** — 大常规文件（>16 MiB）的多线程压缩。输出为单个标准 Brotli 流，可被任意现有解码器直接解码。`-T 0` 自动取核数；≤16 MiB 文件或 `N=1` 时回落到原串行路径，现有脚本行为不变。
- 其余参数、默认值和行为与上游 `brotli 1.2.0` 一致。

## 构建

仅 CMake + GitHub Actions；无 Python 打包、Bazel 发布、研究工具链。

```sh
cmake -S . -B out -DCMAKE_BUILD_TYPE=Release -DBROTLI_BUILD_TOOLS=ON -DBROTLI_DISABLE_TESTS=ON
cmake --build out --parallel
```

CI 在 `master` 和 `edge` 分支 push、PR 及手动触发上运行。

## 许可证

MIT，见 `LICENSE`。
