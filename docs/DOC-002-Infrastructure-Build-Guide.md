# DOC-002 – Infrastructure Build Guide

**Version:** 1.0

**Document Owner:** Platform Engineering

**Classification:** Internal

---

# Revision History

| Version | Date | Author | Description |
|----------|------|--------|-------------|
| 1.0 | 2026-07-30 | Platform Engineering | Initial Release |

---

# Table of Contents

1. Introduction
2. Purpose
3. Scope
4. Target Environment
5. Infrastructure Prerequisites
6. Hardware Requirements
7. Virtual Machine Standards
8. Network Prerequisites
9. DNS Requirements
10. NTP Requirements
11. Storage Requirements
12. Infrastructure Validation Checklist

---

# 1. Introduction

This document describes the infrastructure required to deploy the Enterprise Kubernetes Platform.

It provides implementation guidance for preparing VMware infrastructure, networking, operating systems, storage, and supporting services before Kubernetes installation.

This document assumes that VMware vSphere has already been deployed and is operational.

---

# 2. Purpose

The objectives of this guide are to:

- Standardise infrastructure deployment.
- Ensure consistency across environments.
- Reduce deployment risk.
- Provide repeatable implementation procedures.
- Establish enterprise infrastructure standards.

---

# 3. Scope

This document includes:

- VMware infrastructure preparation
- Virtual machine provisioning
- Operating system standards
- Network preparation
- DNS configuration
- NTP configuration
- Storage preparation
- Infrastructure validation

This document does not include:

- Kubernetes installation
- Platform services deployment
- Application deployment

---

# 4. Target Environment

| Component | Technology |
|-----------|------------|
| Virtualization | VMware vSphere |
| Hypervisor | VMware ESXi |
| Management | VMware vCenter |
| Guest Operating System | Ubuntu Server 24.04 LTS |
| Kubernetes Distribution | Upstream Kubernetes |
| Container Runtime | containerd |

---

# 5. Infrastructure Prerequisites

The following infrastructure shall be available before deployment:

- VMware vCenter operational
- VMware ESXi hosts available
- Shared datastore configured
- Enterprise DNS operational
- Enterprise NTP operational
- Internet connectivity for package installation
- Administrative access to vCenter
- Ubuntu Server 24.04 LTS installation media

---

# 6. Hardware Requirements

The reference platform requires the following minimum infrastructure.

| Resource | Minimum |
|----------|----------|
| ESXi Hosts | 3 |
| CPU per Host | 16 vCPU |
| Memory per Host | 64 GB |
| Shared Storage | 1 TB SSD |
| Network | 10 GbE Recommended |

Production environments should be sized according to workload requirements.

---

# 7. Virtual Machine Standards

| Node | vCPU | Memory | Disk |
|------|------|--------|------|
| Control Plane | 4 | 8 GB | 100 GB |
| Worker Node | 8 | 16 GB | 150 GB |

Virtual machines shall use:

- VMware Virtual Hardware latest supported version
- EFI firmware
- VMware Paravirtual SCSI Controller
- VMXNET3 network adapters

---

# 8. Network Prerequisites

The following networks shall be available:

| Network | Purpose |
|----------|---------|
| Management Network | VMware Management |
| Kubernetes Node Network | Control Plane and Worker Nodes |
| Storage Network | Longhorn Replication (recommended) |
| External Network | Client Access |

Each node shall have a static IP address.

---

# 9. DNS Requirements

Forward and reverse DNS records shall exist for every Kubernetes node.

Example:

| Hostname | Purpose |
|----------|---------|
| k8s-control-01 | Control Plane |
| k8s-worker-01 | Worker |
| k8s-worker-02 | Worker |

All nodes shall resolve each other using fully qualified domain names.

---

# 10. NTP Requirements

All infrastructure components shall synchronise time using enterprise NTP servers.

Time synchronisation is mandatory for:

- Kubernetes
- Certificates
- Authentication
- Logging
- Monitoring

---

# 11. Storage Requirements

Shared storage shall provide:

- High availability
- Low latency
- Snapshot capability
- Backup integration
- Sufficient capacity for future expansion

Longhorn will provide persistent storage for Kubernetes workloads after platform installation.

---

# 12. Infrastructure Validation Checklist

Before proceeding to Kubernetes installation, verify:

- VMware vCenter operational
- ESXi hosts healthy
- Virtual machines deployed
- Static IP addresses assigned
- DNS resolution working
- NTP synchronisation successful
- Internet connectivity verified
- Ubuntu Server installed on all nodes

