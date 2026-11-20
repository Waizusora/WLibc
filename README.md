# Wlibc

Wlibc (Wicofe C Library) is the C standard library and POSIX runtime project for Wicofe.

## Direction

- Fork the pinned musl 1.2.6 upstream snapshot, subject to the detailed source import and component license audit.
- Pinned upstream commit: `7813e9119eb5fe681b6750b8a033a0490bca0941`; see [upstream baseline](docs/UPSTREAM_BASELINE_MUSL_1.2.6.md) and [upstream lock](upstream/musl.lock).
- Port the runtime to the native Wicofe syscall ABI; Linux syscall behavior is not the target ABI.
- Target ISO C17 and IEEE Std 1003.1-2024 (POSIX.1-2024 / Issue 8).
- Do not target GNU or BSD compatibility extensions.
- Implement additional POSIX interfaces in a separate Wicofe POSIX layer when that separation is architecturally useful.
- Provide C runtime, process startup, errno mapping, threading/TLS integration, time APIs, and conformance tests.
- Integrate IANA TZ Database data through a TZif runtime in userspace.
- Support static and dynamic linking as separate milestones, with dynamic linking gated on a defined Wicofe executable and loader ABI.

## Ownership boundaries

- [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI) owns syscall identifiers, calling conventions, public data layouts, raw errors, and ABI versioning.
- [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel) implements kernel-side ABI behavior.
- Wlibc owns ISO C/POSIX C interfaces and their userspace runtime implementation.
- [Wicofe-Lib](https://github.com/Waizusora/Wicofe-Lib) owns shared Rust userspace libraries and safe ABI wrappers; it is not a replacement for Wlibc.
- [Wicofe-SDK](https://github.com/Waizusora/Wicofe-SDK) owns target sysroot and toolchain packaging.
- [Wicofe-Spec Unix userspace foundation](https://github.com/Waizusora/Wicofe-Spec/blob/main/userspace/UNIX_USERSPACE_FOUNDATION_0.1.md) coordinates cross-repository requirements.

## Current status

- Upstream baseline selected and pinned for audit.
- Upstream root copyright notice preserved.
- First-pass syscall and license audits documented.
- musl source tree not yet imported.
- Wicofe raw syscall backend not implemented; carry-flag result encoding, initial errno values, and register-preservation rules are adopted, but syscall IDs and kernel entry/return implementation remain pending.
- No complete POSIX implementation or verified Wicofe runtime is claimed.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for the Wlibc technical documentation.

## Licensing

Original Wlibc-authored work is licensed under MIT where Wlibc has the necessary licensing rights; see [LICENSE](LICENSE), [COPYRIGHT](COPYRIGHT), and [licensing and provenance policy](docs/LICENSING_AND_PROVENANCE.md). Imported musl source and all other third-party material retain their upstream licenses, copyright notices, and file-level terms. The exact source revision and component-level audit are recorded separately.

## Wicofe-wide principles

Wicofe is an independent Unix-like, POSIX-compatible operating system and software ecosystem, designed as an alternative to established and widely adopted software ecosystems and technologies, including GNU. Native components must use Wicofe interfaces and must not require Linux libc or GNU compatibility layers. Prefer permissive licenses; avoid GPL/AGPL, and consider LGPL only when no suitable permissive alternative exists. Reuse or fork upstream software when implementing it from scratch would be disproportionately difficult. See the [canonical project principles](https://github.com/Waizusora/Wicofe-Doc/blob/main/PROJECT_PRINCIPLES.md).
