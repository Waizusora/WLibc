# Libc Documentation Index

This directory documents the C standard library/runtime port and integration for Wicofe.

## Architecture and design

- [C library and runtime porting architecture 0.1](LIBC_PORTING_ARCHITECTURE_0.1.md) — draft upstream selection criteria, syscall adaptation, process startup, memory, threads/TLS, dynamic linking and conformance requirements.

## Scope
- libc source selection, porting patches, and syscall glue.
- C headers, startup objects, process entry, environment, and thread integration.
- errno mapping, signals, file descriptors, memory, time, and process behavior.
- Compatibility tests and upstream maintenance.

## Ownership
The canonical syscall ABI is defined by [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI) and implemented by [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel). Shared Rust APIs belong to [Wicofe-Lib](https://github.com/Waizusora/Wicofe-Lib). [Wicofe-SDK](https://github.com/Waizusora/Wicofe-SDK) packages the target sysroot and toolchain.

## Porting and license policy
A Linux-targeted libc is not plug-and-play. Record exact upstream version, license, patches, runtime assumptions, and test evidence. Imported source retains its upstream license and notices.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
