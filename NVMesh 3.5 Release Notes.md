# NVMesh 3.5 Release Notes <img src="./rn-media/NVIDIA_logo.png" width="220" alt="The NVIDIA logo." />

<!--
SPDX-FileCopyrightText: Copyright (c) 2026 NVIDIA CORPORATION & AFFILIATES. All rights reserved.
SPDX-License-Identifier: Apache-2.0
-->

# Change log

| Version |          Date           | Release | Soul                           |
| :-----: | :---------------------: | :-----: | ------------------------------ |
|   1.0   | <nobr>2026-07-12</nobr> |  3.5.0  | First release of NVMesh 3.5.0. |

# General

The main objective of the NVMesh 3.5.0 release is to provide storage-resilience and operational-recovery improvements. These include three-way mirroring, RAID‑60 striped erasure-coded volumes, VPG reserved-space reclaim, and drive reinstatement after eviction, which together expand volume protection options and simplify capacity and hardware recovery workflows.

See [Release Index](https://nvidia.atlassian.net/wiki/spaces/NSV/pages/2831793511/Release+index) for package details.

# Functionality

## Three-way Mirroring

[NVMESH-7774](https://jirasw.nvidia.com/browse/NVMESH-7774) \- Three-way mirroring adds support for creating RAID‑1 and RAID‑10 mirrored volumes with three copies instead of the default two-copy layout. This provides an additional mirror copy for deployments that require higher volume availability.

## RAID‑60 / Striped EC

[NVMESH-7877](https://jirasw.nvidia.com/browse/NVMESH-7877) \- RAID‑60, also referred to as Striped Erasure Coding, enables a volume to be striped across multiple erasure-coded groups. This allows a protected volume to use more drives in parallel while keeping erasure-coding protection within each group.

## VPG Reduction/Reclaim

[NVMESH-7872](https://jirasw.nvidia.com/browse/NVMESH-7872) \- VPG reduction/reclaim allows administrators to return unused reserved capacity from a non-default VPG. The VPG view exposes allocated reserved space so reclaim operations can be made according to the currently consumed capacity.

## Drive Reinstate

[NVMESH-8456](https://jirasw.nvidia.com/browse/NVMESH-8456) \- Drive reinstate enables a previously evicted drive to be returned to service after the underlying issue has been resolved, without deleting and rediscovering the drive. Related API support was added under [NVMESH-8457](https://jirasw.nvidia.com/browse/NVMESH-8457).

## Kafka 4.1.x Update

[NVMESH-7667](https://jirasw.nvidia.com/browse/NVMESH-7667) \- NVMesh 3.5.0 updates the supported Kafka broker version to 4.1.x. Kafka now runs in KRaft mode, ZooKeeper is no longer supported, and Java 17 or newer is required.

## NVMesh CSI Driver Update, version 1.9.2

This CSI Driver supports both this version of NVMesh and the following earlier versions: NVMesh 2.7.2, NVMesh 3.3, and NVMesh 3.4.

### New Features

[NVMESH-7800](https://jirasw.nvidia.com/browse/NVMESH-7800) \- Adapt the CSI Driver to the Management API Version SemVer format while preserving backward compatibility with the previous single-number API Version.

[NVMESH-9280](https://jirasw.nvidia.com/browse/NVMESH-9280) \- Add support for NVMesh 3.5.0 in the CSI Driver compatibility matrix.

### Bug Fixes

[NVMESH-8558](https://jirasw.nvidia.com/browse/NVMESH-8558) \- Improve management failover handling so publish and unpublish requests do not repeatedly retry a failed management server before using a healthy one.

[NVMESH-8804](https://jirasw.nvidia.com/browse/NVMESH-8804) \- Tolerate management API responses that omit `bdev.referenceIDs` while waiting for volume detach.

[NVMESH-9239](https://jirasw.nvidia.com/browse/NVMESH-9239) \- Make `ControllerUnpublishVolume` idempotent when the Kubernetes node or NVMesh client no longer exists.

[NVMESH-9242](https://jirasw.nvidia.com/browse/NVMESH-9242) \- Honor the `forceDetach` configuration in `ControllerUnpublishVolume` so controller-side detach behavior follows the configured policy.

[NVMESH-9243](https://jirasw.nvidia.com/browse/NVMESH-9243) \- Correct a misleading timeout message in the attach flow when waiting for volume I/O to become enabled in management.

[NVMESH-9286](https://jirasw.nvidia.com/browse/NVMESH-9286) \- Enforce read-only handling for raw block and encrypted volumes.

### Security Fixes

[NVMESH-7810](https://jirasw.nvidia.com/browse/NVMESH-7810) \- Address additional image-scan CVEs in the CSI Driver container image.

[NVMESH-8861](https://jirasw.nvidia.com/browse/NVMESH-8861) \- Update the distroless Python base image to address CVE-2026-7210.

[NVMESH-8862](https://jirasw.nvidia.com/browse/NVMESH-8862) \- Backport libexpat 2.8.1 to address CVE-2026-45186.

[NVMESH-8863](https://jirasw.nvidia.com/browse/NVMESH-8863) \- Pin `urllib3` to version 2.7.0 or later to address GHSA-mf9v-mfxr-j63j.

[NVMESH-8864](https://jirasw.nvidia.com/browse/NVMESH-8864) \- Pin `urllib3` to version 2.7.0 or later to address GHSA-qccp-gfcp-xxvc.

[NVMESH-8868](https://jirasw.nvidia.com/browse/NVMESH-8868) \- Pin `urllib3` to version 2.7.0 or later to address CVE-2026-21441.

[NVMESH-8869](https://jirasw.nvidia.com/browse/NVMESH-8869) \- Bump the `cryptography` dependency floor to address CVE-2026-34073.

[NVMESH-8871](https://jirasw.nvidia.com/browse/NVMESH-8871) \- Pin `urllib3` to version 2.7.0 or later to address CVE-2026-44432.

### Compatibility

- NVMesh 2.7.2, 3.3, 3.4, 3.5
- Kubernetes 1.22 \- 1.34

### Upgrade Notes

- NVMesh 3.5.0 requires NVMesh CSI Driver 1.9.2 or later; earlier CSI Driver versions are not supported with this release.
- When upgrading, upgrade the CSI Driver to version 1.9.2 before upgrading **nvmesh-management** to NVMesh 3.5.0.
- NVMesh 3.5.0 deployments on Kubernetes require Kubernetes control plane version 1.34 or later.

# CLI Changes

## Command Changes

- **drive reinstate** \- new command to reinstate an evicted drive back into service using the **/disks/reinstate** REST API introduced in management API version 18.0.0.

- **--raid-level** in **volume create** and **vpg create** now accepts **sec** for Striped Erasure Coding.

- **upgrade-agent keepalive** \- new command to request a keepalive from an upgrade agent. This command was also released in NVMesh 3.4.0-HF1.

- **vpg reclaim** \- new command to reclaim unused reserved space from a VPG.

# Resolved Issues

<!--
Template for new table entries
| [NVMESH-](https://jirasw.nvidia.com/browse/NVMESH-) | | |
-->

| Ticket | Description | Comments |
| :-: | :-- | :-- |
| <nobr>[NVMESH‑7524](https://jirasw.nvidia.com/browse/NVMESH-7524)</nobr> | Added management API documentation versioning support so generated API docs can reflect the NVMesh API version and track endpoint history. | |
| <nobr>[NVMESH‑7874](https://jirasw.nvidia.com/browse/NVMESH-7874)</nobr> | Documented the Ubuntu `needrestart` behavior that may restart NVMesh services during package upgrades. Added guidance to disable automatic restarts for NVMesh services during upgrades. | |
| <nobr>[NVMESH‑8697](https://jirasw.nvidia.com/browse/NVMESH-8697)</nobr> | Documented Intel IOMMU DMAR warnings that originate from an Intel IOMMU driver issue. Users should consult their distribution or vendor for a kernel that includes the fix. | |
| <nobr>[NVMESH‑8753](https://jirasw.nvidia.com/browse/NVMESH-8753)</nobr> | Fixed a TOMA volume lifecycle issue that could create overlapping GPT entries during rapid volume delete/create workflows and move affected drives into critical state. | |
| <nobr>[NVMESH‑8870](https://jirasw.nvidia.com/browse/NVMESH-8870)</nobr> | Updated Logs Collector output so management logs and non-management NVMesh component logs are split into separate per-host archives for easier review. | |
| <nobr>[NVMESH‑8966](https://jirasw.nvidia.com/browse/NVMESH-8966)</nobr> | Fixed `nvmesh volume rebuild --allow-allocation-on-offline-drives` CLI payload rendering so the command no longer fails with a client-side SyntaxError before sending the RPC. | |
| <nobr>[NVMESH‑9054](https://jirasw.nvidia.com/browse/NVMESH-9054)</nobr> | Fixed RDMA memory leaks identified by kmemleak during kernel testing. | |
| <nobr>[NVMESH‑9238](https://jirasw.nvidia.com/browse/NVMESH-9238)</nobr> | Fixed a SoftiWARP transmit-path crash observed while creating an XFS filesystem over TCP erasure-coded volumes. | |
| <nobr>[NVMESH‑9300](https://jirasw.nvidia.com/browse/NVMESH-9300)</nobr> | Fixed a 3-way mirror recovery-write crash by giving each recovery write leg its own SGL. | |
| <nobr>[NVMESH‑9348](https://jirasw.nvidia.com/browse/NVMESH-9348)</nobr> | Fixed an NDU path where the new `nvmeibc` module could fail to load with `Invalid argument` when upgrading over an Atom 3.4.0 environment. | |
| <nobr>[NVMESH‑9458](https://jirasw.nvidia.com/browse/NVMESH-9458)</nobr> | Relaxed the `nvmesh-management` package dependency to accept any `mongosh` 2.x version (`< 3.0.0`), replacing the previous strict pin (`<= 2.9.0`). This resolves installation failures on hosts running MongoDB 8.0.26 or later, which include `mongosh` 2.9.1. | |

# Known Issues

| Ticket | Description | Workaround & Comments |
| --- | :-- | :-- |
| <nobr>[NVMESH‑8524](https://jirasw.nvidia.com/browse/NVMESH-8524)</nobr> | Systems may report an Intel IOMMU warning during NVMesh operation. This warning is caused by an Intel IOMMU driver issue and requires a newer kernel that includes the fix. | Consult the distribution or vendor for a kernel with the Intel IOMMU fix, or request that the fix be backported. |
| <nobr>[NVMESH‑8728](https://jirasw.nvidia.com/browse/NVMESH-8728)</nobr> | Striped Erasure Coding (`sec`) volumes are not yet supported by the Kubernetes client (`k8s_client`). | Support is planned for a future release. |
| <nobr>[NVMESH‑9569](https://jirasw.nvidia.com/browse/NVMESH-9569)</nobr> | A single-drive reinstate may briefly take a protected volume offline, causing client I/O errors during the offline window. | Fix targeted for NVMesh 3.5.0-HF1. |

# Documentation

[Documentation Repository](https://gitlab-master.nvidia.com/excelero/nvmesh-documentation)

## Support Matrix Update

The updated NVMesh support matrix is available at [NVMesh Support Matrix](https://confluence.nvidia.com/display/NSV/NVMesh+Support+Matrix).

**<u>Note:</u>** Kernels from 6.8.0 up until 6.14.6 suffer from a kernel workqueue crash in ​​cma_netevent_work_handler, as described [here](https://bugzilla.redhat.com/show_bug.cgi?id=2363273). NVMesh is incompatible with these kernels ([NVMESH-6447](https://jirasw.nvidia.com/browse/NVMESH-6447)) as is. The NVMesh team has inserted a patched version of the relevant non-NVMesh kernel modules to fix this issue and make NVMesh compatible.

# Pre-built Packages

All NVMesh 3.5.0 packages can be found in NVIDIA's artifactory [here](https://urm.nvidia.com/artifactory/sw-ngc-nvmesh-generic-local/3.5.0).

# Upgrade

Upgrading from this version to future versions will be best conducted using the mNDU feature, see above for more details.

Upgrading from versions prior to NVMesh 3.2.0-HF2 is not possible. Upgrading from 3.2.0-HF2 is with a cold upgrade. From NVMesh 3.3.0 and onwards, it is recommended to perform upgrades using mNDU. For these versions, hot upgrade is supported.

**<u>Note:</u>** [NVMESH-7874](https://jirasw.nvidia.com/browse/NVMESH-7874) - For Ubuntu 22.04 and above, it is required to disable automatic service restarts during package upgrades. Ubuntu uses a utility called `needrestart` that may trigger an automatic restart of NVMesh services during an `apt install` or `apt upgrade`. To prevent this for the NVMesh services, create the a configuration file named `/etc/needrestart/conf.d/needrestart_nvmesh.conf` with the following contents:

```
$nrconf{override_rc} ||= {};
$nrconf{override_rc}->{qr(^nvmesh.\*\.service$)} = 0;
```

# Rocky Linux 10.1 Environment Setup

## Overview

This section describes how to configure a **Rocky Linux 10.1** system with:

- **Python 3.12** — system default, application runtime
- **Python 3.10** — side-by-side, required for build/compile-time dependencies
- **Node.js 18** — replacing the native newer version
- **Java** — OpenJDK with version fallback
- **MongoDB Community Edition 8.0**
- **Kafka client** — confluent-kafka 2.8.0 with bundled librdkafka 2.8.0, running under Python 3.12

**All commands must be run as root or with sudo.**

## 1. Base System Preparation

    yum update -y
    yum install -y wget curl tar gzip ca-certificates epel-release dnf-plugins-core
    dnf config-manager --set-enabled crb

## 2. Install Python 3.10 (Build/Compile Dependency)

    yum groupinstall -y "Development Tools"
    yum install -y gcc make openssl-devel bzip2-devel libffi-devel zlib-devel \
                   xz-devel sqlite-devel readline-devel tk-devel ncurses-devel

    cd /tmp
    wget https://www.python.org/ftp/python/3.10.14/Python-3.10.14.tgz
    tar xzf Python-3.10.14.tgz
    cd Python-3.10.14
    ./configure --enable-optimizations --prefix=/usr/local
    make -j"$(nproc)"
    make altinstall

**Verify:** python3.10 --version → Python 3.10.14

## 3. Install Node.js 18

### Remove the native Node.js

    yum remove -y nodejs npm || true
    yum module reset -y nodejs || true
    yum module disable -y nodejs || true

### Install Node.js 18 from NodeSource

    curl -fsSL https://rpm.nodesource.com/setup_18.x | bash -
    yum install -y nodejs-18.* --allowerasing

**Verify:** node --version → v18.x.x

## 4. Install Java (with version fallback)

    yum install -y java-21-openjdk-headless \
      || yum install -y java-21-openjdk \
      || yum install -y java-17-openjdk-headless \
      || yum install -y java-1.8.0-openjdk

    echo 'export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))' | tee /etc/profile.d/java.sh
    echo 'export PATH=$JAVA_HOME/bin:$PATH' | tee -a /etc/profile.d/java.sh
    source /etc/profile.d/java.sh

**Verify:** java -version

## 5. Install MongoDB 8.0

> Uses RHEL 9 repo (no RHEL 10 repo available yet). Requires AVX CPU support.

### Add the MongoDB 8.0 repo

Create `/etc/yum.repos.d/mongodb-org-8.0.repo` so that MongoDB can be installed directly using yum:

    cat > /etc/yum.repos.d/mongodb-org-8.0.repo <<REPO
    [mongodb-org-8.0]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/8.0/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://pgp.mongodb.com/server-8.0.asc
    REPO

MongoDB `.rpm` packages can also be downloaded directly from the [MongoDB repository](https://repo.mongodb.org/yum/redhat/). Downloads are organized by Red Hat / CentOS version, MongoDB release version, and architecture.

### Install

To install the latest stable version of MongoDB, run:

    yum install -y mongodb-org

### Enable and start

    systemctl daemon-reload
    systemctl enable --now mongod

**Verify:** mongod --version → db version v8.0.x

## 6. Install Kafka Client (confluent-kafka)

The confluent-kafka wheel from PyPI bundles its own **librdkafka 2.8.0**.
The system package librdkafka may be present but is **not loaded** by Python.

### Install

    python3.12 -m pip install confluent-kafka

### Verify

    python3.12 -c "import confluent_kafka; print('confluent-kafka:', confluent_kafka.__version__); print('librdkafka   :', confluent_kafka.libversion()[0])"

Expected:

    confluent-kafka: 2.8.0
    librdkafka   : 2.8.0

## 7. Verification Checklist

| Component           | Command                                              | Expected         |
|---------------------|------------------------------------------------------|------------------|
| OS                  | cat /etc/rocky-release                               | Rocky Linux 10.1 |
| Python (runtime)    | python3.12 --version                                 | Python 3.12.x    |
| Python (build-only) | python3.10 --version                                 | Python 3.10.14   |
| Node.js             | node --version                                       | v18.x.x          |
| Java                | java -version                                        | 21 / 17 / 1.8    |
| MongoDB             | mongod --version                                     | v8.0.x           |
| confluent-kafka     | python3.12 -c "import confluent_kafka; ..."          | 2.8.0            |
| librdkafka runtime  | python3.12 -c "... confluent_kafka.libversion() ..." | 2.8.0            |

## 8. Known Notes

1. **Python 3.12** is the application runtime.
2. **Python 3.10** is build-only (make altinstall, does not overwrite system Python).
3. **Node.js** native newer version must be removed and module disabled before installing Node 18.
4. **MongoDB Community Edition 8.0** — installed from the MongoDB yum repository for Red Hat / CentOS 9 compatible packages.
5. **Kafka client** — bundled librdkafka 2.8.0 inside wheel; system RPM unused by Python.
6. **Java** — JAVA_HOME only needed if running a Kafka broker locally.
