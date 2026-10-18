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

Linux binaries are statically linked against musl (built inside Alpine):
- `MALLOC=libc` — standard C allocator (jemalloc is omitted; it does not static-link cleanly against musl)
- `BUILD_TLS=no` — TLS disabled (not needed for localhost development)
- `LDFLAGS=-static` — fully static; the binary carries no runtime dependency on any system library
- ARM64 is built in an aarch64 Alpine environment via QEMU — the same path as x64, with no separate cross-toolchain

A musl-static binary runs on **any** Linux host — glibc or musl — with nothing installed, and has no runtime NSS dynamic loading (the hidden dependency a static glibc binary would otherwise keep).

macOS binaries link against libSystem, which is always present, so they are self-contained without static linking. The x64 build is cross-compiled on an ARM64 runner with `-arch x86_64`.

These are development binaries optimized for portability, not production performance. For production deployments, use the official Valkey packages with jemalloc and TLS support.

## Source & License

Built from [valkey-io/valkey](https://github.com/valkey-io/valkey) — the open-source high-performance key/value store.

The binaries are Valkey and stay under its BSD 3-Clause License; every release carries the upstream license text as the `LICENSE` asset next to the binaries, as the license asks of binary distributions. The build scripts of this repository are under the [BSD 3-Clause License](LICENSE) as well.

This project is NOT affiliated with, endorsed by, or sponsored by the Valkey contributors or the Linux Foundation.