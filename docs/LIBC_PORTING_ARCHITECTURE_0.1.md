# Wlibc Porting Architecture 0.1

**Status:** Proposed architecture. No complete musl fork, native syscall backend, or POSIX-conformant Wicofe runtime is claimed.

## 1. Purpose

Wlibc (Wicofe C Library) is the official C standard library and POSIX runtime for Wicofe. The implementation direction is to fork musl, subject to exact-version, license, and provenance review, then port its runtime to the native Wicofe syscall ABI.

Wlibc targets the declared ISO C and POSIX.1 profile. GNU and BSD compatibility extensions are not project goals. Additional POSIX interfaces may be implemented in a separate Wicofe POSIX layer when separation is architecturally useful.

The cross-component requirements are defined by [Wicofe Unix userspace foundation 0.1](https://github.com/Waizusora/Wicofe-Specs/blob/main/userspace/UNIX_USERSPACE_FOUNDATION_0.1.md).

## 2. Ownership boundaries

| Component | Responsibility |
|---|---|
| Wlibc | ISO C runtime, POSIX C interfaces, startup objects, syscall adaptation, errno mapping, TZif runtime and conformance tests |
| Wicofe-ABI | Canonical syscall identifiers, calling conventions, public structures, raw errors and ABI compatibility |
| Wicofe-Kernel | Implements process, memory, filesystem, signal, thread, clock and other kernel-side ABI operations |
| Wicofe POSIX layer | Optional separately organized POSIX interfaces; uses the same ABI and conformance contract |
| Wicofe-Lib | Shared Rust userspace libraries; not a replacement for Wlibc |
| Wicofe-SDK | Cross-toolchain, target configuration, sysroot assembly and developer-facing packaging |
| Wicofe-Build | Whole-system build orchestration and integration |

Wlibc must consume canonical Wicofe-ABI definitions. It must not assign syscall numbers, redefine kernel structures independently or rely on Linux syscall behavior merely because the upstream source supports Linux.

## 3. Upstream implementation and provenance

The selected implementation direction is a musl fork. musl's project-level license is MIT. Original Wlibc-authored material is also licensed under MIT as an explicit repository-specific exception to the Wicofe project default. Before importing source, record:
- exact upstream version and source revision;
- core and per-file license obligations;
- bundled code, headers, startup files, generated files and optional component provenance;
- patch inventory and rationale;
- architecture support and runtime assumptions;
- upstream maintenance and patch-forwarding strategy.

Imported source retains its upstream license and notices. Original Wicofe patches must remain identifiable. The project dependency/license policy applies to all imported and transitive material.

## 4. Porting layers

A complete port is a coordinated set of layers:

1. **Headers and public types:** ISO C/POSIX declarations, target types, constants, feature-test behavior and ABI declarations.
2. **Syscall adaptation:** architecture-specific entry stubs and wrappers implementing the exact Wicofe-ABI contract.
3. **Process startup:** entry point, initial stack parsing, arguments, environment, constructors and termination.
4. **Memory and allocation:** mapping, protection, unmapping, allocator integration and failure semantics.
5. **File and I/O:** descriptors, open/read/write/close, metadata, pipes, polling and filesystem semantics as the ABI supports them.
6. **Process and signals:** process/thread lifecycle, wait semantics, signal delivery/masks and interruption/restart behavior.
7. **Threading and TLS:** thread creation, synchronization primitives, TLS layout and runtime initialization.
8. **Time and synchronization:** clocks, timers, calendar-time conversion, POSIX TZ strings and IANA TZif data.
9. **Dynamic linking:** executable interpreter, relocation model, shared-object ABI, TLS and loader behavior, gated on the Wicofe executable ABI.
10. **Locale and other standard facilities:** implement the declared ISO C/POSIX requirements with maintainable dependencies.

No Linux syscall backend or Linux process ABI may remain as an implicit runtime dependency.

## 5. POSIX conformance policy

- Select and document the ISO C edition and POSIX.1 edition before declaring a conformance claim.
- Target full conformance to the declared POSIX profile, including required options and explicitly selected optional facilities.
- GNU and BSD compatibility extensions are excluded from project requirements.
- Additional POSIX interfaces may be supplied by a separate POSIX layer. They must share Wlibc's headers, types, ABI rules, error behavior and conformance suite.
- Maintain an auditable per-interface matrix: required, implemented, partially implemented, unsupported, and verified.
- A configure probe or successful application build is not proof of semantic conformance.

## 6. Syscall and error boundary

The raw kernel interface is owned by Wicofe-ABI. Each Wlibc wrapper maps documented raw results into the C/POSIX contract, including errno, interruption, partial completion, blocking and cancellation behavior where required.

The ABI baseline must select the raw syscall error convention before wrappers hardcode an encoding or errno mapping. Generate or validate wrapper constants against canonical ABI definitions where practical.

All user pointers and lengths must follow the ABI's validation rules. Wlibc must not pass compiler-private Rust layouts or undocumented structures into the kernel.

## 7. Startup, process entry and termination

Define the executable entry contract jointly with Wicofe-SDK and Wicofe-Kernel. Specify:
- initial stack layout and alignment;
- argument and environment representation;
- process entry register/stack state per architecture;
- relocation and TLS initialization order;
- constructor/destructor execution rules;
- exit status and termination semantics;
- behavior for malformed startup data.

Static and dynamic executable support must be stated separately. Startup code must be testable independently from higher-level stdio and allocation facilities.

## 8. Threads and TLS

Thread support is a kernel ABI dependency, not a late libc-only feature. Before enabling the POSIX thread interfaces, the ABI must define thread creation/termination, scheduling interaction, synchronization/wait primitives, signal interaction, cancellation, and TLS setup.

Specify TLS model, thread pointer register usage, static/dynamic TLS requirements, destructor behavior and interaction with dynamic loading. Validate each supported architecture. Do not claim POSIX thread conformance until lifecycle, cancellation, synchronization, TLS and error semantics pass tests.

## 9. Dynamic linking and executable formats

Dynamic linking is a separate milestone. It requires a defined executable format, interpreter path, relocation types, memory protections, shared-object loading, TLS, symbol resolution and loader/kernel contract.

Wlibc does not target GNU symbol-versioning or GNU loader conventions as compatibility requirements. Wicofe defines its executable and shared-object ABI on its own terms.

If an initial system release uses static linking, document that limitation explicitly.

## 10. Timezone runtime

Wlibc owns the userspace time conversion runtime, including the POSIX time interfaces and TZ environment handling required by the selected profile. IANA TZ Database files are installed under `/usr/share/zoneinfo/`; TZif parsing and conversion are performed in-process by Wlibc or a tightly coupled runtime component.

Timezone selection is system configuration, conventionally represented by `/etc/localtime` and an optional `/etc/timezone` identifier. Timezone changes must never alter the kernel UTC clock or monotonic clocks. A timezone daemon is not required for `localtime()`, `mktime()`, or `tzset()`.

## 11. Testing and conformance

Required test layers:
- compile and link tests for C consumers;
- startup tests for argument/environment parsing and exit status;
- syscall wrapper tests for success, error, interruption and partial I/O;
- memory mapping and allocator stress tests;
- file, pipe, polling and filesystem behavior tests;
- process, signal, thread and TLS conformance tests;
- locale and character conversion tests;
- time, POSIX TZ string, TZif and DST transition tests;
- static/dynamic loader tests if those modes are claimed;
- applicable ISO C and POSIX conformance suites, with every exclusion documented;
- real execution tests on Wicofe for x86-64 and AArch64.

Report compile-only, link-only, emulator execution and hardware execution separately. A host test against host libc does not validate Wlibc.

## 12. Open decisions

- Exact musl revision and import strategy.
- ISO C and POSIX.1 editions and optional feature profile.
- Static-only initial milestone versus dynamic-linker scope.
- Canonical process entry and initial stack layout.
- Raw syscall error convention and errno mapping.
- Thread, signal, synchronization and TLS ABI readiness.
- Executable format and dynamic-loader contract.
- Compiler runtime, unwind and exception support.
- License/provenance review for all imported and optional components.

## 13. Completion criteria

Wlibc is not complete until its source/version/license and patch set are recorded; startup and syscall glue match Wicofe-ABI; required ISO C and POSIX semantics are implemented; headers are integrated into the SDK sysroot; and reproducible target execution/conformance evidence exists. Unsupported facilities must be reported explicitly rather than silently emulated with incompatible host behavior.
