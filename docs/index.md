---
title: Rusty
description: Small, focused Rust ports of CLI utilities missing from the Rust ecosystem.
---

# Rusty

> A growing collection of small Rust ports of useful CLI tools that exist in other ecosystems but lack a mature Rust equivalent. Each port is a separate crate, independently published.

## Published ports

### [rusty-ts](https://github.com/jsh562/rusty-ts)

**A Rust port of moreutils `ts`** — prefix each line of stdin with a timestamp.

```sh
some-command | rusty-ts                     # default format
some-command | rusty-ts '%Y-%m-%d %H:%M:%S' # custom strftime
some-command | rusty-ts -i                  # elapsed since previous line
some-command | rusty-ts --tz=America/New_York
```

Adds explicit timezone control (`-u`, `--tz=<IANA>`) and a strict moreutils-compat mode for byte-equal output. Static binaries on Linux x86_64/aarch64, macOS x86_64/aarch64, Windows x86_64.

- **Install:** `cargo install rusty-ts` · `cargo binstall rusty-ts`
- **Crates.io:** [crates.io/crates/rusty-ts](https://crates.io/crates/rusty-ts)
- **Docs:** [docs.rs/rusty-ts](https://docs.rs/rusty-ts)
- **Source:** [github.com/jsh562/rusty-ts](https://github.com/jsh562/rusty-ts)

---

### [rusty-sponge](https://github.com/jsh562/rusty-sponge)

**A Rust port of moreutils `sponge`** — soak up all of stdin, then write it atomically to a file. Solves the `cmd file > file` shell-truncation race.

```sh
sort file.txt | rusty-sponge file.txt       # safe in-place rewrite
producer | rusty-sponge | consumer          # batched pipeline passthrough
echo "new" | rusty-sponge -a log.txt        # append mode
```

Sibling-tempfile + atomic-rename for the regular-file path. Hybrid in-memory + tempfile-spill buffer scales to inputs of any size. Strict moreutils-compat mode reproduces the original byte-for-byte for documented inputs. Static binaries on Linux x86_64/aarch64, macOS x86_64/aarch64, Windows x86_64.

- **Install:** `cargo install rusty-sponge` · `cargo binstall rusty-sponge`
- **Crates.io:** [crates.io/crates/rusty-sponge](https://crates.io/crates/rusty-sponge)
- **Docs:** [docs.rs/rusty-sponge](https://docs.rs/rusty-sponge)
- **Source:** [github.com/jsh562/rusty-sponge](https://github.com/jsh562/rusty-sponge)

---

## What's coming

Future ports are drawn from a curated catalog of CLI tools missing from the Rust ecosystem.

Each port is selected by impact (real user demand) × effort (small, focused surface) × gap severity (no Rust equivalent yet) × maintenance burden (sustainable for a solo maintainer over years).

## Why these tools?

Most of these utilities are 20+ years old, stable, and deeply useful — but they're packaged for ecosystems that don't ship on every platform. A Rust port ships as a single static binary that runs on Linux, macOS, and Windows with no language runtime to install. `cargo install` becomes the universal one-liner.

The goal isn't to replace the originals. If you have moreutils installed, keep using it. The Rust ports are for people who don't, or for platforms where the original doesn't ship.

## Principles

- **Behavioral compatibility first.** Each port has a Strict mode that matches the original byte-for-byte for documented inputs, so muscle memory and shell scripts transfer.
- **One job per crate.** No mega-utilities. Each crate is independently named, versioned, and published.
- **Static and portable.** Single static binary that works wherever the original's semantics permit.
- **Honest gap accounting.** If a Rust equivalent already exists and is good, no duplication — it goes in the "covered" list.

## About

Maintained by [James Han](https://github.com/jsh562). Each port is dual-licensed under MIT OR Apache-2.0 — the standard Rust ecosystem posture.

The Rusty ports are clean-room Rust reimplementations of well-known CLI utilities; they contain no source code from the originals. Behavioral interfaces (flag sets, exit codes, output formats) are observed from the published behavior of the originals and reproduced from scratch. The same posture as [`uutils/coreutils`](https://github.com/uutils/coreutils).
