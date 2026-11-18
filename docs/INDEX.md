# Wlibc Documentation Index

This directory documents Wlibc (Wicofe C Library), the C standard library and POSIX runtime for Wicofe.

## Architecture and design
- [C library and runtime porting architecture 0.1](LIBC_PORTING_ARCHITECTURE_0.1.md) — musl-fork direction, native syscall adaptation, process startup, memory, threads/TLS, linking and POSIX conformance.
- [Unix userspace foundation 0.1](https://github.com/Waizusora/Wicofe-Specs/blob/main/userspace/UNIX_USERSPACE_FOUNDATION_0.1.md) — normative cross-component WFH, POSIX and timezone requirements.

## Scope
- ISO C and POSIX interfaces for the declared conformance profile.
- C headers, startup objects, process entry, environment, and thread integration.
- errno mapping, signals, file descriptors, memory, time, and process behavior.
- TZif parsing and IANA timezone conversion.
- POSIX conformance tests and upstream maintenance.

GNU and BSD compatibility extensions are not project targets. Additional POSIX interfaces may be supplied by a separate POSIX layer under the same ABI and conformance contract.

## Ownership
The canonical syscall ABI is defined by [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI) and implemented by [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel). Shared Rust APIs belong to [Wicofe-Lib](https://github.com/Waizusora/Wicofe-Lib). [Wicofe-SDK](https://github.com/Waizusora/Wicofe-SDK) packages the target sysroot and toolchain.

## Porting and license policy
A Linux-targeted libc is not plug-and-play. Record exact upstream version, license, patches, runtime assumptions, and test evidence. Imported source retains its upstream license and notices. Original Wlibc-authored material is MIT-licensed; see [licensing and provenance policy](LICENSING_AND_PROVENANCE.md).

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
