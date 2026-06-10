# go-asmgen

🌐 **[Website](https://go-asmgen.github.io)** · 📚 **[Documentation](https://go-asmgen.github.io/docs/)**

**Ergonomic generation of Go-compatible Plan 9 assembly for non-amd64
architectures** — the multi-architecture counterpart to what [`avo`][avo] does
for amd64.

`avo` encodes instruction bytes itself, which is exactly what makes extending it
to new ISAs expensive. go-asmgen instead **emits Plan 9 assembly text and lets
the Go toolchain assembler (`cmd/asm`) encode it**, so each new architecture
only needs an ABI layout model plus a thin instruction-emit surface — not a
byte-level encoder.

## Repositories

| Repo | What it is |
|------|------------|
| [**asmgen**](https://github.com/go-asmgen/asmgen) | the library: ABI0 layout models + Plan 9 emit, starting with arm64 |
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
  cross-checked against the Go declaration in CI — the cheapest, strongest
  correctness check — and exercised by a runtime test on native arm64.
- **100% test coverage** of the library, enforced as a CI gate on every repository.

## Status

v0 — proof of pipeline: arm64, ABI0, 8-byte int/ptr arguments and results.
Widening type support (sub-word ints, floats, alignment/padding) and additional
ISAs (riscv64, loong64) are on the roadmap; each new case is checked against
asmdecl.

## Links

- **Website (landing):** <https://go-asmgen.github.io> — built with Hugo.
- **Documentation:** <https://go-asmgen.github.io/docs/> — MkDocs Material,
  versioned with [mike]; source in [go-asmgen/docs](https://github.com/go-asmgen/docs).

[avo]: https://github.com/mmcloughlin/avo
[mike]: https://github.com/jimporter/mike
