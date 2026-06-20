<p align="center"><img src="https://raw.githubusercontent.com/go-asmgen/brand/main/social/go-asmgen.png" alt="go-asmgen" width="720"></p>

# go-asmgen

🌐 **[Website](https://go-asmgen.github.io)** · 📚 **[Documentation](https://go-asmgen.github.io/docs/)**

**Ergonomic generation of Go-compatible Plan 9 assembly for every 64-bit Go
target** — amd64, arm64, riscv64, loong64, ppc64le (VSX) and s390x (vector
facility, big-endian).

[`avo`][avo] does this for amd64 by encoding instruction bytes itself — powerful,
but exactly what makes extending it to new ISAs expensive. go-asmgen instead
**emits Plan 9 assembly text and lets the Go toolchain assembler (`cmd/asm`)
encode it**, so each architecture is just a thin move/register surface over a
shared ABI0 layout model — not a byte-level encoder. avo stays the richer choice
for amd64-specific work; go-asmgen's niche is **one uniform builder across every
target**.

## Repositories

| Repo | What it is |
|------|------------|
| [**asmgen**](https://github.com/go-asmgen/asmgen) | the library: shared ABI0 layout (`abi`) + per-arch builders (`amd64`/`arm64`/`riscv64`/`loong64`/`ppc64le`/`s390x`) + Plan 9 writer (`emit`) |
| [**docs**](https://github.com/go-asmgen/docs) | MkDocs Material documentation, served at [/docs/](https://go-asmgen.github.io/docs/) |
| [**go-asmgen.github.io**](https://github.com/go-asmgen/go-asmgen.github.io) | the Hugo landing page |

## Principles

- **Delegate encoding to `cmd/asm`.** Emitting text, not bytes, is what keeps
  adding an architecture cheap and keeps every instruction encoded by the same
  toolchain that compiles the rest of the program.
- **Target ABI0, not ABIInternal.** ABI0 (stack-based, FP-relative) is the
  stable contract hand-written `.s` targets; ABIInternal can change between Go
  releases.
- **Verified by `go vet` asmdecl.** Every emitted `name+offset(FP)` is
  cross-checked against the Go declaration in CI, and exercised by a runtime
  test — natively on amd64 and arm64, under qemu-user for riscv64, loong64,
  ppc64le and s390x (the s390x job exercising the big-endian path).
- **100% test coverage** of the library, enforced as a CI gate on every repository.

## Status

Released — `go get github.com/go-asmgen/asmgen@latest` (latest **v0.5.0**).

All six 64-bit targets, ABI0, at functional parity — each with the same set of
runtime-proven examples: scalars (signed/unsigned ints 1/2/4/8 bytes, pointers,
32/64-bit floats), struct/slice/string aggregates, fixed-size arrays by value,
stack frames + arbitrary TEXT flags (`NewFuncFlags`), and SIMD via the `Raw`
escape hatch — **SSE2 + AVX2** (amd64), **NEON** (arm64), **RVV** (riscv64),
**LSX + LASX** (loong64), **VSX** (ppc64le), **vector facility** (s390x,
big-endian), up to 256-bit. Each architecture differs only in its move table. A
typed vector helper and first-class vector *types* are the main remaining items.

## Links

- **Website (landing):** <https://go-asmgen.github.io> — built with Hugo.
- **Documentation:** <https://go-asmgen.github.io/docs/> — MkDocs Material,
  versioned with [mike]; source in [go-asmgen/docs](https://github.com/go-asmgen/docs).

[avo]: https://github.com/mmcloughlin/avo
[mike]: https://github.com/jimporter/mike
