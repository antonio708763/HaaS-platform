# Architecture Decision Records (ADRs)

Purpose:  
This document records major technical decisions for **HaaS-platformV2**.

Format: **Context → Decision → Alternatives → Consequences**.

This file exists so future rebuilds and client deployments can understand *why* the platform was built a certain way, not just *how*.

Status values:
- proposed
- accepted
- deprecated
- superseded

---

## Table of Contents

- [ADR-0001: Proxmox VE 9.1 as Host Hypervisor](#adr-0001-proxmox-ve-91-as-host-hypervisor-accepted)
- [ADR-0002: Debian 12 Golden Template (VM100)](#adr-0002-debian-12-golden-template-vm100-accepted)
- [ADR-0003: GPU Passthrough for VAAPI (Research Track)](#adr-0003-gpu-passthrough-for-vaapi-research-track-accepted)
- [ADR-0004: SSH Key-Only Authentication](#adr-0004-ssh-key-only-authentication-accepted)
- [ADR-0005: Proxmox Firewall Baseline](#adr-0005-proxmox-firewall-baseline-accepted)
- [ADR-0006: UFW Firewall on Debian Service VMs](#adr-0006-ufw-firewall-on-debian-service-vms-accepted)
- [Future ADRs](#future-adrs)

---

## ADR-0001: Proxmox VE 9.1 as Host Hypervisor [accepted]

**Date:** 2026-02-07

**Context:**  
HaaS-platform requires a stable hypervisor with:
- VM lifecycle control
- snapshot/backup support
- ZFS integration
- VFIO passthrough capability
- simple bridged networking support

Lab host: `labcore01` (`192.168.1.123`).

**Decision:**  
Use **Proxmox VE 9.1.1** (kernel `6.17.2-1-pve`) as the host hypervisor.

**Alternatives considered:**
- ESXi (paid licensing, restricted free tier)
- Ubuntu + KVM (manual management overhead)
- XCP-ng (Xen-based, different tooling ecosystem)

**Consequences:**
- + Mature ecosystem and strong community adoption
- + Integrated backup + snapshot tooling
- + Supports ZFS + VFIO passthrough workflows
- - Lab storage is currently a degraded single SSD (not production safe)

---

## ADR-0002: Debian 12 Golden Template (VM100) [accepted]

**Date:** 2026-02-07

**Context:**  
A standardized VM baseline is required for client-ready deployments.
Rebuilding VMs manually creates configuration drift and increases support cost.

A template-based approach allows:
- consistent clones
- faster provisioning
- repeatable security posture
- simpler documentation

**Decision:**  
Create **VM100** as the Debian 12 Golden Template.

VM100 is treated as the baseline source of truth for all service VMs.

**Alternatives considered:**
- Build every VM manually (slow, inconsistent)
- Use LXC templates (less flexible for some workloads)
- Use cloud-init images only (requires extra automation maturity)

**Consequences:**
- + All production VMs can be cloned from one hardened baseline
- + Reduces build time and human error
- + Supports consistent backup/restore expectations
- - Requires strict change control on VM100
- - Template must remain minimal and stable

Reference doc:
- `vm/vm100-golden-template.md`

---

## ADR-0003: GPU Passthrough for VAAPI (Research Track) [accepted]

**Date:** 2026-02-07

**Context:**  
Some stacks (example: Immich) benefit from hardware acceleration (VAAPI decode).

Lab hardware includes a legacy GPU:
- Radeon HD 6750 (Juniper PRO)
- PCI ID: `1002:68bf`

GPU passthrough is not considered part of the standard client baseline, but must be documented for research and validation.

**Decision:**  
Use VFIO passthrough of GPU + HDMI audio device into a Debian service VM for VAAPI testing.

Driver stack:
- Mesa r600 VAAPI driver
- `/dev/dri` device mapping into containers

**Alternatives considered:**
- Software decode only (CPU heavy)
- iGPU passthrough (untested on current lab build)
- Dedicated bare-metal Docker host (reduced snapshot flexibility)

**Consequences:**
- + VAAPI hardware acceleration confirmed working
- + Allows validation of media workloads
- - Requires `q35` machine type
- - No live snapshots/migration with passthrough devices

Note:
This is a **research track** feature and should not be assumed for client baseline deployments.

---

## ADR-0004: SSH Key-Only Authentication [accepted]

**Date:** 2026-02-09

**Context:**  
Password-based SSH access increases risk of brute-force attacks.
Client-ready infrastructure requires hardened remote access defaults.

**Decision:**  
Use SSH key-only authentication:
- Ed25519 keys
- Disable root login
- Disable PasswordAuthentication

Example key:
- `~/.ssh/haas_proxmox.pub`

Example users:
- `haas-admin@labcore01`
- `user1default@debian12-base`

**Alternatives considered:**
- Password + fail2ban
- Key + password hybrid

**Consequences:**
- + Strong baseline security posture
- + Reduces attack surface significantly
- - Requires key lifecycle management
- - Requires documented recovery process if keys are lost

---

## ADR-0005: Proxmox Firewall Baseline [accepted]

**Date:** 2026-02-09

**Context:**  
The Proxmox host is high value infrastructure and should expose minimal ports.
Proxmox WebUI is frequently targeted on the open internet.

**Decision:**  
Enable a baseline Proxmox firewall rule set allowing only:
- SSH (22/tcp)
- Proxmox WebUI (8006/tcp)

All other inbound traffic is dropped.

**Alternatives considered:**
- Host-only firewall rules
- UFW rules on the host (less integrated with Proxmox tooling)

**Consequences:**
- + Minimizes exposed services on hypervisor
- + Matches least privilege philosophy
- - Firewall rules must be maintained carefully to avoid lockout

---

## ADR-0006: UFW Firewall on Debian Service VMs [accepted]

**Date:** 2026-02-09

**Context:**  
Service VMs host containers and may expose multiple services internally.
A baseline firewall is needed before reverse proxy deployment.

**Decision:**  
Enable UFW on Debian service VMs with default deny policy.

Allow baseline ports:
- 22/tcp (SSH)
- 80/tcp and 443/tcp (reverse proxy access, when applicable)

**Alternatives considered:**
- nftables (more complex for baseline standardization)
- iptables (manual maintenance)

**Consequences:**
- + Easy to document and support
- + Clear baseline security posture
- - Rules will need refinement as stacks grow

---

## Future ADRs

Planned decisions that must be documented once finalized:

- ADR-0007: Reverse proxy selection (Traefik vs Nginx) [proposed]
- ADR-0008: ZFS mirror storage for client deployments [proposed]
- ADR-0009: Static IP reservations + VLAN segmentation standard [proposed]
