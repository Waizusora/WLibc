# Libc Documentation Index

This directory documents the C standard library/runtime port and integration for Wicofe.

## Scope
- libc source selection, porting patches, and syscall glue.
- C headers, startup objects, process entry, environment, and thread integration.
- errno mapping, signals, file descriptors, memory, time, and process behavior.
- Compatibility tests and upstream maintenance.

## Ownership
The canonical syscall ABI is defined by [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI) and implemented by [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel). Shared Rust APIs belong to [Wicofe-Lib](https://github.com/Waizusora/Wicofe-Lib).

## Porting and license policy
A Linux-targeted libc is not plug-and-play. Record exact upstream version, license, patches, runtime assumptions, and test evidence. Imported source retains its upstream license and notices.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
