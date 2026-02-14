# Glossary (HaaS-platformV2)

Purpose:  
Define project-specific terminology for consistent communication across documentation, support, and customer-facing materials.

Scope:  
Terms used throughout HaaS-platformV2 documentation and future client deployments.

This glossary ensures:
- Technical team uses consistent language
- Customer documentation is clear and precise
- No ambiguity in support tickets or runbooks

---

## Table of Contents

- [Core Concepts](#core-concepts)
  - [HaaS Node](#haas-node)
  - [Service Stack](#service-stack)
  - [Reference Build](#reference-build)
- [Architecture Layers](#architecture-layers)
  - [Host](#host)
  - [Service VM](#service-vm)
  - [Proxy Network](#proxy-network)
- [Hardware Terms](#hardware-terms)
  - [Research Hardware](#research-hardware)
  - [Client SKU](#client-sku)
- [Operational Terms](#operational-terms)
  - [Lab Phase](#lab-phase)
  - [Production Ready](#production-ready)
- [Notes](#notes)
- [Alphabetical Quick Reference](#alphabetical-quick-reference)

---

## Core Concepts

### HaaS Node
A complete deployed appliance (hardware + software) delivered to a customer.

Example:  
Proxmox host + Debian Service VM + multiple service stacks = 1 HaaS Node

---

### Service Stack
A cohesive group of Docker containers providing one customer-facing feature.

Example:  
`stacks/immich/` = Immich photo management stack

---

### Reference Build
A fully documented, tested, and reproducible deployment target intended for production-quality standards.

This contrasts with lab/research builds which may use experimental hardware or incomplete standards.

---

## Architecture Layers

### Host
The bare-metal Proxmox VE hypervisor running on customer hardware.

Role:
- VM management
- ZFS storage management
- Networking bridge configuration

Example lab reference IP:  
`192.168.1.123` (static/reserved in production)

---

### Service VM
A standardized Debian 12 VM hosting all Docker service stacks.

Purpose:
- Container isolation
- Backup/restore operations
- Template-based scaling

Example VMID range:
- VM100 = golden template
- VM200 = Docker host (service VM)

Networking:
- Direct LAN access via `vmbr0` (no NAT)

---

### Proxy Network
A shared Docker network named `proxy` used for reverse proxy routing to all service stacks.

Created once per Docker host:

```bash
docker network create proxy
```

Used by:
- Traefik reverse proxy stack
- All service stacks that need HTTPS exposure

---

## Hardware Terms

### Research Hardware
Lab-only components used for architecture validation.

These are explicitly **NOT production suitable**.

Example lab hardware:
- Ryzen 3 2200G
- Radeon HD 6750
- Single SSD (DEGRADED)

---

### Client SKU
A standardized hardware bundle sold to customers.

Example SKUs:

**Basic SKU**
- i3 CPU
- 16GB RAM
- 256GB SSD
- 2TB HDD

**Plus SKU**
- i5 CPU
- 32GB RAM
- 512GB SSD
- 4TB HDD
- Optional GPU

---

## Operational Terms

### Lab Phase
Research/experimentation stage (v1.x).

Goals:
- Validate architecture
- Document patterns
- Test service stacks

Hardware may be non-standard or degraded during this phase.

---

### Production Ready
Customer-facing deployment quality (v2.x+).

Minimum requirements typically include:
- ZFS mirror (no single SSD failure risk)
- VLAN segmentation (future standard)
- Automated backups
- Restore verification procedures
- Uptime/reliability expectations

---

## Notes

### Terminology evolution
As the platform matures, language becomes more client-focused.

Examples:
- Lab → Research Hardware → Client SKU
- Single VM → Service VM → HaaS Node
- Immich stack → Photo Stack → Private Photos

---

### Customer-facing language (avoid jargon)
Use simplified names when talking to clients:

- "HaaS Node" → "Home Server Box"
- "Service Stack" → "Photos App" / "Files App"
- "Reference Build" → "Standard Setup"

---

### Support ticket categorization (recommended)
Suggested tags for issue tracking:

- `#hardware` → Client SKU failure
- `#host` → Proxmox issues
- `#vm` → Service VM problems
- `#stack-immich` → Photos stack issues
- `#network` → Proxy/reverse proxy problems

---

### Versioned terms
- v1.x = Lab/Research
- v2.x = Reference Build
- v3.x = Customer Production (future)

---

## Alphabetical Quick Reference

HaaS Node, Host, Lab Phase, Production Ready, Proxy Network, Reference Build, Research Hardware, Service Stack, Service VM, Client SKU
