# valkey-server

Pre-built Valkey server binaries for development environments.

## What is this?

This repository provides cross-platform Valkey binaries built from [valkey-io/valkey](https://github.com/valkey-io/valkey) via GitHub Actions. Valkey does not provide pre-built binaries for macOS — this repository fills that gap for development tools that embed Valkey as an infrastructure dependency.

## Platforms

| File | Platform |
|------|----------|
| `valkey-server-linux-amd64` | Linux x64 |
| `valkey-server-linux-arm64` | Linux ARM64 |
| `valkey-server-darwin-amd64` | macOS x64 (Intel) |
| `valkey-server-darwin-arm64` | macOS ARM64 (Apple Silicon) |

Windows is not supported — Valkey (a C project built around POSIX) does not compile for Windows natively. Tools embedding these binaries fall back to in-memory mode on Windows, or connect to an external Valkey/Redis server.

## Download

Binaries are available on the [Releases](https://github.com/gabion-dev/valkey-server/releases) page. Each release includes SHA256 checksums.

## Build details

Linux binaries are built on Ubuntu 22.04, natively for each architecture, and link only glibc's `libc` and `libm` from the host:
- `MALLOC=libc` — standard C allocator (jemalloc is omitted)
- `BUILD_TLS=no` — TLS disabled (not needed for localhost development)
- Lua scripting stays in, linked into the server: since 9.1 Valkey builds Lua as a module and finds even the built-in one through `dlopen(NULL)`/`dlsym`, so the server keeps its symbols exported (`-rdynamic`)

glibc is compatible only upward, so a binary built against glibc 2.35 runs on every glibc distribution of that age or newer (the [manylinux](https://peps.python.org/pep-0600/) practice). A fully static musl binary is no longer possible: a static musl program has no `dlopen`, and Valkey 9.1 does not start without it. musl distributions (Alpine) are not supported. The build fails if the binary needs any other library, a symbol version newer than `GLIBC_2.35`, or stops exporting the Lua entry point.

macOS binaries link against libSystem, which is always present, and target macOS 13. The x64 build is cross-compiled on an ARM64 runner with `-arch x86_64`.

Before a release is published, every binary is started on a native machine of its own architecture and must report the tag it was built from, serve on localhost and run a Lua script: on Linux inside clean containers of Ubuntu 22.04, 24.04 and 26.04, Debian 12 and 13, Rocky Linux 9, Fedora, Amazon Linux 2023 and (x64) Arch Linux; on macOS 15 and 26, Apple Silicon and Intel.

These are development binaries optimized for portability, not production performance. For production deployments, use the official Valkey packages with jemalloc and TLS support.

## Source & License

Built from [valkey-io/valkey](https://github.com/valkey-io/valkey) — the open-source high-performance key/value store.

The binaries are Valkey and stay under its BSD 3-Clause License; every release carries the upstream license text as the `LICENSE` asset next to the binaries, as the license asks of binary distributions. The build scripts of this repository are under the [BSD 3-Clause License](LICENSE) as well.

This project is NOT affiliated with, endorsed by, or sponsored by the Valkey contributors or the Linux Foundation.