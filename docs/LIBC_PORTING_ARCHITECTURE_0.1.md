# C Library and Runtime Porting Architecture 0.1

**Status:** Draft proposal. This document defines the porting plan and compatibility requirements. It does not claim that a libc implementation has been selected, imported, or successfully ported to Wicofe.

## 1. Purpose

Define the C userspace runtime layer required by native C applications and by software that depends on standard C interfaces. Wicofe-Libc adapts a selected upstream libc/runtime to the Wicofe userspace ABI; it does not own the syscall contract or kernel implementation.

## 2. Ownership boundaries

| Component | Responsibility |
|---|---|
| Wicofe-Libc | C headers/runtime, startup objects, syscall adaptation, libc behavior and conformance tests |
| Wicofe-ABI | Canonical syscall identifiers, calling conventions, public structures, raw errors and ABI compatibility |
| Wicofe-Kernel | Implements process, memory, filesystem, signal, thread and other kernel-side ABI operations |
| Wicofe-Lib | Shared Rust userspace libraries; not a replacement for libc |
| Wicofe-SDK | Cross-toolchain, target configuration, sysroot assembly and developer-facing packaging |
| Wicofe-Build | Whole-system build orchestration and integration |

The libc port must consume canonical Wicofe-ABI definitions. It must not assign syscall numbers, redefine kernel structures independently or rely on Linux syscall behavior merely because the upstream libc supports Linux.

## 3. Upstream implementation selection

No upstream libc is selected by this document. Candidate evaluation must consider:
- exact upstream version and maintenance status;
- license of the core library, startup files, headers, bundled code and optional components;
- compatibility with the project's dependency/license policy;
- availability of an architecture abstraction suitable for x86-64 and AArch64;
- syscall and kernel-interface assumptions;
- threading, TLS, signals, process creation and dynamic-linker requirements;
- patch size, upstreamability and long-term maintenance cost;
- conformance test coverage and known portability limitations.

Keep imported source under its upstream license and preserve copyright notices. Record local patches separately and document whether each patch can be submitted upstream. Do not infer license compatibility from the top-level license alone; inspect vendored and optional components too.

## 4. Porting layers

A complete port is a coordinated set of layers:

1. **Headers and public types:** target-specific constants, types, feature-test behavior and ABI declarations.
2. **Syscall adaptation:** architecture-specific syscall entry stubs and wrappers that implement the exact Wicofe-ABI contract.
3. **Process startup:** entry point, initial stack parsing, arguments, environment, auxiliary data if defined, constructors and termination.
4. **Memory and allocation:** mapping, protection, unmapping, allocator integration and failure semantics.
5. **File and I/O:** file descriptors, open/read/write/close, metadata, pipes, polling and filesystem semantics as the ABI supports them.
6. **Process and signals:** process/thread lifecycle, wait semantics, signal delivery/masks and interruption/restart behavior.
7. **Threading and TLS:** thread creation, synchronization primitives, thread-local storage layout and runtime initialization.
8. **Time and synchronization:** clocks, timers, futex-like or other primitives only if standardized by Wicofe-ABI.
9. **Dynamic linking:** relocation model, interpreter path, shared-object ABI, symbol versioning and loader behavior if dynamic linking is supported.
10. **Locale and other facilities:** implement only facilities with defined requirements and maintainable dependencies.

Do not claim full POSIX compatibility from a subset of successful C programs. Publish a tested function/behavior matrix.

## 5. Syscall and error boundary

The raw kernel interface is owned by Wicofe-ABI. Each libc wrapper must map documented raw results into the C library contract, including errno, interruption, partial completion and blocking behavior.

The ABI baseline currently leaves the raw syscall error convention undecided. Do not hardcode an error encoding or errno mapping until the ABI specification chooses one. Generate or validate wrapper constants against the canonical ABI source where practical.

All user pointers and lengths must follow the ABI's validation rules. The libc must not pass compiler-private Rust layouts or undocumented C structures into the kernel.

## 6. Startup, process entry and termination

Define the executable entry contract jointly with Wicofe-SDK and Wicofe-Kernel. Specify:
- initial stack layout and alignment;
- argument and environment representation;
- process entry register/stack state per architecture;
- relocation and TLS initialization order;
- constructor/destructor execution rules;
- exit status and termination semantics;
- behavior for malformed startup data.

Startup code must be testable independently from higher-level stdio or allocation facilities. Static and dynamic executable support must be stated separately.

## 7. Threads and TLS

Thread support is a major ABI dependency, not a late libc-only feature. Before enabling a threaded libc build, the kernel ABI must define thread creation/termination, scheduling interaction, synchronization/wait primitives, signal interaction, and TLS setup.

Specify TLS model, thread pointer register usage, static/dynamic TLS requirements, destructor behavior and interaction with dynamic loading. Validate per architecture. Do not advertise pthread compatibility until lifecycle, cancellation, synchronization, TLS and error semantics pass conformance tests.

## 8. Dynamic linking and executable formats

Dynamic linking is optional until the executable loader, relocation types, memory protections, interpreter lookup, shared-object loading, TLS and symbol resolution contracts are implemented and tested.

If the first system release uses static linking, document that limitation explicitly. Do not include a dynamic loader merely because an upstream libc normally ships one; its contract must match Wicofe's executable format and kernel services.

## 9. Headers and application compatibility

Expose headers through the Wicofe-SDK sysroot. Header declarations, constants and structure layouts must match the target ABI. Avoid copying host headers into the target sysroot.

Maintain compatibility profiles for:
- ISO C language/runtime levels;
- selected POSIX interfaces;
- compiler builtins and compiler runtime dependencies;
- common application build systems and configure probes.

A configure probe succeeding is not proof that the probed behavior is implemented correctly.

## 10. Testing and conformance

Required test layers:
- compile and link tests for C and C++ consumers where supported;
- startup tests for argument/environment parsing and exit status;
- syscall wrapper tests for success, error, interruption and partial I/O;
- memory mapping and allocator stress tests;
- file, pipe, polling and filesystem behavior tests;
- process, signal, thread and TLS conformance tests;
- static/dynamic loader tests if those modes are claimed;
- upstream libc test suites, with unsupported cases documented;
- real execution tests on Wicofe for x86-64 and AArch64.

Report compile-only, link-only, emulator execution and hardware execution separately. A host test against host libc does not validate Wicofe-Libc.

## 11. Open decisions

- Upstream libc candidate and exact version.
- Static-only initial release versus dynamic-linker scope.
- Required C/POSIX compatibility profile.
- Canonical process entry and initial stack layout.
- Raw syscall error convention and errno mapping.
- Thread, signal, synchronization and TLS ABI readiness.
- Executable format and dynamic-loader contract.
- Compiler runtime, unwind and exception support.
- License/provenance review for all imported and optional components.

## 12. Completion criteria

A libc port is not complete until its source/version/license and patch set are recorded; startup and syscall glue match Wicofe-ABI; required process, memory, file, signal, thread and TLS semantics are implemented; headers are integrated into the SDK sysroot; and reproducible target execution/conformance evidence exists. Mark unsupported facilities explicitly rather than silently emulating incompatible host behavior.
