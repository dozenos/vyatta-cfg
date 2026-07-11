# AGENTS.md

## Project purpose
Legacy Vyatta configuration system: config back-end, base configuration templates, and config-mode CLI completion mechanism. Maintained for compatibility on DozenOS LTS trains; new feature work goes into `dozenos-1x` Python.

## Tech stack
- C/C++. Autotools build (`configure.ac`, `Makefile.am`).
- Build deps (`debian/control`): `debhelper (>= 10)`, `libglib2.0-dev`, `libboost-filesystem-dev`, `libapt-pkg-dev`, `libtool`, `flex`, `bison`, `autoconf`, `automake`, `pkg-config`, `cpio`, `dh-autoreconf`.
- Debian packaging via `dpkg-buildpackage`.

## Build / test / run
```
autoreconf -i &&./configure && make
# or, in tree:
dpkg-buildpackage -uc -us -tc -b
```
No upstream test harness — validation happens at the integration level inside the live ISO build.

## Repository layout
- `src/`, `lib/`, `scripts/`, `functions/` — C/C++ sources, helper scripts, shell functions for the legacy CLI shell (`vbash`).
- `etc/` — installed templates and skeleton config.
- `debian/` — packaging.
- `configure.ac`, `Makefile.am` — autotools.

## Cross-repo context
Pulled into ISO builds via `dozenos/dozenos-build` (listed in an internal repository). Pairs at runtime with `dozenos/vyatta-bash` (the patched bash that hosts the CLI). Functionality has been progressively rewritten into `dozenos/dozenos-1x` (Python conf-mode/op-mode scripts) and `dozenos/vyconf` (future OCaml session daemon).

## Conventions
- Commit/PR title: `component: T12345: description` (Phorge task ID at https://dozenos.dev). Enforced by `dozenos/.github/.github/workflows/check-pr-message.yml@production`.
- Release-train branches: `rolling`, `circinus` (1.5 LTS), `sagitta` (1.4 LTS), `equuleus` (1.3 LTS).
- Backports via `@Mergifyio backport <branch>` (Mergify built-in command).
- Reusable workflows pinned to `dozenos/.github/.github/workflows/<name>.yml@production` — changes ship immediately on merge.
- Mergify config (single rule, adds `conflicts` label) lives in this repo.

## Notes for future contributors
- Treat as maintenance-only. New features go to `dozenos-1x`. Touch this repo only for bug fixes on LTS trains or to keep templates compatible with current Debian.
- The `pr-mirror-repo-sync.yml` workflow runs serially per merged PR; expect a downstream PR opened in an internal repository after merge. If `mirror-failed` label appears, see `dozenos/.github` PRMirrorOnboarding.md.
