# musl Upstream Baseline 1.2.6

**Status:** Pinned for source audit; source has not yet been imported into Wlibc.

## 1. Selected baseline

Wlibc selects the following upstream snapshot as its initial implementation baseline:

| Field | Value |
|---|---|
| Project | musl |
| Version file | `1.2.6` |
| Ref | `master` snapshot |
| Commit | `7813e9119eb5fe681b6750b8a033a0490bca0941` |
| Tree | `dc8444f8e5dfcc3a592dd62653d17a61f8d773a1` |
| Upstream repository | https://git.musl-libc.org/git/musl |
| Independently checked mirror | https://repo.or.cz/musl.mirror.git |
| GitHub source mirror used for file inspection | https://github.com/ifduyue/musl |
| Mirror refresh observed | 2026-11-17 |
| Committer date | 2026-10-14 22:48:56 UTC |

The commit is `7813e9119eb5fe681b6750b8a033a0490bca0941` (message: `regex: avoid int overflow in regcomp`). The mirror showed this as the current master tip when checked on 2026-11-17. The source tree reports `VERSION=1.2.6`.

This is a pinned upstream snapshot, not a claim that the tag `v1.2.6` itself is the selected commit. Re-check the official upstream ref before importing. No source archive checksum is recorded because the archive has not been downloaded.

## 2. Source layout audit

The pinned Git tree contains 2,897 entries and 19 architecture directories. Wicofe's current initial architecture targets are x86-64 and AArch64. The port must use only those two architecture backends initially; the other upstream architecture directories are not Wicofe targets.

Relevant upstream areas include:
- `arch/<arch>/syscall_arch.h` and `arch/<arch>/bits/syscall.h.in`: Linux syscall entry and syscall-number definitions.
- `src/internal/syscall.h`: generic Linux syscall dispatch, raw return handling, and cancellation-aware syscall helpers.
- `src/thread/`: thread creation, exit, cancellation, waits, TLS, and synchronization integration.
- `src/internal/futex.h`: Linux futex-based synchronization assumptions.
- `src/linux/`: Linux-specific helper implementations.
- `src/mman/`: virtual-memory wrappers with Linux syscall assumptions.
- `src/time/` and `src/internal/vdso.c`: time and Linux vDSO integration.
- `crt/`, `ldso/`, and `arch/<arch>/crt_arch.h`: process entry, ELF startup, relocation, and dynamic-loader assumptions.

The musl source is a Linux-targeted C library. It is a code foundation only; its Linux syscall ABI and Linux process/runtime assumptions are not Wicofe contracts.

## 3. Initial architecture selection

- x86-64: port startup assembly, syscall entry, thread pointer/TLS, atomics, cancellation syscall path, relocation and ELF startup against the Wicofe contracts.
- AArch64: port startup assembly, `svc` entry, thread pointer/TLS, atomics, cancellation syscall path, relocation and ELF startup against the Wicofe contracts.

Do not copy the upstream syscall number tables into Wicofe. Wlibc must consume generated or canonical definitions from Wicofe-ABI.

## 4. POSIX scope

Wlibc's target is the declared ISO C and POSIX.1-2024 profile, not Linux, GNU, or BSD compatibility. Upstream non-standard interfaces must not become Wlibc public requirements merely because they are present in musl.

Do not delete upstream implementation files solely because their public API is non-standard: internal helpers may support standardized interfaces. Classify each public symbol and dependency before removing or excluding it.

musl's historical README statement about POSIX 2008 base coverage is not evidence of full POSIX.1-2024 conformance. Wlibc must maintain its own interface matrix and test results.

## 5. License and provenance findings

The pinned upstream `COPYRIGHT` states that musl as a whole is licensed under MIT. It also records third-party or separately attributed portions, including:
- TRE regular-expression implementation under 2-clause BSD terms;
- math routines from multiple contributors under permissive terms;
- ARM and AArch64 optimized memory routines with separate notices;
- DES implementation under BSD terms;
- Blowfish implementation placed in the public domain with fallback permissive terms;
- smoothsort under an MIT-style notice;
- architecture-specific contributions and public-domain exceptions for headers and startup files.

The exact upstream `COPYRIGHT` file is preserved at [`licenses/musl/COPYRIGHT`](../licenses/musl/COPYRIGHT). The repository-level MIT license for original Wlibc work does not replace these notices.

This is a first-pass review of the upstream project notice and tree. It is not yet a completed file-by-file legal audit. Before source import, scan all selected files and build inputs for file-level notices, generated content, bundled components, and any terms not fully described by the root notice.

## 6. Import gate

Before source import:
1. Re-check that the selected commit is still the intended baseline and obtain the exact source archive.
2. Record and verify the archive SHA-256.
3. Preserve upstream `COPYRIGHT`, all file-level notices, and applicable third-party license texts.
4. Produce a per-file/component license inventory for the selected x86-64 and AArch64 source closure.
5. Exclude non-target architecture ports and non-required public extensions only after dependency analysis.
6. Record the source import method and retain upstream history where possible.
7. Run a clean upstream baseline build for x86-64 and AArch64 before applying Wicofe patches.
8. Keep upstream import and Wicofe port patches as separate, reviewable commits.

## 7. Current status

- Upstream revision: selected and pinned for audit.
- Root license: MIT, confirmed from the pinned upstream `COPYRIGHT`.
- Third-party notice review: first-pass only; detailed audit pending.
- Source archive hash: not recorded.
- Source import/fork: not performed.
- Wicofe syscall backend: blocked on ABI specification.
- POSIX.1-2024 conformance: target selected; implementation and verification not started.
