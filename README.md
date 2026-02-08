Purpose:
High-level overview of the HaaS-platform repository. Entry point for understanding the project structure, current lab state, and path to client reference builds.

Scope:
Lab/research phase documentation. NOT representative of final client deployments.

This repository validates:
- Proxmox → Debian VM → Docker stack architecture  
- GPU passthrough + VAAPI acceleration
- Documentation patterns for reproducible deployments  
- Service stack modularity and standardization  

---

## Table of Contents

### Platform Overview
- [Purpose and Scope](#purpose-and-scope)
- [Current Architecture](#current-architecture)
- [Repository Status](#repository-status)

### Repository Structure
- [Root Level](#root-level)
- [Documentation](#documentation)
- [Hardware](#hardware)
- [Host Configuration](#host-configuration)
- [Service VM](#service-vm)
- [Application Stacks](#application-stacks)

### Getting Started
- [Lab Access](#lab-access)
- [Development Workflow](#development-workflow)

### Roadmap
- [Lab Phase](#lab-phase)
- [Reference Build Phase](#reference-build-phase)
- [Business Phase](#business-phase)

### Warnings and Limitations
- [Notes](#notes)

---

## Purpose and Scope

HaaS Platform (Hardware as a Service) researches standardized self-hosting appliances for residential customers. Delivers private cloud, media server, photo backup—locally owned, subscription-free.

Current State: Lab v1.0 (research hardware, single SSD storage)  
Target State: Client reference builds v2.0+ (redundant storage, automated deployment)

---

## Current Architecture

Research Hardware (Ryzen 3 2200G + Radeon HD 6750)
├── Proxmox VE 9.1.1 (192.168.1.123) [DEGRADED storage ⚠️]
│   └── Debian 12 VM (VMID 900, 192.168.1.50)
│       └── Docker
│           ├── Immich (VAAPI accelerated)
│           ├── Vaultwarden
│           └── Uptime Kuma

---

## Repository Status

| Component | Status | Details |
|-----------|--------|---------|
| Documentation | 80% complete | Hardware, host, VM fully documented |
| Lab Hardware | Active | Research platform (see hardware/) |
| Proxmox Host | Running | DEGRADED storage (lab only) |
| Service VM | Running | Debian 12 + Docker |
| Service Stacks | 1 complete | Immich (photos) |

---

## Repository Structure

### Root Level
HaaS-platform/
├── docs/                 Decision log, glossary
├── hardware/             Component specifications
├── host/                 Proxmox host configuration
├── vm/                   Debian service VM template
├── stacks/               Docker service stacks
├── philosophy/           Design principles
└── README.md            This file

### Documentation
- docs/decision-log.md - Architecture Decision Records (ADRs)
- docs/glossary.md - Project terminology

### Hardware
- hardware/lab-build-research.md - Current research platform specs

### Host Configuration
- host/network-design.md - Lab networking (vmbr0 → LAN)
- host/proxmox-setup.md - Proxmox installation + storage

### Service VM
- vm/debian12-base.md - Debian 12 Docker host template

### Application Stacks
- stacks/immich/ - Photo management (VAAPI accelerated)

---

## Getting Started

### Lab Access

Proxmox host
ssh root@192.168.1.123

Service VM  
ssh user@192.168.1.50

### Development Workflow

git clone https://github.com/[yourusername]/HaaS-platform.git /srv/HaaS-platform

Deploy stack example (Immich)
cd /srv/HaaS-platform/stacks/immich
cp .env.example .env
# Edit .env then:
docker compose up -d

---

## Roadmap

### Lab Phase (Current → v1.5)
- [x] Proxmox host + Debian VM operational
- [x] Immich stack deployed (GPU accelerated)  
- [ ] Reverse proxy + HTTPS
- [ ] Backup strategy (host + VM + stacks)
- [ ] Additional stacks (Nextcloud, Jellyfin)

### Reference Build Phase (v2.0)
[x] VM template specification
[ ] ZFS mirror storage (no single points of failure)
[ ] Static IP assignments + VLAN segmentation
[ ] Automated deployment scripts
[ ] Client onboarding procedures

### Business Phase (v3.0+)
Hardware SKUs → Pricing → Legal → Sales → Remote support

---

## Notes

- Storage Warning: Lab rpool is DEGRADED (single SSD with data errors). NOT suitable for production.
- Hardware Scope: Current platform uses legacy components for research only.
- Networking: Simple single-subnet design (192.168.1.0/24). VLANs planned for production.
- Snapshots: GPU passthrough VMs require disk-only snapshots (no RAM state).

Lab validated: GPU passthrough → VAAPI → Immich processing confirmed working.

---

Next milestone: Complete stacks/immich/ documentation → Reverse proxy deployment → Backup validation.
