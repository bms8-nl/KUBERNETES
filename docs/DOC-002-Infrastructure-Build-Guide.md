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


---

# 13. VMware vSphere Preparation

## 13.1 Overview

The Kubernetes platform is deployed on VMware vSphere using Ubuntu Server virtual machines.

Before virtual machines are created, verify that the vSphere environment is healthy and capable of supporting the platform.

---

## 13.2 vCenter Validation

Verify the following:

| Validation Item | Expected Result |
|-----------------|-----------------|
| vCenter Server | Operational |
| ESXi Hosts | Connected |
| Cluster | Healthy |
| Datastores | Accessible |
| Networks | Available |
| Licensing | Valid |
| Time Synchronisation | Operational |

Resolve all vCenter health warnings before continuing.

---

## 13.3 ESXi Host Validation

Each ESXi host shall be validated for:

- CPU health
- Memory health
- Storage connectivity
- Network adapter status
- Time synchronisation
- Hardware compatibility
- VMware Tools compatibility

---

## 13.4 Datastore Standards

The datastore used for Kubernetes virtual machines should provide:

| Requirement | Recommendation |
|-------------|----------------|
| Storage Type | SSD or All-Flash |
| Availability | Shared |
| Snapshot Support | Yes |
| Thin Provisioning | Enabled |
| Expansion | Supported |

---

## 13.5 Port Group Requirements

Create or validate the required port groups.

| Port Group | Purpose |
|------------|---------|
| Management | ESXi and vCenter management |
| Kubernetes | Cluster node communication |
| Storage (Optional) | Dedicated storage replication |
| Backup (Optional) | Backup traffic |

Production environments should separate management, application, and storage traffic where practical.

---

# 14. Virtual Machine Deployment

## 14.1 Naming Standard

Virtual machines shall follow a consistent naming convention.

| VM Name | Purpose |
|----------|---------|
| k8s-control-01 | Control Plane |
| k8s-worker-01 | Worker Node |
| k8s-worker-02 | Worker Node |

Additional worker nodes shall continue the numbering sequence.

---

## 14.2 VM Hardware Configuration

### Control Plane

| Resource | Value |
|----------|-------|
| vCPU | 4 |
| Memory | 8 GB |
| Disk | 100 GB |
| Firmware | EFI |
| SCSI Controller | VMware Paravirtual |
| Network Adapter | VMXNET3 |

---

### Worker Nodes

| Resource | Value |
|----------|-------|
| vCPU | 8 |
| Memory | 16 GB |
| Disk | 150 GB |
| Firmware | EFI |
| SCSI Controller | VMware Paravirtual |
| Network Adapter | VMXNET3 |

---

## 14.3 VM Placement

Distribute Kubernetes virtual machines across ESXi hosts where possible.

Example:

| ESXi Host | Virtual Machines |
|------------|------------------|
| ESXi-01 | k8s-control-01 |
| ESXi-02 | k8s-worker-01 |
| ESXi-03 | k8s-worker-02 |

This placement reduces the impact of a single host failure.

---

## 14.4 Virtual Disk Configuration

Recommendations:

- Use thin provisioning where organisational policy permits.
- Allocate separate disks for application data if required.
- Avoid unnecessary snapshots on production systems.
- Monitor datastore utilisation regularly.

---

## 14.5 Network Adapter Configuration

Each virtual machine shall:

- Use VMXNET3 adapters.
- Connect to the Kubernetes network.
- Use static IPv4 addressing.
- Register in enterprise DNS.
- Reach gateway, DNS, and NTP servers.

---

## 14.6 VM Deployment Checklist

Verify the following before installing Ubuntu Server:

- Virtual machines created successfully.
- Correct hardware allocated.
- EFI firmware configured.
- VMXNET3 adapter installed.
- Correct port group assigned.
- Static IP plan prepared.
- DNS records created.
- Host placement validated.
- Datastore capacity confirmed.


---

# 15. Ubuntu Server Installation

## 15.1 Overview

All Kubernetes nodes shall run Ubuntu Server 24.04 LTS using a consistent installation standard.

Only the minimal server installation shall be used to reduce the operating system attack surface and simplify maintenance.

---

## 15.2 Installation Media

Use:

| Item | Standard |
|------|----------|
| Operating System | Ubuntu Server 24.04 LTS |
| Installation Type | Minimal Installation |
| Architecture | x86_64 (AMD64) |
| Boot Mode | UEFI |

Verify the integrity of the installation media before deployment.

---

## 15.3 Disk Partitioning

The recommended disk layout is:

| Mount Point | Purpose |
|-------------|---------|
| /boot/efi | EFI System Partition |
| /boot | Boot Files |
| / | Operating System |
| /var | Logs and Containers |
| /tmp | Temporary Files |
| swap | Optional (Refer to Kubernetes requirements) |

Partition sizes shall be adjusted according to organisational standards and workload requirements.

---

## 15.4 Hostname Configuration

Assign a unique hostname to each node.

Examples:

| Hostname | Role |
|----------|------|
| k8s-control-01 | Control Plane |
| k8s-worker-01 | Worker |
| k8s-worker-02 | Worker |

Hostnames shall match DNS records.

---

## 15.5 Static Network Configuration

Each node shall use a static IPv4 address.

Configure:

- IP Address
- Subnet Mask
- Default Gateway
- DNS Servers
- Search Domain

Dynamic IP addressing shall not be used for Kubernetes nodes.

---

## 15.6 Package Repository Configuration

Configure the operating system to use approved Ubuntu package repositories.

Recommended practices:

- Enable security updates.
- Remove unused repositories.
- Use enterprise mirrors where available.
- Keep repository configuration consistent across all nodes.

---

## 15.7 Initial Package Update

After installation:

- Update package indexes.
- Install the latest security updates.
- Reboot if required.
- Verify successful startup before continuing.

All nodes shall run the same operating system patch level.

---

# 16. Operating System Configuration

## 16.1 Administrative Accounts

Administrative access shall follow organisational security policies.

Recommendations:

- Use named administrator accounts.
- Limit use of the root account.
- Grant administrative privileges through sudo.
- Enforce strong password policies.

---

## 16.2 SSH Configuration

SSH shall be configured using the following standards:

| Setting | Recommendation |
|---------|----------------|
| Protocol | SSH Version 2 |
| Root Login | Disabled |
| Password Authentication | Disabled where key-based authentication is implemented |
| Public Key Authentication | Enabled |
| Idle Timeout | Configured |

Administrative access shall be restricted to authorised personnel.

---

## 16.3 Time Synchronisation

Verify that:

- NTP is operational.
- Time is synchronised.
- Time zone is configured according to organisational standards.

Consistent time is required for authentication, certificates, logging, and Kubernetes operations.

---

## 16.4 Name Resolution

Verify successful resolution of:

- Local hostname
- Peer Kubernetes nodes
- Enterprise DNS servers
- Internet repositories

Forward and reverse DNS resolution shall function correctly.

---

## 16.5 Operating System Validation

Confirm the following before proceeding:

- Ubuntu Server installed successfully.
- Static networking operational.
- Hostname configured correctly.
- DNS resolution working.
- NTP synchronised.
- Administrative access verified.
- Latest updates installed.
- System reboot completed successfully.


---

# 17. Operating System Hardening

## 17.1 Overview

All Kubernetes nodes shall be hardened before Kubernetes software is installed.

The objective is to reduce the attack surface, improve operational security, and establish a consistent security baseline across the platform.

---

## 17.2 Security Principles

Operating system hardening shall follow these principles:

- Least privilege
- Secure by default
- Defence in depth
- Principle of minimal services
- Standardised configuration
- Continuous patch management

---

## 17.3 Package Management

The operating system shall:

- Install only required packages.
- Remove unnecessary software.
- Disable unused package repositories.
- Apply security updates regularly.
- Use approved software sources only.

Package consistency shall be maintained across all Kubernetes nodes.

---

## 17.4 Service Hardening

Disable or remove unnecessary services.

Examples include:

- Legacy network services
- Unused printing services
- Unused file-sharing services
- Development services not required for production

Only essential services shall remain enabled.

---

## 17.5 User and Account Security

Administrative accounts shall comply with organisational security policies.

Requirements include:

- Individual administrator accounts
- Strong password policies
- Sudo for privileged access
- Root account protected
- Removal of unused accounts
- Periodic access review

---

## 17.6 SSH Hardening

SSH shall be configured according to enterprise standards.

| Setting | Standard |
|---------|----------|
| SSH Version | Version 2 |
| Root Login | Disabled |
| Public Key Authentication | Enabled |
| Password Authentication | Disabled where feasible |
| Empty Passwords | Disabled |
| Idle Session Timeout | Configured |
| Login Banner | Enabled |

Administrative access should originate only from approved management networks.

---

## 17.7 Firewall Baseline

Each node shall implement host-level firewall protection.

Minimum requirements include:

- Permit SSH from authorised management networks.
- Permit Kubernetes control plane communication.
- Permit required worker node communication.
- Deny unnecessary inbound traffic.
- Allow required outbound traffic.

Firewall rules shall be documented and version controlled.

---

## 17.8 Kernel Configuration

Kernel parameters shall be configured to support Kubernetes and improve system security.

Examples include:

- IP forwarding enabled
- Bridge network filtering enabled
- IPv4 packet forwarding configured
- Swap disabled prior to Kubernetes installation

Kernel configuration shall be consistent across all cluster nodes.

---

## 17.9 Audit Configuration

Operating system auditing should record:

- User logins
- Privilege escalation
- Service changes
- Authentication failures
- System configuration changes

Audit records shall be retained according to organisational policies.

---

## 17.10 Security Validation

Verify the following before continuing:

- Latest security updates installed.
- Unnecessary services removed.
- SSH hardened.
- Firewall configured.
- Swap disabled.
- Kernel parameters validated.
- Administrative access verified.
- Audit logging operational.


---

# 18. Infrastructure Validation

## 18.1 Overview

Infrastructure validation confirms that all prerequisite components have been deployed, configured, and verified before Kubernetes installation.

No Kubernetes software shall be installed until all validation activities have been successfully completed.

---

## 18.2 Infrastructure Validation Checklist

| Validation Item | Status |
|-----------------|--------|
| VMware vCenter Operational | □ |
| ESXi Hosts Healthy | □ |
| Datastores Available | □ |
| Networks Configured | □ |
| Virtual Machines Created | □ |
| Ubuntu Installed | □ |
| Static IP Addresses Assigned | □ |
| DNS Resolution Verified | □ |
| NTP Synchronisation Verified | □ |
| SSH Access Verified | □ |
| Firewall Configured | □ |
| Operating System Hardened | □ |
| Latest Security Updates Installed | □ |
| Internet Connectivity Verified | □ |

All validation items shall be completed before proceeding.

---

## 18.3 Operating System Validation

Each Kubernetes node shall be verified for:

- Successful system boot
- Correct hostname
- Static network configuration
- DNS resolution
- Time synchronisation
- SSH accessibility
- Available disk capacity
- Available memory
- CPU allocation
- Security baseline compliance

---

## 18.4 Infrastructure Acceptance Criteria

The infrastructure shall meet the following acceptance criteria:

| Requirement | Acceptance Criteria |
|-------------|---------------------|
| Availability | All infrastructure components operational |
| Networking | Full connectivity between nodes |
| Name Resolution | Forward and reverse DNS operational |
| Time Synchronisation | NTP synchronised |
| Security | Hardening complete |
| Capacity | Meets sizing requirements |
| Documentation | Complete and approved |

---

## 18.5 Documentation Requirements

The following information shall be recorded:

- VMware inventory
- Virtual machine inventory
- IP address allocation
- DNS records
- Network diagrams
- Storage allocation
- Administrator contacts
- Software versions
- Infrastructure approvals

Documentation shall be stored in the platform documentation repository.

---

## 18.6 Operational Handover

Before Kubernetes installation begins, the infrastructure team shall confirm:

- Infrastructure build completed.
- Validation successfully completed.
- Security baseline implemented.
- Required documentation completed.
- Administrative access transferred where appropriate.
- Outstanding issues documented.

---

## 18.7 Risks

Potential risks include:

| Risk | Mitigation |
|------|------------|
| Incorrect VM sizing | Validate against sizing standards |
| DNS configuration errors | Perform forward and reverse lookup testing |
| Time drift | Validate NTP before installation |
| Storage capacity shortage | Monitor utilisation and growth |
| Network misconfiguration | Verify connectivity between all nodes |
| Security baseline incomplete | Perform configuration review |

---

## 18.8 Exit Criteria

The Infrastructure Build phase is complete when:

- All virtual machines are operational.
- Ubuntu Server is installed on every node.
- Static networking is functional.
- DNS and NTP are operational.
- Security hardening is complete.
- Infrastructure validation has passed.
- Documentation has been approved.
- The environment is ready for Kubernetes installation.

---

# 19. References

The following documents are related to this guide:

- DOC-001 – Enterprise Solution Architecture
- DOC-003 – Kubernetes Installation Guide
- DOC-006 – Security Hardening
- DOC-008 – Operations Runbook
- DOC-009 – Disaster Recovery
- DOC-010 – Best Practices

---

# 20. Conclusion

This document defines the infrastructure standards and implementation procedures required to prepare an enterprise VMware environment for Kubernetes deployment.

Completion of this guide confirms that the infrastructure is secure, standardised, validated, and ready for the Kubernetes installation process described in **DOC-003 – Kubernetes Installation Guide**.

---

**End of Document**

