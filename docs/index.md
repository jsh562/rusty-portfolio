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

### [rusty-vipe](https://github.com/jsh562/rusty-vipe)

**A Rust port of moreutils `vipe`** — pop `$EDITOR` mid-pipe, edit the buffered bytes interactively, resume the pipeline with the edited output.

```sh
grep ERROR /var/log/syslog | rusty-vipe | xargs -I {} report-bug.sh "{}"
some-command | rusty-vipe --suffix=.json   # syntax-highlight hint
some-command | rusty-vipe --editor='code --wait'
```

Cross-platform TTY reattachment (`/dev/tty` on Unix, `CONIN$`/`CONOUT$` on Windows) lets the editor drive a real terminal while the pipeline's stdin/stdout stay piped. Editor exits non-zero → no bytes downstream + preserved exit code (clamped on Windows for codes >254). Strict moreutils-compat mode emits byte-equal "exited nonzero, aborting" stderr. Static binaries on Linux x86_64/aarch64, macOS x86_64/aarch64, Windows x86_64.

- **Install:** `cargo install rusty-vipe` · `cargo binstall rusty-vipe`
- **Crates.io:** [crates.io/crates/rusty-vipe](https://crates.io/crates/rusty-vipe)
- **Docs:** [docs.rs/rusty-vipe](https://docs.rs/rusty-vipe)
- **Source:** [github.com/jsh562/rusty-vipe](https://github.com/jsh562/rusty-vipe)

---

### [rusty-pee](https://github.com/jsh562/rusty-pee)

**A Rust port of moreutils `pee`** — `tee` but to commands instead of files. Fan a single stdin stream out to N concurrent shell-spawned children, aggregate their exit codes, surface failures cleanly.

```sh
journalctl -p err | rusty-pee 'grep selinux > sel.log' 'wc -l > count.txt'
some-command | rusty-pee --capture 'cat' 'cat'   # argv-ordered output
some-command | rusty-pee --strict 'validator-A' 'validator-B'
```

Sync blocking `write_all` loop provides natural backpressure — slow children pace the parent to the slowest reader, parent peak memory stays `O(BUFSIZ × N)` even on multi-GiB inputs. When a child closes its stdin early, the parent drops it from the live-set and continues feeding survivors. Default-mode exit aggregation uses `max(child_codes)`; Strict mode uses bitwise OR (byte-equal moreutils). Library API takes `Box<dyn Write + Send>` sinks rather than command strings, so embedders fan out without subprocesses. Static binaries on Linux x86_64/aarch64, macOS x86_64/aarch64, Windows x86_64.

- **Install:** `cargo install rusty-pee` · `cargo binstall rusty-pee`
- **Crates.io:** [crates.io/crates/rusty-pee](https://crates.io/crates/rusty-pee)
- **Docs:** [docs.rs/rusty-pee](https://docs.rs/rusty-pee)
- **Source:** [github.com/jsh562/rusty-pee](https://github.com/jsh562/rusty-pee)

---

### [rusty-pwgen](https://github.com/jsh562/rusty-pwgen)

**A Rust port of Theodore Ts'o's `pwgen`** — generate pronounceable or random passwords from the OS CSPRNG.

```sh
rusty-pwgen                     # default: 160 pronounceable, 8-char passwords
rusty-pwgen 16 5                # 5 passwords, 16 chars each
rusty-pwgen -s -y 24 1          # secure-random + symbols, 24-char single
rusty-pwgen -H seed.txt 12 10   # reproducible: same seed → same passwords
```

Faithful port of `pw_phonemes.c` (alternating consonant/vowel groups with digraphs `ch sh ph th gh ng`, `NOT_FIRST` constraint on `gh`/`ng`). Secure mode samples uniformly from the active character set via Rust's `OsRng`. `-H` reproducible mode chains SHA-256 over the seed file/stdin into ChaCha20Rng; the contract is **locked at v0.1.0** — any change is a MAJOR bump. Library API exposes `Pwgen` + `PwgenBuilder` with three generation methods (`generate_one`/`generate_n`/`iter`); `default-features = false` strips clap and friends for embedders. Static binaries on Linux x86_64/aarch64, macOS x86_64/aarch64, Windows x86_64.

- **Install:** `cargo install rusty-pwgen` · `cargo binstall rusty-pwgen`
- **Crates.io:** [crates.io/crates/rusty-pwgen](https://crates.io/crates/rusty-pwgen)
- **Docs:** [docs.rs/rusty-pwgen](https://docs.rs/rusty-pwgen)
- **Source:** [github.com/jsh562/rusty-pwgen](https://github.com/jsh562/rusty-pwgen)

---

### [rusty-detox](https://github.com/jsh562/rusty-detox)

**A Rust port of Doug Harple's `detox(1)`** — sanitize messy filenames through a configurable filter pipeline.

```sh
rusty-detox -n 'My Résumé (final v2).pdf'   # preview the rename
rusty-detox -r ./Downloads/                  # recursive batch
rusty-detox -s utf_8 *.pdf                   # pick the utf_8 sequence
echo 'hello world.txt' | inline-detox        # streaming companion binary
```

Implements the upstream filter pipeline (`uncgi`, `iso8859_1`, `utf_8`, `safe`, `wipeup`, `max_length`, plus `safe_platform` auto-enabled on Windows) over a `Sequence` type. Three built-in sequences (`default`, `iso8859_1`, `utf_8`) plus user-defined sequences via `~/.detoxrc` parsed by a hand-rolled recursive-descent parser (zero dep impact on library-only builds). Recursive walking is depth-first leaves-up; collisions resolve with a monotonic `_N` suffix inserted BEFORE the final extension token. Cross-device rename falls back to copy + fsync + rename + unlink with best-effort metadata preservation. Strict mode mirrors upstream's exact stderr format. Library API (`Detox`, `DetoxBuilder`, `Sequence`, `Filter`, `DetoxError`) is `default-features = false`-clean — no clap, no walkdir, no `anyhow` in the public surface. Static binaries on Linux x86_64/aarch64, macOS x86_64/aarch64, Windows x86_64.

- **Install:** `cargo install rusty-detox` · `cargo binstall rusty-detox`
- **Crates.io:** [crates.io/crates/rusty-detox](https://crates.io/crates/rusty-detox)
- **Docs:** [docs.rs/rusty-detox](https://docs.rs/rusty-detox)
- **Source:** [github.com/jsh562/rusty-detox](https://github.com/jsh562/rusty-detox)

---

### [rusty-pv](https://github.com/jsh562/rusty-pv)

**A Rust port of Andrew Wood's `pv(1)`** — pipe viewer with progress bar, elapsed timer, ETA, rate, bytes-transferred display, and token-bucket rate limiting.

```sh
rusty-pv ubuntu.iso > /dev/sdb         # progress + ETA copying a file
rusty-pv -L 5M big.iso > /mnt/out      # throttle to 5 MiB/s
some-cmd | rusty-pv -N "stage 1" | xz  # labelled mid-pipeline progress
rusty-pv -n -s 100M big.iso > out      # numeric mode for dialog --gauge
```

Single-threaded copy loop with a monotonic `Instant`-driven token-bucket throttle that converges to the configured rate within ±5% measured end-to-end wall-clock. Instantaneous rate uses EMA smoothing with α=0.3 (locked at v0.1.0 — Strict-mode byte-equal compat depends on it). IEC binary (KiB=1024) by default; `--si` switches to SI decimal (kB=1000) uniformly across all rate/byte fields. Fixed visual display field order (`[name:] p t e/I r b a`) regardless of CLI argv order. SIGPIPE handler restored before the copy loop so broken-pipe exits with code 141 matching upstream. Library API exposes `Pv` + `PvBuilder` + `Reporter` (Send-only) + `Progress` (`#[non_exhaustive]`) + `PvError`; `default-features = false` strips clap/crossterm/signal-hook/fd-lock — only `thiserror` remains in the runtime tree. Static binaries on Linux x86_64/aarch64, macOS x86_64/aarch64, Windows x86_64.

- **Install:** `cargo install rusty-pv` · `cargo binstall rusty-pv`
- **Crates.io:** [crates.io/crates/rusty-pv](https://crates.io/crates/rusty-pv)
- **Docs:** [docs.rs/rusty-pv](https://docs.rs/rusty-pv)
- **Source:** [github.com/jsh562/rusty-pv](https://github.com/jsh562/rusty-pv)

---

## What's coming

Initial Uploads > Bugs + Optimizations > Enhancements   
or Other Libraries

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
