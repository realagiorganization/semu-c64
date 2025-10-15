# Agent Onboarding Guide

This document gives automation agents and new contributors the minimum
information needed to get `semu-c64` ready for development work.

## Local Environment Setup

- `git`, `curl`, `bunzip2`, `sha1sum` (or `shasum` on macOS), `dd`, `make`
- [mos-c64-clang](https://github.com/llvm-mos/llvm-mos) for 6502 builds
- Device Tree Compiler (`dtc`)
- `mkfs.ext4` (package `e2fsprogs` on most systems)
- Optional: `clang-format` 12+ for formatting (`brew install clang-format`,
  `sudo apt install clang-format`)

Installation hints:
- Debian/Ubuntu: `sudo apt install git curl bzip2 build-essential dtc e2fsprogs`
- macOS (Homebrew): `brew install dtc e2fsprogs llvm-mos/homebrew-mos/llvm-mos`

Ensure that `mos-c64-clang` and `dtc` appear in `PATH` before building.

## Clone and Build

```bash
git clone https://github.com/onnokort/semu-c64.git
cd semu-c64
make              # builds the 6510 binary, needs mos-c64-clang
make check        # runs the PC emulator with downloaded kernel/rootfs
```

Notes:
- Set `C64=0 make` to build the host (x86_64) version with the system compiler.
- `make check` downloads kernel and initrd images; it needs network access.
- `make clean` removes build artifacts; `make distclean` also prunes fetched
  images.

## Refreshing Linux Artifacts

Run `scripts/build-image.sh` to rebuild the Linux kernel and Buildroot rootfs
from source. This script clones upstream repositories and may take a long time.
It expects a POSIX shell environment with `git`, `nproc`, and the Buildroot
toolchain prerequisites available.

## Development Workflow Reminders

- Follow the coding style summarized in `CONTRIBUTING.md` and run
  `clang-format -i *.[ch]` before committing.
- Document relevant build or runtime changes in `README.md` when appropriate.
- See the new "Submitting changes" section in `CONTRIBUTING.md` for git branch,
  commit, and push workflow details.

## Automation Logs

- `artifacts/logs/make_clean.log` – baseline cleanup before builds.
- `artifacts/logs/make_all.log` – C64-target build (fails if `mos-c64-clang`
  is missing).
- `artifacts/logs/make_clean_c64.log` – cleanup prior to host build.
- `artifacts/logs/make_all_host.log` – host build output (requires `dtc` for
  `minimal.dtb` generation).
- `artifacts/logs/make_check.log` – test run via `make check` (depends on C64
  toolchain).

## Useful References

- `README.md` – project overview, build, and run instructions
- `configs/` – Buildroot, BusyBox, and Linux configuration baselines
- `mk_linux_reu.py` – utility to assemble the REU image for VICE
- `scripts/build-image.sh` – end-to-end refresh of kernel/rootfs artifacts
