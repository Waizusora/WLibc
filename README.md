# Wicofe-Libc

C standard library and runtime integration for Wicofe.

## Scope

- Adapt a suitable libc implementation to the Wicofe syscall and process ABI.
- Define startup objects, process entry, environment handling, and system-call glue.
- Provide headers and documented C interfaces required by native applications.
- Establish compatibility tests for supported libc behavior.

## Important constraints

A libc designed for Linux or another Unix-like kernel is not plug-and-play. Its syscall layer, threading model, signal behavior, process interfaces, filesystem assumptions, and runtime startup must be ported or adapted to Wicofe.

The project must evaluate upstream license obligations and all patches before selecting or importing an implementation. Do not assume that every libc or optional component is compatible with the Wicofe license policy.

## Dependencies

The syscall contract is owned by Wicofe-ABI and implemented by Wicofe-Kernel. Shared non-libc userspace APIs belong in Wicofe-Lib.

## Status

Initial repository baseline; no complete Wicofe libc port is claimed.

## License

The repository's original code targets MPL-2.0. Imported libc sources retain their upstream licenses and notices.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.
