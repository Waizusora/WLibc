# Wlibc Native Syscall Port Audit 0.1

**Status:** Initial source audit complete; raw result/error convention adopted; implementation prerequisites remain.

## 1. Source baseline

This audit applies to the pinned musl 1.2.6 snapshot documented in [musl Upstream Baseline 1.2.6](UPSTREAM_BASELINE_MUSL_1.2.6.md), commit `7813e9119eb5fe681b6750b8a033a0490bca0941`.

## 2. Confirmed Linux-specific entry points

| Upstream area | Observed Linux assumption | Wicofe action |
|---|---|---|
| `arch/x86_64/syscall_arch.h` | `syscall` instruction, Linux register mapping, Linux syscall number in RAX, Linux return convention | Replace with Wicofe-ABI x86-64 entry stub |
| `arch/aarch64/syscall_arch.h` | `svc 0`, Linux syscall number in X8, arguments in X0-X5 | Replace with Wicofe-ABI AArch64 entry stub |
| `arch/*/bits/syscall.h.in` | Linux syscall number tables | Generate Wicofe syscall constants from Wicofe-ABI; never reuse Linux numbers |
| `src/internal/syscall.h` | Linux syscall dispatch, `-ENOSYS` probing and cancellation syscall path | Replace generic raw-call and cancellation boundary |
| `src/internal/syscall_ret.c` | Linux raw negative-errno return convention | Replace with adopted Wicofe carry-flag result decoding |
| `src/internal/futex.h`, `src/thread/__wait.c`, `src/thread/__timedwait.c` | Linux futex wait/wake semantics | Map to a documented Wicofe wait/wake or synchronization ABI |
| `src/thread/clone.c`, `src/linux/clone.c` | Linux clone-based thread/process creation | Replace with Wicofe process/thread creation contract |
| `src/thread/__set_thread_area.c` and arch variants | Linux thread-area/TLS setup | Implement Wicofe thread-pointer/TLS contract per architecture |
| `src/thread/__syscall_cp.c` and arch cancellation stubs | Linux signal/cancellation interruption assumptions | Define cancellation-safe blocking syscall contract |
| `src/mman/` | Linux mmap/mprotect/munmap semantics and flags | Map to Wicofe VM ABI and exact flag semantics |
| `src/internal/vdso.c`, time sources | Linux vDSO symbols and clock interfaces | Use Wicofe clock ABI; optional Wicofe vDSO only after specified |
| `crt/`, `ldso/`, `arch/*/crt_arch.h` | Linux ELF process entry, auxiliary vector, loader and relocation assumptions | Define Wicofe executable/ELF startup ABI before porting |
| socket and signal wrappers | Linux socket and realtime-signal syscall conventions | Map to Wicofe socket and signal contracts |

## 3. Adopted Wicofe raw syscall result convention

Wicofe-ABI has adopted the following convention for x86-64 and AArch64:

- x86-64: RAX carries the result or positive errno; RFLAGS.CF clear means success and set means failure.
- AArch64: X0 carries the result or positive errno; PSTATE.C clear means success and set means failure.
- Successful negative signed values remain valid and do not indicate errors.
- Wlibc must capture the carry result inside the same compiler-visible assembly operation as the trap instruction, with explicit register and memory clobbers.
- On failure, Wlibc converts the positive raw errno into the POSIX interface's failure return and thread-local `errno`.

The canonical errno constants, syscall IDs, detailed register preservation contract, signal context and per-call interruption semantics remain to be specified in Wicofe-ABI. See its [calling convention](https://github.com/Waizusora/Wicofe-ABI/blob/main/docs/SYSCALL_CALLING_CONVENTION_PROPOSAL_0.1.md) and [technical review](https://github.com/Waizusora/Wicofe-ABI/blob/main/docs/SYSCALL_CONVENTION_TECHNICAL_REVIEW_0.1.md).

## 4. Remaining ABI prerequisites, in dependency order

1. Canonical errno values and public header generation.
2. Exact GPR preservation and condition-code clobber contract.
3. Process entry stack, argc/argv/envp/auxiliary data, exit and exec semantics.
4. Memory map/unmap/protect semantics, page size discovery and mapping flags.
5. File descriptor, open/read/write/close, metadata, pipe and poll semantics.
6. Process/thread creation, join/reap, thread exit and thread pointer/TLS setup.
7. Wait/wake primitives, robust synchronization and cancellation interruption.
8. Signal masks, delivery, actions, restart rules and signal return frame.
9. Clocks, timers and sleep interruption semantics.
10. Socket and resolver transport primitives.
11. ELF executable format, relocation, dynamic loader path, TLS model and shared object ABI.

Each ABI contract must include types, alignment, error semantics, interruption, blocking, partial completion, permissions, version and tests.

## 5. First implementation slice after prerequisites

Implement in this order:
1. Architecture-specific raw syscall entry for x86-64 and AArch64, after the kernel entry-frame contract is fixed.
2. Raw result decoding and errno conversion.
3. Minimal process termination and descriptor I/O wrappers for a freestanding smoke program.
4. Page-size query and memory mapping.
5. Process startup and static executable support.
6. Thread/TLS primitives and POSIX threads.
7. Signals, clocks, VFS operations, sockets and remaining interfaces.
8. Dynamic linker only after the executable ABI and relocation model are stable.

No fake syscall IDs or placeholder Linux syscall fallbacks are permitted.

## 6. Validation

- Compile-time checks for syscall argument widths and structure layouts.
- Unit tests for raw result decoding and errno mapping.
- Architecture-specific QEMU runtime tests for syscall entry and return.
- Negative tests for invalid user pointers and unsupported calls.
- GCC and Clang optimized stub tests, including supported LTO configurations.
- POSIX conformance tests for each wrapper's observable behavior.

A test must distinguish kernel mechanism correctness from libc-level POSIX semantics.

## 7. Status

- Upstream baseline: pinned.
- Linux-specific entry-point audit: initial findings recorded.
- Raw syscall result/error convention: adopted for x86-64 and AArch64.
- Canonical errno and syscall IDs: pending.
- Native syscall code: not implemented.
- x86-64/AArch64 runtime validation: pending.
- POSIX.1-2024 conformance: not verified.
