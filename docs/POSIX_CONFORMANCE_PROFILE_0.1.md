# Wlibc POSIX Conformance Profile 0.1

**Status:** Accepted target; implementation and verification not started.

## 1. Normative target

- C language baseline: ISO C17, as aligned with POSIX.1-2024.
- POSIX baseline: IEEE Std 1003.1-2024, also known as The Open Group Base Specifications Issue 8.
- Scope for Wlibc: Base Definitions requirements relevant to C headers/types plus the System Interfaces volume's C interfaces and required runtime semantics.
- Overall Wicofe POSIX scope: Shell and Utilities requirements are implemented by the shell and system utility projects, not inside Wlibc itself.

Official standard reference: https://standards.ieee.org/ieee/1003.1/7700/

## 2. Compatibility policy

- No GNU compatibility API target.
- No BSD compatibility API target.
- No Linux syscall or binary ABI compatibility target.
- No non-standard public API is required merely because it exists in the musl source.
- Internal implementation helpers may remain where needed to implement standardized interfaces.
- Additional POSIX interfaces may be organized in a separate Wicofe POSIX layer. Such interfaces must use Wlibc's public types, headers, error conventions, ABI and test suite; they must not become a parallel libc or syscall ABI.

## 3. Completeness policy

The project goal is full implementation of the selected POSIX.1-2024 profile, including applicable required interfaces and selected optional facilities. The implementation must maintain an interface matrix with:
- standard section and interface name;
- required/option status and dependencies;
- public header and symbol;
- implementation owner (Wlibc core or POSIX layer);
- kernel ABI dependencies;
- implementation status;
- test suite and evidence;
- known deviations.

Do not claim full POSIX conformance based only on musl's upstream claims, successful compilation, or a subset of libc tests. Each required interface and semantic requirement must be implemented and verified.

## 4. System boundary

Wlibc is responsible for ISO C runtime facilities and POSIX C interfaces. It does not alone implement the complete POSIX operating-system environment:
- Wicofe-Kernel provides mechanisms through Wicofe-ABI.
- Wlibc implements C library and POSIX System Interfaces behavior.
- Mysh implements the POSIX shell language and command interpreter requirements.
- Wicofe system utility repositories provide required POSIX utilities.
- Wicofe-SDK provides headers, startup objects, compiler target and sysroot integration.
- Wicofe-Tests owns cross-component conformance and integration evidence.

## 5. Feature and extension handling

Public headers must expose only the selected ISO C/POSIX interfaces by default. Feature-test macros must follow the selected standard's rules. Internal source may use private feature macros where necessary; those macros must not accidentally expose GNU/BSD interfaces to Wicofe applications.

A separate POSIX layer may be used for optional or separately packaged POSIX interfaces, but it must not split required POSIX behavior into incompatible ABIs or duplicate standard types.

## 6. Certification wording

Use "targets POSIX.1-2024" or "POSIX.1-2024 conformance work" while implementation is incomplete. Do not claim certification or use certification marks unless the relevant formal process and trademark permissions have been completed.
