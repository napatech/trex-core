# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TRex is a high-performance, DPDK-based network traffic generator by Cisco. It supports three traffic modes:
- **STF (Stateful)**: Flow-based stateful traffic for testing NAT, DPI, firewalls, IPS/IDS
- **STL (Stateless)**: Stream-based packet generation for routing/switching, RFC2544 benchmarks
- **ASTF (Advanced Stateful)**: High-scale TCP/UDP with L7 application emulation (HTTP, HTTPS, etc.)

## Build System

Uses WAF (Python-based build tool). All build commands run from `linux_dpdk/`:

```bash
cd linux_dpdk

# Configure (required before first build)
./b configure                    # default configuration
./b configure --with-bird        # with Bird routing daemon
./b configure --with-mana        # with Azure Mana driver
./b configure --no-mlx           # without Mellanox drivers
./b configure --sanitized        # with address sanitizer (GCC 4.9+)
./b configure --gcc7             # use GCC 7.4
./b configure --tap              # add tap driver for Azure

# Build
./b build

# Package
./b pkg
```

Build output goes to `linux_dpdk/build_dpdk/`. Shared libraries go to `scripts/so/<arch>/`.

Requires GCC 4.7.0+ (supports GCC 6, 7, 8). Python 3.3+ required for build scripts.

## Testing

Tests run from `scripts/`:

```bash
cd scripts

# Run full regression tests
./run_regression

# Run functional tests only (no hardware needed)
./run_functional_tests

# Run specific test (via pytest-style args passed to trex_unit_test.py)
./run_regression --test-name <test>
```

The test runner is `scripts/automation/regression/trex_unit_test.py`. Test suites are organized under:
- `scripts/automation/regression/functional_tests/` - Functional tests
- `scripts/automation/regression/astf_tests/` - ASTF mode tests
- `scripts/automation/regression/bird_tests/` - Bird integration tests
- `scripts/automation/regression/emu_tests/` - EMU (client-side protocols) tests

Python 3.4+ required for tests.

## Architecture

### C++ Engine (`src/`)

The high-performance data plane:
- `main_dpdk.cpp` — DPDK mode entry point (the main production binary)
- `main.cpp` — Simulator mode entry point (for testing without hardware)
- `bp_sim.cpp/.h` — Core packet simulation engine
- `src/stx/` — Traffic mode subsystems: `stf/`, `stl/`, `astf/`, `astf_batch/`
- `src/44bsd/` — Embedded TCP/IP stack (4.4 BSD-derived)
- `src/rpc-server/` — JSON-RPC server for control plane communication
- `src/pal/` — Platform Abstraction Layer for NIC portability
- `src/drivers/` — NIC-specific driver code
- `src/tunnels/` — Tunneling protocol support
- `src/hdrh/` — HDR Histogram for latency tracking
- `src/gtest/` — Google Test-based C++ unit tests
- `src/sim/` — Simulation framework

### Python Control Plane (`scripts/`)

- `scripts/automation/trex_control_plane/interactive/trex/` — Interactive TRex client library (main API)
- `scripts/automation/trex_control_plane/interactive/trex_stl_lib/` — Stateless client library
- `scripts/dpdk_setup_ports.py` — DPDK port configuration utility
- `scripts/dpdk_nic_bind.py` — NIC binding tool

### External Libraries (`external_libs/`)

Vendored dependencies including DPDK, yaml-cpp, ZMQ, JSON, valijson, BPF, libibverbs. These are checked into the repo.

## Key Conventions

- Commits require sign-off: `git commit -s` (DCO compliance)
- Mimic existing code style — no project-wide linter enforced
- C++ compiler flags include `-Wall -Werror` (GCC) — all warnings are errors
- The build system generates `version.c`/`version.h` from the `VERSION` file at the repo root
