# Wlibc Licensing and Provenance Policy

**Status:** Accepted project decision; implementation audit pending.

## 1. License for original Wlibc work

Original Wlibc source code and documentation for which the project has the
necessary licensing rights are licensed under MIT, as stated in the repository
LICENSE. This is an explicit repository-level exception to the Wicofe project
default license. Existing contributor-owned or third-party material must not
be relicensed without the necessary rights and remains subject to its applicable
license.

The MIT choice permits modification, redistribution, static or dynamic
linking, and commercial use, subject to preserving the applicable copyright
and permission notices.

## 2. musl upstream

musl is the planned source-code foundation. Its project-level license is MIT.
The exact musl commit must be selected and recorded before source import.

The upstream license and provenance record must be preserved. Do not replace
upstream copyright holders with Wicofe attribution. Preserve the upstream
COPYRIGHT file, file-level notices, and notices for bundled components.
Some portions of musl identify separate BSD, public-domain, or other
permissive terms; each imported component must be reviewed independently.

Upstream reference: https://git.musl-libc.org/cgit/musl/tree/COPYRIGHT

## 3. Import gate

No upstream code may be imported until the following are recorded:

1. Upstream repository and exact commit.
2. Source archive hash.
3. Upstream COPYRIGHT and relevant per-file notices.
4. Third-party component inventory and license texts.
5. Selected and excluded optional components.
6. Local patch inventory with rationale.
7. License-audit result and approval.

## 4. Wlibc modifications

Keep original Wlibc files and patches identifiable. New Wlibc-authored files
are covered by the repository MIT license unless they contain third-party
material or carry an explicit file-level notice. Changes to imported files
must retain the upstream notices and applicable license terms.

## 5. Distribution

Source and binary distributions must include the notices and license texts
required by all included components. A root MIT LICENSE is not a substitute
for third-party notices.

## 6. Current status

- Original Wlibc repository license: MIT.
- musl source import: not yet performed.
- Exact musl revision: not selected.
- Third-party provenance audit: pending source selection.
- Wicofe-native syscall port: not implemented.
