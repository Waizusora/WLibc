# Wlibc

Wlibc (Wicofe C Library) is the C standard library and POSIX runtime project for Wicofe.

## Direction

- Fork musl as the initial implementation base, subject to exact-version, license, and provenance review.
- Port the runtime to the native Wicofe syscall ABI; Linux syscall behavior is not the target ABI.
- Target the declared ISO C and POSIX.1 conformance profile.
- Do not target GNU or BSD compatibility extensions.
- Implement additional POSIX interfaces in a separate Wicofe POSIX layer when that separation is architecturally useful.
- Provide the C runtime, process startup, errno mapping, threading/TLS integration, time APIs, and conformance tests.
- Integrate IANA TZ Database data through a TZif runtime in userspace.
- Support static and dynamic linking as separate milestones, with dynamic linking gated on a defined Wicofe executable and loader ABI.

## Ownership boundaries

- [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI) owns syscall identifiers, calling conventions, public data layouts, raw errors, and ABI versioning.
- [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel) implements kernel-side ABI behavior.
- Wlibc owns ISO C/POSIX C interfaces and their userspace runtime implementation.
- [Wicofe-Lib](https://github.com/Waizusora/Wicofe-Lib) owns shared Rust userspace libraries and safe ABI wrappers; it is not a replacement for Wlibc.
- [Wicofe-SDK](https://github.com/Waizusora/Wicofe-SDK) owns target sysroot and toolchain packaging.
- [Wicofe-Specs Unix userspace foundation](https://github.com/Waizusora/Wicofe-Specs/blob/main/userspace/UNIX_USERSPACE_FOUNDATION_0.1.md) coordinates the cross-repository requirements.

## Status

Architecture and porting plan only. No complete musl fork, native syscall backend, complete POSIX implementation, or verified Wicofe runtime is claimed.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for the Wlibc technical documentation.

## Licensing

Original Wlibc code is licensed under MIT; see [LICENSE](LICENSE) and [licensing and provenance policy](docs/LICENSING_AND_PROVENANCE.md). Imported musl source and all other third-party material retain their upstream licenses, copyright notices, and file-level terms. The exact musl revision and component-level provenance audit are required before source import.
