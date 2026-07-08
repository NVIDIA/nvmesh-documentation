# NVMesh 3.4.0 User Guide

<!--
SPDX-FileCopyrightText: Copyright (c) 2026 NVIDIA CORPORATION & AFFILIATES. All rights reserved.
SPDX-License-Identifier: Apache-2.0
-->

[![Release](https://img.shields.io/badge/Release-v3.4-brightgreen)](./ROADMAP.md)

- [NVMesh 3.4.0 User Guide](#nvmesh-340-user-guide)
- [Copyright and Trademark Information](#copyright-and-trademark-information)
- [Preface](#preface)
- [Acronyms and Terms](#acronyms-and-terms)
- [Introduction to NVMesh Technology](#introduction-to-nvmesh-technology)
  - [Overview](#overview)
    - [Management](#management)
    - [Client / Initiator](#client--initiator)
    - [Server / Target](#server--target)
    - [Topology Manager (TOMA)](#topology-manager-toma)
  - [Software Packages](#software-packages)
  - [Deployment Layout](#deployment-layout)
  - [Target - Client Communication](#target---client-communication)
  - [High Availability](#high-availability)
    - [Server Availability](#server-availability)
    - [Network Availability](#network-availability)
    - [Drives](#drives)
  - [Load Balancing](#load-balancing)
  - [Drive Format Types](#drive-format-types)
    - [Overview](#overview-1)
    - [4k+8 Formatting](#4k8-formatting)
      - [Checking for 4k+8 Format Compatibility](#checking-for-4k8-format-compatibility)
    - [4k+0 Drives](#4k0-drives)
    - [Formatting Timeouts](#formatting-timeouts)
  - [Logical Volume Types](#logical-volume-types)
    - [Concatenated](#concatenated)
      - [Concatenated Volumes, Single Target](#concatenated-volumes-single-target)
      - [Concatenated Volumes, Multiple Targets](#concatenated-volumes-multiple-targets)
    - [Striped RAID-0](#striped-raid-0)
      - [RAID-0 Volumes, Single Target](#raid-0-volumes-single-target)
      - [RAID-0 Volumes, Multiple Targets](#raid-0-volumes-multiple-targets)
    - [Mirrored RAID-1 Volumes](#mirrored-raid-1-volumes)
    - [Striped \& Mirrored RAID-10 Volumes](#striped--mirrored-raid-10-volumes)
    - [Erasure-coded Volumes](#erasure-coded-volumes)
      - [RAID-6 (6+2) Logical Volumes with Single Node Failure Protection](#raid-6-62-logical-volumes-with-single-node-failure-protection)
      - [RAID-6 (8+2) Logical Volumes with Dual Node Failure Protection](#raid-6-82-logical-volumes-with-dual-node-failure-protection)
      - [Supported Erasure-coded Volume Combinations](#supported-erasure-coded-volume-combinations)
      - [Target Node Redundancy](#target-node-redundancy)
  - [Access Modes](#access-modes)
  - [Zones](#zones)
  - [Minimal Configurations](#minimal-configurations)
    - [Single Node System](#single-node-system)
    - [Minimal Cluster for Mirrored Volumes, RAID-1/10](#minimal-cluster-for-mirrored-volumes-raid-110)
    - [Minimal Recommended Cluster for RAID-1/10](#minimal-recommended-cluster-for-raid-110)
    - [Minimal Recommended Cluster for RAID-6](#minimal-recommended-cluster-for-raid-6)
  - [Security](#security)
    - [Encrypted Volumes](#encrypted-volumes)
    - [Securing Volume Attachments](#securing-volume-attachments)
    - [Mutual TLS](#mutual-tls)
    - [Audit Logs](#audit-logs)
    - [Planned security features](#planned-security-features)
  - [CRC Check](#crc-check)
  - [512-byte Block Size Emulation](#512-byte-block-size-emulation)
- [NVMesh Software Installation](#nvmesh-software-installation)
  - [Installation Overview](#installation-overview)
  - [Prerequisites](#prerequisites)
    - [Hardware Requirements](#hardware-requirements)
    - [Memory Requirements](#memory-requirements)
      - [Clients](#clients)
      - [Targets](#targets)
      - [Per TB of storage](#per-tb-of-storage)
      - [Per drive per client](#per-drive-per-client)
      - [Per Drive](#per-drive)
      - [Per Target](#per-target)
      - [Per NIC](#per-nic)
      - [Examples](#examples)
      - [Full Calculator](#full-calculator)
    - [NVMe Device Requirements](#nvme-device-requirements)
      - [Supported NVMe Devices](#supported-nvme-devices)
      - [Drive Sector Size](#drive-sector-size)
    - [Networking / NIC Requirements](#networking--nic-requirements)
      - [RDMA Configurations](#rdma-configurations)
    - [Software Requirements](#software-requirements)
      - [SELinux](#selinux)
      - [Management Servers](#management-servers)
      - [Clients and Targets](#clients-and-targets)
        - [RHEL and Rocky 8.x (rpm)](#rhel-and-rocky-8x-rpm)
        - [Ubuntu 22.04 (deb)](#ubuntu-2204-deb)
      - [Operating System](#operating-system)
      - [NVIDIA (Mellanox) OFED](#nvidia-mellanox-ofed)
    - [Network Connectivity](#network-connectivity)
      - [Overview](#overview-2)
      - [RDMA Networking](#rdma-networking)
      - [RoCEv2](#rocev2)
      - [InfiniBand](#infiniband)
      - [Non-RDMA Networking – TCP/IP](#non-rdma-networking--tcpip)
    - [Firewalls and Specific Ports](#firewalls-and-specific-ports)
      - [Communication Matrix](#communication-matrix)
        - [Management Servers](#management-servers-1)
        - [Clients and Targets](#clients-and-targets-1)
        - [Inter-TOMA Communication](#inter-toma-communication)
    - [Mongo Access](#mongo-access)
    - [Kafka Access](#kafka-access)
    - [NTP Time Synchronization](#ntp-time-synchronization)
  - [Network Configuration](#network-configuration)
    - [RoCEv2 Multi-pathing](#rocev2-multi-pathing)
    - [SoftiWarp / TCP-IP](#softiwarp--tcp-ip)
    - [TCP performance tuning](#tcp-performance-tuning)
      - [`nvmesh.conf` and the TCP affinity script](#nvmeshconf-and-the-tcp-affinity-script)
      - [Flow steering, RSS, and multi-port listeners](#flow-steering-rss-and-multi-port-listeners)
      - [Latency versus throughput](#latency-versus-throughput)
      - [NVMesh common module parameter: `cq_vec_flags_tcp`](#nvmesh-common-module-parameter-cq_vec_flags_tcp)
      - [Configuration-oriented recommendations](#configuration-oriented-recommendations)
  - [Software Delivery](#software-delivery)
    - [NVMesh Packages](#nvmesh-packages)
    - [Software Delivery for Red Hat Distributions](#software-delivery-for-red-hat-distributions)
    - [Software Delivery for Ubuntu](#software-delivery-for-ubuntu)
  - [Installation Instructions](#installation-instructions)
    - [Prepare Security Certificates (Optional)](#prepare-security-certificates-optional)
      - [Preparing Certificate Authorities](#preparing-certificate-authorities)
      - [Preparing Component Certificates](#preparing-component-certificates)
      - [Deploying Certificates](#deploying-certificates)
    - [Install MongoDB, NodeJS and Kafka](#install-mongodb-nodejs-and-kafka)
      - [Install MongoDB](#install-mongodb)
      - [Securing MongoDB Access](#securing-mongodb-access)
      - [Configuring Authentication for MongoDB](#configuring-authentication-for-mongodb)
      - [Install NodeJS](#install-nodejs)
      - [Installing Kafka Servers](#installing-kafka-servers)
      - [Install Kafka Libraries](#install-kafka-libraries)
      - [Install NVMesh Management](#install-nvmesh-management)
    - [Install NVMesh Clients and Targets](#install-nvmesh-clients-and-targets)
    - [Configure TCP/IP Support](#configure-tcpip-support)
    - [Start the NVMesh Client](#start-the-nvmesh-client)
    - [Exclude Drives](#exclude-drives)
    - [Start the NVMesh Target](#start-the-nvmesh-target)
    - [(Optional) Assign Targets to Zones](#optional-assign-targets-to-zones)
    - [Format Drives](#format-drives)
- [Getting Started](#getting-started)
  - [Drives Management](#drives-management)
    - [Overview](#overview-3)
    - [Drives Table](#drives-table)
  - [Volume Creation and Attachment](#volume-creation-and-attachment)
    - [Login to the Management GUI](#login-to-the-management-gui)
    - [Verify Client and Target Registration](#verify-client-and-target-registration)
    - [Create a Volume](#create-a-volume)
    - [Attach a Volume to a Client](#attach-a-volume-to-a-client)
- [General Settings](#general-settings)
- [Client and Target Configuration](#client-and-target-configuration)
  - [Configuration Profiles](#configuration-profiles)
    - [Configuration Elements](#configuration-elements)
      - [Standard Options](#standard-options)
      - [Advanced Options](#advanced-options)
  - [Client and Target Options](#client-and-target-options)
    - [Common Client and Target Options](#common-client-and-target-options)
    - [Client Specific Options](#client-specific-options)
    - [Target Specific Options](#target-specific-options)
    - [Tracing Related Options](#tracing-related-options)
    - [Unsupported Options](#unsupported-options)
  - [Module Parameters](#module-parameters)
- [Storage Configuration](#storage-configuration)
  - [Target Classes](#target-classes)
    - [Overview](#overview-4)
    - [Creating Target Classes](#creating-target-classes)
  - [Drive Classes](#drive-classes)
    - [Overview](#overview-5)
    - [Creating Drive Classes](#creating-drive-classes)
  - [Volume Provisioning Groups](#volume-provisioning-groups)
  - [Protection Domains](#protection-domains)
  - [Read-only Access](#read-only-access)
  - [Client Operations](#client-operations)
    - [Attaching and Detaching Volumes to Clients](#attaching-and-detaching-volumes-to-clients)
    - [Attachment Status](#attachment-status)
      - [Volume Status Example](#volume-status-example)
      - [IO Disabled](#io-disabled)
  - [Drive Segment Zeroing](#drive-segment-zeroing)
  - [Volume Rebuild Prioritization](#volume-rebuild-prioritization)
  - [TOMA Configuration](#toma-configuration)
    - [The "toma\_rpc" Tool](#the-toma_rpc-tool)
  - [Data Scrubbing](#data-scrubbing)
- [Management Configuration](#management-configuration)
  - [Management Options](#management-options)
    - [General Options](#general-options)
    - [Kafka Options](#kafka-options)
    - [MongoDB \& MongoDB NVMesh Metadata Connection Options](#mongodb--mongodb-nvmesh-metadata-connection-options)
    - [SMTP Options](#smtp-options)
    - [Statistics Options](#statistics-options)
    - [Backup Options](#backup-options)
  - [Management Scalability](#management-scalability)
    - [Mongo Replica Sets](#mongo-replica-sets)
    - [Load Balancers](#load-balancers)
    - [Kafka Brokers](#kafka-brokers)
- [Monitoring](#monitoring)
  - [Dashboard](#dashboard)
    - [Summary-level Status Dashboard](#summary-level-status-dashboard)
    - [Capacity](#capacity)
    - [Alerts](#alerts)
  - [Volume State](#volume-state)
    - [Action](#action)
    - [Status](#status)
    - [Health](#health)
    - [Volume’s per-Client State](#volumes-per-client-state)
  - [Client State](#client-state)
  - [Target State](#target-state)
  - [PROC-fs (/proc) Statistics](#proc-fs-proc-statistics)
    - [Client Volume Statistics](#client-volume-statistics)
    - [Client and Target Drive Statistics](#client-and-target-drive-statistics)
  - [PROCFS (/proc) Monitoring Information](#procfs-proc-monitoring-information)
    - [General Notes](#general-notes)
    - [/proc/nvmeib](#procnvmeib)
    - [/proc/nvmeiba](#procnvmeiba)
    - [/proc/nvmeibc](#procnvmeibc)
    - [/proc/nvmeibs](#procnvmeibs)
  - [Management State](#management-state)
- [Logging](#logging)
  - [System Logs](#system-logs)
  - [Management Logs](#management-logs)
  - [Binary Tracing](#binary-tracing)
- [Alerts](#alerts-1)
  - [Error Alerts](#error-alerts)
  - [Warning Alerts](#warning-alerts)
- [NVMesh Best Practices](#nvmesh-best-practices)
  - [Performance Best Practices](#performance-best-practices)
    - [CPU Interrupt Affinity and IRQ Balancing](#cpu-interrupt-affinity-and-irq-balancing)
      - [OS-Native IRQ Balancer](#os-native-irq-balancer)
      - [Mellanox Affinity (optional)](#mellanox-affinity-optional)
      - [Mellanox Tune (optional)](#mellanox-tune-optional)
    - [Tuned](#tuned)
    - [NUMA Architectures](#numa-architectures)
      - [Review NUMA Topology](#review-numa-topology)
      - [arnic\_prefer](#arnic_prefer)
  - [Multi-Path Configuration](#multi-path-configuration)
    - [Ethernet \& RoCE](#ethernet--roce)
      - [General Considerations](#general-considerations)
      - [ConnectX Ethernet Adapter Considerations](#connectx-ethernet-adapter-considerations)
      - [RDMA Atomic Limitations](#rdma-atomic-limitations)
  - [Hardware Related Items](#hardware-related-items)
    - [NVMe Drives](#nvme-drives)
      - [NVMe Devices](#nvme-devices)
    - [PCI Considerations](#pci-considerations)
      - [NVMe Devices](#nvme-devices-1)
      - [NICs](#nics)
      - [PCI Multiplexers](#pci-multiplexers)
      - [Inter-CPU Connectivity](#inter-cpu-connectivity)
  - [Network Considerations](#network-considerations)
    - [Multi-switch Topologies](#multi-switch-topologies)
    - [Dedicated Storage Network](#dedicated-storage-network)
    - [InfiniBand](#infiniband-1)
    - [Ethernet](#ethernet)
  - [Kernel Configuration](#kernel-configuration)
    - [Serial Console](#serial-console)
    - [Free Memory Space](#free-memory-space)
  - [Mongo Best Practices](#mongo-best-practices)
    - [Authentication](#authentication)
    - [Memory Constraints](#memory-constraints)
    - [High Availability](#high-availability-1)
  - [Best Practices for AMD EPYC Processors](#best-practices-for-amd-epyc-processors)
    - [BIOS Settings](#bios-settings)
      - [Processor Settings](#processor-settings)
      - [System Profile Settings](#system-profile-settings)
    - [IOMMU](#iommu)
    - [Interrupt management](#interrupt-management)
      - [NVMe drives](#nvme-drives-1)
      - [NICs](#nics-1)
    - [Other NVIDIA NIC settings](#other-nvidia-nic-settings)
    - [NVMe drives](#nvme-drives-2)
    - [Others](#others)
- [Maintenance](#maintenance)
  - [NVMesh Health Check](#nvmesh-health-check)
  - [NVMesh Logs Collections](#nvmesh-logs-collections)
  - [Hardware Operations](#hardware-operations)
    - [Drive Failure \& Replacement](#drive-failure--replacement)
      - [Evicting a Drive](#evicting-a-drive)
      - [Volume Rebuild](#volume-rebuild)
    - [NIC Failure and Replacement](#nic-failure-and-replacement)
      - [General](#general)
      - [RoCE Specific](#roce-specific)
      - [InfiniBand Specific](#infiniband-specific)
    - [Moving a Drive between Targets](#moving-a-drive-between-targets)
    - [Resizing a Drive](#resizing-a-drive)
  - [Software Operations](#software-operations)
    - [Modify management IP](#modify-management-ip)
    - [Reset factory defaults](#reset-factory-defaults)
    - [Rename hostname](#rename-hostname)
    - [Upgrading NVMesh](#upgrading-nvmesh)
    - [Altering the per-Client Journal Space](#altering-the-per-client-journal-space)
  - [Key Rotation / Certificate Renewal](#key-rotation--certificate-renewal)
  - [Target Cleanup](#target-cleanup)
  - [Cluster Cleanup](#cluster-cleanup)
- [Configuration Limits](#configuration-limits)

# Copyright and Trademark Information

© 2026 NVIDIA All rights reserved.

Specifications are subject to change without notice.

NVMesh® is a registered trademark of NVIDIA.

All other brands or products are trademarks or registered trademarks of their respective holders and should be treated as such.

# Preface

Whenever instructions are given to perform an action via the GUI, there is an alternative option to perform it using the RESTful API and for almost all actions via the CLI. For brevity, the instructions are only specific for the GUI in many cases.

**<u>Audience</u>**

The primary audience for this document is intended to be storage and/or application administration personnel responsible for installing and deploying NVMesh.

**<u>Feedback</u>**

We continually try to improve the quality and usefulness of documentation. If you have any corrections, feedback, or requests for additional documentation, send an e-mail message to <nvmesh-documentation@nvidia.com>.

# Acronyms and Terms

| Acronym | Description |
| --- | --- |
| Hidden volume | A hidden volume is a volume or a part of a volume attached to a client for the client to perform recovery operations on the volume. Such attachments happen only on targets. <br><br> As the volume is only attached for recovery by the storage system, it does not have a /dev device. |
| NVMeOF | The NVMe over Fabrics standard. |
| RDMA IO | This is IO executed using RoCE or InfiniBand for communication. |
| SIW | SoftiWarp, which provides an RDMA API, but performs communication over TCP without RDMA. <br>Often referred to as TCP in module parameter names. |
| SIW IO | This is IO executed using SIW for communication, in contrast to RDMA IO. |

# Introduction to NVMesh Technology

NVMesh is a high performance, distributed, shared, multi-attach, software-defined, block storage solution. It provides low-latency failure-protected storage access with in-server NVMe flash performance characteristics utilizing commodity off-the-shelf components.

It enables utilization of NVMe drives, potentially spread over many physical systems, as a unified, redundant storage pool.

As NVMesh is a software only solution, it has the flexibility to provide redundant, centrally managed storage in a hyper-converged architecture or a disaggregated (top-of-rack) storage solution.

## Overview

NVMesh is comprised of four main software elements:

1. A centralized management server

1. An initiator or storage client, which presents the virtualized storage as block devices

1. A target or storage server running on nodes with physical NVMe drives

1. A topology manager (TOMA), which runs on the same nodes as the targets

<div align="center"><img src="./ug-media/image1.png" style="width:6.5in;height:2.13889in" alt="A diagram of key NVMesh components." />

<em>NVMesh Software and Hardware Components</em></div>

### Management

Management is the centralized administration element of an NVMesh deployment providing storage management, configuration and monitoring functionality.

It presents a RESTful API, and a web-based GUI. An adjunct CLI can be used to interact with the management server. Both the GUI and the CLI interact only via the RESTful API with the management server’s backend.

Management’s key roles are:

- Keep an inventory of relevant resources.

- Manage virtual volume life cycle.

- Manage association or attachment of volumes to consumers, i.e., initiators or clients.

- Inform clients of the relevant resources for attachments.

- Provide observability data for the entire cluster.

Management is used to implement "management"-level functionality only and is not part of a volume’s control or data path. If management is not available, i.e., not functional or not accessible, block devices will continue to work. However, life cycle operations, such as creating, extending and deleting volumes will not be possible. Management is instrumental also for attaching and detaching volumes from clients. Finally, if there are network changes on a target that clients need to be aware of, such as an IP address change, then this information flows through management and will fail to reach clients if a functioning management is not accessible.

Management is a web server-based application and can run on virtually any Linux system, including co-residing with targets or clients. Management does require some allocation of CPU power, memory and drive space, with actual resource requirements dependent on the system load, i.e., the scale of the cluster and the activity within. Multiple management instances can be installed in a cluster for scalability and redundancy. Management can be run in a containerized fashion.

The underlying technologies are NodeJS and MongoDB. Kafka is used to communicate between management and the endpoints, i.e., clients and targets.

Once installed, management is controlled via the nvmeshmgr service.

### Client / Initiator

The NVMesh client or initiator provides block device access functionality for compute nodes. Virtual volumes are attached to clients. They manifest themselves as block devices, usually under the `/dev/nvmesh` directory.

A node that has one or more NVMe devices to share and runs the target software as well is called a converged node. Management may also be run on a client or on a converged node.

<div align="center"><img src="./ug-media/image2.png" style="width:7.24306in;height:3.74653in" alt="Diagram of the NVMesh communications flow." />

<em>NVMesh Component Interactions</em></div>

Clients communicate over Kafka with management and receive instructions and updates on which volumes to attach to, the layout of the volumes across drives and the means of accessing the targets that hold these drives. Clients report their status to management regarding the ability to perform IO to the relevant volumes.

Clients communicate with targets using one of the supported fabrics, InfiniBand, RoCE or SIW. They also interact with the TOMAs co-residing with targets using the same communication channels.

Once installed, clients are controlled via the nvmeshclient service. Running this service triggers additional services being run:

- nvmeshtrace – runs NVMesh tracing facilities.

- nvmeshagent – runs nvmeshAgent.py which performs management-sourced instructions.

- nvmeshcm – runs nvmeshcm.py, which is the implementation of the MCS, which processes communications with management.

Client functionality is implemented using a set of kernel modules, as described in the following table.

| Kernel Module | Function |
| --- | --- |
| nvmeibc | This is the main NVMesh client kernel module including all logic for block device implementation, such as mirroring and erasure coding logic. |
| nvmeiba | The NVMesh atom module is a shim for maintaining open connections to the block devices while the nvmeibc kernel module is reloaded, which facilitates hot upgrades. <br><br> This is the only kernel module that is not restarted upon a hot upgrade. Therefore, changes in the atom functionality lead to cold upgrades. <br><br>Additional functionality to make upgrade processes faster such as keeping memory allocations intact is performed by the nvmeib_keeper module. |
| nvmeib_common <br> nvmeib_common_public | These modules contain common client and target functionality. |
| nvmeib_common_siw_public | This module contains common code for calling SIW functionality. |
| nvmeib_common_mlx5_public | This module contains common code for calling MLX5 (Mellanox version 5) functionality. |
| nvmeib_keeper | This module is used to maintain ownership of data structures when nvmeibc is hot-upgraded to avoid lengthy de-allocations and subsequent re-allocations. |
| siw | This module is a standard Linux kernel module. However, NVMesh replaces it with an altered version with enhancements primarily for stability and performance. |

Module dependencies are shown in the graph below, including the target’s kernel mode (nvmeibs).

The target module, nvmeibs, makes function calls into nvmeibc, but is not formally dependent on it.

The NVMesh kernel modules are dependent on multiple Linux kernel modules also.

<div align="center"><img src="./ug-media/image37.png" style="width:7.24306in;height:3.74653in"
alt="Diagram of NVMesh module dependencies." /></div>

**<u>Notes:</u>**

1. Previous versions of NVMesh supported multiple client instances on the same physical node. This is no longer supported or documented.

1. NVMesh has a facility for exposing volumes using NVMe-over-Fabrics (NVMeOF) with an NVMeOF gateway.

### Server / Target

The target or server software identifies and maintains storage hardware, i.e., NVMe drives and compatible NICs, and provides storage clients access to physical drives for IO and provides shared memory for IO synchronization operations. Targets report these resources and their status to management. They listen for clients connecting to IO resources, connect them and then perform IO operations on their behalf.

The core target functionality is implemented using kernel modules. Targets also run a user-space software component called TOMA (Topology Manager), described in the next section. The target and the TOMA work closely together. If the TOMA cannot interact with the server, it will not function. If the server cannot interact with the TOMA on the node, it will not accept connections from clients. Target and TOMAs interact using a netlink interface, a common means of communicating between user mode and kernel mode software.

All targets are also clients as NVMesh uses the client block driver software to perform operations such as volume rebuilding. The client used is the one on the node with the drive that has the most up-to-date data that is the source of truth for rebuilds or other client-originated operations. So, for the up-to-data on the drive to be used, a target is needed, and to rebuild potentially stale data, a client is needed.

The target shares hardware resource state with management. This communication is conducted over Kafka.

Clients connect to targets using one of the supported fabrics, InfiniBand, RoCE or SIW. They use these connections for sending IO operations to drives and for performing synchronization operations with locks and other data elements stored in target memory, to indirectly synchronize with other clients and ensure data consistency. Clients use these connections also to interact with TOMAs to obtain permission to access virtual volumes and to know how to access them. The target serves as a conduit for client-TOMA interactions.

Once installed, targets are controlled via the nvmeshtarget service. This service is dependent on the nvmeshclient service.

### Topology Manager (TOMA)

A topology manager (TOMA) is run alongside the target software. TOMAs cooperatively manage detecting the current topology, which is the combination of component liveness and virtual volume definitions and state. The TOMAs use this information to direct clients how to perform IO to maintain data correctness.

The TOMAs running in an NVMesh cluster, or more precisely a zone within the cluster, communicate between themselves and form a quorum, with one node elected as the leader.

The leader distributes the topology state to the other TOMAs in the cluster, known as followers. All TOMAs direct clients connected to drives on the node how to perform IO. The TOMAs detect error conditions, such as node reboots or missing drives, and are responsible for error handling activities. They monitor for events like drive or network status changes. TOMAs initiate rebuilds and prevent "split brain" in mirrored volume configurations.

## Software Packages

NVMesh comprises the following software packages in the form of rpm files or deb files depending on the operating system being installed on.

| Package Name | Description |
| --- | --- |
| `nvmesh-base` | `nvmesh-base` provides the basic NVMesh software directories and shared components, such as the management agent, MCS and tracing facilities, required for the system to function. |
| `nvmesh-client` | `nvmesh-client` provides the client software. |
| `nvmesh-target` | `nvmesh-target` provides the target software including TOMA. |
| `nvmesh-utils` | `nvmesh-utils` provides a set of utilities often required for NVMesh, such as: <ul><li>`nvmesh` - The NVMesh CLI, see the NVMesh CLI Guide for details.</li><li>`nvmesh_logs_collector` - a utility for collecting system-wide information for support to diagnose system issues.</li></ul> |
| `nvmesh-management` | `nvmesh-management` provides the management server software. |
| `nvmesh-monitor` | `nvmesh-monitor` provides Prometheus plugins. |
| `nvmesh-interopdb` | `nvmesh-interopdb` provides interoperability information required by the management server for managed non-disruptive upgrades. |
| `nvmesh-upgrader` | `nvmesh-upgrader` provides the upgrade agent that handles system upgrades and maintenance operations. |

Package dependencies are shown in the following diagram:

```mermaid
flowchart TB
    classDef nvidiaGreen fill:#76b900,stroke:#2d5c1f,color:#fff

    direction LR
    nvmesh-utils
    nvmesh-monitor
    nvmesh-upgrader
    nvmesh-interopdb

    nvmesh-base
    nvmesh-management
    nvmesh-client
    nvmesh-target

    nvmesh-utils --> nvmesh-base
    nvmesh-utils --> nvmesh-management
    nvmesh-base --> nvmesh-client
    nvmesh-client --> nvmesh-target

    class nvmesh-utils,nvmesh-monitor,nvmesh-upgrader,nvmesh-interopdb,nvmesh-base,nvmesh-management,nvmesh-client,nvmesh-target nvidiaGreen
```

## Deployment Layout

One or more NVMesh components can be run on a single machine, providing storage system architecture flexibility. A typical setup will comprise several client nodes and several target nodes. The management servers can run virtually anywhere with TCP/IP connectivity to the client and target nodes.

For best performance, clients and targets should communicate using RDMA. If there are multiple ports or NICs, NVMesh will attempt to use all of them by default. It is possible to limit which NICs will be used. NVMesh supports RDMA over ethernet (RoCEv2) and over InfiniBand. It also has a TCP option, implemented using SoftiWarp. A target node running the target module should have one or more NVMe SSDs.

For RDMA implementations, Mellanox OpenFabrics Enterprise Distribution (MOFED) RDMA drivers, DOCA drivers, or "inbox", i.e., native Linux, RDMA drivers can be used.

## Target - Client Communication

Clients communicate directly with targets for the data or IO path. There is no metadata manager lookup, redirection or other system bottleneck often associated with SDS solutions.

Also, there is no communication between clients and no communication between targets to fulfill client-initiated IO.

Target nodes do communicate for rebuilding data as needed. However, this is done via clients running on the same nodes as the targets communicating with other targets.

TOMAs perform regular control path communication with each other utilizing a light-weight status or keep-alive protocol based upon RAFT.

## High Availability

NVMesh provides high availability for both software and hardware components.

### Server Availability

NVMesh constantly monitors the availability of the cluster nodes. When a target server fails due to a hardware or software problem, the other target servers will identify the failure and change the work mode for the relevant volumes. They will continue to function in a degraded mode if possible. The failover time is determined through configuration options.

### Network Availability

NVMesh constantly monitors the availability of network links, both in the clients and the targets. Components attempt to use all active links in parallel. Data sent over a link that failed will be retransmitted over alternative active connections.

### Drives

In a protected volume, drives may fail. Missing contents will be rebuilt on alternative capacity via mirrored copies or based on parity calculations, depending on the volume’s protection method.

## Load Balancing

By default, NVMesh clients leverage all available network links or paths between themselves and the targets. Traffic is aggregated over multiple links. If a link fails, the client will automatically divert or retransmit traffic over other available links.

**<u>Note:</u>** When a link fails and becomes active again, it may take up to 30 seconds before the client starts using this link.

## Drive Format Types

### Overview

Drives must be formatted before use. NVMesh supports two format types:

- 4k+8 formatting. 4k is the size of data in each drive block. 8 is the number of metadata bytes per drive block.

- 4k+0 formatting.

### 4k+8 Formatting

This formatting supports all NVMesh volume types, including erasure-coded volumes.

This format type appears available for formatting in management for drives supporting 4K+8 bytes sectors with the NVMe standard Metadata Pointer Support capability.

All blocks are zeroed with the format operation for this format type.

The following describes the on-disk structure of such formatting:

| Section Name             | Size                                |
| ------------------------ | ----------------------------------- |
| MBR (master boot record) | 4 KiB                               |
| GPT Header               | 4 KiB                               |
| GPT Table                | 20 KiB                              |
| NVMesh "Private Area"    | Up to ~0.5% of the drive’s capacity |
| Journal                  | 2 GiB                               |
| Journal Metadata         | 32 MiB                              |
| …                        |                                     |
| Data                     |                                     |
| …                        |                                     |
| GPT Table – second copy  | 20 KiB                              |
| GPT Trailer              | 4 KiB                               |

#### Checking for 4k+8 Format Compatibility

To check whether a drive can be used for 4k+8 formatting, use the nvme command-line tool supplied with the nvme-cli package in the supported operating system distribution or the one supplied with the `nvmesh-target` package at this path: `/opt/nvmesh/target-repo/scripts/nvme-cli/nvme`.

Perform the following steps as root:

1. Run `nvme list` to get a list of all devices on the node.

1. Identify the device name from the Node column for the device to be checked, usually this can be done using the SN (serial number) or the Model.
   1. For instance, for a device with a block device name of `/dev/nvme1001`, run:

      `nvme id-ns -H /dev/nvme1001`

1. The last lines of output contain the supported block formats. If there is a line like the following with a Metadata size of 8 bytes and a Data size of 4096 bytes, this drive supports 4k+8 bytes sectors.

```
LBA Format 3 : Metadata Size: 8 bytes - Data Size: 4096 bytes - Relative
Performance: 0x2 Good (in use)
```

To verify support for Metadata Pointer Support, locate the mc section of the output from that same command. If it is supported, it should appear as follows with the wording "Metadata Pointer Supported":

```
mc : 0x3

[1:1] : 0x1 Metadata Pointer Supported

[0:0] : 0x1 Metadata as Part of Extended Data LBA Supported
```

### 4k+0 Drives

Erasure coded volumes will not be placed on drives with this format type. It will be used only for drives that do not support 4K+8 bytes sectors.

**<u>Note:</u>** Drives with 4k+8 bytes sectors, but without Metadata Pointer Support are not supported in NVMesh in general.

### Formatting Timeouts

Formatting operations typically take a few seconds to a few minutes, depending on the size of the drive and the performance of TRIM operations on it, sometimes referred to as erase operations or deallocations.

Most drives format within a few seconds, but some drives, especially with larger capacity, may require more time.

The default format timeout is determined by the nvmeibs module parameter, submit_wait_timeout, set to 15 seconds by default. The target will increase the format timeout per drive by this value upon a failed format.

## Logical Volume Types

NVMesh supports multiple volume types that vary in their data layout and their level of data protection. The supported logical volume types are:

- Concatenated

- Striped RAID-0

- Mirrored RAID-1

- Striped & Mirrored RAID-10

- Erasure-coded

Volumes are allocated from the pool of storage devices managed by NVMesh.

Volumes may utilize a portion of one device or portions of multiple devices. For instance, a small "Concatenated" volume may be allocated from a small part of a single drive. A small, protected volume will be allocated from a small part of multiple drives.

A portion of a device may also be the entire device. A large "Concatenated" volume could require allocation of multiple entire drives.

A single drive may host a portion of a single volume or may host portions of multiple volumes. In this context, the term a portion of a volume may also refer to the entire volume.

### Concatenated

#### Concatenated Volumes, Single Target

<div align="center"><img src="./ug-media/image3.png" style="width:6.5in;height:4.375in"
alt="Diagram of the layout of concatenated volumes on a single target." />

<em>Concatenated volumes on a single target</em></div>

- Vol1 is a concatenated volume contained within a single drive.
  - LBAs are mapped to a contiguous range on the single drive.
  - Performance of the volume is limited to the performance of the one drive.

- Vol2 is a concatenated volume like Vol1, but it has a larger size.

- Vol3 is a concatenated volume larger than a single drive.
  - LBAs are contiguous across Drive 2 and then continue onto part of Drive 3.
  - Sequential IO is limited to the performance of 1 drive, regardless of how many drives make up the volume.
  - Random (4K) read and write performance across the entire volume LBA range is equivalent to about 2 drives, dependent upon how other volumes allocated on Drive 3 use it.

- Volume capacity overhead is 0%, i.e., the volume size is equivalent to the aggregate segment allocation.

- Concatenated volumes offer no protection against failures. If any drive or host that is part of a concatenated volume is unavailable, the volume will go offline. A failed drive results in permanent data loss.

#### Concatenated Volumes, Multiple Targets

<div align="center"><img src="./ug-media/image4.png" style="width:6.5in;height:2.13889in"
alt="Diagram of the layout of concatenated volumes on multiple targets." />

<em>Concatenated volumes across multiple targets</em></div>

- Vol1, Vol2 and Vol3 are the same as in the previous example.

- Vol4 is a concatenated volume larger than two drives.
  - LBAs are contiguous across a portion of Drive 3 and then continue across Drive 4 and Drive 5.
  - Sequential IO is limited to the performance of one drive, regardless of how many drives make up the volume.
  - Random (4K) read/write performance across the entire volume LBA range may be as high as that of three drives.

- Volume capacity overhead is 0%, i.e., the volume size is equivalent to the aggregate segment allocation.

- Concatenated volumes offer no protection against failures. If any drive or host that is part of a concatenated volume is unavailable, the volume will go offline. A failed drive results in permanent data loss.

### Striped RAID-0

#### RAID-0 Volumes, Single Target

<div align="center"><img src="./ug-media/image5.png" style="width:6.5in;height:4.27778in"
alt="Diagram of the layout of RAID-0 volumes on a single target." />

<em>RAID-0 volumes on a single target</em></div>

- Vol1 is a RAID-0 volume with a width of 3.
  - LBAs are spread across stripes on Drives 1, 2 and 3, writing 32 × 4K blocks (128K) before continuing to the next drive. While the segment size on each drive is the same, the segments do not need to be in the same physical LBA range on each drive.
  - Sequential performance of the single volume can be as high as the aggregate performance of all 3 drives.
  - Random (4K) read/write performance across the entire volume LBA range may be as high as 3 drives.

- Vol2 is a concatenated volume with a capacity smaller than Drive 2. This demonstrates how segments from different types of volumes can be placed on the same physical drive.

- Vol3 is a RAID-0 volume with a width of 2.
  - Sequential performance of the single volume can be as high as the aggregate performance of 2 drives.
  - Random (4K) read/write performance across the entire volume LBA range may be as high as that of 2 drives.

- Volume capacity overhead is 0%, i.e., the volume size is equivalent to the aggregate segment allocation.

- RAID-0 volumes offer no protection against failures. If any drive or host that is part of a concatenated volume is unavailable, the volume will go offline. A failed drive results in permanent data loss.

#### RAID-0 Volumes, Multiple Targets

<div align="center"><img src="./ug-media/image6.png" style="width:6.5in;height:2.125in"
alt="Diagram of the layout of RAID-0 volumes across multiple targets." />

<em>RAID-0 volumes across multiple targets</em></div>

- Vol1 is a RAID-0 volume with a width of 6 allocated across 2 target nodes.
  - Volume LBAs are striped across repeating 128K stripes (32 × 4 KB blocks) on each drive before continuing to the next drive.
  - If the target node had a single 400GbE interface, 4 × PCIe Gen5 drives could saturate the interface. By spreading over 2 target nodes, sequential performance of the single volume can be as high as the aggregate performance of all 6 drives.
  - Random (4K) read/ write performance across the entire volume LBA range can be as high as 6 drives.

- Vol2 is a RAID-0 volume with width 4.

- Volume capacity overhead is 0%, i.e., the volume size is equivalent to the aggregate segment allocation.

### Mirrored RAID-1 Volumes

A mirrored RAID-1 volume consists of an exact copy or mirror of a set of data on two or more drives. Data is always separated drive-wise. It is optional whether to separate across nodes. Other separation constraints can be set using protection domains.

**<u>Note:</u>** It is planned to enable more than 2 copies of data in an upcoming version.

If the required volume capacity is larger than the drives or the segments of available space on the current drives, space will be allocated from additional drive pairs and concatenated logically in mirrored pairs serially, as in the description of concatenated volumes.

<div align="center"><img src="./ug-media/image7.png" style="width:6.5in;height:2.11111in"
alt="Diagram of mirrored volumes." />

<em>Mirrored RAID-1 volumes across multiple nodes</em></div>

- Vol1 is a RAID-1 volume allocated on 2 target nodes and 2 drives. Mirrored segments are allocated on different targets. The volume size fits within a single drive, so 2 drives are sufficient. Segment allocations are mirrored between Drives 1 and 4.
  - Sequential read performance can be as high as 2 drives as reads alternate between mirrors.
  - Sequential write performance is limited to the equivalent of a single drive as both mirrors are written to.
  - Random (4K) read performance can be as high as 2 drives.
  - Random (4K) write performance is limited to that of 1 drive.

- Vol2 is like Vol1. Its volume size is smaller than a single drive. Mirrored segments are the same size, but at different physical LBA ranges on Drive 1 and 5.

- Vol3 is a RAID-1 volume allocated on 2 target nodes and 4 drives. Its volume size exceeds that of a single drive. LBAs are contiguous and mirrored across Drive 2 and Drive 6, then continue mirrored between Drive 3 and 4. Mirrored segments are the same size, but they can be on different physical LBA ranges on different drives.
  - Sequential read IO performance may be as high as the aggregate of 2 drives regardless of how many drives make up the volume.
  - Sequential write performance is limited to a single drive.
  - Random (4K) read performance across the entire volume LBA range may be as high as that of 4 drives.
  - Random (4K) read performance on limited LBA ranges is typically limited to that of 2 drives.
  - Random (4K) write performance across the entire volume LBA range is limited to 2 drives.
  - Random (4K) write performance on limited LBA ranges is equivalent to that of 1 drive.

- Volume capacity overhead is 100%, i.e., the volume size is equivalent to 50% of the allocated physical space.

- These volumes can remain online if any one drive fails, or either target host fails. In this degraded mode, write performance is the same while read performance is cut in half.

### Striped & Mirrored RAID-10 Volumes

A striped and mirrored RAID-10 volume is simultaneously striped across multiple drives with a fixed stripe width and mirrored across drives, typically across different nodes. Separation is handled in the same manner as for RAID-1 volumes.

<div align="center"><img src="./ug-media/image8.png" style="width:6.5in;height:2.125in"
alt="A diagram of striped and mirrored RAID-10 volumes across multiple nodes." />

<em>Striped and mirrored RAID-10 volumes across multiple nodes</em></div>

- Vol1 is a RAID-10 volume with width 3, deployed on 2 targets. Virtual LBAs are striped across repeating 128K stripes (32 x 4 KB blocks) on mirrored drive pairs, then continuing to the next mirrored drive pair.
  - Segments on Drives 1, 2 and 3 are mirrored to Drive 6, 4 and 5.
  - Sequential and random (4K) write performance is as high as that of 3 drives as writes are synchronously written to mirrored segment pairs.
  - Sequential and random (4K) read performance is as high as that of 6 drives as reads are alternated between mirrors.

- Vol2 is a RAID-10 volume with width 2, also deployed across the same 2 targets.
  - Sequential and random (4K) write performance of the single volume is the up to the aggregate performance of 2 drives.
  - Sequential and random (4K) read performance of the single volume is up to the aggregate performance of 4 drives.

- Volume capacity overhead is 100%, as the volume size is equivalent to 50% of the allocated segments.

- Stripe height is 128K. This means that data is mapped round robin across 32 × 4K blocks (128K) to a pair of mirrored drive segments before continuing to the next drive pair.

- RAID-10 volumes can remain online if any one drive fails, or either target host fails. In degraded mode, write performance is the same while read performance is reduced due to the loss of the failed drives.

### Erasure-coded Volumes

An erasure-coded volume stores data and parity across multiple devices. It uses distributed erasure coding, a method of data protection in which data blocks and redundant parity blocks are distributed across a set of devices, forming one protected logical storage unit. Most often erasure coding is used to form a RAID-6 volume that provides N+2 data protection, meaning that one or two devices may fail, but the volume will still be intact. Another erasure-coded option is RAID-5 that provides N+1 data protection, i.e., with only a single parity block per stripe of N data blocks.

In all cases, data protection is achieved by spreading the data across distinct drives. It is optional whether to also spread the data across different nodes to facilitate providing a functional or available volume in lieu of a server failure. For RAID-6 (N+2) volumes, it is optional whether to maintain dual sparing of servers also. Other separation constraints can be set using protection domains.

If the required volume capacity is larger than the drives or the segments of available space on the current drives, space will be allocated from additional drive pairs and concatenated logically in mirrored pairs serially, as in the description of concatenated volumes.

The following examples are for RAID-6. RAID-5 is nearly identical.

#### RAID-6 (6+2) Logical Volumes with Single Node Failure Protection

<div align="center"><img src="./ug-media/image9.png" style="width:6.5in;height:1.91667in"
alt="A diagram of RAID-6 (6+2) volumes across multiple nodes." />

<em>RAID-6 (6+2) volumes across multiple nodes</em></div>

- Vol1 is a RAID-6, 6+2, dual-parity volume spread across 4 target nodes. Virtual LBAs are striped across 8 drives total. Each stripe consists of 6 data chunks and 2 parity chunks. The parity chunks are rotated over the segments to avoid imbalanced drive wear from read-modify-write operations.
  - Sequential and random (4K) read performance is as high as that of all 8 drives
  - Sequential and random (4K) write performance is heavily dependent on the CPUs, the drives, the initiator host, the network and the random operation size. Typical sequential write performance for a single volume of 6+2 or more often 8+2 reaches as much as 10-20 GB/s, most often limited by the drives sequential write performance capabilities.
  - Random (4K) write performance will be lower, as the operation is implemented using a read-modify-write operation to the relevant data block and to the parities. Typical performance is 100-150K writes from a single client and 100K-300K writes from multiple clients in aggregate, most often limited by the underlying drives random write performance capabilities.

- Volume capacity overhead for such a 6+2 erasure-coded volume is 33%, i.e., the volume size is equivalent to 75% of the allocated drive space. RAID-6 is variable with 8+2 being a popular choice with 25% overhead, or 80% useable drive capacity.

- Erasure-coded volumes protect against both drive and host failures. The minimum number of hosts needed to create a volume and survive a host failure is calculated by taking the number of data segments (6), plus parity segments (2) and dividing that result by the number of parity segments. For example, (6+2)/2 = 4 target nodes minimum. 8 target nodes would be required for dual node redundancy for such a volume.

- In this 6+2 example, any 1 host or any two drives can fail, and the volume will remain online.

#### RAID-6 (8+2) Logical Volumes with Dual Node Failure Protection

<div align="center"><img src="./ug-media/image10.png" style="width:6.5in;height:1.45833in"
alt="A diagram of RAID-6 (8+2) volumes across 10 nodes." />

<em>RAID-6 (8+2) volumes across 10 nodes</em></div>

- Vol1 is a RAID-6, 8+2, dual-parity volume allocated on 10 target nodes. Virtual LBAs are striped across the 10 drives. Each stripe consists of 8 data chunks and 2 parity chunks. The parity chunks are rotated over the segments to balance wear from read-modify-write operations.
  - Sequential and random (4K) read performance is as high as the aggregate of all 10 drives.
  - Sequential and random (4K) write performance are heavily dependent on the CPUs, the drives, the initiator host and the network.
  - See the previous section for more specific estimates.

- Volume capacity overhead for this example (8+2) is 25%, i.e., volume size is equivalent to 80% of the allocated segments.

- In this 8+2 example, any 2 hosts or any two drives can fail, and the volume will remain online.

#### Supported Erasure-coded Volume Combinations

Erasure-coded stripes can be as small as width 2 and up to 12 in total.

The 8+2 combination is widely used. However, even non-typical combinations such as 1+2 are viable.

| Parity Drives | Data Drives |
| :-----------: | :---------: |
|       1       |   1 – 11    |
|       2       |   1 - 10    |

The amount of data written per device before moving to the next device in the stripe is fixed at 4K. An 8+2 volume stripe has 32K of data and 8K of parity.

**<u>Note:</u>** Trim operations are ignored for erasure coded volumes.

#### Target Node Redundancy

Dual parity RAID-6 volumes can be configured with the following target redundancy levels:

- **N+2 Target Redundancy** - Only one volume segment per target node. With this redundancy level, the volume will remain available with up to two target failures, i.e., two drive segments can fail concurrently. In any case, data will remain durable with up to two permanent drive failures.

- **N+1 Target Redundancy** – Up to two volume segments per target node. With this redundancy level, the volume will remain available upon a single target failure. However, it will remain available even if two drives fail or are unavailable regardless of whether they are in the same target.

- **No Target Redundancy** – No restriction on volume segments per target node. With this redundancy level, the volume may become unavailable upon a single target failure.

RAID-1 and RAID-10 volumes protect against a single drive failure and a single target node failure.

RAID-6 volumes protect against both drive and host failures. The minimum number of target hosts needed to create a volume and survive a host failure is calculated by taking the number of data segments (D), plus parity segments (P) and dividing that result by the number of parity segments (D+P)/P. For example: (6+2)/2 = 4 target nodes minimum. 8 target nodes would be required for a RAID-6 6+2 volume to have dual node redundancy.

**Minimum Number of Target Nodes**

| Data + Parity Drives | Dual Target <br>Node Redundancy | Single Target <br>Node Redundancy | No Target <br>Redundancy |
| --- | --- | --- | --- |
| 6+2 | 8 targets | 4 targets | 1 target |
| 8+2 | 10 targets | 5 targets | 1 target |

## Access Modes

By default, NVMesh volumes are shared read-write volumes enabling multiple clients to attach to the same volume. However, for some use cases, it is useful to ensure the volume is being accessed only by a single client exclusively or is in a read-only mode. A volume can be accessed with a single access mode across the entire cluster and this is considered the volume’s current mode. If a volume is not attached to any client, it will not have a mode.

Trim operations are considered write operations in this context.

NVMesh supports the following access modes:

- Shared Read-Write – This is the default access mode. With this mode, all clients that have the volume attached can simultaneously read and write into it.

- Exclusive Read-Write – A client that has the volume attached can read and write into it with the assurance that no other client will access it as well. This is useful for using a local file system in a dynamic environment, such as a container-oriented one.

- Shared Read-Only – All clients that have the volume attached can only read from it, useful for accessing a local file system from multiple clients in read-only mode, which provides high performance, with the assurance that no client will write into it.

**<u>Note:</u>** The combination of exclusive read-write and shared read-only are useful for implementing the "WORM", write-once read-many, strategy for high performance semi-static data, such as a model that is sporadically updated. A single client writes the data using the exclusive read-write access mode into a local file system, such as XFS or EXT4, and once detached multiple clients can read the data using the shared read-only mode.

Volumes are created without any access mode set. It is defined once the first client has attached the volume or whenever a client attaches, and the volume is without an access mode. Once all clients have detached, the volume reverts to having no access mode set.

When a client attempts to attach to a volume that has a mode set that is different from the requested one or there is another client in exclusive read-write mode, the attachment will fail. In this case, preemption is needed to force a mode change. For clarity, a preemption is needed for one client to forcefully gain access in exclusive read-write mode when another client is already attached in this mode.

If a client is attached to a volume and another client preempts it to alter the access mode but does not request that other clients be detached, the first client will remain attached. All outstanding and subsequent I/O will fail upon the preemption. To restore IO access, it will be necessary to detach and reattach the volume. A preempting client can set a flag in its attach command that mandates that all other clients are detached.

## Zones

With a standard NVMesh installation, drives are in a single pool. This limits the total number of targets and volumes supported. Zones are useful to increase the limits. Once this optional functionality has been turned on, a target must be assigned to a zone before volumes will make use of the drive space on the target. Zones’ IDs are a user-chosen positive integer.

Zoning functionality is turned on and off via general settings.

With zoning on, a zone column is added in the targets table in the targets section of the GUI. If zoning is turned on after volumes have been assigned to a target, but prior to turning on zoning, the target will automatically be assigned to zone 1. It is not permitted to re-assign a target to a different zone after it has been assigned. The only way to do this in practice is to remove the target by evicting or relocating its drives, deleting it from the management and then restarting it. This will make it appear as a new zone-assignable target. The target will initially appear to not be in any zone. Choose one or more such targets and click approve to assign them a zone by id.

Volumes comprise one or more chunks of logical block addresses. The chunk is internally comprised of one or more protection-RAIDs. Each protection-RAID implements the volume type’s protection logic by spreading the stored data onto drives from one or more targets. Protection-RAIDs must be confined to a single zone. In theory, a large volume can be comprised of several chunks and protection-RAIDs spread across multiple zones. However, the current implementation prevents cross-zone volumes. This limitation also applies when the system needs spare space to rebuild a volume.

**<u>Note:</u>** When using zones and relocating a drive, it is imperative to move it to a different target in the same zone.

Zoning should not be turned off when there are volumes and targets associated with zones already.

The recommended order of operations when zoning is required is as follows:

1. Start the management.

1. Turn on zoning.

1. Start targets.

1. Associate the targets with zones.

## Minimal Configurations

### Single Node System

If high availability is not required, it is possible to install the NVMesh client, target and management on a single server. The administrator can create concatenated, RAID-0 and protected volumes without target redundancy.

### Minimal Cluster for Mirrored Volumes, RAID-1/10

NVMesh places mirrored data on separate drives and targets, so the minimum configuration requires at least 2 targets. Volume status is determined by the TOMA leader. Electing a leader requires at least 3 targets to avoid split brain issues.

Hence, for a RAID-1/10 volume with target redundancy, the minimal number of targets is 3. Therefore, the minimal number of drives in the system is also 3, one per target. If target redundancy is not required, a single target with 2 drives is sufficient.

Management can be deployed on a single host. For redundancy, management servers should be deployed on more than one node. Management stores its data in a MongoDB database. To provide redundancy for the database, typically 3 nodes are the minimum.

In summary, an NVMesh cluster with redundancy comprises at least 3 hosts.

<div align="center"><img src="./ug-media/image11.png" style="width:6.5in;height:2.52778in"
alt="A diagram of a minimal redundant NVMesh cluster." />

<em>Minimal Redundant NVMesh Cluster</em>

<img src="./ug-media/image12.png" style="width:2.79688in;height:1.40752in"
alt="Legend for previous diagram." />

<em>NVMesh Diagram Legend</em></div>

### Minimal Recommended Cluster for RAID-1/10

The recommended minimal NVMesh cluster has 4 servers with NVMe drives to enable creation of mirrored volumes in balanced host pairs.

<div align="center"><img src="./ug-media/image13.png" style="width:6.5in;height:1.08333in"
alt="A diagram of a minimal recommended NVMesh cluster." />

<em>Minimal recommended NVMesh cluster</em></div>

### Minimal Recommended Cluster for RAID-6

For a redundant NVMesh system with erasure-coded RAID-6 volumes, at least 3 servers are required for management’s MongoDB database, which can also run the management itself. The servers may also be targets, i.e., run the NVMesh target stack.

The minimal recommended number of targets is dependent upon the RAID-6 parameters chosen and the level of redundancy required.

For dual server redundancy, which is recommended for availability, the number of servers should be at least the total number of data and parity blocks per stripe for the RAID-6 volumes used.

If dual-drive redundancy and single server redundancy is sufficient, half the number of servers (rounded up) is sufficient, but not less than 3\.

Foregoing server redundancy is not recommended.

## Security

### Encrypted Volumes

Volume encryption is currently not intrinsic to NVMesh. It is on the roadmap and should be added in an upcoming version. In the meantime, for encrypting volume contents, use Linux facilities such as `cryptsetup` and `/etc/crypttab` to set up encryption on volumes as a layer on top of NVMesh and if needed automatically load the encrypted volume into the client node’s block device space.

### Securing Volume Attachments

NVMesh facilitates securing volume attachment, i.e., ensuring that only validated clients can attached to a specific volume.

**<u>Note:</u>** The application of this functionality to a volume will be determined by whether it has associated VSGs.

Key pairs are managed from the Key Pairs sub-section of the Settings section in the GUI. To transfer a key to a client, download it from the management, and copy the file generated to `/etc/nvmesh/keys` on the clients for which volume access is permitted, for all volumes associated with this key pair.

To associate volumes with a key pair, generate a volume security group (VSG) in the Volume Security Groups sub-section of the Settings section and choose the key pairs for it. Then in the volume definition, associate the volume with the relevant volume security groups.

**<u>Note:</u>** The default built-in volume provisioning groups (VPGs) do not refer to any VSGs. To use VPGs with secure volume attachment, generate new ones that refer to the appropriate VSGs.

### Mutual TLS

For secure deployments, especially in 3<sup>rd</sup> party environments, mutual TLS (mTLS) can be used to validate endpoints. Mutual TLS, which can be defined as mandatory, uses security certificates for identifying peer components. mTLS is used to verify the following interactions by putting the appropriate certificates in `/etc/nvmesh/tls` unless stated otherwise:

1. For Kafka-based interactions, the following certificate types are required:
   1. Kafka Server certificate, which is stored in Kafka’s certificate key store instead of the path above.

   1. Certificate to identify a management server to Kafka.

   1. Certificate to identify an MCS instance to Kafka. This serves all elements communicating via the MCS, i.e., clients, targets and the nvmeshagent.

   1. Certificate to identify a TOMA to Kafka. <br> **<u>Note:</u>** the TOMA’s zone is embedded in the certificate.

1. RESTful API and https-based GUI access:
   1. A certificate for regular client-side access.

   1. A certificate for admin client-side access.

   1. A certificate for identification of the management server-side.

   1. **<u>Note:</u>** For browser-based access, the certificate will need to be loaded into the browser’s certificate store.

   1. **<u>Note:</u>** The same certificate can be used for both a browser and the CLI, however care should be taken as operations will appear as coming from the same user / entity in the audit log.

1. Management to Management:
   1. Hostname-based certificate to enable management high availability.

1. Mongo access:
   1. Hostname-based certificate for Mongo itself, stored in Mongo’s certificate path.

   1. Hostname-based certificate for management to access Mongo, based on the Mongo client-side configuration.

### Audit Logs

Audit logs for all management operations can be found in the management logs, with the "isAudit" flag set to true. Audit logs include the user that initiated the operation, a unique id for the request, and a unique id for the result.

### Planned security features

The following features are on the roadmap for an upcoming NVMesh release.

- Native encrypted volumes integrated into NVMesh.

- ABAC – Attribute based access control critical for multi-tenancy, which can be used to ensure that a tenant can only see NVMesh resources that it has been associated with, e.g., when listing volumes.

## CRC Check

CRC verification is an option volume feature.

CRC verification, also known as the "CRC Check" feature, adds a CRC signature to every block. The CRC signature is generated as part of write operations and is verified on read operations. If a CRC signature does not match the data in the block during a read operation, the IO will be treated in the same way as a hardware bad sector and the data will be restored through the standard data protection mechanisms. For non-protected volumes, this feature is largely irrelevant.

The performance impact of enabling the feature is as follows:

- Mirrored RAID-1/10 volumes: The relative impact will be more significant for large IOs.

- Erasure-coded RAID-6 volumes: There is no overhead for write operations. For read operations, the relative impact will be more significant for large IOs.

In the current implementation, overall performance for CRC verification and generation is dependent on the speed of the kernel’s CRC calculation. For most cases, this is directly related to the AVX capabilities of the processors running on the client nodes. For a single 4k I/O, the latency overhead is typically around one microsecond. For high throughput, there may be a more significant effect that is hardware dependent. For example, on a system with dual Xeon Silver CPUs of 16 cores each, the CRC check reduced performance from 40 GB/s to 30 GB/s.

**<u>Note:</u>** It is recommended to test the overhead on specific hardware for precise overhead estimations.

**<u>Note:</u>** The CRC Check can be enabled only on volumes located on drives with Metadata Support, i.e., with support for the 4k+8 block format.

## 512-byte Block Size Emulation

**<u>Note:</u>** This functionality is currently **<u>disabled</u>**.

Permanent deletion has not been decided upon. Therefore, this section has not been removed yet.

NVMesh provides an option for attaching volumes and setting the kernel block size to 512b. 512b operation is achieved through emulation. 4k blocks are read and written to the drives. To ensure data consistency, read-modify-write operations required for operations on part of a 4k block are done under lock. Therefore, this functionality is available only for volumes with data-protection, i.e., RAID-1/10/60 volume types.

To attach a volume with 512b block emulation, add the `--sub-block-io-allowed` flag to the attach command.

# NVMesh Software Installation

These instructions assume familiarity on the part of the administrator with tasks such as installing packages, making changes to configuration files and general Linux systems administration knowledge. You will be guided through a sample installation, creation of a logical volume and attachment of that volume to a client.

## Installation Overview

1. Prepare for installation
   1. Validate the hardware and software requirements as detailed in this user guide and the NVMesh Interoperability Matrix.

   1. For RDMA setups, install Mellanox OFED software if required.

   1. Review and configure the systems, for instance repository definitions, for the appropriate software delivery methods, see [Software Delivery](#software-delivery).

1. Prepare security certificates
   1. See [Prepare Security Certificates (Optional)](#prepare-security-certificates-optional).

   1. This step can be skipped for non-production or non-secure environments.

1. Set up management servers:
   1. Install and set up MongoDB. For highly availability, configure a single multi-replica MongoDB database across multiple nodes.

   1. Install NodeJS.

   1. Install Kafka.

   1. Install the `nvmesh-management` and `nvmesh-interopdb` packages.

   1. Configure specific management options in `/etc/nvmesh/management.js.conf`.

   1. Start the `nvmeshmgr` service.

   1. (Optional) Set up a management HA cluster, i.e., set up multiple management servers. For more information refer to [Management Scalability](#management-scalability).

1. Set up the clients and targets:
   1. For clients, install the `nvmesh-base`, `nvmesh-client` and `nvmesh-upgrader` packages.

   2. For targets, install the `nvmesh-target` package also.

   3. Define the management servers’ addresses (Kafka and REST) and the NICs to use in `/etc/nvmesh/nvmesh.conf`.
      1. Optionally, configure TCP/IP Support.

   4. On targets:
      1. As needed, exclude any NVMe drives that should not be used by NVMesh, see [Exclude Drives](#exclude-drives).

      2. Enable and start the `nvmeshtarget` service, which will also start the client.

   5. On clients that are not targets:
      1. Start the `nvmeshclient` service.

1. Define volumes:
   1. Log in via a web browser.

   2. If this is the first login to the Management, perform the following 2 steps:
      1. Add a new administrative user.

      2. Log out, then log in as the new administrative user.

   3. Use the Drives page to format the drives before use by NVMesh.

   4. Go to the Volumes page, and then press "+". Create a volume.

   5. Go to the Clients page, choose a client and use the Attach/Detach button to attach the volume to the client. On the client it will appear as a block device under the /dev/nvmesh path.

   6. The steps above can be done using the NVMesh CLI or via REST commands.

## Prerequisites

These prerequisite categories for installing NVMesh are:

- Hardware Dependencies

- Software Dependencies

- Network Connectivity

- Firewalls and Specific Ports

- NTP Time Synchronization

### Hardware Requirements

It is important to validate the hardware requirements as detailed in this user guide.

### Memory Requirements

The following provides a gross estimation of the memory usage for clients and targets. Precise measurements may differ for specific use cases, and it is recommended to validate the numbers in practice for known use cases.

#### Clients

Clients use a negligible amount of memory as data operations are synchronous and do not utilize a cache. They reserve 256 MB for internal tracing.

#### Targets

Targets use memory proportional to the size of the NVMe devices being served. The amount of memory required also varies by the number of IO queues (IOQs) supported by the NVMe devices as well as the number of attached clients.

The number of IOQs supported by a drive can be found in its smart file. For each device managed by a target, there is a file named `/proc/nvmeibs/smart<N>`. This file has a key=value format and includes the device’s serial number and the number of NVMe queues by type.

Following is an example:

```
[root@nvme1076 18:03:32 ~]# grep -e Serial -e Queue /proc/nvmeibs/smart[0-2]
/proc/nvmeibs/smart0:Serial Number=BATS91040FA71P0FGN
/proc/nvmeibs/smart0:Submission Queues=128
/proc/nvmeibs/smart0:Completion Queues=128
/proc/nvmeibs/smart1:Serial Number=S4C9NA0M400300
/proc/nvmeibs/smart1:Submission Queues=128
/proc/nvmeibs/smart1:Completion Queues=128
/proc/nvmeibs/smart2:Serial Number=S4C9NF0M500283
/proc/nvmeibs/smart2:Submission Queues=128
/proc/nvmeibs/smart2:Completion Queues=128
```

The target reserves 4 MB per node and 24 MB per core, in addition to the client reservation, for internal tracing.

#### Per TB of storage

64 MB of memory is consumed per 1 TB of drive space.

#### Per drive per client

14.13 MB of memory is consumed for every drive per client and channel.

The number of channels a client has is determined by the network connectivity and the settings for the `nr_max_channels_per_disk`, `nr_max_channels_per_path`, and `nr_max_channels_per_path_tcp` module params for nvmeibc, the client kernel module, see [Module Parameters](#module-parameters).

On the target, the default value for the target kernel module param `nvmeibs_nordda_io_req_num` is 96, which translates into ~14 MB. If this value is altered, the required memory will be ~150.7 KB per entry.

#### Per Drive

132 KB of memory is consumed per NVMe IOQ. For example, for a drive with 128 IOQs, 16.5 MB is consumed.

Another 5 MB is used per drive for the journaling mechanism.

#### Per Target

For each target, 64K buffers of 4 KB are reserved for all kernel tracing by default, i.e., 256 MB.

This can be tuned by writing a new configuration into the module parameter "config" for nvmeib_common_public.

The targets also run TOMA, which consumes memory. The amount of memory is dependent upon more than just the local resources and clients, dependent on the amount of volume metadata. In general, this is relatively insignificant and can be ignored in most cases. The TOMA does always reserve 12 MB for tracing.

So, for tracing a total of 268 MB is used.

#### Per NIC

Shared receive queues are allocated per NIC on the targets. The per-NIC SRQ consumes 406.5 MB.

#### Examples

For the two example configurations, drives have a 3.2 TB NVMe capacity with 128 queues.

- 3.2 TB = 3.2x 64 MB = 204.8 MB

- 128 IOQs = 128x 132 KB = 16.5 MB + 5 MB for journaling = 21.5 MB per drive

- 256 MB for Tracing + 406.5 for 1 NIC = 662.5 MB

Connectivity is over RDMA with default module params, i.e., 4 channels x 96 x 150.7 KB = 56.51 MB per client.

All numbers are in MB, unless stated otherwise.

| Drives | Clients | NICs | Capacity-based | Client-based | Drive-based | NIC & Target | Total |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| 1 | 4 | 1 | 204.8 | 226.0 | 21.5 | 674.5 | 1127 MB |
| 24 | 96 | 2 | 4,915 | 130,194 | 516 | 1,081 | ~133.5 GB |

#### Full Calculator

For a more elaborate calculation, see the [NVMesh Memory Calculator](https://docs.google.com/spreadsheets/d/1ZidJd1Qwq1ZLT9YkA19Q9BzLZZfJDMxfGUfCN5FRE9s/edit?usp=sharing).

### NVMe Device Requirements

#### Supported NVMe Devices

Drives not explicitly listed in the [NVMesh 3.4.0 Interoperability Matrix - Drive Interoperability](https://github.com/NVIDIA/nvmesh-documentation/blob/3.4.0/NVMesh%203.4.0%20Interoperability%20Matrix.md#drive-interoperability) have not been tested and may not function correctly. A new model that is a newer version in a family of drives that have been tested is highly likely to function properly.

#### Drive Sector Size

For RAID-6 and for CRC functionality, NVMe drives must support end-to-end data protection, sometimes referred to as having "long blocks" support or as supporting advanced metadata sector formats. This block or sector format has 4096 (4k) data bytes and 8 metadata bytes for a total of a 4104-byte sector size. This is also referred to as a 4K+8B sector format. The NVMe specification supports two ways of accessing such blocks, as inline metadata or via separate metadata pointers. NVMesh requires the latter, i.e., via separate metadata pointers.

NVMe drives must be formatted before they can be used by NVMesh. NVMe drives that support 4K+8B sectors will be formatted with that sector size. If the drives do not support 4K+8B, they will be formatted with 4K sectors. Finally, if they do not support 4K+8B or 4K sectors, they will be formatted with 512B sectors.

Drives formatted with the 4K+8B format can be used for any type of volume, but this is mandatory for RAID-6 volumes and for CRC functionality. Drives formatted with 4K or 512B sector sizes will not have RAID-6 volumes or volumes for which CRC is requested allocated on them. Such drives can be put into drive classes, volumes and volume provisioning groups like 4K+8B drives.

See [Drive Format Types](#drive-format-types) for instructions for verifying the supported sector formats on an NVMe drive.

### Networking / NIC Requirements

NVMesh supports TCP, RoCEv2 and InfiniBand RDMA networking setups.

When configuring a non-RDMA setup, any ethernet NIC can be used. Ethernet NICs with speeds of 10 Gb/s or faster are recommended and practically mandatory. See [Configure TCP/IP Support](#configure-tcpip-support) for more information.

Running NVMesh over high-latency WAN-like links is not recommended.

#### RDMA Configurations

For RDMA, NVIDIA NICs are mandatory.

No special settings are required beyond standard network-side settings required for efficient use of RDMA.

### Software Requirements

#### SELinux

SELinux may prevent NVMesh kernel modules from loading successfully. Either disable SELinux or if SELinux is required, create rule that allows kernel modules that reside in the NVMesh directories to be inserted.

#### Management Servers

NVMesh management servers rely on MongoDB and NodeJS.

MongoDB provides a document-based database for storing persistent storage system metadata. MongoDB access is required from the management server but does not have to be installed on the same node.

NodeJS is the web and application serving platform.

Kafka is used for communication between management and all other components.

The following non-NVMesh packages are required for management servers, i.e., for the `nvmesh-management` package:

- mongodb-mongosh v2.1 – 7.0

- mongodb-org-tools v5.0 – 7.0

- nodejs v1:17.0 – 2:18.0

This version of NVMesh is compatible with Mongo versions 5.0 – 7.0.

This version of NVMesh is compatible with NodeJS version 18.

**<u>Note:</u>** This is not always captured correctly by the package constraints due to the additional prefix in the package version to the NodeJS version.

**<u>Note:</u>** Documentation may lay actual product constraints.

#### Clients and Targets

##### RHEL and Rocky 8.x (rpm)

Non-NVMesh package dependencies for Red Hat Enterprise Linux (RHEL) 8.x and compatible distributions like Rocky Linux are presented in the following table.

| Package | Dependencies |
| --- | --- |
| nvmesh-base | ethtool <br> smartmontools <br> util-linux |
| nvmesh-client | nvmesh-base <br> xz |
| nvmesh-target | nvmesh-client + <br> 2.6.0 >= librdkafka >= 1.6.1, Recommended: > 2.1.2 <br> pciutils |

For RDMA environments, on service startup, the following packages are required:

- libibverbs
- librdmacm
- libibcm
- libibmad
- libibumad

##### Rocky Linux 10.x (rpm)

Non-NVMesh package dependencies for Rocky Linux 10.x are presented in the following table.

**Note:** On Rocky Linux 10.x, librdkafka is not available as a system RPM. The confluent-kafka Python wheel bundles its own librdkafka 2.8.0, so no system librdkafka package is required for the target.

| Package | Dependencies |
| --- | --- |
| nvmesh-base | ethtool <br> smartmontools <br> util-linux |
| nvmesh-client | nvmesh-base <br> xz |
| nvmesh-target | nvmesh-client + <br> pciutils |

For RDMA environments, on service startup, the following packages are required:

- libibverbs
- librdmacm
- libibmad
- libibumad

##### Ubuntu 22.04 (deb)

Non-NVMesh package dependencies for the Ubuntu 22.04 distribution are presented in the following table:

| Package | Dependencies |
| --- | --- |
| nvmesh-base | ethtool <br> smartmontools <br> util-linux |
| nvmesh-client | nvmesh-base <br> xz-utils |
| nvmesh-target | nvmesh-client <br> 2.6.0 >= librdkafka >= 1.6.1, Recommended: > 2.1.2 <br> pciutils |

In addition, for RDMA environments, on service startup, the following are required:

- libibverbs1
- librdmacm1
- libibcm1
- libibmad5
- libibumad3
- ibverbs-providers

#### Operating System

Supported operating system versions and the associated kernels can be found in the [NVMesh 3.4.0 Interoperability Matrix - Operating System Interoperability](https://github.com/NVIDIA/nvmesh-documentation/blob/3.4.0/NVMesh%203.4.0%20Interoperability%20Matrix.md#operating-system-interoperability).

#### NVIDIA (Mellanox) OFED

For some environments, it is desired to install the NVIDIA (Mellanox) OFED software. OFED is generally supported, although adaptations may need to be made for newer versions.

It is not imperative to used OFED.

### Network Connectivity

#### Overview

NVMesh benefits from a network supporting RDMA, i.e. RoCEv2 or InfiniBand.

#### RDMA Networking

When RDMA connectivity is available, NVMesh’s data path can leverage it. RDMA requires either Ethernet NICs supporting RoCEv2 or InfiniBand adapters. Communication between management and the clients and targets, including TOMA, is done using Kafka, i.e., RDMA is not employed.

#### RoCEv2

For Ethernet RoCEv2, reliable UDP/IP connectivity is required to establish connectivity between clients and targets. To this extent, it may be necessary to enable some form of flow control in the switching infrastructure with methods such as Global Pause or Priority Flow Control (PFC) or to deploy Explicit Congestion Notification (ECN) and Lossy RoCE configurations or employ Zero-touch-RoCE or combinations of these techniques. This is covered in the RoCEv2 section. Consulting with NVIDIA (Mellanox) networking experts is recommended.

#### InfiniBand

For InfiniBand network fabrics, there is typically little required configuration. The only considerations for NVMesh are potentially limiting which interfaces are to be used by NVMesh and the rate at which it may query the subnet manager. In a pure InfiniBand environment or for performance considerations, it may be necessary or recommended to configure IPoIB as a communication means between the clients and targets to management servers, which is done over TCP/IP.

For RoCEv2 or InfiniBand, control path and data path communication may share a network connection but may also use separate networks if desired.

**<u>Note:</u>** When employing separate (multi-rail) InfiniBand networks, each should have different subnet designations.

#### Non-RDMA Networking – TCP/IP

NVMesh’s data path can be implemented on emulated RDMA, specifically SoftiWarp (SIW). This is supported with standard Ethernet NICs. SIW uses TCP/IP underneath.

**<u>Note 1:</u>** IPv6 is not supported in non-RDMA, standard TCP/IP configurations.

**<u>Note 2:</u>** Mixed clusters with NVMesh clients and targets configured with both Ethernet, RoCEv2 or TCP, and InfiniBand are not currently supported. For Ethernet environments, it is possible to have some inter-node communication leverage RDMA with others resorting to TCP.

### Firewalls and Specific Ports

In general, it is simplest to install NVMesh with Linux firewalls disabled. However, it is ill-advised from a security perspective.

| Domain | Source | Target | Source Port | Destination Port | Protocol |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Mongo | Management | Mongo<sup>1</sup> | \* | 27017 | TCP |
| Kafka | All NVMesh nodes | Kafka Broker<sup>1</sup> | \* | <nobr>9092-9093<sup>2</sup></nobr> | TCP |
| GUI | \* | Management | \* | 40003 | TCP |
| REST | \* | Management | \* | 40013 | TCP |
| Data Path | Clients | Targets | \* | 4791 | UDP (RoCE) |
| Data Path | Clients | Targets | NR | NR | InfiniBand |
| Data Path | Clients | Targets | <nobr>7915-7930<sup>4</sup></nobr> <br> 8915<sup>5</sup> | <nobr>7915-7930<sup>4</sup></nobr> <br>8915<sup>5</sup> | TCP (SIW) |
| TOMA | Targets | Targets | \* | 4791 | UDP (RoCE) |
| TOMA | Targets | Targets | NR | NR | InfiniBand |
| TOMA | Targets | Targets | \* | 4100 | UDP<sup>6</sup> |

**<u>Notes:</u>**

1 – Mongo and Kafka are usually run on the same servers as Management, but this is not mandatory.

2 – Kafka also listens on an additional ephemeral port. These ports are configurable. Follow Kafka documentation.

3 – Configurable, see [Management Configuration](#management-configuration), config.port and config.webSocketServerPort.

4 – Configurable via the nvmeib_common/tcp_base_port_id and nvmeib_common/tcp_num_ports module parameters.

5 – Used for path discovery, configuration via the nvmeib_common/iwarp_find_path_sock_port module parameter.

6 – When RDMA is not used, i.e., configuration is set to "TCP-ONLY", then TOMA uses port 4100 for communication.

#### Communication Matrix

##### Management Servers

TCP ports 4000-4001 must be open by default on management servers. This is configurable, see [Management Configuration](#management-configuration). These ports are used for administration and provisioning communication for GUI access (port 4000) and for the REST API (port 4001). These ports should be made available via the firewall to any endpoint that should have access to these functions.

For systems using iptables, port 4000 can be enabled with this command (as root) for instance:

```bash
iptables -I INPUT 1 -m state --state NEW -m tcp -p tcp --dport 4000 -j ACCEPT -m comment --comment NVMesh-Management
```

For systems using firewalld, port 4000 can be enabled with this command (as root) for instance:

```bash
firewall-cmd --permanent --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 4000 -j ACCEPT -m comment --comment NVMesh-Management
```

Replace the "4000" in the examples above with "4001" or any other port numbers that need to be opened. These ports only need to be opened on systems running management.

When deploying management servers with load balancers or via K8s, additional networking and firewall configuration changes may be required.

##### Clients and Targets

For targets using RoCE for the data path, firewalls should be configured to allow traffic to UDP port 4791, as this is the reserved RoCEv2 port number.

For targets using SIW for the data path, UDP port 4100 and a range of TCP ports starting from 7915 should be opened. By default, the number of ports is 16. It can be defined at startup time using the module parameter tcp_num_ports for the nvmeib_common module.

##### Inter-TOMA Communication

TOMAs are part of the targets. In TCP-only setups, they will communicate using destination port 4100 and UDP and may communicate in both directions.

For RoCE setups, they will use UDP and port 4791 as a destination like other RDMA traffic to the node.

### Mongo Access

Management must have access to Mongo. Access should be enabled from management nodes to all Mongo replicas as any may be the current primary.

Mongo listens on port 27017 by default. This is configurable.

Most often, Mongo is run on the management nodes, but this may not be the case, for instance in K8s setups.

### Kafka Access

All NVMesh components must have access to Kafka brokers.

Kafka with kraft is used, which listens on ports 9092 and 9093 by default. This is configurable.

Most often, Kafka brokers are run on the management nodes, but this may not be the case, for instance in K8s setups.

### NTP Time Synchronization

Proper time synchronization between NVMesh cluster nodes is important for logs accuracy. It is highly recommended to configure NTP time synchronization on all servers running clients, targets and management servers.

**<u>Note:</u>** Consult NTP documentation for time synchronization best practices. In general, it is recommended to configure a minimum of three time servers as primary time servers and use a service like chronyd.

## Network Configuration

For explicit instructions for RDMA network setup, use general NVIDIA RDMA setup documentation.

In general, jumbo frames are recommended. For RoCE, an MTU of 4200 is often recommended.

### RoCEv2 Multi-pathing

NVMesh implements its own mechanisms for multi-pathing, using multiple active QPs, across different paths, and continuous path discovery to maintain awareness of network errors and ensure data delivery. Bonding and LAG configurations are currently not supported by NVMesh. DOCA virtualization facilities can also be used.

It is recommended to connect NVMesh servers to two or more switches for high availability, whether using single or multi rail configurations.

Multiple ports can be on the same subnet or separated. If multiple ports share a subnet, it is important to avoid ARP issues by setting arp_ignore to 1 and arp_announce to 2 for these interfaces in the kernel. One way of doing this would be to embed these lines into a file in `/etc/sysctl.d`, for instance `/etc/sysctl.d/90-lan-multipath.conf`, which would set this definition for all NICs in the server.

Conversely, when ports are on separate subnets, something like the following is recommended:

```
[root@nvmesh-server root]# cat /etc/sysctl.d/85-ipv4-arp.conf
net.ipv4.conf.all.rp_filter=2
net.ipv4.conf.default.rp_filter=2
net.ipv4.conf.all.arp_ignore=2
net.ipv4.conf.default.arp_ignore=2
net.ipv4.conf.all.arp_announce=1
net.ipv4.conf.default.arp_announce=1
net.ipv4.conf.all.arp_notify=1
```

### SoftiWarp / TCP-IP

To enable TCP/IP connectivity, set the following in `/etc/nvmesh/nvmesh.conf`: `TCP_ENABLED="Yes"`.

To disable RDMA and use only TCP/IP, set `TCP_ONLY="Yes"` in the same conf file.

Targets will listen for incoming connections for both RDMA and TCP for NICs for which RoCE and TCP has been enabled, and the TCP_ONLY setting is not in place. Clients will prefer RoCE and will not fallback to TCP.

**<u>Note:</u>** IPv6 is not supported in this version.

### TCP performance tuning

NVMesh ships with **conservative defaults** that prioritize broad compatibility and stable behavior across many hardware and workload combinations. Those defaults are **not tuned for a specific topology** (for example, a particular core count, NUMA layout, or number of NICs). For SoftiWarp (SIW) over TCP, peak throughput and latency usually require aligning **network receive steering**, **SoftiWarp transmit threading**, and **NVMesh completion-queue vector selection** with your CPUs and NIC placement.

The following areas are the main levers. Validate changes with your workload; incorrect affinity can reduce performance or increase latency.

#### `nvmesh.conf` and the TCP affinity script

When TCP/SIW is enabled, NVMesh can run `nvmesh_set_tcp_affinity` at service start to configure receive-side steering (RSS-related queues, RPS, optional flow steering, and IRQ affinity) for each SIW interface. This is controlled globally by **`TCP_SET_AFFINITY`**: set to `No` or `False` to skip the script entirely (for example, if you manage IRQ/RPS manually).

When affinity setup is enabled, optional variables in `/etc/nvmesh/nvmesh.conf` are passed through to `nvmesh_set_tcp_affinity` as follows:

| Variable | Role |
|----------|------|
| **`TCP_NUM_RX_QUEUE`** | Target number of RX queues (`--queues`). |
| **`TCP_SET_NUM_RX_QUEUE`** | If `Yes` or `True`, sets the queue count via ethtool when needed (`--setrxq`). |
| **`TCP_NUM_CHANNELS`** | Number of NRCH channels to use (`--channels`). |
| **`TCP_RX_CPU_AFFINITY_DOMAIN`** | CPU affinity domain for mapping queues to CPUs (`--domain`), for example `pernuma`, `persocket`, `fullspread`, `pernuma_thread0`, `persocket_thread0`, or an explicit comma-separated CPU list. |
| **`TCP_RX_SET_IRQ_AFFINITY`** | Set to `No` or `False` to avoid programming IRQ affinity from the script (`--noirq`). |
| **`TCP_FLOW_STEER`** | Flow-steering mode (`--flowsteer`): `rfs`, `ntuple`, or `none`. See [Flow steering, RSS, and multi-port listeners](#flow-steering-rss-and-multi-port-listeners). |
| **`TCP_RPS_MODE`** | RPS mode (`--rpsmode`): for example `alldomain`, `irq_range_wrap`, `irq_neighbour`, `off`, or `donttouch`. |

The script also supplies the SIW base port (`--port`) so steering matches NVMesh’s listener layout. See the script’s help output on a node (`nvmesh_set_tcp_affinity --help`) for the full set of options and semantics.

Unless **`TCP_RX_SET_IRQ_AFFINITY`** is set to **`No`** or **`False`**, the affinity script **programs IRQ affinities** for the relevant NIC queues. **Disable system IRQ balancing** for those interfaces (for example turn off the **`irqbalance`** service, and any similar daemon that periodically reapplies IRQ masks), so it does **not override** NVMesh’s settings after startup. If you rely on **`irqbalance`** or vendor IRQ tools instead, use **`TCP_RX_SET_IRQ_AFFINITY="No"`** and manage affinity consistently in one place. For general background on **`irqbalance`** versus static affinity, see [CPU Interrupt Affinity and IRQ Balancing](#cpu-interrupt-affinity-and-irq-balancing).

#### Flow steering, RSS, and multi-port listeners

The **`TCP_FLOW_STEER`** variable selects among these modes. If it is **not** set in `nvmesh.conf`, the affinity script uses its built-in default, which is **`none`**.

- **`none`** — NVMesh does **not** install static flow-steering rules. The NIC chooses the receive queue from the **RSS indirection table** and the **RSS hash** of each packet (when RSS is enabled). To improve spreading across queues in this mode, the **target opens multiple secondary SIW TCP listeners** on consecutive ports starting at the configured base port. With the **`tcp_num_ports`** common-module parameter at **`0`** (the default), the **listener count is the number of online CPUs**. That gives **different TCP flows (different destination ports)** distinct listeners so they **hash differently under RSS**, improving parallelism. For a fixed listener count, set **`tcp_num_ports`** to a non-zero value; see [Module Parameters](#module-parameters).

- **`ntuple`** — The script enables **ntuple** filtering and installs **static ethtool rules** that map each SIW listener port to a **specific RX queue**. That lets the **NVMesh client** attach each I/O channel to a **chosen listener port** so traffic for that channel is steered to the **intended RX queue**, which can yield more predictable and better spreading than RSS alone. This mode is **not** the default because **not all NICs or drivers support ntuple filtering**, and hardware tables enforce a **limited number of rules**—enable it only after confirming support on your adapter.

- **`rfs`** — Uses **accelerated RFS** (where the driver supports it): receive processing is steered so that traffic tends to be handled on the **same CPU as related socket and SoftiWarp TX work**, which reduces cross-CPU handoffs and can **lower latency**. Requirements differ from `ntuple`. See [Latency versus throughput](#latency-versus-throughput) for how this fits with client-side module settings, and the script comments and help output on the node for details.

#### Latency versus throughput

TCP tuning for NVMesh often trades **aggregate bandwidth** against **consistent low latency**. **Throughput** is usually helped by **spreading** work—many RX queues, RSS or static **ntuple** rules, and a wide mapping of channels to CPUs—so the NIC and host process more packets in parallel. **Latency** is usually helped by **locality**: keeping **softirq receive handling**, the **TCP socket**, and **NVMesh’s IO and locking path** on the **same or nearby CPUs** so work does not bounce across cores or NUMA nodes.

**Latency-oriented recommendations (SoftiWarp / TCP clients)**

1. **`TCP_FLOW_STEER="rfs"`** in `/etc/nvmesh/nvmesh.conf` — When the affinity script runs with **`rfs`**, it configures receive steering so that **RFS** can direct flows toward the CPUs where the application and SoftiWarp transmit context run, which helps **keep the socket aligned with the IO CPU** instead of spreading receives arbitrarily across RSS queues. This depends on driver support for RFS and related interfaces; see [Flow steering, RSS, and multi-port listeners](#flow-steering-rss-and-multi-port-listeners).

2. **`nr_get_by_cpu_index_tcp=1`** on the **client** — Set on the **`nvmeibc`** kernel module (for example via `modprobe.d` or `/sys/module/nvmeibc/parameters/nr_get_by_cpu_index_tcp` after load). The default is often **`0`** (disabled); set **`1`** for latency-oriented CPU–channel affinity. With **`1`**, the client selects the **network receive / IO channel (nrch)** using the **CPU index** of the submitting context, which **pins the data path to the originating CPU’s channel** instead of generic selection. Values: **`0`** disables CPU-index selection; **`1`** enables it; **`2`** or higher enables CPU-index selection **with fallback** to other schemes if needed.

3. **`lock_ch_get_method_tcp=1`** on the **client** — Also on **`nvmeibc`**. **`1`** selects the **BY_CPU** policy: the **locks channel** is chosen **by CPU** ( **`0`** = LRU with sharding tie-break, **`2`** = sharding by destination address). For lowest latency, **`1`** keeps lock traffic aligned with the same CPU-centric view as the IO path. On many installations this is already the default; confirm with `/sys/module/nvmeibc/parameters/lock_ch_get_method_tcp`.

4. **`cq_vec_flags_tcp=6`** on the **NVMesh common** module (`nvmeib_common`) — Completion queues for TCP/SIW use a **bitmask**. **`6`** sets **both** **bit 1** (index-based vectors from the channel or CQ creator index) **and** **bit 2** (the **same** completion vector for send and receive CQs). That combination **selects by index** and keeps **send and receive completions on the same completion vector** (so they share the same IRQ / **CPU** affinity for that vector), which tightens locality on the completion path versus using only one of those bits. The shipped default is often **`4`** (**bit 2** only—same SCQ/RCQ vector **without** index-based selection). Set via `modprobe.d` or `/sys/module/nvmeib_common/parameters/cq_vec_flags_tcp` where supported. See [NVMesh common module parameter: `cq_vec_flags_tcp`](#nvmesh-common-module-parameter-cq_vec_flags_tcp).

Together, **`rfs`** addresses **NIC receive locality**; **`nr_get_by_cpu_index_tcp`**, **`lock_ch_get_method_tcp`**, and **`cq_vec_flags_tcp`** align **IO, locking, and completion handling** with the **CPU and channel** that drive the workload. These choices can **reduce peak aggregate throughput** if they concentrate work on fewer hardware queues or reduce spreading; validate with your workload.

**Throughput-oriented contrast**

For **maximum bandwidth**, favor the patterns in [Configuration-oriented recommendations](#configuration-oriented-recommendations) and [Flow steering, RSS, and multi-port listeners](#flow-steering-rss-and-multi-port-listeners): for example **`TCP_FLOW_STEER="none"`** or **`"ntuple"`**, higher queue and channel counts, and NUMA-aware spreading—often at the cost of **higher tail latency** or more cross-CPU traffic.

See [Module Parameters](#module-parameters) for defaults and full descriptions of **`nr_get_by_cpu_index_tcp`**, **`lock_ch_get_method_tcp`**, and **`cq_vec_flags_tcp`**.

#### NVMesh common module parameter: `cq_vec_flags_tcp`

The **`cq_vec_flags_tcp`** parameter on the NVMesh common module (`nvmeib_common`) applies to **completion-queue vector selection for TCP (SIW)** paths. It uses the same flag bits as **`cq_vec_flags`** for RDMA: bit 0 reserves vector 0 for userspace, bit 1 selects **index-based** vector assignment, and bit 2 uses the **same completion vector for send and receive** CQs. For **latency-oriented** TCP tuning, **[Latency versus throughput](#latency-versus-throughput)** recommends **`cq_vec_flags_tcp=6`** (**bit 1** index-based **and** **bit 2** same SCQ/RCQ vector), which differs from a typical default of **`4`** (same SCQ/RCQ only, **without** index-based selection). Tuning this can help when spreading or concentrating completions to match your IRQ and CPU layout. See [Module Parameters](#module-parameters) for the authoritative description and current defaults.

#### Configuration-oriented recommendations

These are **starting points**, not universal rules—always measure with representative I/O.

- **Many CPU cores**  
  You can often increase **`TCP_NUM_RX_QUEUE`** and **`TCP_NUM_CHANNELS`** (within NIC and driver limits) so traffic and completions spread across more queues and CPUs. Prefer a **NUMA-aware** **`TCP_RX_CPU_AFFINITY_DOMAIN`** (for example `pernuma` or an explicit list of CPUs local to the NIC) to avoid remote-memory access. For receive-side spreading, see [Flow steering, RSS, and multi-port listeners](#flow-steering-rss-and-multi-port-listeners): default **`TCP_FLOW_STEER`** relies on **RSS** and multi-port listeners; **`TCP_FLOW_STEER="ntuple"`** can improve steering when your hardware supports it. Consider **`TCP_RPS_MODE`** as well; incorrect combinations can hurt latency, so change one dimension at a time.

- **Small number of cores**  
  Use **fewer RX queues** and **lower channel counts** so you do not spread work across more CPUs than exist or starve application threads. **`persocket`** or a **short explicit CPU list** for **`TCP_RX_CPU_AFFINITY_DOMAIN`** is often appropriate. On **clients**, you can cap TCP channel usage with the **`nvmeibc`** module parameters **`nr_max_channels_per_path_tcp`** (maximum IO channels per path for TCP) and **`max_lock_channels_tcp`** (maximum lock channels per disk for TCP, including secondary channels). Lower values reduce connection and locking parallelism—appropriate when core count is low. See [Module Parameters](#module-parameters). If hyper-threads contend on the same physical core, consider pinning workloads or reducing parallelism so pairs of hyper-threads are not saturated by competing work. Avoid aggressive RPS unless you have verified IRQ load.

- **One NIC per NUMA node (multi-NIC)**  
  Treat each NIC as a **separate NUMA domain**: use **`pernuma`** (or per-device CPU lists) so each interface’s steering stays on the local socket.

- **Single NIC**  
  A single interface must carry all traffic; balance **queue count** with **core count** and leave headroom for the OS and NVMesh userspace. **`persocket`** or **`fullspread`** may apply depending on whether you want to concentrate on one socket or use both sockets on dual-socket servers—profile both if the NIC is attached to one NUMA node. If IRQ affinity is managed by another tool (for example vendor tuning), set **`TCP_RX_SET_IRQ_AFFINITY`** to `No` and avoid double-configuration.

- **Manual IRQ/RPS tuning**  
  If you already apply a vendor profile or custom IRQ layout, disable NVMesh’s IRQ step with **`TCP_RX_SET_IRQ_AFFINITY="No"`**, or disable the whole script with **`TCP_SET_AFFINITY="No"`**, and keep **`TCP_RPS_MODE=donttouch`** unless you intend to change RPS from NVMesh.

For RDMA-centric tuning (IRQ balance, NVIDIA tools, and related topics), see [CPU Interrupt Affinity and IRQ Balancing](#cpu-interrupt-affinity-and-irq-balancing). TCP/SIW-specific tuning complements that guidance when traffic uses SoftiWarp instead of or in addition to RoCE.

## Software Delivery

### NVMesh Packages

NVMesh software components for Linux are delivered in the form of RPM or DEB packages.

See [**<u>Software Packages</u>**](#software-packages) for details on current packages.

The `nvmesh-management` and `nvmesh-base` packages, which are dependent on `nvmesh-utils`, can be installed together or independently. The `nvmesh-client` package is installed on top of `nvmesh-base` and then optionally the `nvmesh-target` package.

### Software Delivery for Red Hat Distributions

For Red Hat distributions and clones such as Rocky Linux, any valid RPM installation method can be used. Typically, yum and dnf are the preferred choices.

For yum with remote repositories, an account and password are needed. Create a yum repository configuration file, such as `/etc/yum.repos.d/nvmesh.repo`, with contents as follows:

```
[NVMesh]
name=NVMesh repository
baseurl=https://[user]:[password]@acme.com/nvmesh-generic/3.4.0/el/8.6/x86_64/ gpgcheck=0
enabled=1
```

Replace user and password in the example above with your credentials.

### Software Delivery for Ubuntu

For apt with remote repositories, an account and password are needed. Add the username and password by adding the following lines to `/etc/apt/auth.conf`:

```
machine urm.acme.com
login <USER>
password <PASSWORD>
```

Create an apt repository configuration file, such as `/etc/apt/sources.list.d/nvmesh.list` as follows:

```bash
#### Create the repository configuration file
echo 'deb [arch=amd64] https://urm.acme.com/artifactory/nvmesh-generic-local/3.4.0/ub/24.04/x86_64 noble main' | sudo tee /etc/apt/sources.list.d/nvmesh.list

#### If a GPG key is needed then this command should obtain it. The key is NOT uploaded by default for every version
wget -O - https://<USER>:<PASSWORD>@urm.acme.com/artifactory/nvmesh-generic-local/3.4.0/ub/24.04/x86_64/conf/nvmesh.gpg.key | sudo apt-key add -
```

To fetch the latest changes from the apt repo and verify that NVMesh is available, use:

```bash
sudo apt -o Dir::Etc::sourcelist="sources.list.d/nvmesh.list" -o Dir::Etc::sourceparts="-" -o APT::Get::List-Cleanup="0" update
sudo apt list | grep nvmesh
```

To install `nvmesh-base` for instance, use:

```bash
sudo apt install nvmesh-base
```

## Installation Instructions

The following sections provide detailed instructions for the installation and configuration of NVMesh. For an overview, see [Installation Overview](#installation-overview).

### Prepare Security Certificates (Optional)

The following diagram outlines the key entities for the security certificate chain.

<div align="center"><img src="./ug-media/image14.png" style="width:7.24306in;height:3.7375in"
alt="Certificates hierarchy." /></div>

Vault’s PKI engine serves as the security repository for the root certificate authority (CA) and the intermediate CA. It can be used to generate all required certificates.

First, authorizing certificates are generated. Then, for each component, a certificate signed by the intermediate CA can be generated. A rotation policy should be set up adhering to security policies and any project specific requirements. Care must be taken when updating the authorizing certificates to avoid downtime. A maintenance window may be required.

#### Preparing Certificate Authorities

To prepare the certificate authorities, perform the following steps:

1. Install Vault CLI, see [Hashicorp Instructions](https://developer.hashicorp.com/vault/docs/install).

1. Your vault user should have the following policies defined for it by the vault administrator:

```
path "sys/mounts/*" {
  capabilities = [ "create", "read", "update", "delete", "list" ]
}

path "sys/mounts" {
  capabilities = [ "read", "list" ]
}

path "pki*" {
  capabilities = [ "create", "read", "update", "delete", "list", "sudo" ]
}
```

3. Generate a root CA. Following is an example interaction for doing this. The root CA does not need to be generated for every deployment. For instance, a single root CA can serve all NVMesh deployments or all NVMesh deployments belonging to a common entity.

```bash
#### Enable the pki secrets engine at the pki path
$vault secrets enable pki
Success! Enabled the pki secrets engine at: pki/

#### Set a maximum time-to-live (TTL) of 3650 days (10 years)
$ vault secrets tune -max-lease-ttl=3650d pki
Success! Tuned the secrets engine at: pki/

#### Generate the nvmesh.com root CA, storing the certificate file
$ vault write -field=certificate pki/root/generate/internal \
  common_name="nvmesh.com" \
  issuer_name="nvmesh-root" \
  ttl=3650d > nvmesh_root_ca.crt

#### Configure the CA and CRL URLs
$ vault write pki/config/urls \
  issuing_certificates="$VAULT_ADDR/v1/pki/ca" \
  crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls
```

4. Generate an intermediate CA. The intermediate CA can be used for most tasks instead of the root CA with greater flexibility. It is recommended to use an intermediate CA per project or per NVMesh cluster.

```bash
#### First, enable the pki secrets engine at the pki_int path
$ vault secrets enable -path=pki_int pki
Success! Enabled the pki secrets engine at: pki_int/

#### Set a maximum time-to-live (TTL) of 1825 days (5 years) – tune this per security requirements
$ vault secrets tune -max-lease-ttl=1825d pki_int
Success! Tuned the secrets engine at: pki_int/

#### Generate the intermediate certificate signing request (CSR) as pki_intermediate.csr
$ vault write -format=json pki_int/intermediate/generate/internal \
  common_name="nvmesh.com Intermediate Authority" \
  issuer_name="nvmesh-dot-com-intermediate" \
  | jq -r '.data.csr' > pki_intermediate.csr

#### Sign the intermediate certificate with the root CA private key, and save the generated certificate as intermediate.crt
$ vault write -format=json pki/root/sign-intermediate \
  issuer_ref="nvmesh-root" \
  csr=@pki_intermediate.csr \
  format=pem_bundle ttl="1825d" \
  | jq -r '.data.certificate' > intermediate.crt

#### Store the CRT in vault
$ vault write pki_int/intermediate/set-signed certificate=@intermediate.crt
Success! Data written to: pki_int/intermediate/set-signed

#### Create the CA chain file which will be distributed to the endpoints
$ cat intermediate.crt nvmesh_root_ca.crt > ca_chain.crt
```

5. The ca_chain.crt file will be distributed to the endpoints, along with the certificates generated in the next section.

#### Preparing Component Certificates

Preparing certificates is not described in this documentation. Common security practices should be employed.

#### Deploying Certificates

It is recommended to place node certificates in `/etc/nvmesh.tls`, although this is configurable through variables in `/etc/nvmesh/nvmesh.conf` and `/etc/nvmesh/management.js.conf`. Then, set a softlink from `/etc/nvmesh.tls` to `/etc/nvmesh/tls`, which is the default configuration.

In case NVMesh is uninstalled from the node, the softlink will be removed but not the certificates themselves, making their deletion optional, as a separate step.

The `/etc/nvmesh.tls` directory or an alternative one should be readable by the following users: root, excelero (for management) and mongod (for mongo) and writable by root.

The variables concerning security and certificates are:

1. In `nvmesh.conf`:
   1. REST authentication:
      1. The means of authenticating against the management by the other components is set in `_REST_AUTH_METHOD`, which takes a value of either `MTLS` or `credentials`. The default is `MTLS`.

      1. For REST authentication, the CA chain file is needed. It is set using the variable `_REST_CA` with a default value of `/etc/nvmesh/tls/ca_chain.crt`.

   1. KAFKA authentication:
      1. The variable `KAFKA_TLS_ENABLED` should be set to `true` to authenticate against Kafka.

      1. The variable `KAFKA_CA` defines the CA chain file.

      1. The variables `KAFKA_MCS_CERTIFICATE` and `KAFKA_MCS_KEY` determine the certificate file and encryption key respectively with which the MCS authenticates.

      1. The variables `KAFKA_TOMA_CERTIFICATE` and `KAFKA_TOMA_KEY` determine the file and encryption key respectively with which the TOMA authenticates.

1. In `management.js.conf`:
   1. Management to management authentication:
      1. Set `config.websocket.auth.useHAWithMTLS` to `true`.

      1. Set `crt`, `key` and `ca` in `config.websocket.auth.tlsOptions` to the location of the node’s management to management certificate, key and chain files respectively.

   1. REST API authentication:
      1. Set `config.server.auth.authenticationMethod` to `MTLS`.

      1. Set `crt`, `key` and `ca` in `config.server.auth.tlsOptions` to the location of the node’s REST certificate, key and chain files respectively.

   1. KAFKA authentication:
      1. Set `config.kafkaConnection.transport.TLS` to `true`.

      1. Set `certFile`, `keyFile` and `CAFile` in `config.kafkaConnection.transport` to the location of the node’s REST certificate, key and chain files respectively.

   1. Mongo authentication:
      1. Set both `config.nvmesh{mongo,MetadataMongo}Connection.transport.TLS` to `true`.

      1. Set `certificateKeyFile` and `CAFile` in both of `config.nvmesh{mongo,MetadataMongo}Connection.transport` to the location of the node’s REST combined certificate and key bundle and chain file respectively. The bundle contains a concatenation of the key followed by the certificate.

1. For the Kafka brokers:
   1. Create the `Truststore.jks` file as follows, which defines the signing chain of trust:

```bash
$ keytool -keystore Truststore.jks -alias CAChain -import -file ca_chain.crt -storepass <password> -noprompt
Certificate was added to keystore
```

2. Create jks files, one for each component, `Management` and `Kafka`, repeating the following steps for each of them:

```bash
$ keytool -keystore <COMPONENT>.jks -alias CAChain -import -file ca_chain.crt -storepass <password> -noprompt
# Output: Certificate was added to keystore

$ openssl pkcs12 -export -in <COMPONENT>.crt -inkey <COMPONENT>.key -out <COMPONENT>.p12 -name <COMPONENT> -password pass:<password>

$ keytool -importkeystore -destkeystore <COMPONENT>.jks -srckeystore <COMPONENT>.p12 -srcstoretype PKCS12 -alias <COMPONENT> -deststorepass <password> -srcstorepass <password>
# Output: Importing keystore <COMPONENT>.p12 to <COMPONENT>.jks...
# Warning: The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore <COMPONENT>.jks -destkeystore <COMPONENT>.jks -deststoretype pkcs12".

$ rm <COMPONENT>.p12
```

3. In the configuration files, `/opt/kafka/config/kraft/{server,management}.properties` place the following lines defining the location of the jks files. In this context, the "server" component is Kafka.

```
security.protocol=SSL
ssl.keystore.location=/etc/nvmesh.tls/<COMPONENT>.jks
ssl.keystore.password=<password>
ssl.truststore.location=/etc/nvmesh.tls/Truststore.jks
ssl.truststore.password=<password>
ssl.client.auth=required
```

### Install MongoDB, NodeJS and Kafka

Management servers use MongoDB as a persistent data store and NodeJS for WebUI and API services. NVMesh uses Kafka for cross-component communication.

If your Linux distribution does not include MongoDB, NodeJS or Kafka packages with the versions required for NVMesh, it will be necessary to add them using Internet hosted repos. Alternatively, you may download the packages manually if Internet access is not available.

NodeJS must be installed on the same node as the Management Servers.

MongoDB and Kafka brokers are often installed on the same nodes. This is not mandatory.

A typical highly availability NVMesh management setup will comprise 3 or 5 servers with all these 3 external packages installed on them as well as the management software itself.

#### Install MongoDB

**<u>Note:</u>** Previous versions of NVMesh required various versions of MongoDB. If upgrading NVMesh, upgrading MongoDB may also be required. It is often required to update MongoDB in steps, e.g., first upgrade from Mongo 3.6 to Mongo 4.0 and then continue to upgrade to 4.2. Consult Mongo literature as needed.

This version of NVMesh is compatible with Mongo versions 5.0 – 7.0. It is recommended to install MongoDB 7.0.

Detailed instructions for installing MongoDB 7.0 can be found [here](https://www.mongodb.com/docs/v7.0/tutorial/install-mongodb-on-red-hat/) for RedHat-compatible and [here](https://www.mongodb.com/docs/v7.0/tutorial/install-mongodb-on-ubuntu/) for Ubuntu.

For Redhat 8.x, create /etc/yum.repos.d/mongodb-org-7.0.repo with the following contents:

```
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-7.0.asc |
```

For Redhat 9.x use:

```
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-7.0.asc
```

For Rocky Linux 10.x, no MongoDB 7.0 repo for RHEL 10 is available yet. Use the RHEL 9 repo as a workaround. MongoDB 7.0 requires AVX CPU support.

```
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=0
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-7.0.asc
```

Then run the following command unless a specific subversion is needed:

```bash
dnf install -y mongodb-org-server mongodb-org-mongos mongodb-org mongodb-org-tools
```

For Ubuntu 20.04 and 22.04, perform the following steps:

```bash
### Import the public key
sudo apt-get install gnupg curl
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
  sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
  --dearmor

### Create the apt source, for Ubuntu 22.04
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

### Create the apt source, for Ubuntu 20.04
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

### Reload the package database
sudo apt-get update

### Install the database
sudo apt-get install -y mongodb-org-server mongodb-org-mongos mongodb-org mongodb-org-tools
```

**<u>Note:</u>** Mongo 7.0 is not available for Ubuntu 24.04.

After installation, check whether the service started cleanly and verify that it is listening on tcp/27017, as follows:

```bash
#### Start mongod service
[root@in-c243-n1 ~]# systemctl start mongod

#### Enable service upon boot
[root@in-c243-n1 ~]# systemctl enable mongod

#### Check service status
[root@in-c243-n1 ~]# systemctl status mongod
● mongod.service - MongoDB Database Server
  Loaded: loaded (/usr/lib/systemd/system/mongod.service; enabled; vendor preset: disabled)
  Active: active (running) since Wed 2025-03-19 18:18:06 UTC; 20h ago
  Docs: https://docs.mongodb.org/manual
  Main PID: 16483 (mongod)
  Memory: 1.0G
  CGroup: /system.slice/mongod.service
  └─16483 /usr/bin/mongod -f /etc/mongod.conf

#### Verify listening on TCP port 27017
[root@in-c243-n1 ~]# lsof -i -nP | grep mongod | grep LISTEN
mongod 16483 mongod 14u IPv4 252017 0t0 TCP *:27017 (LISTEN)
```

#### Securing MongoDB Access

For background information, see the following links:

- <https://www.mongodb.com/docs/manual/tutorial/configure-ssl/>

- <https://www.mongodb.com/docs/manual/tutorial/configure-ssl-clients/>

For secure MongoDB access, deploy certificates as described in [Deploying Certificates](#deploying-certificates).

The following files are required:

- `/etc/nvmesh.tls/Mongo.crt`
- `/etc/nvmesh.tls/Mongo.key`
- `/etc/nvmesh.tls/Mongo.key-crt-bundle.crt`
  - This is a concatenation of the Mongo.key file and the Mongo.crt file above.
- `/etc/nvmesh.tls/ca_chain.crt`

With these files in places, the following command can be used to verify access:

```bash
mongosh management --host $(hostname -f):27017 --tls --authenticationDatabase '\$external' --authenticationMechanism MONGODB-X509 --tlsCertificateKeyFile /etc/nvmesh.tls/Management.key-crt-bundle.crt --tlsCAFile /etc/nvmesh.tls/ca_chain.crt
```

To install Management in a high availability setup, see [Management Scalability](#management-scalability).

#### Configuring Authentication for MongoDB

See [Enable Access Control on Self-Managed MongoDB Deployments](https://www.mongodb.com/docs/manual/tutorial/enable-authentication/) for instructions for configuring authentication to enable access control for MongoDB.

See [MongoDB Connection Options](#Mongo_Connection_Options) for guidance on configuring authenticated access to MongoDB from the Management Servers.

#### Install NodeJS

To install NodeJS, setup the nodesource repository for your Linux distribution as described [here](https://nodejs.org/en/download) or follow the instructions below. Note that NodeJS 18.x is mandatory for this version of NVMesh.

For RHEL 8.x / Rocky 8.x compatible, perform the following:

```bash
curl --silent --location https://rpm.nodesource.com/setup_18.x | sudo -E bash –
yum -y install nodejs
```

For Rocky Linux 10.x, the distribution ships a newer Node.js by default, which must be removed before installing Node.js 18:

```bash
yum remove -y nodejs npm || true
yum module reset -y nodejs || true
yum module disable -y nodejs || true
curl -fsSL https://rpm.nodesource.com/setup_18.x | bash -
yum install -y nodejs-18.* --allowerasing
```

For Ubuntu, perform the following:

```bash
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash –
apt-get install -y nodejs
```

#### Installing Kafka Servers

Kafka must be installed on one or more servers, as it is used for communication between NVMesh management and other components. It is recommended to use at least 3 Kafka servers to ensure high availability. Typically, a Kafka server instance is co-installed with every management instance and Mongo instance, but this is not strictly warranted.

JDK is a pre-requisite for installing a Kafka server.

For RHEL 8.x / Rocky 8.x compatible, perform the following or similar to install a JDK:

Option 1: Minimal JDK without monitoring tools

```bash
yum -y install java-1.8.0-openjdk
```

Option 2: Use a more complete JDK for monitoring tools as well

```bash
yum -y install java-1.8.0-openjdk-devel
```

For Rocky Linux 10.x, `java-1.8.0-openjdk` is not available. Install a newer JDK with version fallback:

```bash
yum install -y java-21-openjdk-headless \
  || yum install -y java-21-openjdk \
  || yum install -y java-17-openjdk-headless
```

Set `JAVA_HOME` so Kafka can locate the JDK:

```bash
echo 'export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))' | tee /etc/profile.d/java.sh
echo 'export PATH=$JAVA_HOME/bin:$PATH' | tee -a /etc/profile.d/java.sh
source /etc/profile.d/java.sh
```

For Ubuntu, perform the following:

```bash
apt-get install -y default-jdk
```

Next, download Kafka as tar/gz file, as unfortunately there is no commonly used RPM packaging, as follows:

Download the package, e.g., to /tmp

```bash
cd /tmp
wget https://archive.apache.org/dist/kafka/3.7.0/kafka_2.13-3.7.0.tgz
```

Extract the package to /opt

```bash
cd /opt
tar xf /tmp/kafka_2.13-3.7.0.tgz
```

Link the specific version to the generic directory

```bash
ln -s /opt/kafka_2.13-3.7.0 /opt/kafka
```

Setup a system-d service for Kafka by generating a file named `/etc/systemd/system/kafka.service` with the following contents:

```
[root@nvme243 15:59:11 root]# systemctl cat kafka
# /etc/systemd/system/kafka.service
[Unit]
Description=Apache Kafka server (broker)
Documentation=http://kafka.apache.org/documentation.html
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
Environment="LOG_DIR=/var/log/kafka"
ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/kraft/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh

[Install]
WantedBy=multi-user.target
```

Configure the Kafka nodes by modifying `/opt/kafka/config/server.properties` and adding something such as the following for a 3-node Kafka setup, inserting the appropriate IP addresses and passwords:

```
process.roles=broker,controller
controller.listener.names=CONTROLLER
log.dirs=/var/lib/kafka
log.retention.ms=-1
log.retention.minutes=-1
log.retention.hours=-1
log.retention.bytes=-1
auto.create.topics.enable=false
acks=all
controller.quorum.voters=1@<IP1>:9093,2@<IP2>:9093,3@<IP3>:9093
node.id=<NODE-ID, e.g. 1, 2 and 3>
offsets.topic.replication.factor=3
min.insync.replicas=2
default.replication.factor=3
listeners=SSL://<MYIP>:9092,CONTROLLER://<MYIP>:9093
advertised.listeners=PLAINTEXT://<MYIP>:9092
inter.broker.listener.name=SSL
listener.security.protocol.map=CONTROLLER:SSL,SSL:SSL
ssl.keystore.location=/etc/nvmesh.tls/Kafka.jks
ssl.keystore.password=<password>
ssl.truststore.location=/etc/nvmesh.tls/Truststore.jks
ssl.truststore.password=<password>
ssl.client.auth=required
authorizer.class.name=org.apache.kafka.metadata.authorizer.StandardAuthorizer
super.users=User:Kafka;User:Management
ssl.principal.mapping.rules=RULE:.*OU=([a-zA-Z.0-9@-]+).*$/$1/,DEFAULT
```

SSL should also be used for the Kafka-RAFT protocol between them by placing the following or similar in `/opt/kafka/config/kraft/management.properties`:

```
ssl.keystore.location=/etc/nvmesh.tls/Management.jks
ssl.keystore.password=infra-pass
ssl.truststore.location=/etc/nvmesh.tls/Truststore.jks
ssl.truststore.password=infra-pass
```

Generate and distribute a new random UUID for the cluster using

```bash
cd /opt/kafka

#### On any node generate the UUID
KAFKA_CLUSTER_ID=$(bin/kafka-storage.sh random-uuid)
echo $KAFKA_CLUSTER_ID #### will print out the new uuid

#### Insert this UUID to all nodes in the cluster
KAFKA_CLUSTER_ID="your_cluster_id"
bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties
```

Make final preparations for the node, as follows:

```bash
mkdir -p /var/lib/kafka/
systemctl enable kafka
```

Start the Kafka service by running, `systemctl start kafka` and monitoring using, `systemctl status kafka`.

#### Install Kafka Libraries

Any node with NVMesh components must communicate with NVMesh management and therefore requires confluent-kafka and its dependencies.

For RHEL 8.x / Rocky 8.x compatible, to install perform the following as root:

```bash
yum install python2-devel librdkafka-devel-2.1.1-1.cflt.el8.x86_64
pip2.7 install confluent-kafka==2.1.1 configparser
```

For Rocky Linux 10.x, Python 2.7 is not available. The confluent-kafka wheel for Python 3.12 bundles its own librdkafka 2.8.0, so no system librdkafka package is required:

```bash
python3.12 -m pip install confluent-kafka
```

Verify:

```bash
python3.12 -c "import confluent_kafka; print('confluent-kafka:', confluent_kafka.__version__); print('librdkafka   :', confluent_kafka.libversion()[0])"
```

Expected output:

```
confluent-kafka: 2.8.0
librdkafka   : 2.8.0
```

For Ubuntu, to install perform the following as root:

```bash
apt update
apt install python2-dev librdkafka-dev
pip2.7 install confluent-kafka==2.1.1 configparser
```

#### Install NVMesh Management

Install the `nvmesh-management` and `nvmesh-utils` packages on the appropriate server(s).

For RHEL compatible, perform the following:

```bash
yum -y install nvmesh-management nvmesh-utils
```

For Ubuntu, perform the following:

```bash
apt-get install -y nvmesh-management nvmesh-utils
```

Upon successful installation, Management will be set to automatically start at boot time. Deploying for HA is covered as an advanced topic in the [Management Scalability](#management-scalability) section.

After package installation, review the file, `/etc/nvmesh/management.js.conf`, for installation specific setting changes required. Details on this options available for this configuration file can be found in [Management Options](#management-options).

Once configured, start the service:

```bash
systemctl start nvmeshmgr
```

At this point, Management should be running. Record the IP addresses or hostnames of the Management Servers for configuring Clients and Targets. The IP address 172.10.100.201 will be used in this example.

To verify that the management database was initialized properly, and that Management is active, use a browser and attempt to connect to, [_https://172.10.100.201:4000_](https://172.10.100.201:4000) substituting your Management IP address or hostname. Depending on the environment’s security posture, it may be necessary to open this port in the firewall or other security mechanisms guarding networking traffic. Often, a workaround is to open an SSH tunnel for this port, using the command below tuned for the relevant address or hostname and then accessing the Management GUI via [_https://localhost:4000_](https://localhost:4000/).

```bash
ssh -L 4000:localhost:4000 172.10.100.201
```

To avoid security exception handling in the browser, it is recommended to use [mTLS](#mutual-tls).

If things are working properly, you should see the following login screen:

<div align="center"><img src="./ug-media/image15.png" style="width:4.2462in;height:3.15147in"
alt="A screenshot of a login screen." /></div>

The default login is `admin`/`admin`. Upon initial login, you will be prompted to change the password. This is highly recommended, but not mandatory.

### Install NVMesh Clients and Targets

Install the `nvmesh-utils`, `nvmesh-base` and `nvmesh-client` packages for server(s) intended to run only the client software. For targets, install the `nvmesh-target` package in addition.

Servers that will consume block devices backed by NVMesh volumes need only the client package. Servers that have NVMe drives that are to be used to generate volumes, i.e., will serve as targets, need the `nvmesh-target` package as well. For targets, the client package is mandatory.

For RHEL compatible, perform the following:

```bash
yum -y install nvmesh-utils nvmesh-base nvmesh-client <nvmesh-target>
```

For Ubuntu, perform the following:

```bash
apt-get install -y nvmesh-utils nvmesh-base nvmesh-client <nvmesh-target>
```

On all client and target servers, define the management servers and the NICs to use in the `/etc/nvmesh/nvmesh.conf` configuration file. Review if other settings should be set.

For all configuration options, see the [Client and Target Options](#client-and-target-options) section.

The interaction with the management servers is defined using the following fields:

- `_REST_SERVERS` – a comma separated list of management servers in the format of `<SERVER NAME/IP>:<REST PORT>`. The REST port is 4001 by default.

- `_REST_AUTH_METHOD` – either `credentials` for user/password-based login or `MTLS`.

- `_REST_CA` (if using MTLS for authentication) – for MTLS-based REST authentication, the full path name of the certificate file, e.g., `/etc/nvmesh/tls/rest_ca.crt`.

- `KAFKA_SERVERS` – a comma separated list of Kafka servers, usually run from the management servers, in the format of `<SERVER NAME/IP>:<KAFKA PORT>`. The Kafka port is 9092 by default.

To set the NICs to be used for the data path, set the following fields. Note that the communication with the Management servers is not limited to the NICs listed here:

- `CONFIGURED_NICS` – a comma separated list of NICs to use. A NIC can be defined as `<INTERFACE NAME>` or `<MLX DEVICE:PORT>`. For NICs with multiple IPs, the specific IP to use can be set using `<INTERFACE NAME:[IP]>`.

### Configure TCP/IP Support

To configure an NVMesh node to use TCP/IP instead of RDMA on Ethernet NICs, perform the following modification to `/etc/nvmesh/nvmesh.conf`:

1. If there is an entry for `TCP_ENABLED`, set it to `Yes`.

1. Otherwise, add a line for this setting, as follows: `TCP_ENABLED="Yes"`.

The previous setting will enable simultaneous use of TCP/IP and RDMA. To disable RDMA entirely, perform the same for the setting `TCP_ONLY`.

### Start the NVMesh Client

Upon successful installation of the `nvmesh-client` package, the Client will be set to automatically start at boot time. It will fail to do so until the configuration file has been set up as described in the previous sections.

After setting up the configuration file, start the Client service as follows:

Start the Client service

```bash
systemctl start nvmeshclient
```

If the Client service had been disabled, enable it

```bash
systemctl enable nvmeshclient
```

Verify the service is running properly

```bash
systemctl status nvmeshclient
```

In case of error, additional information may be available in logs, e.g.:

```bash
journal -u nvmeshclient
```

### Exclude Drives

By default, all NVMe drives are automatically assigned to NVMesh, except drives that are already mounted or identified as being in use, such as boot drives. Other drives can be excluded as well. The drives assigned to NVMesh are serviced by the Target, while the boot drives, mounted drives and the excluded drives will continue to be serviced by the standard kernel nvme driver.

NVMesh maintains the list of excluded NVMe drives in `/etc/nvmesh/target_devices.conf`.

This file can be modified using the `nvmesh_target` utility or edited manually.

This file is only relevant for Targets. Its contents are a list of Linux device paths and serial numbers.

To identify the drives serial numbers, list all NVMe drives:

```bash
sudo nvme list
```

To exclude a specific drive:

```bash
nvmesh_target exclude nvme <nvme_serial>
```

To include (un-exclude) a drive that had been excluded:

```bash
nvmesh_target include nvme <nvme_serial>
```

**<u>Note:</u>** The nvme command is provided by the nvme-cli package for most Linux distributions. The command from this package will not recognize drives already managed by NVMesh and with the standard NVMesh enumeration. To view all drives, use `/opt/nvmesh/target-repo/scripts/nvme-cli/nvme`.

### Start the NVMesh Target

Upon successful installation of the `nvmesh-target` package, the Target will be ready to start, but it is not set do so automatically at boot time.

After setting up the configuration file, start the Target service as described below.

At this point, the Target should be active and report itself and any available (not excluded) NVMe drives and configured NICs to the Management Servers. The reported NVMe drives will be visible at the Management level. The drives assigned to NVMesh will become available to the shared storage pool after being formatted.

Start the Target service

```bash
systemctl start nvmeshtarget
```

Enable the Target service upon boot

```bash
systemctl enable nvmeshtarget
```

Verify the service is running properly

```bash
systemctl status nvmeshtarget
```

In case of error, additional information may be available from the logs, e.g.:

```bash
journal -u nvmeshtarget
```

### (Optional) Assign Targets to Zones

For a description of Zones and their utility, see [Zones](#zones).

To enable zoning:

1. Stop all Management Servers.

1. Set true for `config.enableZones` in `/etc/nvmesh/management.js.conf`.

1. Restart all Management Servers.

The best indicator that the functionality is working is a new column Zone in the Targets section in the GUI. There should also be a new button named Approve.

To assign Targets to a Zone:

1. Use the Targets table’s multi-select functionality to select a group of Targets to assign to the same zone.

1. Click the Approve button.

1. In the pop-up dialog, fill in the zone-id, a positive integer, for the Targets.

1. Click Approve in the dialog and then confirm in the Targets section.

Note that once volumes have been allocated on the Drives on a Target, that Target will no longer be re-assignable to a different zone, as this may lead to data loss.

### Format Drives

Drives must be formatted for use with NVMesh.

**<u>Note:</u>** This operation is destructive and will cause loss of access to any data previously written to the drive.

To format a single drive using the GUI:

1. Go to the Targets tab.
1. Click the target that contains this drive.
1. Click the Format button for the desired drive.
1. Enter the current user’s password to re-authenticate.
1. Click OK to confirm the format operation.

An alternative method which also enables formatting multiple drives at once using the GUI:

1. Go to the Drives tab.
1. Select the drives to format or mark the top-most checkbox to select all drives.
1. Click Format.
1. Enter the current user’s password to re-authenticate.
1. Click OK to confirm the format operation.

# Getting Started

A simple workflow is presented for configuring an NVMesh system for the first time. This workflow introduces the Management Servers GUI, an innovative web-based interface for managing the system.

The workflow consists of the following steps:

- Introduction to the Management GUI
- Managing drives
- Managing volume groups
- Managing volumes
- Attaching volumes
- Resizing volumes

## Drives Management

### Overview

The drives screen lists all the drives in the system and provides the ability to format them.

### Drives Table

<div align="center"><img src="./ug-media/image16.png" style="width:7.24306in;height:3.32917in"
alt="A screenshot of a the drives table." /></div>

The Drives table provides the following details (in the Status column) on drives detected by the system. The Health column presents additional explanatory information.

| Status | Meaning |
| --- | --- |
| Uninitialized | The drive is not initialized for NVMesh usage and cannot be used until it is formatted. <br><br> Drives that are in use by other software, such as a boot drive, will be automatically excluded. Formatting will not be possible for such drives. |
| Ingesting | The drive is in the process of being moved from the stock NVMe driver to the NVMesh driver. This operation should be rapid and will rarely be seen. |
| Frozen | The drive is in the initial formatting stage. This operation should be rapid and will rarely be seen. |
| Formatting | The drive is being actively formatted and making progress. |
| Format Error | A format error occurred during the format operation. |
| Initializing | The drive has completed formatting, and blocks are now being initialized, i.e., zeroed. <br><br> The drive can be used for logical volume definition, but it may not be ready yet for a volume to come online if the blocks used by the volume have not been initialized yet. Initializing progress is presented as a percentage as it is reported from the Target. |
| Excluded | The drive had been administratively excluded from NVMesh. |
| Ok | The drive was formatted by NVMesh and is available for use. |
| Error | An error occurred during the initialization of the drive. |
| Missing | The drive is currently missing from the cluster. This means that is no longer being reported by the last Target on which it was reported. |

## Volume Creation and Attachment

After completing installation of the management, client and target software on selected hosts, the next step is volume definition. For the example, the following configuration will be used:

| Hostname   | # of Drives | Management | Client | Target |
| ---------- | :---------: | :--------: | :----: | :----: |
| nvmeshmgr1 |      0      |    Yes     |   No   |   No   |
| appsrv1    |      0      |     No     |  Yes   |   No   |
| appsrv2    |      0      |     No     |  Yes   |   No   |
| appsrv3    |      0      |     No     |  Yes   |   No   |
| targetsrv1 |      2      |     No     |   No   |  Yes   |
| targetsrv2 |      2      |     No     |   No   |  Yes   |
| targetsrv3 |      2      |     No     |   No   |  Yes   |

This represents a disaggregated NVMesh implementation with 3 hosts acting as clients, 3 as targets and nvmeshmgr1 as the sole management.

Note that although targetsrv1, targetsrv2 and targetsrv3 are marked above as targets, the nvmeshclient service is required for the nvmeshtarget service and will be run on these nodes, which could also behave as clients.

### Login to the Management GUI

By default, the management server comes with a single default account/password, `admin`/`admin`.

Login to the management server from a browser substituting its IP address or hostname. For example, <https://nvmeshmgr1:4000>.

<div align="center"><img src="./ug-media/image17.png" style="width:5.66464in;height:4.85323in"
alt="A screenshot of a the login dialog box." /></div>

Upon initial login you will be prompted to change the default password.

After login, the dashboard view appears:

<div align="center"><img src="./ug-media/image18.png" style="width:7.24306in;height:4.26528in"
alt="A screenshot of a the NVMesh dashboard." /></div>

At this point, it is suggested to create a new administrative user. To do so, click settings and then users from the left side menu to see this screen:

<div align="center"><img src="./ug-media/image19.png" style="width:7.24306in;height:4.21944in"
alt="A screenshot of a computer AI-generated content may be incorrect." /></div>

Click the + sign button in the lower right corner to add a new user as shown in the example below. The password set should be considered temporary as the GUI will ask to change it upon initial login by the new user.

<div align="center"><img src="./ug-media/image20.png" style="width:7.24306in;height:6.69375in"
alt="A screenshot of a setting up a new user." /></div>

The dialog box options are as follows:

- Email - a valid Email address as an account login.
- Password - a (temporary) password.
- Role provides a pulldown List:
  - Admin accounts can make all changes to the system including creating other accounts.
  - Observers can only monitor the system and cannot make any changes.
- Email Notifications Level provides a pulldown List. Email notifications will only be sent if outgoing email settings are configured properly.
  - NONE: No messages will be sent to the account.
  - WARNING: System Warnings and Errors will be sent to the account.
  - ERROR: System Errors will be sent to the account.

After clicking the Add button, confirm or commit the changes by clicking the Save button or the Save Changes link.

<div align="center"><img src="./ug-media/image21.png" style="width:7.24306in;height:2.95278in"
alt="A screenshot demonstrating NVMesh's save button." /></div>

**<u>Note:</u>** Most changes in NVMesh require final confirmation by clicking Save Changes or the modifications will be lost!

<div align="center"><img src="./ug-media/image22.png" style="width:7.24306in;height:2.05278in"
alt="A screenshot demonstrating NVMesh's success response to save." /></div>

After successfully adding the new user, logout using the button in the top right corner and then log back in as the newly created user.

### Verify Client and Target Registration

In the example described above, there are 3 clients and 3 targets.

Choose the clients menu item on the left to see a screen like the following one, which has 5 clients.

<div align="center"><img src="./ug-media/image23.png" style="width:7.24306in;height:2.60833in"
alt="A screenshot of the Client's table in the NVMesh GUI." /></div>

For all nodes on which the `nvmesh-client` package has been installed and the `nvmeshclient` service started, there should be a row in the clients table. As there are no volumes yet, there is little to do other than verify that the active clients appear.

Similarly, clicking the targets menu item should show a screen like the following:

<div align="center"><img src="./ug-media/image24.png" style="width:7.24306in;height:2.62222in"
alt="A screenshot of the Target's table in the NVMesh GUI." /></div>

Clicking on a target name will present that node’s NICs and NVMe devices. Feel free to explore, but the main objective at this stage is to verify the appearance of the targets and that their status is OK.

### Create a Volume

After verifying that the management is functioning and that the clients and targets are reporting to the management server, a volume can be created. Once created, it can be attached to clients for use. Click the volumes menu item on the left side of the screen for the following screen. Then, click the + sign to add a new volume.

<div align="center"><img src="./ug-media/image25.png" style="width:7.24306in;height:3.84306in"
alt="A screenshot of the Volume's table in the NVMesh GUI." /></div>

After clicking the button, a new dialog box opens prompting for the attributes of the new volume. In this example, a 400GB RAID-1 (mirrored) volume will be created.

<div align="center"><img src="./ug-media/image26.png" style="width:7.24306in;height:3.91528in"
alt="A screenshot of the volume creation dialog." /></div>

The volume dialog box options are as follows:

- Name – a short name without special characters. This will be used as the block device name on the client, usually in /dev/nvmesh.
- Description - an optional human readable description of the volume.
- Volume Capacity – specify the size of the volume, or select Maximum, which means to generate a volume as large as possible that meets the provisioning criteria.
  - Unit Type - Choose the volume size units.
- Volume Provisioning Group or Custom Tab
  - Volume Provisioning Groups allow specifying drive selection criteria in an automated fashion. This topic is covered in the Functionality Reference section.
  - Custom allows specifying all volume definition settings manually.

After entering or selecting options for the Name, Description and Volume Capacity fields, select the "DEFAULT_RAID_1_VPG" Volume Provisioning Group. A complete description of volume provisioning groups (VPGs) can be found in the section [Volume Provisioning Groups](#volume-provisioning-groups). NVMesh will select areas of available space on NVMe devices that meet the necessary criteria for the selected Volume Type. In this example, it will use 400GB of space from one drive on one target, and 400GB of space from a drive on a different target. Mirrored segments should reside on different targets.

The usage bar at the bottom of the dialog box displays the proposed capacity usage based on the currently specified options. To complete the volume creation, click the Add button.

Upon successful creation, a screen such as this will appear:

<div align="center"><img src="./ug-media/image27.png" style="width:7.24306in;height:2.31736in"
alt="A screenshot of a successfully created volume." /></div>

After the successful creation of a volume, it can be attached to clients for consumption.

### Attach a Volume to a Client

Volumes must be attached to clients so they can be consumed. When attaching a volume to a client with default parameters, it is made available as a block device named `/dev/nvmesh/<vol_name>` and operates like a regular Linux block device. To attach the volume created in the previous section to a client, go to the Clients tab, choose the relevant client and press the "Attach/Detach" button above the clients table. This will open a dialog box, in which the volume and other volumes can be attached to the chosen clients, as shown in the following screenshots.

<div align="center">
<img src="./ug-media/image28.png" style="width:7.24306in;height:1.975in"
alt="A screenshot of the Client table before attaching BigVolume." />

<img src="./ug-media/image29.png" style="width:7.24306in;height:2.98889in"
alt="A screenshot of the dialog for attaching BigVolume." />

<img src="./ug-media/image30.png" style="width:7.24306in;height:2.30972in"
alt="A screenshot of the Client table after attaching BigVolume." />

</div>

Log into the client node, for instance using SSH, and verify that the Linux block device `/dev/nvmesh/<vol_name>` has been created, as follows:

```
[root@nvme1034 17:04:40 ~]$ ls -l /dev/nvmesh/BigVolume
brw-rw---- 1 root disk 252, 256 Jul 6 17:24 /dev/nvmesh/BigVolume
```

To verify minimal block device functionality and attributes, use fdisk, as follows:

```
[root@nvme1034 17:28:06 ~]# fdisk -l /dev/nvmesh/BigVolume
Disk /dev/nvmesh/BigVolume: 372.5 GiB, 399999238144 bytes, 97656064 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 131072 bytes
```

See the [Volume Status Example](#volume-status-example) section for methods to get detailed status information on the NVMesh block device.

The block device is ready to be used just like any local NVMe or other block device. You can create a file system on it or use it as a raw block device. On subsequent service restarts, the device will automatically be attached if it was attached at service stop time and the configuration profile for this client is defined to perform auto-attach, which is the default. To prevent this behavior, explicitly detach the device via the management server using the same buttons.

# General Settings

In the NVMesh GUI, click Settings and then click General to reach the general settings governing various aspects of NVMesh behavior. After making any changes in settings, use the "Save" button at the top of the panel to persist them.

The following table describes these general settings:

| Sub-Section | Setting | Description |
| --- | --- | --- |
| General | Cluster ID | This is a name that can be used to identify the cluster useful for parsing query results via automation and for identifying a cluster in the GUI. <br>The cluster ID appears at the top of the GUI. |
| General | Default Unit Type | The unit type determines whether to show volume and drive capacities in base 2 (binary) or base 10 (decimal). <br>When decimal is chosen, the convention for volume and drive size will be decimal based, i.e. using kilobytes, megabytes, gigabytes, terabytes, etc. <br>For binary, the convention used will refer to kibibytes, mebibytes, gibibytes, tebibytes, etc. <br>The default unit type will be applied for all users that have not chosen a specific unit type. |
| General | User Unit Type | The user unit type will be applied for the current user. See the description above for default unit types for the meaning of unit type. |
| General | Default Domain | The default email domain to use for users that do not supply a domain name explicitly. |
| General | Customer Name | The customer’s name for which support should <br>associate notifications from this system. |
| General | Send logs | Specifies the severity of logs sent to support. |
| General | Send statistics | Determines whether to periodically send statistics to support. This functionality is deprecated. |
| Advanced | Automatic Log Out Threshold | The timeout of the GUI and API access (in milliseconds). <br>After the timeout expires the GUI and API will automatically logout all logged in users. |
| Advanced | Fix in Sanity and Recover | Determines whether to fix items in MongoDB when performing the "sanity and recover flow" that is part of the startup of the first Management in a cluster. <br>Currently, there is only one specific item that is fixed, which are the available blocks, i.e., the areas on the drive that can be allocated to volumes. |
| Advanced | "Keep Alive" Intervals | The time frame between keep alive messages sent per component to the management. <br>A component will be considered down if it has not sent any message in a grace period that is 3 times this time frame. |
| Advanced | Maximum JSON Size | The largest JSON message size supported. <br>⚠️: Modifying this setting may result in system instability or unexpected behavior. |
| Advanced | Reserved Blocks | The percentage of reserved blocks at the start of a managed NVMe device. <br>⚠️: Modifying this setting may result in system instability, data loss, or unexpected behavior. |
| Advanced | Compatibility Mode | Use NVMesh’s version of dynamic libraries instead of the operating system versions to avoid compatibility issues. This mimics container behavior to reduce dependencies. |
| Advanced | Enable Legacy Formatting | Determines whether to allow legacy formatting on metadata supported drives via the RESTful API. |
| Advanced | Enable Volumes <br>Access Via <br>NVMf – System <br>Default | The default value used for new volumes with regard to enabling access via theNVMf protocol. |
| Advanced | Enable Erasure Coded Volume Creation | This can be used to disable creation of EC volumes via the GUI only. <br>It does not affect creating EC volumes via the RESTful API or CLI. <br>This option will not hide existing EC volumes. |
| Advanced | Disable Old Managements when in Upgrade Mode | When in managed upgrade mode, old managements will not accept new requests. |
| Zones | Enable | Determines whether the zones functionality is globally enabled in the system. See Zones. |
| Zones | Randomness | The amount of randomness to insert into zone selection for volume allocation. Should be between 0 and 100 percent. <br>Default is 20. |
| Zones - <br> Selection Weights | Number of Segments in Zone | The weight to attribute to the number of segments already in a zone. A larger value of segments decreases the chance the zone will be chosen. <br>Default is 150. |
| Zones - <br> Selection Weights | Number of Targets in Zone | The weight to attribute to the number of targets in a zone. A larger number of targets increases the chance the zone will be chosen. <br>Default is 120. |
| Zones - <br> Selection Weights | Average Time in <br>Zone Allocation <br>Queue | The time weight to attribute to time spent in allocation. A larger amount of time spent decreases the chance the zone will be chosen. <br>Default is 50. |
| Zones - <br> Selection Weights | Logging | Logging Level |

# Client and Target Configuration

The guided installation example enables the deployment of a basic NVMesh environment. The following section serves as a reference for additional and advanced options that may be necessary or desired due to redundancy requirements, media selection or security requirements.

## Configuration Profiles

Configuration profiles are used to make configuration changes for multiple clients and targets centrally.

Each node is associated with one configuration profile.

Upon installation, there are 3 built-in configuration profiles that cannot be deleted, as follows:

1. "NVMesh Default" - a read-only configuration profile that is equivalent to the default configuration after a fresh installation of NVMesh.
1. "NVMesh Debug" - a configuration profile that can be used for troubleshooting the system in which debug log messages are turned on.
1. "Cluster Default" - this is the configuration profile for any new node added to the cluster. Initially, its contents are the same as NVMesh Default. However, as this is editable, it may differ from NVMesh Default over time.

All configuration elements can also be configured through configuration files on the nodes themselves. Those options are described in the following sections.

**<u>Note:</u>** Configuration elements defined locally on nodes using other means described in the subsequent sections will override settings from configuration profiles.

Nodes can be associated with configuration profiles by editing the configuration profile or by selecting nodes in either the clients or targets screen and pressing the "Configure" button, which will generate a new configuration profile unless all chosen nodes are exactly the nodes associated with a specific configuration profile.

### Configuration Elements

A description and a set of labels are available for every configuration profile. These serve no functional purpose other than for end user’s documentation convenience.

#### Standard Options

| Domain | Name | Description | Default |
| --- | --- | --- | --- |
| Cluster | Kafka Servers | A comma separated list of the Kafka servers used to communicate between management and the endpoints, in the form <Kafka server hostname or IP address : port>, \<hostname/ IP:port>,… <br>Typically, the port number is 9092. | Empty |
| Client | NVMesh User Mode Enabled | Used to enable NVMesh User Mode (nvmeshum) instead of NVMesh’s kernel-based client (nvmeshclient) – this is currently experimental and should not be used in production environments | Off |
| Monitor | Use TLS | Use TLS connectivity when connecting to management. | Off |
| Monitor | Management Username | Management username used for the monitor service. | Empty |
| Monitor | TLS Certificate file | Path to cert file for the TLS connection with management. | Empty |
| Monitor | TLS Key file | Path to the key file for the TLS connection with management. | Empty |
| Monitor | TLS CA file | Path to the CA file for the TLS connection with management. | Empty |
| Monitor | Credentials file | Path to the management credentials file containing data with the following format: `<hostname> <user> <base64 encoded passwd>`. | Empty |

#### Advanced Options

| Domain | Name | Description | Default |
| --- | --- | --- | --- |
| Cluster | Kafka TLS Enabled | Use to enable TLS for Kafka communication. | Off |
| Cluster | Kafka CA | Full path for the CA file. | Empty |
| Cluster | Kafka MCS Certificate | Full path for the MCS cert file. | Empty |
| Cluster | Kafka MCS Key | Full path for the MCS key file. | Empty |
| Cluster | Kafka TOMA Certificate | Full path for the TOMA cert file. | Empty |
| Cluster | Kafka TOMA Key | Full path for the TOMA key file. | Empty |
| Client | There are multiple advanced options for clients. They are all currently experimental and should not be used in production environments. |  |  |
| Node | Configured NICs | The NICs that clients and targets can use for IO. An empty value places no limits. | Empty |
| Node | Blacklist NICs | The NICs that clients and targets must not use for IO. <br>To allow all NICs, leave empty. | Empty |
| Node | IPv4 Only | Only support IPv4 for RoCE and TCP. | On |
| Node | Maximum SM Query Burst | Sets the maximum burst size of queries to the IB Session Manager. <br>This parameter is not relevant for RoCE. <br>A smaller number here will decrease the load on the SM but may increase the initial bring-up time. | Empty |
| Node | TCP Enabled | Enable TCP as a possible transport type for clients and targets. | Off |
| Node | IPv6 Only | Only support IPv6 for RoCE and TCP. | Off |
| Node | TCP Only | Enable usage only of TCP over Ethernet NICs, effectively disabling RoCE. | Off |
| Node | MCS Management timeout | Allows detecting a hanging or dead TCP connection between clients and targets and the management servers <br>With the transition to Kafka for management interactions, this is deprecated and will be removed in a future version. | Empty |
| Target | NVMe IRQ affinity domain | Defines how the interrupts for managed NVMe drives are distributed to CPU cores <br>Empty – not driven from the configuration profile. <br>None – do not configure them, rather use system defaults. <br>Per-NUMA – distribute to the cores of NUMA node to which the drive is connected. <br>Per-socket – distribute to the cores of the CPU socket to which the drive is connected. <br>Full-spread – distribute to all cores. | Empty |
| Logs | Dump ftrace on kernel PANIC | When on, clients and targets will dump fast log (ftrace) buffers on kernel panic, which can be helpful for repetitive major bugs that cause kernel panics. | Off |
| Logs | MCS Logging Level | The MCS’s logging level. | INFO |
| Logs | MCS Logging Verbose Types | This is relevant only when the logging level is set to verbose. <br>It controls which message types will be logged. | Empty |
| Logs | Management Agent Logging Level | The management agent’s logging level. | INFO |
| Logs | TOMA number of trace logs | Specifies the number of files of TOMA trace history files to retain. Use this and the trace log size and the max logs to determine the maximum amount of drive space TOMA traces will use. | Empty |
| Logs | TOMA trace log size | Specifies the size of a single TOMA trace file. | Empty |
| Logs | TOMA buffers per log | Specifies the number of 4K buffers saved to a single trace file per write. This can affect tracing performance and its hit on drive endurance. | Empty |
| Logs | TOMA max logs | Specifies the number of files of TOMA trace history to keep. The minimal value can’t be lower than the number of CPUs. | Empty |
| Logs | Loggers cgroup | Create a cgroup to limit NVMesh logger’s write bandwidth. | Empty |
| Logs | Loggers cgroup write limit | Limit the bandwidth of logger writes to a specific number of bytes per second. | Empty |

## Client and Target Options

Most client and target options reside in the main configuration file, `/etc/nvmesh/nvmesh.conf`.

The configuration file affects both even though some options are applicable only to one or to the other.

**<u>Note:</u>** Options specified in this configuration file only take affect when the associated services are started or restarted.

### Common Client and Target Options

The following options are applicable for both clients and targets.

| Common Client and Target Options |  |
| --- | --- |
| **BLACKLIST_NICS** |  |
| Description | The NICs that clients and targets should not use for IO. <br> An empty value places no limits. <br> By default, clients and targets will attempt to make use of any RDMA capable NIC. <br> If populated, only the NICs in the list will be blacklisted. |
| Default Value | "" (empty) |
| Possible Values | A semi-colon separated list of NIC identifiers in the following format: <br> `BLACKLIST_NICS="<INTERFACE>;<INTERFACE>;<INTERFACE>;..."` |
| **CONFIGURED_NICS** |  |
| Description | The NICs that clients and targets can use for IO. <br> An empty value places no limits. <br> By default, clients and targets will attempt to make use of any RDMA capable NIC. <br> If populated, only the NICs in the list will be utilized. |
| Default Value | "" (empty) |
| Possible Values | A semi-colon separated list of NIC identifiers in the following format: <br> `CONFIGURED_NICS="<INTERFACE>;<INTERFACE>;<INTERFACE>;..."` |
| **IPV4_ONLY** |  |
| Description | Only support IPv4 for RoCE and TCP. |
| Default Value | "No" |
| Possible Values | "Yes" or "No" |
| **IPV6_ONLY** |  |
| Description | Only support IPv6 for RoCE and TCP. |
| Default Value | "No" |
| Possible Values | "Yes" or "No" |
| **KAFKA_SERVERS** |  |
| Description | The Kafka servers used to communicate between management and the endpoints. |
| Default Value | No default value. |
| Possible Values | A comma separated list of the Kafka servers and port numbers in this format. Typically, the port number is 9092. <br> `<Kafka server hostname or IP address : port>, <hostname / IP : port>,…` |
| **MAX_SM_QUERY_BURST** |  |
| Description | For InfiniBand only, the maximum numbers of queries per second to send to the subnet manager. |
| Default Value | 32 |
| Possible Values | Integer Values |
| **TCP_ENABLED** |  |
| Description | Enable use of TCP over Ethernet NICs. |
| Default Value | "No" |
| Possible Values | "Yes" or "No" |
| **TCP_ONLY** |  |
| Description | Allow use **only** of TCP over Ethernet NICs, disabling RDMA. |
| Default Value | "No" |
| Possible Values | "Yes" or "No" |

### Client Specific Options

The are currently no options applicable only to clients.

### Target Specific Options

The following options are applicable only to targets.

| Target Specific Options |  |
| --- | --- |
| **NVME_IRQ_AFFINITY_DOMAIN** |
| Description | Defines how the interrupts for NVMesh-managed NVMe drives are distributed across CPU cores. <br> The optimal choice is dependent on the machine’s NUMA architecture and the number of NVMe queues used. |
| Default Value | "pernuma" |
| Possible Values | "pernuma" <br> "persocket" <br> "fullspread" |
| **NVME_IRQ_AFFINITY_RR_RESET** |  |
| Description | Determines whether to reset the round robin for each NVMe device when distributing interrupts. This helps in assigning specific queues to cores, including the interrupt handlers. This can be useful when the number of queues per NVMe device exceeds the number of cores or when attempting to achieve "hero" numbers. In the latter case, use only the cores with interrupts assigned to them. |
| Default Value | false |
| Possible Values | true or false |

### Tracing Related Options

See [Binary Tracing](#binary-tracing) for information on tracing related options configurable from `/etc/nvmesh/nvmesh.conf`.

### Unsupported Options

The following parameters are still present in the conf file, but they are related to unsupported functionality. They have not been removed in case the functionality is revived and should not be altered or set to be on the safe side:

- NVMF_IP
- ACCESS_METHOD
- ISCSI_TARGET_IP
- ISCSI_INITIATOR_IP
- MLX5_RDDA_ENABLED
- MAX_CLIENT_RSRC
- MCS_MANAGEMENT_TIMEOUT
- COLLECT_STATS
- AUTO_ATTACH_VOLUMES

The following parameters are used for setting logging levels.

- MCS_LOGGING_LEVEL
- AGENT_LOGGING_LEVEL

The following parameter is in development and should be considered an experimental non-supported feature:

- NVMESH_MODE

## Module Parameters

Primary or basic options are set via the `/etc/nvmesh/nvmesh.conf` configuration file as described in the previous section.

Additional parameters are configurable as kernel module parameters. These parameters can be managed using standard module parameter management tools, such as configuration files in `/etc/modprobe.d`.

**<u>Note:</u>** In general, these should be considered advanced options, and it is strongly recommended to use caution when altering them.

Module parameters used to be described in the user guide. This has been moved to a dedicated document.

# Storage Configuration

"Target classes", "drive classes" and "volume provisioning groups (VPGs)" provide administrators with fine-grained control of logical volume provisioning. These tools are presented in this section.

## Target Classes

### Overview

Target classes are logical groupings of target hosts. Target classes are useful in grouping target hosts into distinct classes for limiting host and device selection during volume creation.

Target classes can be used to direct allocation of volumes to certain target hosts by attributes such as their rack location, group ownership or redundancy level.

Target classes are created by giving a name and description to groups of targets by selecting specific targets that have registered with a management.

### Creating Target Classes

To create a new target class:

1. Click Settings, Target Classes.
1. Click the + to open a Target Class dialog box.
1. In the Name field, enter a name for the new Target Class.
1. (Optional) In the Description field, enter a description for the new Target Class.
1. (Optional) In the Protection Domains field, define protection domains to be associated with the targets in this target class. Each protection domain is denoted using the following format, `<protection domain scope: protection domain identifier>`.
   1. Multiple protection domains can be inserted. Use a comma to move from one Protection Domain to the next.
1. Multi-select from the Targets table.
1. Click Add.
1. Click Save Changes to complete the operation.

<div align="center"><img src="./ug-media/image31.png" style="width:7.24306in;height:5.39167in"
alt="A screenshot of a Target Class dialog box." /></div>

## Drive Classes

### Overview

Drive classes are logical groupings of storage devices, i.e. NVMe drives. Drive classes are used to group drives into distinct classes for device selection during volume creation. Drive classes can be used to group certain types of drives together by feature such as high performance, low write endurance or capacity. Drives can be grouped by business or social parameters such as purchase group, data type or project.

Drive classes are created by selecting specific drives.

### Creating Drive Classes

To create a new Drive Class:

1. Click Settings, Drive Classes.
1. Click the + to open a Drive Class dialog box.
1. In the Name field, enter a name for the new Drive Class.
1. (Optional) In the Description field, enter a description for the new Drive Class.
1. (Optional) In the Protection Domains field, define protection domains to be associated with the drives in this Drive Class. Each protection domain is denoted using the following format, `<protection domain scope: protection domain identifier>`.
   1. Multiple protection domains can be inserted using comma-separation.
1. Multi-select drives from the drives table.
1. Click Add.
1. Click Save Changes to complete the operation.

<div align="center"><img src="./ug-media/image32.png" style="width:7.24306in;height:5.29583in"
alt="A screenshot of a Target Class dialog box." /></div>

## Volume Provisioning Groups

Volume Provisioning Groups (VPGs) are groupings of logical volume parameters. The parameters encompass logical volume types as well as drive classes and target classes, which potentially limit the targets and drives selected during volume creation.

VPGs are later used in the volume provisioning process. When a drive that is used for implementing a volume generated from a VPG is evicted, the system will attempt to automatically allocate alternative space per VPG restrictions and automatically rebuild the volume. For volumes defined manually, the rebuild will not be done automatically.

VPGs comprise the following fields:

| VPG Field Name | VPG Field Description |
| --- | --- |
| Name | A name or identifier for this VPG. |
| Description(optional) | An informative description for this VPG. |
| Volume Type | Volumes created with this VPG will be limited to the volume type specified. |
| Target Classes <br>(Optional) | Volumes created with this VPG will have their device selections limited to targets that are members of the specified target classes. |
| Drive Classes <br>(Optional) | Volumes created with this VPG will have their drive selections limited to drives that are members of the specified drive classes |
| Protection Domains <br>(Optional) | Protection domains by which to separate data copies to ensure high availability across this protection domain. |
| Volume Security Groups <br>(Optional) | Volume security groups for which access will be allowed to the volumes generated from the VPG. If no security groups are set, then there is no access limitation. |
| VPG Reserve Space | Defines the capacity of space to be reserved for volumes that are created utilizing this VPG. It is not necessary to reserve any space. <br>Note: Be sure to check the measurement units (GB, TB, etc.). |
| Use for Metadata | Obsolete, will be removed in an upcoming release. <br>This should not be used! |
| Allocate on Offline Hardware | When turned on, then the volume may be allocated on drives that are currently offline or missing. |
| Encrypted Volume | When turned on, the volume will be automatically encrypted using dm-crypt integration on the clients where the volume is attached. |

Following is a screenshot of the VPG creation screen that should be filled it out using the information from the table above. VPG management is found under the Settings tab as Provisioning Groups.

<div align="center"><img src="./ug-media/image33.png" style="width:7.24306in;height:7.51042in"
alt="A screenshot of a Volume Provisioning Group dialog box." /></div>

For convenience, NVMesh includes default VPGs to assist in volume creation as described in the following table.

| Built-in VPGs Name | Built-in VPGs Description) |
| --- | --- |
| DEFAULT_CONCATENATED_VPG | For generating concatenated volumes. |
| DEFAULT_RAID_0_VPG | For generating striped RAID-0 volumes. |
| DEFAULT_RAID_1_VPG | For generating mirrored RAID-1 volumes. |
| DEFAULT_RAID_10_VPG | For generating striped and mirrored RAID-10 volumes with a stripe width of 2. |
| DEFAULT_EC_DUAL_TARGET_REDUNDANCY_VPG | For generating 8+2 erasure coded volumes with full separation. <br>For such volumes, drive space will be required from at least 10 targets. |
| DEFAULT_EC_SINGLE_TARGET_REDUNDANCY_VPG | For generating 8+2 erasure coded volumes with minimal separation. <br>For such volumes, drive space will be required from at least 10 drives spread across at least 5 targets. |
| DEFAULT_STRIPED_EC_DUAL_TARGET_REDUNDANCY_VPG | For generating 8+2 striped and erasure coded volumes with full separation with a stripe width of 2. <br>For such volumes, drive space will be required from at least 20 drives spread across at least 10 targets. <br>**Note: This functionality is not production grade in this version. <br> This should not be used!** |
| DEFAULT_METADATA_RAID_1_VPG | Obsolete, will be removed in an upcoming release. <br>**This should not be used!** |

## Protection Domains

Protection domains are a mechanism to assist in ensuring data availability is aligned to specific data center protection requirements.

Out of the box, NVMesh separates mirrored copies of data or erasure coded elements to drives on different targets. However, all targets and drives within the same zone are considered equivalent.

Protection domains enable enhancing the separation layout with user-defined criteria.

For instance, it may be prudent to separate data for a crucial volume that should always be available to 2 separate racks. Even if there is a total rack failure the volume’s data will still be accessible. Other data separation criteria could be defined for different fault or availability zones such as power supplies, fire suppression systems, data center rows or upgrade zones.

To use protection domain functionality, define the protection domain in which resources are located. Then, during the volume creation process, define which protection domains to consider for separating volume elements. For instance, for rack separation, each target would be labeled with its rack identifier. Then, upon volume creation, "rack" would be set as a separation protection domain.

Instead of assigning protection domain information directly to targets and drives, this is done by grouping them in target classes and drive classes and then associating one or more protection domains with those classes. For a specific target class or drive class, the information is provided using a list of key-value pairs. The key is the protection domain type, and the value is the specific instance. For example, the protection domain type could be "rack" and the specific instance value could be "C-33". In this case, the key-value pair list would be {"rack": "C-33"}.

Key-value pairs can be inserted for target classes and drive classes via the Management GUI or using the RESTful API. At volume creation, only the protection domain keys should be provided. In the example above, the key would be "rack". This functionality enables fine-tuned allocation. For instance, it is possible to create a volume limited to 2 racks by choosing the target classes tagged with "rack" keys "C-32" and "C-33" to ensure data is protected by separating it between these two racks.

## Read-only Access

By default, block devices will be read-write or read-only based on the mode in which they were opened by the application using the block device, most often a file system.

When an application opens a block device in read-only mode, flags will be sent to the block device as part of the open call, which will inform the NVMesh client to set the block device as read-only.

Sometimes, the operating system will still attempt to write data to this volume, usually data correctness for file systems. The default behavior for NVMesh is to prevent such writes. This behavior can be augmented. For more details, contact support.

This behavior enables a common usage pattern of using a local file system for sharing quasi-static data efficiently to many readers where the file system is mounted read-only. In this pattern, the volume is attached to all readers and mounted in a read-only mode.

For data update, the following steps are taken:

1. The clients unmount the file system.
1. The update is done from a single client with a read-write mount.
1. The file system is unmounted from the single updating client.
1. The clients remount the file system in read-only mode.

For example, if a volume is mounted as read-only, all write operations to the volume will be failed, including trim commands as follows:

```
[root@nvme31 17:50:55 nvmesh]# mount -o ro /dev/nvmesh/t1ro /mnt/t1ro

[root@nvme31 17:51:00 nvmesh]# fstrim --verbose /mnt/t1ro
fstrim: /mnt/t1ro: FITRIM ioctl failed: Input/output error

[root@nvme31 17:51:01 nvmesh]# umount /mnt/t1ro

[root@nvme31 17:51:02 nvmesh]# mount /dev/nvmesh/t1ro /mnt/t1ro

[root@nvme31 17:51:17 nvmesh]# fstrim --verbose /mnt/t1ro
/mnt/t1: 920.3 MiB (964972544 bytes) trimmed
```

## Client Operations

Clients use volumes, which are presented as Linux block devices. The state of the associations or attachments of volumes to clients is maintained by the management layer, i.e., it is the source of truth. The management mapping of volumes to clients is the desired state. Management will interact with clients to implement it as much as possible. Deviations from the desired state may be a result of lack of synchronization between the client and the management or reservation mode issues.

### Attaching and Detaching Volumes to Clients

Volumes can be attached to or detached from clients using the GUI.

1. Click Clients in the menu on the left of the GUI.
1. Choose a single client for the operation.
1. Press the "Attach/Detach" button above the clients table.
1. A dialog will appear with all volumes in the cluster.
1. Choose which volumes should be attached to the client and the attachment characteristics as follows:
   1. The reservation mode – there are 3 reservation modes:
      1. Shared read write – this is the default.
      1. Shared read only.
      1. Exclusive read-write.
   1. Preempt other clients on attach – this flag is relevant if the requested attachment contradicts the way the current clients are attached.
      1. If preempt is not set, the attachment will fail.
      1. If preempt is set, the attachment will succeed, and the other clients with the previous volume reservation mode will have I/O disabled for this volume.
   1. Detach Other Clients – this flag provides the option to detach this volume from all other clients while attaching to this client. It can be used with or without explicit preemption.
   1. Reference IDs are used to allow safely attaching the same volume multiple times for multiple K8s containers from different K8s namespaces.
      1. See this [link](https://gitlab-master.nvidia.com/excelero/nvmesh-csi-driver/-/blob/master/docs/src/usage/cross-namespace-volumes.md) (**TBD: make this publicly available.**) for more information.
      1. Setting the reference ID via the GUI will save this information for the current attach or detach operation.
   1. Emulation mode is obsolete functionality originally intended for NVMesh User-mode.

### Attachment Status

Per-client volume attachment status is reflected through color-coding and hover-text in the Clients table.

Clients generate Linux block devices for volumes attached to them. By default, the block device path for the volume will be `/dev/nvmesh/<vol_name>`. The path within `/dev` is configurable through module parameters.

NVMesh-generated are typical Linux block devices, but they are not NVMe drives. As for other block device directories, manual changes inside it are strongly discouraged.

An NVMesh block device is not immediately IO enabled upon generation. IO-enablement is dependent on the ability to connect with the appropriate targets required to furnish IO to this volume and the health of the drives, the targets and the networking to them and reservation modes. When performing attachment via the CLI, it is possible to make the operation synchronous, waiting for IO enablement before reverting with or without a timeout.

**<u>Note:</u>** Once IO has been enabled, the timeout for IO operations is practically infinite.

After IO has been enabled for a volume, it might still become disabled if there are impediments for instance due to drive health issues, target health issues or networking issues. Many of these are automatically remanded with IO becoming enabled again.

To verify the block device’s size and to see other volume attributes, use fdisk, for instance as follows:

```
[root@nvme85 13:55:55 ~]# ls /dev/nvmesh
vjq01-0 vr401-0

[root@nvme85 13:55:56 ~]# fdisk -l /dev/nvmesh/vjq01-0
Disk /dev/nvmesh/vjq01-0: 186.3 GiB, 199999094784 bytes, 48827904 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 786432 bytes
```

Detailed information on the status of an NVMesh block device is available in `/proc/nvmeibc/volumes/<volume_name>/status`. The device status section will describe whether IO is enabled. The volume layout across targets and drives is also visible in this status file.

**<u>Note:</u>** The status contains debug information that is not self-explanatory. The format of the contents is subject to change without notice and should not be considered a formal API.

#### Volume Status Example

The example is presented both as an image and as text because it is difficult to embed it clearly in documentation. It is best viewed in a wide terminal.

<img src="./ug-media/image34.png" style="width:7.24306in;height:1.93542in"
alt="A screenshot of a volume status example." />

```
[root@nvme85 13:56:02 ~]# cat /proc/nvmeibc/volumes/vjq01-0/status
Name=vjq01-0, UUID=b5de5490-8ca3-11f0-941f-55175a0bd63d, size=48827904[blocks], 186[Gb], short_id=256, Sector Size=4096[bytes], ptr=0xffff97054a6ac000, type=visible (0x0)
Reservation Mode: {SHARED_READ_WRITE, version=0x2, preempt=No, rby=nvme85.mtl.labs.mlnx}, Mgmt Report: {fioe_cli=1, last_io_perm=15, attachment_version=1293, type=visible}, "dp_flags" : {"edic": 1, "local_read": 0, "mutable_r_bio": 0, "alw": 96, "alr": 192, "elev": 6, "snake": 1, "jent": 16}, Flags: itm={0/r=0/%=0}
!!! WARNING !!! Data is NOT written! debug di mode is active, inject=3564[b]!
RVMS=0x2
Device status: Attached, Live, with IO (debug:0x200, 1)
IO is currently enabled.
Raid Type: RAID-6
Topology Debug: i=[37..36), ver=1, io_perm=5 nr=0 ns=0[blks], vl=1
Chunk #0: Stripe{Size=192, Width=1} Slice{6+2} Vol Blocks [0..48827903]
  Stripe  Slice   Status  Disk  NVMe ID     0xLBA Start 0xLBA End   Last Known Target   Debug-info
  0       0       Online  S4C9NA0M400255.1  25f000      a21cff      nvme84.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O0,C7,C6) r1v=0x12e lid=0x446|a uid=b5e09e81]
  0       1       Online  S4C9NF0M500192.1  25f000      a21cff      nvme80.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O1,C0,C7) r1v=0x12e lid=0x446|a uid=b5e113b0]
  0       2       Online  S4C9NA0M400422.1  25f000      a21cff      nvme83.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O2,C1,C0) r1v=0x12e lid=0x446|a uid=b5e161d0]
  0       3       Online  S4C9NA0M400287.1  25f000      a21cff      nvme82.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O3,C2,C1) r1v=0x12e lid=0x446|a uid=b5e1d700]
  0       4       Online  S4C9NA0M400241.1  25f000      a21cff      nvme84.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O4,C3,C2) r1v=0x12e lid=0x446|a uid=b5e22520]
  0       5       Online  S4C9NF0M500206.1  25f000      a21cff      nvme80.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O5,C4,C3) r1v=0x12e lid=0x446|a uid=b5e27340]
  0       6       Online  S4C9NA0M400288.1  25f000      a21cff      nvme83.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O6,C5,C4) r1v=0x12e lid=0x446|a uid=b5e2e870]
  0       7       Online  S4C9NA0M400267.1  25f000      a21cff      nvme82.mtl.labs.mlnx  [a=1 p=0 acm=RW sy=1 lm(O7,C6,C5) r1v=0x12e lid=0x446|a uid=b5e33690]
Enforce Read Only: Y, Retry Timeout: 1048576[sec], ext_car_io=N
Sync Stats: r=0/p=0/u=0, OK:f=0/p=0 Done:t=0/m=0/ds=0, L=0[msec], rr=0, TopoSt:{n_Mdeg=0, n_1deg=0} <br>mini_elevator: in_plug={} cache={}
Failed IO: crit=0, detach=0, ignore=0, rider=0, trim=0, other=0 (resub: in=0, out=0, tout=0) {sus_thresh=7, n_binfo=0/0, n_htr0=0, EC4571=0}
MgmtAlert: {arm=bio-ok-1st stable=bio-ok-1st} last_armed: {at=5418221, uw2ro=0}[sec] {n=0, long=0[sec]} conf={stab=5, unpW=18446744073709551, detStu=10}[sec]
Slow Locks 2000[msec]: num_locks=0, last={lock_id=0x0 n_tries=0, n_msec=0, op=U-Ow, dlba[blkst]=0x0, seg=(0,0,0)}
format_version: 1 time: 2025-09-10 11:00:06.412
```

This output provides the following information:

1. In the first line of output, the volume name and its size appear, both in number of blocks and a human readable size. Volumes attached by the administrator should have a visible type. Volumes connected for rebuild or encryption operations will have a hidden type.
1. The second line of output contains reservation information, management report debug information and additional behavioral flags.
1. Further down, depending on the volume status, there is a line that begins with "IO is currently" that informs whether IO is currently enabled or disabled for this volume. Once IO has been initially enabled, the client will not reject IO, instead waiting for IO to become enabled before returning an error. However, this will give an indication on the expected latency or immediacy for IO operations.
1. The volume type appears in the next line.
1. After this, there is a description of the volume’s topology, i.e., of the drive segments comprising the volume and their current state.
1. Next there is an indication of whether the volume enforces read-only mode behavior, see [Read-only Access](#read-only-access), and the current retry timeout for IO. This is the value that begins at 30 and is raised upon the first successful I/O operation.
1. As mentioned above, this output contains lots of additional valuable debug information that may be useful for support if needed.

The `client_processes` file co-located with the status file can provide information to assist in detaching a volume that is "busy", due to the block device being open by an active process.

There are many additional files containing primarily debug information under `/proc/nvmeibc`. Content format may change without notice.

**<u>Note:</u>** The contents and format of the status file are fluid and may change without notice. Therefore, it is not recommended to build parsers for it. For parsing, the `status.json` file is more suitable for machine-reading, due to the JSON format. However, its contents may also change without notice.

#### IO Disabled

IO is sometimes disabled for a specific client for a specific volume. This can be seen in the GUI or by looking at the state field in the volume’s status file, as described above. The root cause may be a missing drive, a target failure or a network connectivity failure.

IO may also be in a suspended state. This will happen if a potential corruption was identified. For instance, if the dirty bits collected are not aligned with the number of drives required for erasure coded volumes, IO will be suspended. Another example is an unidentified NVMe error.

To relieve the suspended state on the volume, detach it and then attach it.

Another option is to run the following command as root:

```bash
echo "#vol_name|volume_suspend=0" > /proc/nvmeibc/cli/cli
```

In the command above, `vol_name` is a placeholder for the actual volume name.

## Drive Segment Zeroing

During initial formatting of a drive, the entire drive is trimmed. By default, the system will use the NVMe trim command to achieve this and per the NVMe standard, this should zero all data on the drive. This system behavior can be altered to explicitly write zeroes if needed either using a special NVMe command for this or via writing zeroes directly.

In addition, when a volume is deleted, drive blocks may contain previously written data. Therefore, these blocks must be zeroed out before reuse for security purposes. This zeroing starts immediately after the volume is deleted. As a result, client reads from the volume will never fetch old data from the drives.

Zeroing behavior is governed by three parameters set directly on targets using the [toma_rpc](toma_rpc#_The_) utility.

To alter the zeroing behavior defined, run commands such as in the following example that sets the current default values, which are to TRIM without verifying that the blocks are later read as zeroes.

```bash
/opt/nvmesh/common-repo/tools/toma_rpc disk-models default is_using_nvme_trim_before_zero on

/opt/nvmesh/common-repo/tools/toma_rpc disk-models default is_zeroing_using_test_and_write off

/opt/nvmesh/common-repo/tools/toma_rpc disk-models default is_zeroing_mandatory off
```

- `is_using_nvme_trim_before_zero` governs whether to use TRIM operations as a means of zeroing.
- `is_zeroing_using_test_and_write` governs whether to read and write zeros as a means of zeroing.
- `is_zeroing_mandatory` can be used to stop all explicit zeroing.

These settings can be set as a default for all drives or using commands for specific drive models. Use `/opt/nvmesh/common-repo/tools/toma_rpc disk-models list` to see the current definitions including built-in non-default behavior to handle some known drive model quirks.

## Volume Rebuild Prioritization

In the volume definition dialog, there are controls for managing rebuild prioritization. They define the relative load to place on rebuilding a volume, including network, disk and compute load versus serving standard I/O. Using a lower value reduces the load, increasing IO/s, but may cause rebuilds to take much more time.

Rebuild priority is defined per volume and should be applicable to all rebuild functions on that volume.

The supported values are between 1 and10, in units of 10%. 10 means maximal rebuild speed (100%), while 1 means 10%, which is the lowest speed available.

A special value of 0 means that the load is defined on each target locally. On each target, the priority or speed of rebuild can be configured via "ioctls".

The granularity of configuration is more precise and allows to set a specific rebuild priority value for each function or type of rebuild. Contact support for fine-grained control instructions. The rebuild priority parameter does not affect which volumes are rebuilt first.

There are other TOMA-specific parameters that control the number of concurrent rebuild processes per target.

## TOMA Configuration

### The "toma_rpc" Tool

The "toma_rpc" tool is the TOMA’s "swiss-knife" providing a variety of knobs for TOMA tuning and some monitoring facilities.

**<u>Note:</u>** There is a long-term plan to move the tuning functionality into management.

The toma_rpc tool is not placed in a one of the directories commonly searched for executables by default in commonly used shells, so unless special steps are taken the full path will be needed to invoke the tool, i.e., `/opt/nvmesh/common-repo/tools/toma_rpc`. For convenience, hereon toma_rpc will be used to imply the full path.

The following sections describe the higher-level options. Each section has a help option with more information. Invoking `toma_rpc help` can provide this information as well.

The `status` section or subset of `toma_rpc` commands provides the same information as the virtual files in `/proc/nvmeibs/toma_status` with the following translation:

| toma_rpc status \<cmd> | /proc/nvmeibs/toma_status/\<file> |
| ---------------------: | :-------------------------------- |
|                    all | all                               |
|                   raft | raft                              |
|          disk_segments | dseg                              |
|          block_devices | bdev                              |
|                  disks | disk                              |
|                     ib | rdma                              |
|               recovery | recover                           |
|                 config | cfg                               |
|               topology | Topo                              |
|                 leader | Leader                            |
|            local_disks | local_disk                        |
|                 memory | mem                               |
|                zeroing | <NO EQUIVALENT>                   |

The following table describes the other `toma_rpc` options:

| Sub-command / Section | Description |
| --- | --- |
| trace-status | Provides basic information on the amount of data written to traces since the current TOMA has started running. |
| simulate | Used by developers for error injection and simulation. <br>⚠️ Do not use unless intentionally meaning to inject errors. This may cause data loss! |
| locate | Used for controlling drives attention LEDs, i.e., make them turn on or off or blink. Requires BIOS VMD support available on most systems with Intel Xeon CPUs. AMD uses IBPI for LED management that the TOMA does not support. |
| disk-models | Enables setting zeroing and formatting parameters for specific disk models overwriting system defaults in general or for more specific models. <br>⚠️ Use with caution! |
| config | Enables setting multiple TOMA operational parameters. This is one of the main tuning sections of the toma_rpc tool providing the following tunables. <br>⚠️ Use caution when making changes to avoid TOMA misbehavior. <br>Changes are made locally on this TOMA and are persistent across restarts. <br>Use `toma_rpc config print` to list the current settings. These parameters are described in the following table. |
| ignore-raft_member | Facilitates ignoring a specific node for RAFT purposes. <br>This is mainly a debug facility. |
| shadow-volume | A debug facility for attaching and detaching shadow volumes that are volumes used for recovery operations initiated by TOMA and implemented by the local clients on a volume that is not locally attached. |

The following tables defines the TOMA configuration parameters defined using `toma_rpc config`.

| Parameter | Description |
| --- | --- |
| raft_leader_heartbeat_timeout_usec | Defines the frequency at which the RAFT leader sends a heartbeat to the followers. |
| raft_min_election_timeout_factor | Defines how many times a heartbeat is missed by a follower for it to invoke a new leader election. This setting combined with the heartbeat timeout itself contribute to defining failover times when a leader stops unexpectedly. |
| max_n_simultaneous_dirty_rebuild | Defines the number of simultaneous dirty bit rebuilds that can be run. <br>Lowering this parameter will not stop currently running rebuilds. |
| max_n_simultaneous_stale_and_txid_rebuild | Defines the number of simultaneous rebuilds used for cleared stale locks and performing transaction ID rebuilds. |
| max_n_simultaneous_scrubbing | Maximum number of simultaneous scrubbing tasks. Default: 2. |
| scrubbing_default_period_days | Default scrubbing period in days, used when not explicitly set per volume. Default: 30. |
| scrubbing_n_blksets_per_iteration | Number of blksets processed per scrubbing iteration. Controls granularity of progress across segments. Default: 16384. |
| scrubbing | Enables or disables scrubbing globally. |
| recovery_client_batch_n_blksets | Defines the number of block sets given to the client for recovery in each batch. <br>The default value of 0 implies using the internal TOMA default. |
| topology_max_praids_in_a_report | Defines the maximum number of protection-RAIDs the TOMA will report on in a single update message. This is limited to avoid huge management updates in a single message. |
| toma_do_not_report_disk_segment_gpts | Defines whether TOMAs report GPT information on local disks to management. |
| mgmt_keep_alive_secs | This parameter will be removed from toma_rpc as it is now configured from the management. |
| mgmt_leader_keep_alive_secs | This parameter will be removed from toma_rpc as it is now configured from the management. |
| report_target_min_between_secs | Defines the minimum time between TOMA reports to management to avoid overloading managements with TOMA reports, i.e., as a throttling means. |
| generic_block_device_support | Deprecated, had been used for non-NVMe drive support. |
| praid_activation_timeout_sec | Defines how long, in seconds, to wait when initializing a PRAID for all components to be ready before proceeding to start it in a degraded state. Default: 20. |
| udp_max_header_length | Defines the maximum UDP header length for the current network so that TOMA does not exceed the network’s MTU for TOMA-TOMA communication. |
| disable_periodic_smart_polling | Defines how often TOMA reads NVMe drives S.M.A.R.T. information. |
| tracer_debug_level | Sets the tracer debug level as described in Binary Tracing. |
| log_snapshotting_mode | Determines whether traces are snapshotted on critical TOMA events. |
| kafka_get_offset_timeout_secs | Determines the timeout for reading information from Kafka. |
| attach_timeout_sec | Determines the timeout for waiting for a volume to be attached and IO enabled by the local client for recovery purposes. |
| enable_networking_periodic_traces | Deprecated, not currently in use. |

## Data Scrubbing

**Note:** This feature should be considered alpha.

Data scrubbing is a background process that periodically reads volume data, validates it across redundant copies, and repairs any detected problems such as bad sectors. RAID-0 and JBOD volumes are not scrubbed, as they have no redundancy to validate or recover from.

Each TOMA independently scrubs its local segments. Scrubbing is only performed on healthy, non-degraded volumes. The process works as follows:

1. TOMA periodically scans its local segments to identify those eligible for scrubbing, based on the configured scrubbing period and the time each segment was last scrubbed.
2. For each eligible segment, TOMA launches a scrubbing recovery task, up to the configured maximum number of simultaneous tasks.
3. The client reads blksets from all redundant sources, verifies that data is consistent between them, and that the local data consistency check is valid where applicable. Bad sectors detected during scrubbing are repaired. Each client reads only the blksets owned by its node.
4. The client reports success or failure back to TOMA, indicating the reason in case of failure (I/O error, data inconsistency, or local data consistency check error).
5. When a segment is fully scrubbed, TOMA reports completion to management. Management aggregates these reports to determine when a full volume scrubbing cycle has completed and presents this to the user.

Scrubbing progress is persistent and survives restarts.

Scrubbing can be enabled or disabled at runtime:

```bash
/opt/nvmesh/tools/toma_rpc/toma_rpc config scrubbing 1  # enable
/opt/nvmesh/tools/toma_rpc/toma_rpc config scrubbing 0  # disable
```

The following `toma_rpc` parameters govern scrubbing behavior, as documented in the [TOMA Configuration](#toma-configuration) section:

| Parameter | Description |
|---|---|
| `max_n_simultaneous_scrubbing` | Maximum number of simultaneous scrubbing tasks. Default: 2. |
| `scrubbing_default_period_days` | Default scrubbing period in days, used when not explicitly set per volume. Default: 30. |
| `scrubbing_n_blksets_per_iteration` | Number of blksets processed per scrubbing iteration. Default: 16384. |
| `scrubbing` | Enables or disables scrubbing globally. |

# Management Configuration

## Management Options

Management options reside in this configuration file, `/etc/nvmesh/management.js.conf`.

This file is read directly by management. The first line and last line in the file should not be modified. The file format should remain as follows.

```python
var config = {};
…
module.exports = config;
```

The following tables describes the options, including defaults and possible values.

### General Options

| Option Name | Description | Default Value | Possible Values |
| --- | --- | --- | --- |
| config.ipIdentificationStrategy | The IP identification or resolution strategy determines how management will choose which IP to listen on. | "FirstInterface" | • Manual - Use the forceIP configuration, which subsequently must be set when using this strategy. <br><br> • FQDN - Resolve via the system hostname. <br><br> • FirstInterfaceDefault - Use the default route interface. <br><br> • SpecificInterface - Use a named interface, which requires setting the specificInterfaceName configuration. <br><br> • FirstInterface - Use the first available interface (default). This is the same behavior as older system versions before this option was introduced. |
| config.port | The TCP port management uses for HTTP communication with GUI clients. | 4000 | Any valid TCP port number, typically above 1024. |
| config.useSSL | Determines whether HTTP communication to the management server is encrypted via SSL. | false | true or false |
| config.webSocketServerPort | The TCP port management uses for dynamic updates from clients and targets. | 4001 | Any valid TCP port number, typically above 1024. |
| config.websocket.\* | This section contains multiple parameters or options for the web sockets used by management. | See the built-in defaults file. | It is not recommended to modify this section. <br><br> An exception is the "useHAWithMTLS" field which by default is false but should be modified to true for any production installation to ensure security posture. <br><br> The location of the TLS credentials is set as described in the security section by default. |
| config.server.\* | This section contains general Node.js server parameters. | See the built-in defaults file. | It is not recommended to modify this section. <br><br> An exception is the "authenticationMethod" field which by default is set to "credentials" but should be modified to "MTLS" for any production installation to ensure security posture. <br><br> The location of the TLS credentials is set as described in the security section by default. |
| config.forceIP | Define the IP address to use for accepting and sending management traffic. See config.ipIdentificationStrategy above for other options | Undefined | An IP address. |
| config.enableExecutionTimers | Used for debugging. | false | true or false |
| config.clearPendingCommitsMaxTimeout | Max timeout in seconds for clearing pending Kafka commits on graceful shutdown. | 30 | An integer value for the number of seconds. A large number may significantly slow down normal shutdown. |
| config.nodeAllocatedMemory | Determines the maximum memory allocated to the node.js process in MB. | 4096 (4 GB). This is achieved also by not setting any value. | Reasonable values range from 4,096 to 16,384. |
| config.enableDistributedRAID | Determines whether to allow creation of erasure coding volumes. | true | true or false |

### Kafka Options

| Option Name | Description | Default Value | Possible Values |
| --- | --- | --- | --- |
| config.kafkaConnection.hosts | A comma separated list of Kafka server definitions, such as, `'nvme80.mtl.labs.mlnx:9092,nvme82.mtl.labs.mlnx:9092'` | Empty | A comma separated list of servers in `<hostname:port number>` format. |
| config.kafkaConnection.enableACL | Determines whether management will define security ACLs for Kafka. This must be set to true for proper security posture. | false | true or false |
| config.kafkaConnection.transport.\* | This section defines the mode of transport, i.e., whether TLS is required and the associated credentials. <br><br> TLS must be turned on for proper security posture. | config.kafkaConnection.transport.TLS is set to false by default meaning that TLS is off and then the other fields are irrelevant. | (config.kafkaConnection.transport.)TLS is a Boolean value. <br><br> CAFile, certFile, keyFile define the certificate file paths. <br><br> passphrase provides the TLS connection passphrase. |

<span id="Mongo_Connection_Options" class="anchor"></span>

### MongoDB & MongoDB NVMesh Metadata Connection Options

| Option Name | Description | Default Value | Possible Values |
| --- | --- | --- | --- | --- |
| config.mongoConnection & <br>config.nvmeshMetadataMongoConnection.\* | The following options are described for Mongo connection settings, as specified in config.mongoConnection. Mongo is used as the central store for NVMesh management data. <br><br> A second MongoDB database is used to store system metadata that describes the cluster layout. The same options apply for connecting to this database from config.nvmeshMetadataMongoConnection. |  |  |
| \*.dbName | The name of the database being accessed. For a dedicated Mongo installation, use the defaults. For a shared Mongo service, ensure that the database name or instance is unique to this cluster. | "management" for mongoConnection. <br><br> "nvmesh_metadata" for nvmeshMetadataMongoConnection. | Any legal Mongo database instance name. |
| \*.protocol | The protocol with which to connect to Mongo. | "mongodb" | Use "mongodb" for local direct access or "mongodb+srv" to use Mongo’s DNS seed list connection format, see [Mongo Connection Strings Documentation](https://www.mongodb.com/docs/manual/reference/connection-string/) for more info. |
| \*.hosts | The URI used to connect to the MongoDB server(s) containing the database. | `'localhost:27017'` | A comma separated list of valid MongoDB servers using hostnames and port. For example, with three-way replication, this value might be: <br> `'host1:27017,host2:27017,host3:27017'` |
| \*.options.replicaSetName | For management database redundancy, this setting is used to define the MongoDB Replica Set name of the Mongo cluster. | "" (empty). | An arbitrary text value, for example "rs0". |
| \*.auth.username | The username for Mongo access control and authentication. <br> Leave undefined in case no database access control is employed. | "" (empty). | An arbitrary text value, for example "johndoe". |
| \*.auth.password | The password for Mongo access control and authentication. <br> Leave undefined in case no database access control is employed. | "" (empty). | An arbitrary text value, for example "NvmeshIsGr8". |
| \*.auth.authenticationDatabase | The mongoDB authentication database for Mongo access control and authentication. | <br> Leave undefined in case no database access control is employed. | "" (empty). | An arbitrary text value, for example "authenticationDB". |
| \*.auth.authenticationMechanism | The mongoDB authentication mechanism for Mongo access control and authentication. <br> Leave undefined in case no database access control is employed. | "" (empty). | One of the supported authentication mechanisms, e.g., “MONGODB-X509”, see [Mongo Authentication Mechanisms](https://www.mongodb.com/docs/manual/core/authentication/) for more info. |
| \*.transport.TLS | Determines whether to use TLS for Mongo communication. <br> If true, then the following 3 options, "certificateKeyFile", "CAFile" and "passphrase" are required. | false | true of false |
| \*.transport.certificateKeyFile | The certificate, PEM file, used to identify to the Mongo server. | "" (empty). | Absolute file paths. |
| \*.transport.CAFile | The certificate, PEM file, of the CA used to identify to the Mongo server. | "" (empty). | Absolute file paths. |
| \*.transport. passphrase | The passphrase of the certificateKeyFile above. | "" (empty). | A valid passphrase string. | Management connects to Mongo on startup. |
| mongoConnection.mongoMaxConnectTries | This parameter determines the number of times to try before exiting. This applies only to connection to main database instance. | 10 | Integers |
| mongoConnection.mongoTimeBetweenConnectTries | Management connects to Mongo on startup. This parameter determines the number of milliseconds to wait between retries. This applies only to connection to main database instance. | 60,000 (60 seconds). | Integers |
| config.mongoConnectOptions.connectTimeoutMS | Mongo connection timeout in milliseconds. | 60,000 (60 seconds). | 1,000 – 30,000 (1 second to 5 minutes). |
| config.mongoConnectOptions.socketTimeoutMS | Mongo socket timeout in milliseconds. | 60,000 (60 seconds). | 1,000 – 30,000 (1 second to 5 minutes). |

### SMTP Options

This functionality has been deprecated.

### Statistics Options

This functionality has been deprecated.

### Backup Options

| Option Name | Description | Default Value | Possible Values |
| --- | --- | --- | --- |
| config.Backup | Various configuration options controlling aspects of the automatic backup of the management database. <br><br> Note: The management backup is saved as an archived Mongo database dump. <br><br> To restore it, run `mongorestore --gzip --archive=<backup_file>.tar.gz` <br><br> For more information on the `mongorestore` command, please refer to the [MongoDB user guide](https://www.mongodb.com/docs/database-tools/mongorestore/). |  |  |
| config.Backup.backupPath | The directory path where backups should be written. | `/var/opt/nvmesh/backups` | A valid directory path name writable by the excelero user. |
| config.Backup.dailyBackupTime | The time of the day that should be considered the daily backup time, i.e., for which the hourly backup becomes the daily backup. | "00:00" (Midnight). | Any 24-hour time value. |
| config.Backup.dailyRotationThreshold | The number of daily backups to keep before the oldest is deleted. | 30 | Integers |
| config.Backup.hourlyBackupInterval | Frequency of database back up in hours. | 1 | Integers |
| config.Backup.hourlyRotationThreshold | The number of hourly backups to keep before the oldest is deleted. | 36, i.e., 1.5 days of hourly backups. | Integers |

## Management Scalability

Management uses NodeJS for front-end HTTP/HTTPS and Mongo as a backend database. To achieve high availability, install Mongo in a replica set configuration, with a minimum of 3 replicas. A minimum of 2 instances of NodeJS should be configured for high availability.

The suggested high availability (HA) configuration is to have 3 redundant instances. Additional instances may improve performance.

With highly available Mongo, i.e., in a replica set configuration, data written into any instance is replicated to others using majority-based commits ensuring that upon failover, all data will be retained.

### Mongo Replica Sets

A Mongo replica-set can be used to improvement Mongo and hence management robustness, i.e., availability and durability. Typically, 3-way replication across 3 servers, a primary and 2 secondaries is recommended.

NVMesh support recommends using `/etc/mongod.conf` to set up replica sets. See [Mongo’s Guide for Deploying a Self-Managed Replica Set](https://www.mongodb.com/docs/manual/tutorial/deploy-replica-set/) for detailed instructions.

Once Mongo replica sets are working properly, configure management to interact with the mongo cluster, see [Mongo Connection Options](#Mongo_Connection_Options).

### Load Balancers

Employing load balancers for NVMesh management setups is possible technically. However, it is not essential for the following reasons, unless there are security concerns and a load balancer is seen as a means for preventing DDoS attacks or similar:

- Non-management NVMesh components communicate with management via Kafka and so this is irrelevant for them.

- Managements communicate with each other using a full mesh.

- The NVMesh CLI communicates with all endpoints based on the configuration defined in `/etc/nvmesh/nvmesh.conf` which should include all managements.

- The GUI is not heavily used for production environments. Access to the GUI is generally not open externally rather requires an SSH tunnel and so a load balancer setup will be complex.

However, should a load balancer still be required, management provides an endpoint for confirming the endpoint is running properly, at the `isAlive` REST API, i.e., as follows:

```
http(s)://{listener_addr}:{listener_port}/isAlive.
```

### Kafka Brokers

Management communicates with the other NVMesh components using Kafka. It is recommended to employ multiple Kafka brokers for high availability and scalability. Topics will be distributed across them dynamically.

# Monitoring

There are various methods for monitoring NVMesh clusters. The simplest methods are via the GUI, the CLI and extensive statistics data available via /proc. The recommended method is to use NVMesh exporters and send data to Prometheus.

## Dashboard

The NVMesh dashboard provides a snapshot of overall cluster status. It comprises 3 areas, as follows.

<div align="center">
<img src="./ug-media/image35.png" style="width:7.24306in;height:3.94861in"
alt="A screenshot of the NVMesh dashboard." />
</div>

### Summary-level Status Dashboard

The Volumes, Targets, Clients and Drives gauges provide a summary level status of the entire cluster.

Click on the gauge name to open the relevant element’s screen.

Each gauge is divided into health categories. Healthy elements are in the standard functional state. Elements with an alarm are functional but have some impediment that should be handled to revert to the healthy state. Critical elements are not functioning properly.

**<u>Note:</u>** All management statuses are as perceived by management and are based on asynchronous reports that may be stale or imprecise due to network or load issues. Validating state at the end points is always recommended for triage.

Clicking on a health count for a specific element leads to the relevant screen for the element filtered by the health category chosen.

### Capacity

The capacity sub-section provides the following graphical elements:

- The "Allocation Chart" presents a single gauge of the effective space available, i.e., the amount of space used for redundancy and the remaining free raw space in the system.

- The four "Largest Volumes" in the system.

- The "Drive Space Allocation per Target" depicts all targets and their current color-coded free space, as follows:

|    Color    | Used Space |
| :---------: | :--------: |
| Light Green | Up to 20%  |
|    Green    |  20 – 40%  |
|   Yellow    |  40 – 60%  |
|   Orange    |  60 – 80%  |
|     Red     |  Over 80%  |

### Alerts

Recent non-acknowledged alerts are presented in this section.

A filter row facilitates searches.

## Volume State

A volume may be in various states. The current state is reflected in the GUI using two columns, Action and Status.

### Action

The Action column reports task-related or action-related information on a volume. For the most part, the action reported is the most recent uncompleted action invoked.

Exceptions are "Rebuild Required" and "Init Encryption Required" that call for administrator action. The following table describes this information in more detail.

| **Lifecycle Stage** | Action | Description |
| --- | --- | --- |
| **Allocation** | **Initializing** | An administrator has allocated a new volume. <br><br> Not all relevant targets, i.e., those with drives on which the volume has been allocated, have acknowledged that they have begun the process of creating the volume. |
| **Allocation** | **Extending** | An administrator has increased the size of the volume. <br><br> Not all relevant targets have acknowledged that they have begun the process of extending the volume. <br><br> This is the equivalent of the Initializing state for new volumes. |
| **Allocation** | **Init Encryption Required** | An administrator has allocated a new encrypted volume. The volume has completed initializing. <br><br> The administrator should initiate encryption initialization. |
| **Allocation** | **Initializing Encryption** | An administrator has allocated a new encrypted volume and initiated encryption initialization, which is still in process. |
| **Steady State** | **Booting** | The volume is not functioning normally. Clients will not be able to perform IO operations on this volume. <br><br> The reports received from the relevant targets indicate that some of them have not yet completed the boot stage for this volume after a restart of the target. |
| **Steady State** | **Adding Passphrase** | The administrator has added a passphrase to the volume. The addition is currently in process. |
| **Steady State** | **Rotating Passphrase** | The administrator has rotated a passphrase for the volume. The rotation is currently in process. |
| **Steady State** | **Deleting Passphrase** | The administrator has deleted a passphrase from the volume. The deletion is currently in process. |
| **Repair** | **Rebuilding** | The volume is available for IO operations from clients. The volume has some segments of data that may not be fully up to date with recent writes into the volume and these are currently in the process of being synchronized or rebuilt. There are enough drives available for the entire volume data at the requested redundancy level. Once all synchronization is complete, the volume should transition to the Online status. <br><br> A progress bar roughly tracks the rebuild. The progress should be considered an estimation. |
| **Repair** | **Rebuild Required** | The volume has some segments of data that were on drives that have been evicted whether by an administrator or automatically. <br><br> Replacement drive space has not been allocated by management yet. <br><br> To begin the allocation of drives for rebuild, see [Drive Failure and Replacement](#drive-failure--replacement). |
| **Repair** | **Marked for Rebuild** | The volume has some segments of data that were on drives that have been evicted whether by an administrator or automatically. <br><br> Replacement drive space has been allocated by management. <br><br> Some of the relevant targets have yet to acknowledge the replacement. |
| **Deletion** | **Marked for Deletion** | An administrator has deleted the volume. It should not be available for IO operations from clients, as detaching all clients is a pre-requisite to volume deletion. <br><br> Some of the relevant targets have yet to acknowledge the deletion. |
| **Deletion** | **Deleting** | An administrator has deleted the volume. It should not be available for IO operations from clients, as detaching all clients is a pre-requisite to volume deletion. <br><br> All relevant targets have acknowledged that they have begun the process of deleting the volume, but not all have completed it yet. |

### Status

The Status column reports the most recent status of a volume based upon reports from the targets. To see more detailed information on an individual volume and the status of the components that it is comprised of, click on the volume name to open a diagram of the volume. Each element has its own Status field shown in a modal box that appears when hovering over it with the mouse.

The following table describes the volume statuses.

| Status | Description |
| --- | --- |
| **Online** | The volume is functioning normally and should be available for IO operations from clients. <br><br> It has no known availability issues per the last report from targets which store data for this volume. |
| **Offline** | The volume is not functioning normally. Clients will not be able to perform IO operations on this volume. <br><br> It has known availability issues per the last report from targets which store data for this volume. |
| **Degraded** | The volume should be available for IO operations from clients. <br><br> The volume does not have enough active fully synchronized drives for the entire volume data at the requested redundancy level. However, the active synchronized drives do have the ability to provide all volume data that is up-to-date and therefore IO is operational. |
| **Unavailable** | There are missing reports from relevant targets that make it impossible to determine the current state of the volume. |

### Health

The volume’s health is conveyed by the color of the volume, as described in the following table. In general, the health is a simpler summary of its state and action based on severity.

| Color | Health | Description |
| --- | --- | --- |
| **Green** | **Healthy** | The volume is functioning normally and should be available for IO operations from clients. |
| **Yellow** | **Alarm** | The volume is functioning normally and should be available for IO operations from clients. <br><br> Health is set to alarm when it is marked as requiring a rebuild, is rebuilding or is in a degraded mode. |
| **Red** | **Critical** | The volume is not functioning normally and should not be available for IO operations from clients. <br><br> Health is set to critical when the status is offline, booting or unavailable. |

### Volume’s per-Client State

When a volume is attached to a client, the volume state can differ between clients and differ from that reported by the target to management. Most often the root cause for the differences are reservation modes and networking issues.

Clients send status reports on their attachments to management. This is reflected in the Clients section of the GUI in the Volume Attachments column. For each client, there is a list of the volumes to which it is attached. A green background color indicates that IO is enabled or functional, while a red one indicates that IO is disabled. When IO is disabled, follow the instructions at [Volume Status Example](#volume-status-example) to get more information on the specific client’s status for the specific volume.

## Client State

The current client state as perceived by management is reflected in the Clients table.

The Volume Attachments column lists the volumes that are supposed to be attached to the client. The background color is used to reflect state.

The Recovery Attachments column presents the volumes attached to a client for volume recovery or encryption purposes, as a "hidden" volume attachment that is not available for regular IO operations.

The Attachments Actions column shows operations that have been initiated for the attachment or detachment of volumes that are still in process.

- A green background indicates a successful attachment with IO enabled for the volume on this client specifically.

- A red background indicates that the attachment itself failed or that IO is currently disabled.

After configuration changes, for instance via configuration profiles, it is necessary to restart the client to apply the changes. When the system recognizes this situation, a yellow warning triangle will appear to the left of the configuration profile name in the Config Profile column.

The Health column provides information on the livelihood of the client. Hovering over the icon in the column will provide additional info.

- A checkmark within a green circle indicates normal functioning.

- An exclamation mark within a yellow triangle indicates that the client is probably running, but reporting an error condition, such as a volume with IO disabled or that it has not been communicating with any management for more than 2 minutes, but less than 5.

- An exclamation mark within a red circle indicates an error state. Either the client has been shut down, or it has not communicated with any management for over 5 minutes.

## Target State

The current target state as perceived by managements is reflected in the Targets table.

The state of a specific target can be viewed by clicking on the relevant target ID, which is the target’s identifier generated from its hostname.

The Drives and NICs columns enumerate the number of each of these elements.

**<u>Note:</u>** when a NIC name is changed or a drive is swapped out, the removed or deprecated elements may need to be deleted manually if the system is unable to determine that they are not simply missing.

The Zone column specifies the logical zone in which this target has been placed.

The Leader column shows the TOMA RAFT-elected leader per the TOMA report from that target.

After configuration changes, for instance via configuration profiles, it is necessary to restart the target to apply the changes. When the system recognizes this situation, a yellow warning triangle will appear to the left of the configuration profile name in the Config Profile column.

The Health column provides information on the livelihood of the target. Hovering over the icon in the column will provide additional info.

- A checkmark within a green circle indicates normal functioning.

- An exclamation mark within a yellow triangle indicates that the target is probably running, but reporting an error condition, such as a missing NIC or Drive or the TOMA component’s status may be unavailable as it has not reported for a while.

- An exclamation mark within a <span class="mark">red</span> circle indicates an error state. Either a target component has been shut down or failed. For instance, the TOMA component’s main process may have terminated, or it has not communicated with any management for over 5 minutes.

When viewing a specific target, its inventory is presented, NICs and drives. Missing elements will be marked as such. The drives are grouped as follows:

| Drive Group | Description |
| --- | --- |
| **Parity Ready** | Ready for use for any type of volume. |
| **Concatenated, Striped and/ or Mirrored** | Ready for use for the volume types in the header, i.e., for non-protected and mirrored volumes but not erasure-coded ones. |
| **Not formatted for NVMesh** | Available for use but have not been formatted for NVMesh. |
| **Excluded** | Not available for use as they are in use by another application or excluded by the user. |

## PROC-fs (/proc) Statistics

NVMesh provides elaborate raw statistics in /proc that can be integrated with third-party monitoring software. These are used by the NVMesh exporter that sends data to Prometheus systems.

**<u>Note:</u>** The information provided via this interface is prone to change or be enhanced without notice and should not be considered a formal future-proof API.

### Client Volume Statistics

Filename: `/proc/nvmeibc/volumes/<volname>/iostats`

Provides a table with a column for each IO type, reads, writes and trims. Each row is a different metric. The volume uptime precedes the table, which can be used to calculate rates, as show in the following example:

```
[root@nvme80 16:34:00 ~]$ cat /proc/nvmeibc/volumes/vjq01-0/iostats
up_time=21.8[sec]
*               |                READ               WRITE                TRIM
num_ops         |                  39                   0                   0
size            |             1138688                   0                   0 [bytes]
total_latency   |              4791.1                 0.0                 0.0 [usec]
total_execution |            717542.8                 0.0                 0.0 [usec]
latency^2       |          82104077.3                 0.0                 0.0
worst_latency   |            712810.0                 0.0                 0.0 [usec]
worst_e2e       |                 713                   0                   0 [msec]
worst_e2e_enbl  |                   0                   0                   0 [sec]
format_version: 1	time: 2025-09-17 13:34:12.216
```

There are multiple latency statistics covering different parts of the end-to-end execution of IO. The steps involved are:

1. An IO request is received by the client’s kernel module.

1. Optional: IO is delayed due to IO throttling. This happens when the maximum number of outstanding IOs for this core has been reached.

1. Optional: There are attempts to execute the IO, but they fail. For instance, a drive was removed or a network connection lost during execution of the IO.

1. Optional: IO execution is delayed as IO on the volume is disabled. For instance, one of the drives of a non-mirrored volume is mandatory but is unreachable.

1. Successful execution

1. Return success to kernel

| **Statistic** | Description |
| --- | --- |
| <nobr>**num_ops** | The number of IOs that the upper kernel layers issued for this volume. <br><br> This number may be different than what the user application issued. For instance, the application may generate 1 MB writes, which the kernel may then split into 8 writes of 128 KB. In this case, this statistic will count 8 operations. |
| <nobr>**size**</nobr> | The total size of all IOs issued. <br> <div align="center">**Note:** Average IO size = size / num_ops</div> |
| <nobr>**Latency - total_latency / worst_latency**</nobr> | This includes the time required to perform steps 5 and 6 above, i.e., the good path execution. <br>< Total is the sum for all operations, while worst is the highest latency. |
| <nobr>**Execution – total_execution**</nobr> | This includes the time from when throttling concluded and execution attempts began, i.e., steps 3 through 6. <br> It is the total across all IOs. |
| <nobr>**End-to-end – worst_e2e**</nobr> | This statistic includes all steps, i.e., provides the worst-case execution from when the IO reached the NVMesh kernel layer. |
| <nobr>**End-to-end + good-path – io_e2e_enbl**</nobr> | This statistic includes all steps except 3 where IO fails due to IO being disabled, so it includes the good path including throttling, which is a reasonable proxy for what the application will experience under IO enabled or good-path conditions. |
| <nobr>**Latency^2**</nobr> | Latency squared is the same statistic as latency but squared per IO to facilitate calculating variance. |

### Client and Target Drive Statistics

A volume’s data is stored on one or more drives. Clients attach to the drives to implement accessing the volume. Statistics for the drives used by the clients aggregated across all relevant volumes are available in `/proc/nvmeibc/disks/<drive_id>/iostats.json` and in aggregate for all targets in `/proc/nvmeibs/disks/<drive_id>/iostats.json`.

This file has a JSON-formatted structure with the statistics of IO for this client with this drive, for all volumes using it. Statistics are broken out by IO size.

The JSON structure is human-readable with the following comments:

- "ops" is the number of operations sent to the volume by the kernel layers. This might be different than the application layer as described in the previous section.

- "size" and "latency" are running totals enabling measuring rates.

- "recovery_read" and "recovery_write" operations are reads and writes issued to rebuild volumes and are separated to avoid tainting application usage statistics.

- "overeager" is an internal term for the now deprecated RDDA functionality.

## PROCFS (/proc) Monitoring Information

Monitoring information, largely debug information, can be found under multiple `/proc` directories. Following are some basic non-comprehensive descriptions.

**<u>Note:</u>** As this information is intended primarily for support purposes, it is subject to change. It should not be considered a steady system API.

### General Notes

- Root permissions may be needed.

- Some items are write-only. Do not write into them without knowing what they do as there may be significant repercussions.

- Each sub-directory represents an NVMesh module and has a version entry reporting module version information in a JSON format.

### /proc/nvmeib

This directory contains some general info common to clients and targets.

| PROCFS entry | Description |
| --- | --- |
| **alloc_diag** | Used by developers to diagnose allocation issues. |
| **rdma/qp_error** | Used by developers to simulate RDMA QP errors. |
| **tracer/\*** | A variety of endpoints used to monitor the nvmeshtracer service’s behavior and configuration. |

### /proc/nvmeiba

The nvmeiba module is a shim layer between the operating system and the client maintaining block device pointers enabling hot upgrades to the underlying client without having to tear down the block devices. The monitoring information here can be useful for diagnosing hot upgrade issues.

| PROCFS entry | Description |
| --- | --- |
| **status & status.json** | Provide status information on the block devices maintained, including path, number of opens & processes connected to them and outstanding IO counts (pending). |
| **users** | A subset of the status above focusing on opens & processes. |

### /proc/nvmeibc

This directory is for the main client module, nvmeibc.

| PROCFS entry | Description |
| --- | --- |
| **cflags** | Presents the CFLAGS with which the module was compiled. |
| **cli/cli** | Used to implement communication between user space scripts and the kernel module. There are some special commands that can be used to work around issues or augment behaviors, such as zero’ing statistics. <br>Warning: Writing to this file may cause unexpected results. <br>Any use should be verified with NVMesh support. |
| **dict_sign** | Is a signature of the dictionary used for the tracer. |
| **disks** | Is a directory with a sub-directory for all disks or drives the client is interacting with to implement any of the attached volumes. <br> See the table below for the entries per DRIVE-ID within that directory. |
| **echo** | For developers. |
| **error_tags_info** | Provides a JSON-formatted list of client error counters, aggregated across all CPUs. Each entry includes the error name, the source file and function where it is defined, and the accumulated count. |
| **instctls** | For user-space programs to control additional instances for multi-instance client functionality, which is deprecated. |
| **inst_list.json** | Provides basic information for each instance of the client in use, related to multi-instance client functionality, which is deprecated. |
| **jam/\<DRIVE-ID>** | In-depth information on journal area management per drive. |
| **mcs/mcs** | Used for communication with management. <br>**Warning:** Reading or writing from this entry may cause unexpected results. |
| **net/\<NIC-ID>/ <br>iostats.json** | Provides statistics on NIC usage per IO operation type. It is similar in format to other client statistics except aggregated by NIC. |
| **nics.json** | Provides state information on the NICs used by the client. |
| **rsrc_info.json** | Provides state information on the networking QPs used. |
| **status** | A high-level summary of a lot of client information. |
| **status.json** | A reduced JSON version of status. |
| **version** | Contains software version information. |
| **volumes** | A directory with a sub-directory per volume as described in a table below. |

| Per "DRIVE-ID" entries | Description |
| --- | --- |
| **cmds** | Implementation of IO to a volume may require one or more disk commands. <br>This entry provides information on the IO commands sent to this drive since last connecting to it. |
| **counters** | Internal counters used to help diagnose incorrect behavior or performance issues. |
| **inj_err** | A mechanism developers use to inject artificial errors for debugging error paths. |
| **interrupts** | A table of interrupts related to this drive, broken down into source, send and recv. |
| **ioch** | A subset of the status focusing on (deprecated) RDDA IO channels, communication paths to the drives. |
| **iostats & iostats.json** | See Client and Target Drive Statistics. |
| **nrch** | A subset of the status focusing on the non-RDDA IO channels, communication paths to the drives. |
| **qps** | Detailed statistics for every QP broken down by operation and by core on which they were executed. |
| **rediscover** | Writing 1 into this file forces the client to disconnect from the drive and "rediscover" it. |
| **status & status.json** | Multiple status entries regarding the drive, including on which target it was last seen, target software information, NICs used to communicate with it for the client and target, communication paths used and statistics on them, journal information for the drive and locking statistics. |

| Per "VOLUME-ID" entries | Description |
| --- | --- |
| **blob.txt** | Mostly information related to the volume state reported to management. |
| **client_processes** | Processes connected to and file opens on this volume’s block device. |
| **flow_cntr.json** | Counters for various flows that recover volume data. |
| **iostats & iostats.json** | See Client Volume Statistics. |
| **io_throttle** | Information on current IO throttling. |
| **profiling & profiling.csv** | Used by developers for performance debugging. |
| **recov_stats** | Statistics on recovery processes. |
| **status & status.json** | A human-readable and JSON format providing key information about volume state with a strong emphasis on helping understand why IO may be disabled for a volume. |

### /proc/nvmeibs

This directory is for the target module, nvmeibs.

It also contains a multitude of TOMA information.

| PROCFS entry | Sub-dir Entry | Description |
| --- | --- | --- |
| **arnic_prefer** |  | Used to control a mechanism for defining NIC preference on a per drive basis. |
| **clients** |  | This is a directory. It contains a sub-directory per client connected to this target with a single file, `qp_stats`, that provides statistics per qp. These may not be collected unless explicitly turned on. <br>The directory also has a `summary` file with a line per client that the GID with which it is connected to the target and the per-client processes running on this target, which can be useful for debugging CPU-hungry processes. |
| **disk_info** |  | A high-level overview of the drives and NICs as seen by the target and used for reporting to management. The format is pipe (‘ \| ’) separated. <br>Note: The same info appears in other PROCFS files in an easier to consume format. |
| **disks** |  | Basic monitoring info and statistics per drive. |
|  | **iostats.json** | See [Client and Target Drive Statistics](#client-and-target-drive-statistics). |
|  | **nvme_qps** | Provides read and write counts per NVMe queue. |
|  | **qps** | Detailed statistics for every client QP broken down by operation and by core on which they were executed. |
|  | **status** | A high-level summary of disk state, statistics and clients connected in YAML format. |
| **locks.\<DRIVE-ID>** |  | Used by TOMA to interact with a drive’s locks. <br>Warning: Reading or writing from this entry may cause unexpected results. |
| **log\<DRIVE-NUM>** |  | Can be used to read the NVMe log of a drive. <br>Use the smart\<drive-num> procfs file with the same drive-num to find the drive’s NVMe serial id or other information. |
| **mcs/mcs** |  | Used for communication with management. <br>Warning: Reading or writing from this entry may cause unexpected results. |
| **nic_gids/\<GID>.csv** |  | Describes the NIC GIDs used by the target. |
| **nics.csv** |  | An alternative high level overview of the NICs used by the target. |
| **nic_stats/ <br>\<NIC-ID>/ <br>iostats.json** |  | Provides statistics on NIC usage per IO operation type. It is similar in format to other client and target IO statistics except aggregated by NIC on the target side. |
| **nvmeof_disks** |  | The NVMe-over-Fabrics drives that the target can access. This can also be used to inform the system to use them, typically via an NVMesh user-mode script. <br>Note: This functionality is not formally supported in this version. |
| **partitions.csv** |  | Drive partition information. |
| **rsrc_info.json** |  | Drive resources used per client. <br>This is less relevant without the deprecated RDDA capability. |
| **serjios.csv & serjio/\<DRIVE-ID>/\*** |  | A target component named "serjio" manages erasure-coded volume journal areas. <br>These entries are used to monitor, debug and optimize its behavior. In rare cases, it may be possible to perform workaround operations here. <br>Warning: Do not attempt to do this without NVMesh support direction. |
| **smart\<DRIVE-NUM>** |  | The files provide SMART output per drive enhanced with basic drive info such as PCI slot and serial ID. <br>It can be useful for locating a drive, using the PCI address and the "dmidecode" utility, for hardware maintenance. <br>SMART information can also be useful for preventive drive replacement with low endurance left. |
| **toma_clients & toma_servers** |  | Used for TOMA-target communication. <br>Warning: Reading or writing from this entry may cause unexpected results. |
| **toma_status** |  | Provides TOMA status through multiple entires. <br>The same information can be snapshotted by sending the TOMA process the SIGUSR1 signal, e.g., "killall -SIGUSR1 nvmeibt_toma". <br>For most toma_status items, the most pertinent information is on the node on which the TOMA leader is running. <br>Note: sometimes there is insufficient buffer space for the entire data through PROCFS, especially when there are many volumes. In this case, the snapshot provides the entire information. |
|  | **all & all.json** | A concatenation of all the other status entries in regular TOMA format or in a JSON format. |
|  | **bdev** | Block devices (volumes) status as seen by this specific TOMA. |
|  | **cfg** | Volumes configuration as received from management. |
|  | **disk** | Status of drives managed by all TOMAs in the zone. |
|  | **dseg** | Status of all volume segments managed by all TOMAs in the zone. |
|  | **leader** | The RAFT leader, which makes cluster-wide volume status decisions. |
|  | **local_disk** | Status of the local drives, per TOMA, including partitions, which may indicate why a drive has been automatically evicted by management. |
|  | **mem** | Memory structures used, which can be useful for memory leak detection. |
|  | **raft** | Status of the RAFT protocol used to choose a leader among the TOMAs in the zone. This will also show who the leader is if there is one. The status on the leader itself can be used for diagnosis of inter-TOMA network communication issues. |
|  | **rdma** | Status of communication channels to other TOMAs in the zone. |
|  | **recover** | Status of volume recovery processes initiated or managed by this TOMA. The initiator is normally the target with the up-to-date data. |
|  | **topo** | The system topology or volume statuses. |
| **version** |  | Contains software version information. |

## Management State

The GUI About screen provides data on component versions for the management itself, such as management build, NodeJS version, Mongo version and Mongo replication state.

Additional reports on the management state of the entire cluster can be found in the monitoring menu sub-section, as follows:

| Sub-menu | Description |
| --- | --- |
| Management Cluster | Presents the management entities that have communicated with this Mongo database instance with the following data points: <ul><li>IP and port numbers being listened on.</li><li>Whether SSL is used for access.</li><li>Software version.</li><li>Successful incoming and outgoing connections to peers.</li></ul> |
| MongoDB | Presents the state, health and role of the MongoDB instances making up the Mongo cluster used as the primary document database for management. |
| Kafka | Presents the IP and port numbers used by Kafka instances and marks the current leader. |
| Upgrade Agents | The upgrade agents are considered extensions of management running on the endpoints as well. Healthy upgrade agents are critical for managed non-disruptive upgrades (mNDU). <br>The upgrade agents provide information regarding the node characteristics that the NVMesh software needs to be aligned with, OS, kernel and OFED as well as the currently installed versions. |

# Logging

NVMesh has 3 logging mechanisms, standard system logs, NVMesh management logs and NVMesh binary tracing. This section provides an overview of each and the means for controlling the granularity and severity of information sent to each. If there are issues in the system, triaging may require increasing the amount of logging generated. If the amount of logging is excessive, these means can help reduce the logging output.

## System Logs

All NVMesh components log info, warnings and error events to the standard system logs. The following table can help attribute the log to a component.

| NVMesh Component Name | System Log Name | Comments |
| --- | --- | --- |
| Management | nvmeshmgr |  |
| TOMA | nvmeibt_toma |  |
| MCS | managementCM |  |
| Management Agent | managementAgent |  |
| Kernel modules | kernel: | For all kernel modules other than the atom modules (nvmeiba), the format of the message will begin with the severity, timestamp, core number and then a function name. The function’s prefix is often the module name. <br>The atom module traces begin with a shorter timestamp and then a function name. The function’s prefix is usually nvmeiba. |
| Tracer | nvmeshtrace |  |

NVMesh kernel module logs will also be available via the dmesg command line utility.

For RHEL-compatible distributions, system logs are typically stored in `/var/log/messages*`.

For Ubuntu, they are usually stored in `/var/log/syslog`.

For both operating systems, the log messages should be available also via journalctl.

System administrators may change the typical system logs definitions and manage them differently including separating them to distinct files. Consult with a local system administrator if they are not available.

**<u>Note:</u>** On some RHEL-compatible distributions, the rsyslog package may be optional. If `/var/log/messages` is not present, this package may not be installed.

The default logging levels are info, warning and error. To configure logging severity:

- Management logging levels can be set dynamically as described via General Settings in the Logging sub-section.

- For nvmeshcm, use the variable `MCS_LOGGING_LEVEL` in `/etc/nvmesh/nvmesh.conf`. Set it to one of the following `DEBUG`, `INFO`, `WARNING` or `ERROR`. Alternatively, set it to `VERBOSE` and then use `MCS_LOGGING_VERBOSE_TYPES` to control the specific verbose functionalities to debug at a verbose level. It is recommended to consult with support in this case.

- For nvmeshagent, use the variable `AGENT_LOGGING_LEVEL` in `/etc/nvmesh/nvmesh.conf`. Set it to `DEBUG` to increase the logging level.

Targets and clients are mainly implemented using kernel modules. Most of their logs and those of the TOMA are maintained by the binary tracing mechanism. `INFO`, `WARNING` and `ERROR` traces are also sent to the system log.

## Management Logs

Managements maintain logs of cluster-wide operations, not only management operations. These are not logs of the management software layer itself. They are accessible via the management’s GUI from the Logs subsection of the Maintenance section.

These logs are divided into `INFO`, `WARNING` and `ERROR` levels. Warning and error logs also generate alerts, see [Alerts](#alerts) for more information on the contents of such logs.

A non-comprehensive list of info log topics follows:

- Users logging in and out.

- Volume lifecycle: creation, extension, going offline, into a degraded state or back online, undergoing rebuild and deletion.

- Target lifecycle: detection, going offline or online, health issues and removal.

- Drive lifecycle: detection, formatting and implicitly being put into the drive pool, being removed from it, going offline or online, health issues, eviction and removal.

- NIC lifecycle: detection and going offline or online.

- Client lifecycle: detection, volume attachments and detachments and removal.

- Volume Provisioning Group lifecycle: reservation of drive space for a VPG.

## Binary Tracing

NVMesh "binary tracing" enables fast lightweight highly controllable logging to enable tracing "customer-side" issues with ease. Out of the box, some binary traces are kept only in memory while others are also stored in persistent storage asynchronously. A drive sync is done every 2 seconds by default and is controlled by the `trace_dump_timeout` module parameter for the kernel module `nvmeib_common`. Data is stored in the `/var/log/nvmesh/trace_daemon` directory. If this directory is on a low-endurance drive, such as a typical boot drive, it may be recommended to use an alternative one.

Logs sent to binary tracing of levels `INFO`, `WARNING` or `ERROR` are also sent to standard system logs.

All binary traces are kept in memory and occasionally dumped to persistent storage. By default, more granular logs of lower severity than info-level logs, often called "trace-level logs" are also stored using this mechanism.

The following module params control tracing levels related to code within that module. Each trace is controlled by a single module parameter. See [NVMesh Module Params](https://github.com/NVIDIA/nvmesh-documentation/blob/3.4.0/NVMesh%203.4.0%20Module%20Params.md) for more details. Traces with a severity level higher than the relevant module param will not be emitted to the tracer.

| Module | Parameter | Comments |
| --- | --- | --- |
| nvmeib_common | tracer_debug_level | As a reminder, nvmeib_common has code common to both client and target, mostly network oriented. |
|  | cm_ephemeral_debug_level | Sets the ephemeral tracing for the RDMA connection manager (CM). |
| nvmeib_common_public | tracer_wq_debug_level | Work-queue related traces, which are also in client and target common code. |
| nvmeibc | tracer_debug_level | The main client kernel module. |
|  | topology_debug_level | For client traces related to the volume topology topic in the client. <br>This code’s main function is determining whether IO should be enabled or not. |
|  | goodpath_debug_level | For client traces related to implementing good path IO. |
|  | goodpath_locks_debug_level | For client traces related to implementing the good path locking operations. |
|  | goodpath_transport_debug_level | For client traces related to implementing networking operations. |
| nvmeibs | tracer_debug_level | The main target kernel module. |
|  | goodpath_debug_level | For target traces related to implementing good path IO. |

Severity levels used for the module parameters are described in the following table.

| Severity | Description |
| --- | --- |
| 1 | Only errors will be kept by the binary tracer and forwarded to the syslog. |
| 2 | Only errors and warnings will be kept by the binary tracer and forwarded to the syslog. |
| 3 | Errors, warning and info level traces will be kept by the binary tracer and forwarded to the syslog. |
| 4 | Errors, warning and info level traces will be kept by the binary tracer and forwarded to the syslog. <br>Trace-level messages will be kept by the binary tracer only. |
| 5 | Errors, warning and info level traces will be kept by the binary tracer and forwarded to the syslog. <br>Trace-level and debug messages will be kept by the binary tracer only. |
| 6 | Errors, warning and info level traces will be kept by the binary tracer and forwarded to the syslog. <br>Trace-level, debug and fine messages will be kept by the binary tracer only. |

When changing these parameters, it is advised not to go below debug level 3, as setting debug levels 1 or 2 may complicate troubleshooting issues.

To change the memory and drive footprint, configure the following options in `/etc/nvmesh/nvmesh.conf`:

| Domain | Parameter | Description |
| --- | --- | --- |
| Kernel Modules | TRACE_BUFS_PER_LOG | Specifies the number of 4K buffers saved to a single trace file. The default value of 4096 translates into 16 MB files. It is not advised to modify this value. |
|  | TRACE_MAX_LOGS | Specifies the number of files to keep. <br>The default value of 64 translates to 1 GB in total when `TRACE_BUFS_PER_LOG="4096"`. <br>The minimal value can’t be lower than the number of CPUs on the server and they will be ignored. To adjust the size of history on the disk, it is best to adjust this value only. |
| TOMA | TOMA_TRACE_LOG_SIZE | Specifies the size of a single TOMA trace file. <br>The default value of 48 translates into 48 MB files. This parameter can be between 1-200. |
|  | TOMA_NUM_OF_TRACE_LOGS | Specifies the number of files of history to keep. <br>The default value of 40 translates to ~1.8 GB in total when `TOMA_TRACE_LOG_SIZE="48"`. <br>This parameter can be between 1-100. |

For additional instructions on how to view binary traces and how to control their footprint, contact NVMesh support.

# Alerts

NVMesh alerts administrators on important topics. The alerts appear in the lower half of the dashboard screen in a table format. There are 2 levels of alerts, Warning and Error. Use the NVMesh tabular GUI to review the alerts. Administrators can acknowledge alerts to remove them from the main dashboard, either individually or using the Ack All button. Some alerts are acknowledged automatically by the system once the condition has been resolved. All alerts including those acknowledged can be seen in the Logs sub-section of the Maintenance section.

## Error Alerts

| Header | Message | ID  | Comments |
| ------ | ------- | --- | -------- |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |

## Warning Alerts

| Header | Message | ID  | Comments |
| ------ | ------- | --- | -------- |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |
|        |         |     |          |

# NVMesh Best Practices

Infrastructure choices, both hardware and software, settings and practices affect NVMesh performance, reliability and failover. This section provides various suggestions for different environments. However, it may not always be a sufficient substitution for technical support nor is every suggestion applicable to all situations.

## Performance Best Practices

The following section describes performance optimization best practices. It is highly recommended to follow these best practices to achieve the lowest IO latencies among others.

### CPU Interrupt Affinity and IRQ Balancing

IRQ balancing and NUMA affinity for NVIDIA NICs is an advanced topic. For more complete information, see the [NVIDIA MLNX_OFED Performance Tuning](https://docs.nvidia.com/networking/display/mlnxofedv24100700/performance+tuning).

The instructions below are general guidelines that apply to clients and targets using NVIDIA RDMA adapters, Ethernet or InfiniBand.

NVIDIA provides the `mlnx_affinity` and `mlnx_tune` tools for tuning adapter IO interrupts for optimal balance and NUMA-local affinity.

#### OS-Native IRQ Balancer

The OS-native IRQ Balancer service alone can often be sufficient.

You can verify the service is running with the systemctl command as follows:

```
root@nvme80 11:54:07 ~]# systemctl status irqbalance
● irqbalance.service - irqbalance daemon
   Loaded: loaded (/usr/lib/systemd/system/irqbalance.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2025-09-18 09:51:54 IDT; 1 weeks 0 days ago
     Docs: man:irqbalance(1)
           https://github.com/Irqbalance/irqbalance
 Main PID: 1652 (irqbalance)
    Tasks: 2 (limit: 407406)
   Memory: 2.3M
   CGroup: /system.slice/irqbalance.service
           └─1652 /usr/sbin/irqbalance --foreground

Sep 18 09:51:54 nvme80.mtl.labs.mlnx systemd[1]: Started irqbalance daemon.
```

If IRQ balancing remains an issue despite the service being active or performance is low, especially if one or more CPU threads are running at 100% during IO, then it is recommended to test using one of the previously mentioned NVIDIA tools.

Further investigation can be conducted by observing output from various tools such as the contents of `/proc/interrupts` or the output of `mpstat -P ALL 1` looking at the percentage of time spent in ’irq’ and ‘soft’. It is important to verify that the adapter interrupts are well distributed between the CPU threads. Additionally, ensure the list of CPU threads that are considered NUMA-local to the adapter aligns with those being used. Use of non-NUMA-local threads will require the use of the CPU interconnects (e.g., UPI or Infinity Fabric) and may impose additional latency reducing peak performance. Both NVIDIA tools are designed to assign NUMA-local for adapter IO. Determining which tools works best may require testing.

#### Mellanox Affinity (optional)

To enable mlnx_affinity by default, set `RUN_AFFINITY_USER` to yes in `/etc/infiniband/openib.conf`.

Then, disable the irqbalance service using `systemctl disable --now irqbalance`.

#### Mellanox Tune (optional)

In addition to being useful in setting interrupt affinity and balance, this tool can be used to report on NIC PCI status, system memory and CPU performance settings by running it without any parameters.

```
Mellanox Technologies - System Report

Operation System Status
Rocky Linux8.10
4.18.0-553.el8_10.x86_64

CPU Status
GenuineIntel Intel(R) Xeon(R) CPU E5-2620 v4 @ 2.10GHz Broadwell
Warning: Frequency 3000.0MHz

Memory Status
Total: 62.28 GB
Free: 49.06 GB

Hugepages Status
On NUMA 1:
Transparent enabled: always
Transparent defrag: madvise

Hyper Threading Status
ACTIVE

IRQ Balancer Status
ACTIVE

Firewall Status
INACTIVE
IP table Status
INACTIVE
IPv6 table Status
INACTIVE

Driver Status
OK: MLNX_OFED_LINUX-24.10-1.1.4.0 (OFED-24.10-1.1.4)
```

In the output above, note the `System info file:` mentioned at the end of the output. This file contains additional details and recommendations for further tuning the system, if applicable.

To apply the recommended `HIGH_THROUGHPUT` profile use the following:

```bash
mlnx_tune -p HIGH_THROUGHPUT
```

The output will be like the example, but the resulting output file will show that it has balanced and set processor affinity for the adapters in a listing at the end of the file.

### Tuned

Tuned is a daemon that uses udev to monitor connected devices and tunes system settings according to a selected profile.

Tuned is distributed on RHEL-compatible and some Ubuntu distributions with pre-defined performance profiles. It is part of the `tuned` package. For the best NVMesh performance results, choose the `latency-performance` profile. This setting should be applied to both clients and targets. To enable this profile on RHEL-compatible distributions, use this command, as root:

**To apply the profile:**

```bash
tuned-adm profile latency-performance
```

**To verify the current profile:**

```
[root@nvme80 12:32:32 root]# tuned-adm active
Current active profile: latency-performance
```

Changes made with `tuned-adm` should be retained across system reboots.

### NUMA Architectures

When setting up NVMesh on servers with non-uniform memory access (NUMA) based CPU architectures, it is important to consider the configuration of NICs and NVMe drives versus the NUMA layout. Since each CPU core has a specific, fixed throughput ceiling in its interconnect path to any one of the NUMA memory regions (nodes), and each CPU has a fixed number of PCIe lanes to NICs and NVMe drives, an IO issued from a CPU core to a specific NIC or NVMe drive may be impacted by the NUMA interconnect throughput.

IO sent from an application running across the CPU cores may suffer from inconsistent performance because some IOs will be issued against a local NUMA node, while other IOs will be issued against a remote NUMA node, with a much slower throughput.

Therefore, it is best to balance the memory accesses according to the specification of the CPU cores and NUMA nodes, the interconnect performance, and the performance specifications of the NICs and NVMe drives.

#### Review NUMA Topology

NUMA nodes and CPU core layout is provided by the lscpu utility.

The Portable Hardware Locality project provides the lstopo utility that lists the NUMA nodes, storage and network devices in a single output. This is typically provided in the hwloc package.

**On a RHEL-compatible system:**

```bash
yum install hwloc #### Additional output deleted for the manual
```

**To view the layout from the command line:**

```
[root@nvme80 12:52:17 root]# lstopo-no-graphics --ignore PU --merge --no-caches
Machine (62GB total)
  Package L#0
    NUMANode L#0 (P#0 31GB)
    Core L#0
    Core L#1
    Core L#2
    Core L#3
    Core L#4
    Core L#5
    Core L#6
    Core L#7
    HostBridge
      PCIBridge
        PCI 01:00.0 (SAS)
          Block(Disk) "sda"
      PCIBridge
        PCI 03:00.0 (Ethernet)
          Net "eno1"
        PCI 03:00.1 (Ethernet)
          Net "eno2"
      PCI 00:11.4 (SATA)
      PCIBridge
        PCIBridge
          PCI 07:00.0 (VGA)
PCI 00:1f.2 (SATA)
  Package L#1
    NUMANode L#1 (P#1 31GB)
    Core L#8
    Core L#9
    Core L#10
    Core L#11
    Core L#12
    Core L#13
    Core L#14
    Core L#15
    HostBridge
      PCIBridge
        PCI 83:00.0 (InfiniBand)
          Net "ib0"
          OpenFabrics "mlx5_0"
      PCIBridge
        PCI 84:00.0 (NVMExp)
      PCIBridge
        PCI 85:00.0 (NVMExp)
      PCIBridge
        PCI 86:00.0 (NVMExp)
      PCIBridge
        PCI 87:00.0 (NVMExp)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
```

The tree-like representation makes it easy to see which drives and NICs are co-located NUMA-wise.

Potential actions or configuration changes based on this information are:

- Physically move NICs to other PCI slots. This depends on the motherboard’s NUMA configurations.
- Physically move NVMe drives to other PCI slots. This depends on the motherboard’s NUMA configurations.
- Set applications to run on specific cores, using the `taskset` or `numactl` commands. to optimize the connectivity between the application IOs and the destination NICs and NVMe drives.

#### arnic_prefer

NVMesh has a mechanism for assigning preferred NICs to drives for manual optimization. When applied, IO operations for the drive will be sent via the specific NIC as much as possible. In network failure scenarios, other NICs could be used. This mechanism coined arnic_prefer is controlled via `/proc/nvmeibs/arnic_prefer`.

## Multi-Path Configuration

Multi-path network configuration may be required for bandwidth aggregation, failover, or both. It is common practice to leverage standard dual-port networking components to provide networking redundancy. Dual discrete single-port components are also often used. Due to performance considerations, multiple networking ports may also be deployed within a single device.

**<u>Note:</u>** Modern DPU-based configurations with HBN on the DPU may provide high availability and aggregation across the ports on the DPU efficiently hiding the complexity. In such cases, these recommendations still apply across DPUs. Dense storage servers may have multiple DPUs with separate HBN setups.

This section lays out recommendations for configurations that should enable NVMesh to achieve high performance and high availability in multi-port environments.

At the heart of current best practices are the following:

- Each node for which redundancy or high performance is required should have links to distinct switches.
- Switches should be interconnected.
- Prefer routed links.
- No spanning tree.
- Intelligent broadcast and multicast control.

<div align="center">
<img src="./ug-media/image36.png" style="width:7.24306in;height:2.60972in"
alt="A diagram exhibiting network high availability." />
</div>

### Ethernet & RoCE

#### General Considerations

For Ethernet networks with IP as a layer 3, it is recommended to implement multiple LANs of limited size and to perform full routing, i.e., ensure reachability from any endpoint to any endpoint. Alternatively, there is no need to have large layer 2 LANs connecting NVMesh endpoints whether physical or virtual.

From a single endpoint, connect the machine to different switches to avoid a single point of failure. Cross-switch LANs are in contradiction to avoiding spanning trees and large broadcasts scopes. Therefore, it is recommended to have different LANs per port, avoiding host-side bonding mechanisms in general, except for HBN-based solutions on the underlying DPUs as mentioned in a note above.

NVMesh leverages RDMA to achieve its extraordinary capabilities. NVMesh has been certified on devices employing RDMA-over-converged Ethernet (a.k.a. RoCE). RoCE v2 is certified and as a routable protocol fits well with the best practices described above.

On the network endpoints, NVMesh can leverage the ability to generally reach from any port to any port to generate multiple paths between clients and targets. This often requires configuring source-based routing at the endpoints so that packets will exit via the port associated with the endpoint address chosen for communication for both sending and receiving packets. The mechanism for doing this differs from operating system to operating system and is also dependent on whether the `NetworkManager` service is active.

Another aspect of RDMA is the usage of the RDMA CM (connection manager facility). Some NIC software stacks separate the settings for the RoCE protocol used by general RoCE messaging and the RDMA CM messaging.

#### ConnectX Ethernet Adapter Considerations

NVIDIA ConnectX series NICs are certified for NVMesh. Upon startup, NVMesh will ensure that the `RDMA_CM` mode is set to RoCEv2. If applying pre-installation tests to ensure RDMA and RDMA_CM functionality, setting this mode using the `cma_roce_mode` utility provided in OFED is recommended.

#### RDMA Atomic Limitations

ConnectX NICs normally perform RDMA atomic operations within the NIC address space on cached memory. Therefore, RDMA atomic operations performed on different NICs or performed on the local CPU are not globally atomic. NVMesh relies on RDMA atomic operations for efficiency. Therefore, even while enduring failover, it is imperative that all clients can reach the same NIC (either port) on a target machine.

It is recommended to have at least two dual-port target server NICs for path failover and NIC failure protection in multi-path environments. Routing should be configured so that clients can reach both ports of each NIC.

## Hardware Related Items

Choices regarding NVMe Drives, NIC cards and PCI slot or channel assignment can have a significant impact on system performance and system limitations. This subsection contains information specific to choices in hardware and hardware combinations.

### NVMe Drives

#### NVMe Devices

NVMesh should be operable with any drive that adheres to the NVMe 1.0 or higher specification.

### PCI Considerations

Use a command like the following example to obtain the relevant information for a PCI device after identifying its bus location or enumeration. 85:00.01 in the example.

**Enumerate NVMe and NVIDIA NICs (Mellanox):**

```
[root@nvme80 16:34:25 ~]# lspci | grep -i -e volatile -e Mellanox
83:00.0 InfiniBand controller: Mellanox Technologies MT27800 Family [ConnectX-5]
84:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller 172Xa/172Xb (rev 01)
85:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller 172Xa/172Xb (rev 01)
86:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller 172Xa/172Xb (rev 01)
87:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller 172Xa/172Xb (rev 01)
```

\*_Deeper dive into specific fields for the one of the NVMe drives_:\*

```
[root@nvme80 16:35:34 ~]# lspci -vvv -s 85:00.0 | grep -e LnkSta: -e LnkCap:
		LnkCap:	Port #0, Speed 8GT/s, Width x4, ASPM not supported
		LnkSta:	Speed 8GT/s (ok), Width x4 (ok)
```

- `LnkCap` refers to the device capabilities.
- `LnkSta` refers to its current status.

If the current status from `LnkSta` does not match device capabilities from `LnkCap`, then in most cases it won’t be possible to get maximum performance from the device.

The `Speed` field refers to the number of transfers per second possible and reflects the PCI generation negotiated between the device and the motherboard the slot used. The `Width` is the number of PCI links used which is also a result of motherboard-device negotiations.

Differences may stem from using an inappropriate slot or a motherboard issue.

#### NVMe Devices

NVMe devices used for data in contrast to boot have mostly converged to U.2, E1.S and E3.S form factors with 4 PCI lanes per drive, i.e., a width of 4. Form factor choices are largely dictated by rack, capacity, density and cost preferences.

M.2 devices are mostly used for boot drives. These are rarely used with NVMesh. They may have only 2 PCI lanes.

Dual-ported drives placed in dual controllers may also have just 2 PCI lanes per controller. NVMesh does not formally support dual controller setups currently.

#### NICs

Slot allocation for NICs should be considered in system design. Typically, a NIC should be inserted into a slot that at least matches its rating to enable it to achieve its maximum bandwidth. Most high-speed NICs utilize an x8 or x16 interface.

Care must be taken in system design and NIC selection regarding aggregate link speed of a card’s network ports. Sometimes the aggregate network link bandwidth exceeds PCI link capabilities for dual-ported NICs. Thus, when designing a system for maximum performance capability and multi-path or multi-port aggregation is desired for higher total bandwidth, utilizing multiple discrete single-port NICs may be preferable. This is not always the case. Hence, attention should be paid to NIC PCI capabilities when choosing specific NICs.

#### PCI Multiplexers

Some hosts employ PCIe multiplexers, usually to increase the number of NVMe drives that can be inserted into the system with each drive having connectivity for all its lanes. This will enable accessing each individual drive at its maximum bandwidth, but the system will not be able to utilize all drives at their maximum bandwidth simultaneously. System designs should be aware of these elements and limitations when estimating total potential performance.

#### Inter-CPU Connectivity

Typically, Intel UPI bandwidth and AMD Infinity Fabric, i.e. inter-CPU connectivity, is lower than PCI bandwidth for each CPU. Therefore, to optimize a system, it is recommended to avoid data crossing between CPUs as much as possible.

NVMesh has tuning facilities to help in this functionality.

**<u>Note:</u>** NVMesh does perform careful memory allocation to avoid memory access across the inter-CPU connectivity mechanisms, but some of this may be determined by server-specific BIOS settings.

## Network Considerations

Overall system performance may be governed by network limitations. This section gives some guidelines from the NVMesh perspective.

### Multi-switch Topologies

In multi-switch topologies, the overall bandwidth of the edge nodes may surpass that of the core of the network. This is largely dependent upon the network topology chosen. For example, traversing leaf or spine switches may be fine while traversing core switches may be undesirable due to network oversubscription.

Localizing volume access may help maximize performance. Target Classes and VPGs are tools for administrating rack-local or similar access patterns.

Sometimes dual switch setups are used for high availability. Due to RDMA atomic limitations, it is highly recommended to use dual-port NICs instead of multiple single-port NICs even though those may have more aggregate PCI bandwidth. In a dual switch setup, if they are interconnected, NVMesh will indiscriminately send traffic cross-switch and intra-switch. Therefore, the interconnect between the switches may become a performance bottleneck if not adequately provisioned or NVMesh tuned for this eventuality.

### Dedicated Storage Network

It is possible to separate NVMesh storage traffic from other traffic, even if the other traffic is RDMA. This can be done by selecting specific network elements for use with NVMesh via the appropriate NIC configuration options.

### InfiniBand

There are currently no InfiniBand-specific performance recommendations.

### Ethernet

For Ethernet, there are a variety of techniques to ensure proper RDMA, for example, Global Pause, Priority Flow Control (PFC), Early Congestion Notification (ECN), Lossy RoCE and most recently Spectrum-X.

NVMesh has been run extensively with all variants although performance may vary. Most recommended are Spectrum-X or a combination of ECN and PFC.

## Kernel Configuration

Various kernel configurations are recommended for both performance and stability with NVMesh.

### Serial Console

NVMesh uses the operating system’s central log, see [System Logs](#system-logs).

For some events, NVMesh may generate messages at a high rate. If a serial console is used, the central log should be configured to suppress sufficiently or the serial console should be run at a reasonable speed, i.e., running at a 9600 baud rate without suppression may be an issue. If the message rate is too fast for the serial console, soft and hard lockups may occur. The serial console is usually enabled from the kernel boot command line using `console=ttyS0` or similar. To increase the speed, use `console=ttyS0,115200n8`.

### Free Memory Space

The default operating system setting for free space left for the kernel is often too low. Increasing the value is often recommended, especially for clients running workloads with high parallelism, multiple threads with high IO depth, especially if this is done in parallel on multiple volumes.

If encountering operating system slowness in such situations, it is recommended to increase the value for `/proc/sys/vm/min_free_kbytes`.

To make the change permanent, use sysctl.

Insert a statement such as `vm.min_free_kbytes=<VALUE>` in a conf file at `/usr/lib/sysctl.d/`.

## Mongo Best Practices

Management relies on the Mongo database. Following are some guidelines or recommendations for configuring the underlying database.

### Authentication

For security purposes, it may be prudent to use mTLS (preferred) or add an authentication layer to protect the data in the management database.

See [Securing MongoDB](#securing-mongodb-access) for instructions.

### Memory Constraints

By default, Mongo will use up to 50% of available RAM on the node.

However usually, a few GBs of memory are sufficient for efficient management behavior.

See storage.[wiredTiger.engineConfig.cacheSizeGB](https://www.mongodb.com/docs/manual/reference/configuration-options/#storage.wiredTiger.engineConfig.cacheSizeGB) for instructions on how to reduce the amount of memory taken by the database beyond standard operating system caches. Reducing it to 8GB is the nominal recommendation.

### High Availability

It is recommended to use Mongo replica sets to ensure high availability of the data and of the database service.

See [Mongo Replica Sets](#mongo-replica-sets) for more information.

See [Management Options](#management-options) for guidance on configuring access to a highly available MongoDB instance with `replicaSets`, specifically `config.mongoConnection.hosts` and `config.mongoConnection.options.replicaSetName`.

## Best Practices for AMD EPYC Processors

This section provides a list of recommendations for AMD EPYC processors.

**<u>Note:</u>** The recommendations are based on experience with the first generations of AMD EPYC and may be less applicable for newer versions.

All recommendations are based on a storage focused point-of-view. For servers used in a converged manner including, but not limited to, file system serving, there may be additional tweaking or optimization required for best performance.

### BIOS Settings

The following fields may differ slightly between server vendors and server BIOS versions.

#### Processor Settings

**`Logical Processor (Hyper-threading) = Enabled`**

Enabling hyper-threading enables NVMesh to run more tasks in parallel. This has been found empirically to improve performance especially for smaller block sizes and for erasure coding write flows.

**`L3 cache as NUMA Domain = Enabled`**

This setting reduces expensive thread migrations across NUMA zones and reduces cache trashing. Disabling this has been found empirically to reduce performance.

**`NPS = 1` (dual-socket systems) or `NPS = 4` (single-socket systems)**

This setting with L3 cache as NUMA domain enabled helps ensure that multiple memory channels are active in parallel enabling higher bandwidth.

#### System Profile Settings

**`CPU Power Management = Maximum Performance`**

**`C-States = Disabled`**

**`X2APIC = Enabled`**

**`Memory Frequency = Maximum Performance`**

**`DRAM Refresh Delay = Performance`**

**`Memory Interleave = Auto`**

It may be possible to set these in software, using tools like tuned-adm, e.g., using tuned-adm profile latency-performance.

### IOMMU

NVMesh supports environments with IOMMUs. However, employing an IOMMU may have performance implications especially on the target side. There is typically a BIOS setting for disabling the IOMMU if desired.

Alternatively, use Linux kernel boot options. Use `cat /proc/cmdline` to view the current boot options.

### Interrupt management

As AMD EPYC CPUs have multiple NUMAs per socket, interrupt management, which typically keeps interrupts within the NUMA node associated with the physical device and its PCI bus location, is too restrictive. Therefore, the system’s `irqbalance` does not always perform optimally and it is recommended to disable this and use the following guidelines instead.

#### NVMe drives

For the NVMe drives, it is recommended to use one of the following two options for interrupt management via the `/etc/nvmesh.conf` file.

1. Set the number of queues in use from each NVMe drive to be that of a physical processor, or as high as possible if there are not enough queues. Then, set `ASSIGN_NVME_IRQS="persocket"` in `/etc/nvmesh/nvmesh.conf`.

1. Set the number of queues in use from each NVMe drive to be that of both physical processors in a dual node system, or as high as possible if there are not enough queues. Then, set `ASSIGN_NVME_IRQS="fullspread"` in `/etc/nvmesh/nvmesh.conf`.

Changes in the above can be implemented without restarting services, by manually running `nvmesh_set_irq_affinity`.

#### NICs

For NVIDIA NICs, it is recommended to verify best practices with their documentation. For NVMesh, it has been found empirically, that setting the interrupts to all cores of the same physical processor to which the NIC is PCI-connected is optimal.

This can be set using the `set_irq_affinity_cpulist.sh` utility. Note that this utility does not persist the settings. Use `/etc/rc.local` and similar mechanisms to ensure it is run on startup.

For instance, if NIC mlx5_1 is connected to physical CPU 1 and this processor comprises logical cores 16-31 and 48-63, use:

```bash
set_irq_affinity_cpulist.sh 16-32,48-63 mlx5_1
```

### Other NVIDIA NIC settings

It has been found empirically that for NVMesh, setting PCI_WR_ORDERING to relaxed for all queue pairs has major performance benefits without infringing on correctness. If other software solutions use the same NICs, it is imperative to ensure that the relaxed ordering is acceptable also for those software solutions.

Setting this is done using standard NVIDIA tools, such as:

```bash
mstconfig -d mlx5_0 set PCI_WR_ORDERING=1
```

or

```bash
mlxconfig -d mlx5_0 set PCI_WR_ORDERING=1
```

### NVMe drives

It is recommended to use as many NVMe queues as possible on each drive, up to 1 per core. There is no need to exceed this. Use the target’s module parameter for this, e.g., for a system with 64 cores add lines like the following in a modprobe configuration file.

```
options nvmeibs min_local_nvmeqs=64
options nvmeibs max_local_nvmeqs=64
```

### Others

For heavy workloads, especially with many drives and many targets, it is recommended to enable per-cpu polling. This is true for Intel processors as well.

This can be set through module parameters, for instance by adding such lines to a modprobe configuration file:

- `options nvmeibs use_pcpu_cq=Y` that sets this functionality for targets.
- `options nvmeibc use_pcpu_cq=Y` that applies to clients.

# Maintenance

The following section describes available troubleshooting tools and provides detailed instructions for various hardware and software related maintenance procedures that may be required during the ongoing operation of NVMesh.

## NVMesh Health Check

There have been a lot of recent changes here – needs to be written still.

## NVMesh Logs Collections

Use `nvmesh_logs_collector` for collecting logs on a node to accelerate issue resolution.

Typically, invocation without any parameters should be sufficient. Run the tool with `--help` for a comprehensive list of documented options.

Following are the results of a run without parameters.

```
[root@nvme80 08:58:38 root]$ /usr/bin/nvmesh_logs_collector
Creating Trace Daemon snapshot...
Creating output directory...
Saving customer information...
Creating log files...
Copying excelero system files...
Collecting nvme and network interfaces interrupts and information...
Collecting journalctl...
Collecting Toma status...
Waiting for Toma status file to be created
Collecting Volumes information...
Running Health Check script...
ERROR: Failed to run nvmesh_health_check
Collecting Toma dependency libraries...
Recording all module params...
Collecting installed packages info...
Collecting NVMeshUM logs...
Collecting syslog...
Filtering syslog...
Dumping database...
Zipping and compressing...
Output directory: nvme80_nvmesh_logs_20250929-085842
--- Done ---
```

In this example, a directory named `nvme80_nvmesh_logs_20250929-085842` was created and a file named `nvme80_nvmesh_logs_20250929-085842.tgz` for portability. Share the tgz file with your NVMesh experts.

## Hardware Operations

The following section provides procedures for common hardware related maintenance operations.

### Drive Failure & Replacement

NVMesh provides volumes with data protection to reduce the probability of data loss when drives fail and to increase storage access availability.

Drive failure detection and subsequent recovery is performed by the targets.

Upon drive failure, the targets will direct clients attached to volumes affected by the failure to move to a degraded mode. In this mode, clients will read and write from the remaining copy for mirrored volumes. For erasure coded volumes, reads will be done by reading the other data blocks in the stripe and one or more parity blocks. Writes will only update the parity blocks in the degraded mode. For unprotected volumes, IO will become disabled. Multiple failures may lead to IO becoming disabled for protected volumes.

Upon drive failure, the target associated with the drive will send an alert to one of the managements that will appear in the Dashboard section, in the Alerts sub-section, with a "Drive Failure" header. In the dashboard, the target’s health will be in the alarm state, unless it is critical for some other reason. The same health state will be shown in the Targets section. Drilling into the target will show the specific drive that has failed using a white exclamation mark on a red background graphic. Hovering over this graphic will display a string describing the drive failure.

If a failed drive cannot be recovered, it can be replaced with an alternative drive or with sufficient spare capacity on other drives. The steps to replace a drive are to evict the failed drive and then rebuild the data to other drives.

**<u>Note:</u>** There is no need to replace a drive with a specific replacement drive. Space can be allocated from other drives in the system that meet the provisioning criteria of the degraded volume, e.g., mirrored copies must be on different hosts and drives used for erasure coded volumes should meet the volume’s defined separation and location criteria.

#### Evicting a Drive

In the Targets section, click on the target with the drive to be evicted. Then, click on the red Evict button on the drive. A pop-up window will prompt for a password reflecting the sensitivity of this operation. Enter the password to proceed. For a failed drive, the red exclamation mark icon, should be replaced with a yellow exclamation mark and hovering on it will display the "Disk Evicted" message. The Evict button is then no longer accessible.

Evicted drives cannot be deleted, like any other drive, until all allocated space on them has been migrated to replacement drive space or has been disassociated from volumes by deleting the volumes.

The same functionality is also available from the Drives section.

#### Volume Rebuild

Any protected volumes that have space allocated on an evicted drive and no other issues, will have a status of "Rebuild Required". In the Volumes section, such volumes can be located by entering "Rebuild" in the Status filter box. To rebuild one or more volumes, use the volume table’s multi-choose functionality and click on the Rebuild button in the top-left corner. A pop-up window will prompt for a password reflecting the sensitivity of this operation. Enter the password to proceed and the rebuild process will be invoked.

**<u>Note:</u>** If the volume had been defined using constraints, these will be applied by the system in choosing capacity for the rebuild process.

- If the volume was defined using a VPG, rebuild space will be allocated from it.
- If the volume was defined using some combination of target classes and drive classes, rebuild space will be allocated from these classes, including space added to them after the original volume definition.
- If the volume was allocated from specifically chosen drives, it may be necessary first to redefine the volume definition constraints.

Once the rebuild process is invoked, NVMesh will begin to create data on the replacement capacity allocated, copying for mirrored volumes and reconstructing for erasure-coded volumes.

New drives, whether as a replacement drive or in general, need to be formatted before they can be used. See [Format Drives](#format-drives) for more information.

### NIC Failure and Replacement

NVMesh provides network high availability options, using multi-pathing and support for multiple NICs. Upon replacing a NIC, it may be necessary to take one or more of the following steps to ensure optimal behavior.

#### General

Ensure that the new NIC is defined as usable in the configuration for this node.

The definition may be set in a configuration profile for this node, see [Configuration Profiles](#configuration-profiles) or directly in `/etc/nvmesh/nvmesh.conf`, see [Installing Client and Targets](#install-nvmesh-clients-and-targets) for more information.

Incorporating networking configuration changes requires restarting NVMesh services on the node.

#### RoCE Specific

If the NIC is in a target, ensure that the malfunctioning NIC no longer appears in the GUI for this target by deleting it from the target.

If the new NIC has the same IP address, this step should not be required.

#### InfiniBand Specific

If the NIC is in a target, ensure that the malfunctioning NIC no longer appears in the GUI for this target by deleting it. Unless the NIC has the same GID burnt into its firmware, this step will be required.

**<u>Note:</u>** If the replaced NIC is not deleted, clients and other targets will continue to attempt to connect to it potentially wasting network resources.

### Moving a Drive between Targets

Drives can be freely moved within the same target, or in between targets of the same cluster. However, drives cannot be imported into other NVMesh clusters. Such drives will be considered foreign and will be automatically evicted by the system and an appropriate alert will be raised.

If a drive is moved to the same target as another drive that holds data that is mirrored from it for one of the mirrored volume types, this will cause a mirror violation, which will be reported as an alert. This will not affect I/O, but it may reduce expected availability. The same applies for erasure-coded volumes and aggregating drives in a target beyond the settings for the volume. Other protection domain separation criteria may also be violated in this way, but they are not alerted on.

**<u>Note:</u>** It typically takes a few seconds for drive removal and insertion operations to be completed. Therefore, it is recommended to wait at least 60 seconds between steps to revert an accidental drive move, i.e., between removing a drive that had been recently inserted it into a different target and them moving it back to the original one.

### Resizing a Drive

Some drives enable the administrator to modify their size. This operation if performed without the necessary preparations on the NVMesh side, may cause data loss. It is important to perform the resize using the following instructions, as it is presumed that resize operations will erase all data on the drive. If this is not the case, consult with NVMesh support.

1. Evict the drive as if it was a failed drive, see [Drive Failure & Replacement](#drive-failure-replacement).
1. Delete the drive.
1. Resize the drive, using the drive vendor’s instructions.
1. Remove and then reinsert the drive physically to make NVMesh rediscover the drive. Alternatively, this can done via the operating system using the system’s PCI manipulation commands. Be sure to correctly identify the drive and as root run `echo 1 > /sys/bus/pci/devices/<PCI_DEVICE>/remove` to remove the drive and then as root run `echo 1 > /sys/bus/pci/rescan` to rediscover it.
1. Format the drive for NVMesh use.

**<u>Note:</u>** If the drive is not evicted, but is resized, it should be auto-evicted and an alert will be generated, as the NVMesh header will be missing.

## Software Operations

This section provides procedures for common software related maintenance operations.

To start an NVMesh cluster, it is recommended to perform the following actions in the order as listed:

| Operation | Instructions | Comments |
| --- | --- | --- |
| Start management(s) | `systemctl start nvmeshmgr` |  |
| Start client(s) | `systemctl start nvmeshclient` | Clients start by default after installation |
| Start target(s) | `systemctl start nvmeshtarget` | As the client is a dependency, it will start automatically. It is recommended to start all targets simultaneously if possible. |

To shut down an NVMesh cluster, it is recommended to perform the following actions in the order listed:

| Operation | Instructions | Comments |
| --- | --- | --- |
| Stop client(s) that are not targets | `systemctl stop nvmeshclient` | This can be performed concurrently on all clients. <br>If there are mounted file systems or volumes in use, unmount or stop these before attempting to stop the clients. |
| Stop the client(s) on the target(s) | `systemctl stop nvmeshclient` | This can be performed concurrently on all targets. It is beneficial to do so to reduce startup time on the cluster later. |
| Stop management(s) | `systemctl stop nvmeshmgr` | This can be done concurrently. |

Uninstall NVMesh Software. Prior to the uninstallation of NVMesh software, stop all NVMesh components, as described above. Once all services are stopped, the NVMesh packages can be uninstalled.

| Operation | Instructions | Comments |
| --- | --- | --- |
| Uninstall NVMesh Packages | `yum remove 'nvmesh-\* '` | For RHEL-compatible |
|  | `apt-get --purge remove 'nvmesh-\* '` | For Ubuntu |

### Modify management IP

When the IP address associated with a management is modified, it will refuse to start and an error message like the following will be logged in `/var/log/messages` or `/var/log/syslog`, depending on operating system distribution:

```
Sep 5 14:35:44 nvme1storage nvmeshmgr[11107]: WARNING: Unable to verify managementId, sleeping for 10 seconds
```

To update the management server ID, create the file that indicates to the management that it should update its ID.

A simple means is to run the following as root: `touch /var/opt/nvmesh/mgr/update_management_id`.

The Management should then proceed to start. There is no need to stop any services for this operation.

### Reset factory defaults

**<u>Warning:</u>** The following procedure will delete all data on the cluster! This procedure is irreversible.

To reset the cluster to a clean configuration, perform the following actions:

1. Stop the cluster, see shutting down NVMesh above.
2. On Management Servers, run `mongosh management --eval 'db.dropDatabase()'`. This will delete all volume configurations and product history. Additional parameters may be needed depending upon the security mechanisms applied to mongo.
   1. On targets, as root, run: `rm -rf /var/opt/nvmesh/toma/\*`.
   2. This will delete all volume state information on the targets.
3. On clients, as root, run: `rm /var/opt/nvmesh/mcs/CLIENT/CONFIGURATION`.
4. Start the cluster, see starting NVMesh above.

### Rename hostname

NVMesh identifies target and clients based on their hostname. Therefore, renaming a host that is configured as part of a cluster requires some administrative steps.

1. The first step is to have the node’s components identify themselves with the new hostname.
   1. Use `systemctl restart nvmeshcm` to restart the communications service to management.
   2. Use `systemctl status nvmeshcm` to validate it is running.
   3. Ensure that the new hostname appears in management with the drives assigned to it.
   4. Any volume attachments to the client will need to be redefined.
2. The second step is to remove the entries for the previous hostname from the clients and targets sections.
   1. This can be done immediately in the Clients section using the multi-select functionality and clicking the Delete button in the top left corner.
   2. For targets, the node will appear as a new target, and the NICs and drives will now be associated with it. This typically takes a few seconds. Once this has happened, the entry with the previous hostname will have no NICs and drives associated with it and can be deleted. This is done in the Targets section using the multi-select functionality and clicking the Delete button in the top left corner.

### Upgrading NVMesh

Upgrading should be done through NVMesh-based managed non-disruptive upgrade.

For completeness, here are the main steps to be done in a non-managed upgrade are:

1. Install new RPMs.
   1. Use `dnf/yum install <nvmesh-RPMs>` for RHEL-compatible OSes.
   2. Use `dpkg -i <nvmesh-debs>` for Ubuntu.
2. For continuous management functions, restart at least one management to the new version but not all, so that there is at least one management with the new version and one with the previous version. This is done with `systemctl restart nvmeshmgr`.
3. Restart clients and targets. For non-disruptive (hot) upgrade, run `nvmesh_clnt_shutdown --upgrade` to notify the client about the hot upgrade and avoid stopping the block devices. Then run `systemctl restart nvmeshclient` to restart the client and target with the new version.
4. Restart the remaining managements.

**<u>Note:</u>** After upgrading a management instance, if changes had been made to `/etc/nvmesh/management.js.conf`, a new file named `/etc/nvmesh/management.js.conf.rpmnew` may be created. This file will include new fields that are critical for the proper function of management. Therefore, compare the contents of the two files and ensure that the new fields exist also in `/etc/nvmesh/management.js.conf` to ensure proper function. Alternatively, replace `/etc/nvmesh/management.js.conf` with `/etc/nvmesh/management.js.conf.rpmnew` and reinsert any adjustments made.

**<u>Note:</u>** Managed non-disruptive upgrade has not been integrated yet with Kubernetes environments.

### Altering the per-Client Journal Space

Targets allocate a fixed amount of journal space per client per EC-capable drive. As the total journal size per drive is fixed, this determines the maximum number of clients that can connect to the drive and limits the amount of outstanding IO to that drive. An administrator may want to increase the amount of journal space per client per drive to allow more parallel IO, potentially increasing per-client performance. An administrator may want to decrease the amount of journal space per client per drive to enable more clients to connect to the same drive.

The fixed amount of journal space per client per EC-capable drive is governed by the `nvmeibs_jrange_num_blocks` module parameter for the `nvmeibs` module.

This setting is per target, but it is recommended to alter it across the entire cluster.

The procedure for altering the setting is as follows:

1. Detach all volumes with segments on the drives on the target from all clients. If updating the entire cluster, detach all volumes from all clients.
   1. It may be sufficient to do a detach for hot-upgrade, but this has not been tested.
2. Confirm that no journals are in use as allocations are kept across target reboots and an allocation of a different size on reboot will prevent target startup. To confirm, ensure that the `/proc/nvmeibs/serjio/<DRIVE-ID>/ranges.csv` files have no reserved ranges except for the quarantined ranges, 0, 1 and 2.
3. Shutdown clients and targets.
   1. It may be sufficient to stop only the targets, but this has not been tested.
4. Modify `nvmeibs_jrange_num_blocks` to the new value.
5. Restart the NVMesh components stopped in step 3.
6. Re-attach the volumes detached in step 1.

## Key Rotation / Certificate Renewal

When NVMesh uses mutual TLS (mTLS) for authentication between components, certificates are stored by default in `/etc/nvmesh.tls/` and referenced via the `/etc/nvmesh/tls/` symlink.

### Certificate Inventory

The following certificates require periodic rotation:

**Client nodes:**

| File | Purpose |
|---|---|
| `Kafka-MCS.crt` / `Kafka-MCS.key` | Client authentication to Kafka MCS |
| `Kafka-UpgradeAgent.crt` / `Kafka-UpgradeAgent.key` | Client authentication for the upgrade agent |
| `kafka_ca_chain.crt` | CA chain for validating Kafka server certificates |

**Target nodes:**

| File | Purpose |
|---|---|
| `Kafka-zone1.TOMA.crt` / `Kafka-zone1.TOMA.key` | TOMA service Kafka client certificate |

**Management nodes:**

| File | Purpose |
|---|---|
| `Kafka-MCS.crt` / `Kafka-MCS.key` | Client authentication to Kafka MCS |
| `Kafka-UpgradeAgent.crt` / `Kafka-UpgradeAgent.key` | Client authentication for the upgrade agent |
| `Kafka-Server.crt` / `Kafka-Server.key` | Kafka broker/controller server certificate |
| `Kafka-Management.crt` / `Kafka-Management.key` | Management service Kafka client certificate |
| `Mgmt-HA.crt` / `Mgmt-HA.key` | Management HA inter-node certificate |
| `Mongo-Server.crt` / `Mongo-Server.key` | MongoDB server certificate |
| `Mongo-Mgmt.crt` / `Mongo-Mgmt.key` | Management-to-MongoDB client certificate |
| `Mongo-Admin.crt` / `Mongo-Admin.key` | MongoDB admin authentication certificate |
| `Rest-CLI.crt` / `Rest-CLI.key` | CLI-to-REST API client certificate |
| `Rest-Server.crt` / `Rest-Server.key` | REST API server certificate |
| `kafka_ca_chain.crt`, `mongo_ca_chain.crt`, `rest_ca_chain.crt`, `ca_chain.crt` | CA chains for the respective services |

### Checking Certificate Expiry

To check the expiry of a PEM certificate file:

```bash
openssl x509 -in /etc/nvmesh.tls/<certificate>.crt -noout -enddate
```

To check all certificates at once:

```bash
for cert in /etc/nvmesh.tls/*.crt; do
  echo -n "$cert: "
  openssl x509 -in "$cert" -noout -enddate 2>/dev/null || echo "not a PEM certificate"
done
```

To check a Kafka JKS keystore:

```bash
keytool -list -v -keystore /etc/nvmesh.tls/<COMPONENT>.jks -storepass <password> | grep -E "Alias|until"
```

### Rotating Certificates

Generate replacement certificates following the same process described in [Prepare Security Certificates](#prepare-security-certificates-optional). Then, for each node:

1. Replace the certificate and key files in `/etc/nvmesh.tls/`.
2. For Kafka broker nodes, rebuild the JKS keystores as described in [Prepare Security Certificates](#prepare-security-certificates-optional) and restart Kafka.
3. For MongoDB nodes, replace the certificate bundle and restart MongoDB, or use `db.rotateCertificates()` in `mongosh` for a hot rotation.
4. Restart the NVMesh components on the node:
   - Management: `systemctl restart nvmeshmgr`
   - Target: `systemctl restart nvmeshtarget`
   - Client: `systemctl restart nvmeshclient`

**Note:** When rotating CA chain certificates, all components that validate against that CA must be updated in a coordinated manner to avoid authentication failures. A maintenance window may be required.

## Target Cleanup

After deleting a target from a system, to insert it back into the system, it is imperative to first clean up its TOMA persistency. This is doing by removing the contents of the `/var/opt/nvmesh/toma` directory. The full procedure would be as follows:

```bash
systemctl stop nvmeshtarget
rm -rf /var/opt/nvmesh/toma/*
systemctl start nvmeshtarget
```

## Cluster Cleanup

To clean up an entire cluster in order to "start over" performance the following operations:

1. Stop all NVMesh components and Kafka brokers.
2. Remove all TOMA persistencies, i.e., on all targets, as follows: `rm -rf /var/opt/nvmesh/toma/*`.
3. Remove all cached client attachment information, i.e., on all clients, as follows: `rm -rf /var/opt/nvmesh/mcs/*`.
4. On the Kafka brokers, remove current messages by removing all data in `/var/lib/kafka` except for `meta.properties` from that directory that is critical for restarting the brokers.
5. Clear the Mongodb with a command such as the following from a management node: `mongosh mongodb://<MONGO-IP-ADDRESS>:27017/management --file /opt/nvmesh/management/clearDB.js`.
6. Restart the components in the same order as when starting a new cluster.

## General Troubleshooting

### TCP

#### siw module fails to unload

Software iWARP (SIW) uses a custom TCP congestion algorithm, `siw_tcp_ca`, so that ACK processing can be tied to RDMA completion handling. While any sockets are still using this congestion algorithm, the kernel keeps the SIW module in use and `rmmod siw` (or an equivalent unload) will fail. To locate such sockets, run:

```bash
ss -Hnti | grep siw_tcp_ca
```

To close a matching socket by its local and peer addresses (replace `<local>` and `<peer>` with the values from the listing), use:

```bash
ss -K src <local> dest <peer>
```

# Configuration Limits

The following sections describe various system limits. Some of these may be approximations. Specific per use case testing is always warranted.

| Item | Limitation |
| --- | --- |
| Client Limitations |  |
| Max clients | 4096 |
| Client hostname length | 60 characters |
|  |  |
| Drive Limitations |  |
| Max drives in a target | 60 |
| Max drives for a single volume | 256 |
| Max drives in a zone | 1500 |
| Max drive segments in a zone | 100,000 for 4 targets. <br>The maximum is dictated by the number of targets multiplied by the number of segments. Higher segment counts may require increasing RAFT timeouts. |
| Max volume stripe width | 300 |
| Max concurrent rebuilds per target | 16 <br>Higher is technically possible but not recommended. |
| Max clients connecting to a single drive <br><br>**<u>Note:</u>** When there are many clients attaching to many small sub-drive volumes, it may be hard to track the current state. There is an open feature request to improve observability for this. | 1024 |
|  |  |
| Drive Class Limitations |  |
| Name | 1024 Unicode characters |
| Description | 1024 Unicode characters |
| Max drives | 1500 |
| Max drive classes in a cluster | 5000 |
|  |  |
| Key Pair Limitations |  |
| Name | 1024 Unicode characters |
| Description | 1024 Unicode characters |
|  |  |
| Networking Limitations |  |
| NICs per node | 10 |
| NIC ports per node | 10 |
| NICs per zone | 500 |
|  |  |
| Target Limitations |  |
| Max targets per zone | 256 |
| Max NVMe drives per target | 60 |
| Target hostname length | 60 characters |
|  |  |
| Target Class Limitations |  |
| Name | 1024 Unicode characters |
| Description | 1024 Unicode characters |
| Max target classes in a cluster | 5000 |
|  |  |
| User Limitations |  |
| Username | 32 characters, uppercase and lowercase English letters, digits, and any of the following special characters: <br><ul><li>`.\_%+-`</li></ul> |
| Password | 6 to 32 Unicode characters |
|  |  |
| Volume Limitations |  |
| Name | 24 characters, uppercase and lowercase English letters, digits, and any of the following special characters: <br><ul><li>`\_+-=`</li></ul> |
| Description | 1000 Unicode characters |
| Max volume segments per zone | 16384 |
| Min volume size | 1 GB |
| Max volume size | Limited by cluster capacity |
| Max clients connected to a single volume | 1024 |
|  |  |
| Volume Provisioning Group Limitations |  |
| Name | 1024 Unicode characters |
| Description | 1024 Unicode characters |
| Max VPGs per cluster | 5000 |
|  |  |
| Volume Security Group Limitations |  |
| Name | 1024 Unicode characters |
| Description | 1024 Unicode characters |

The max segments limit applies to volumes that were allocated in one shot, and which are implemented as a single drive per volume segment. Otherwise, count the total number of segments in the volume. Each allocation or expansion of a volume will add the minimal number of segments.

See the following table for the one shot or minimum segments allocated to a volume.

| Volume Type        | Minimum Segments Allocated  | Formula Variable Name |
| ------------------ | --------------------------- | --------------------- |
| Concatenated / LVM | 1                           | N<sub>LVM</sub>       |
| RAID-0             | Volume Width                | VW                    |
| RAID-1             | 2                           | N<sub>R1</sub>        |
| RAID-10            | Volume Width                | VW                    |
| Erasure-coded (EC) | Data Blocks + Parity Blocks | DB, PB                |

$$N_{Segments} = N_{LVM} + \sum_{RAID-0} VW + 2N_{R1} + 2\sum_{RAID-1} VW + \sum_{EC} (DB + PB)$$
