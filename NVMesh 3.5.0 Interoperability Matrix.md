# NVMesh Interoperability Matrix

<!--
SPDX-FileCopyrightText: Copyright (c) 2026 NVIDIA CORPORATION & AFFILIATES. All rights reserved.
SPDX-License-Identifier: Apache-2.0
-->

## Table of Contents

<!-- TOC -->
- [NVMesh Interoperability Matrix](#nvmesh-interoperability-matrix)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Drive Interoperability](#drive-interoperability)
    - [Drive Terminology](#drive-terminology)
    - [NVMe Drive Certification Process (High-Level)](#nvme-drive-certification-process-high-level)
      - [1) Prerequisites and Environment Readiness](#1-prerequisites-and-environment-readiness)
      - [2) Baseline Functional Validation](#2-baseline-functional-validation)
      - [3) Data Path Validation](#3-data-path-validation)
      - [4) Resiliency and Operations Validation](#4-resiliency-and-operations-validation)
      - [5) Performance Qualification](#5-performance-qualification)
      - [6) Defect Handling and Traceability](#6-defect-handling-and-traceability)
  - [CPU Interoperability](#cpu-interoperability)
  - [NIC Interoperability](#nic-interoperability)
  - [Operating System Interoperability](#operating-system-interoperability)
  - [Linux Kernel Interoperability](#linux-kernel-interoperability)
<!-- /TOC -->

## Introduction

The following document presents validated NVMesh interoperability.

Suggested additions are welcome via Github Issues for the repository hosting this document.

## Drive Interoperability

This section provides a list of NVMe drives validated to be interoperable with NVMesh.

If a drive is not listed, it does not mean that it will not work, rather that it has not been validated and documented.

An EC-Certified drive can be used to form erasure-coded volumes.

The drives are listed by manufacturer in alphabetical order.

| Manufacturer | Model / Series | EC-Certified | Form Factor | Notes |
|---|---|---|---|---|
| DapuStor | H5300 | Yes | U.2, E1.S, E3.S |  |
| Dell | Express Flash PM1725a | Yes | U.2 |  |
| Exascend | PD4 | Yes | U.3 |  |
| Intel | DC P4800X Optane | No | U.2, AIC |  |
| Intel | DC P5800X Optane | No | U.2 |  |
| Kioxia | CD5 | No | U.2 |  |
| Kioxia | CM5 | Yes | U.2, AIC | Initial formatting for 12 TB+ may require additional configuration. |
| Kioxia | CD6 | No | U.2 |  |
| Kioxia | CM6 | Yes | U.2 | Initial formatting for 12 TB+ may require additional configuration. |
| Kioxia | CM7 | Yes | E3.S |  |
| Kioxia | CD8 | No | E3.S |  |
| Micron | 6500 ION | Yes | U.3, E1.L | Thin firmware variant; FIPS+SED SKU validated with production firmware, non-FIPS/non-SED also validated in QA context. |
| Micron | 7100 Series | No | U.2, AIC |  |
| Micron | 7300 Series | No | U.2, M.2 | Requires NVMesh to reset the drive after formatting it. |
| Micron | 7400 Series | No | U.3, M.2, E1.S | Requires NVMesh to reset the drive after formatting it. |
| Micron | 9100 Series | No | U.2, AIC |  |
| Micron | 9200 Series | No | U.2 |  |
| Micron | 9300 Series | No | U.2 | Requires NVMesh to reset the drive after formatting it. |
| Netlist | N1951 Series | Yes | U.2, AIC |  |
| Netlist | N1962 Series | Yes | U.2 |  |
| Phison | EPW5970 (X1) | Yes | U.2 |  |
| Samsung | PM953 | No | U.2 |  |
| Samsung | PM963 | No | U.2, M.2 |  |
| Samsung | PM983 | No | U.2, NF1 |  |
| Samsung | PM9A3 | No | E1.S, U.2, M.2 |  |
| Samsung | PM9D3a | Yes | E3.S |  |
| Samsung | PM1725a | Yes | U.2, AIC |  |
| Samsung | PM1725b | Yes | U.2, AIC |  |
| Samsung | PM1733 | Yes | U.2 | Precondition recommendation: write full drive twice to improve read performance. |
| Samsung | PM1743 | Yes | U.2, E3.S |  |
| Samsung | PM1753 | Yes | E3.S |  |
| Samsung | SM963 | No | U.2 |  |
| Samsung | SZ985 | No | AIC |  |
| Samsung | XS1715 | No | U.2, AIC |  |
| Sandisk | SN861 | Yes | U.2, E1.S, E3.S |  |
| Solidigm | DC P432X Series | No | U.2, E1.L |  |
| Solidigm | DC P4420 Series | No | U.2 |  |
| Solidigm | DC P4500 Series | No | U.2, AIC |  |
| Solidigm | DC P4600 Series | No | U.2, AIC |  |
| Solidigm | DC P4510 Series | No | U.2 |  |
| Solidigm | DC P4610 Series | No | U.2 |  |
| Solidigm | D5-P5316 Series | Yes | U.2, E1.L | EC-capable but generally not a recommended usage profile. |
| Solidigm | D7-P5500 Series | Yes | U.2 |  |
| Solidigm | D7-P5510 Series | Yes | U.2 |  |
| Solidigm | D7-P5520 Series | Yes | U.2, E1.S, E1.L |  |
| Solidigm | D7-P5600 Series | Yes | U.2 |  |
| WD | Ultrastar SN200 Series | Yes | U.2, AIC |  |
| WD | Ultrastar SN620 | No | U.2, AIC |  |
| WD | Ultrastar SN630 | No | U.2, AIC |  |
| WD | Ultrastar SN640 | No | U.2, AIC |  |
| WD | Ultrastar SN840 | Yes | U.2 |  |

### Drive Terminology

- `U.2`: 2.5-inch NVMe form factor
- `AIC`: Add-in card
- `M.2`: PCIe mini card
- `NF1`: Samsung NF1 (sometimes called M.3)
- `E1.L`: EDSFF 1U Long
- `E1.S`: EDSFF 1U Short
- `E3.S`: EDSFF 3rd-gen short form factor

### NVMe Drive Certification Process (High-Level)

This is the recommended process to self-qualify a drive, e.g., for drives not listed above.

#### 1) Prerequisites and Environment Readiness
- Use NVMe drives only.
- For erasure-coded volumes, ensure drive support for 4K + 8B sectors sometimes called metadata support, long block support or VSS. The drive should support placing the 8B metadata in separate buffers.
- Build a minimum cluster that matches the targeted volume types:
  - RAID-1 / concatenated - at least 2 drives
  - RAID-10 - at least 4 drives
  - Erasure coding (for example 4+2) - at least 6 drives
- Ensure network and host setup meets NVMesh deployment requirements.

#### 2) Baseline Functional Validation
- Verify drive discovery in NVMesh management UI and CLI.
- Validate NVMesh drive formatting operations, both lifecycle and idempotent behavior.
- Confirm that the SMART health telemetry is readable and consistent.

#### 3) Data Path Validation
- Run representative read and write workloads using NVMesh block volumes.
- Run representative read and write workloads on filesystems on top of NVMesh block volumes.
- Perform trim or discard checks where relevant.
- Validate correctness and stability over sustained runtime, e.g., 24 hours.

#### 4) Resiliency and Operations Validation
- Validate hot-plug behavior while services are active.
- Validate drive migration and reinsertion recognition without unnecessary reformat.
- Validate drive eviction and rebuild procedures.
- Validate recovery behavior under target and drive disruption scenarios.

#### 5) Performance Qualification
- Benchmark through NVMesh volumes with production-like I/O profiles.
- Compare against direct kernel-NVMe baseline for the same patterns.
- Track both throughput and latency consistency under steady state and stress.

#### 6) Defect Handling and Traceability
- Collect full diagnostics and log bundles when issues are observed.
- Record reproducibility details such as: topology, firmware, workload pattern, and impact.
- File Github Issues with sufficient evidence to distinguish formatting, data path, rebuild, and UI classes of failures.

## CPU Interoperability

x86 64-bit CPUs and NVIDIA Grace ARM-based CPUs have been tested extensively. Caveats will be listed in release notes.

Other ARM-based CPUs may work as well, but this has not been formally validated.

## NIC Interoperability

NVMesh supports RDMA communication with all NVIDIA mlx5-based NICs, i.e., all NICs from ConnectX-4 onwards, including Bluefield DPUs.

NVMesh supports non-RDMA communication, i.e., based on TCP / SoftiWarp with any NIC.

## Operating System Interoperability

NVMesh is known to be interoperable with RHEL-compatible 8.x, Ubuntu 20.04 and 22.04 operating system distributions.

NVMesh clients and targets are known to be interoperable with Ubuntu 24.04 distributions.

Other distributions may work, but they have not been validated.

## Linux Kernel Interoperability

NVMesh is known to be interoperable with Linux kernels from 4.18 through 6.14. Known caveats will be listed in release notes.
