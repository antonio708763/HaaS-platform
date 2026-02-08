Purpose:
Define project-specific terminology for consistent communication across documentation, support, and customer-facing materials.

Scope:
Terms used throughout HaaS-platform repository and future client deployments.

This glossary ensures:
- Technical team uses consistent language
- Customer documentation is clear and precise
- No ambiguity in support tickets or runbooks

---

## Table of Contents

### Core Concepts
- [HaaS Node](#haas-node)
- [Service Stack](#service-stack)
- [Reference Build](#reference-build)

### Architecture Layers
- [Host](#host)
- [Service VM](#service-vm)
- [Proxy Network](#proxy-network)

### Hardware Terms
- [Research Hardware](#research-hardware)
- [Client SKU](#client-sku)

### Operational Terms
- [Lab Phase](#lab-phase)
- [Production Ready](#production-ready)

### Notes
- [Notes](#notes)

---

## Core Concepts

### HaaS Node
Complete deployed appliance (hardware + software) delivered to customer.
Example: Proxmox host + Debian service VM + 3 service stacks = 1 HaaS Node

text

### Service Stack
Cohesive group of Docker containers providing one customer feature.
Example: stacks/immich/ = Immich photo management stack

text

### Reference Build
Fully documented, tested, reproducible deployment targetting production.
Contrasts with: Lab/research builds (experimental hardware/config)

text

---

## Architecture Layers

### Host
Bare-metal Proxmox VE hypervisor running on customer hardware.
IP: 192.168.1.123 (lab), static/reserved in production
Role: VM management, ZFS storage, networking bridge

text

### Service VM
Single Debian 12 VM (VMID 900+) hosting all Docker service stacks.
Purpose: Container isolation, snapshots, GPU passthrough
Networking: Direct LAN access via vmbr0 (no NAT)

text

### Proxy Network
Shared Docker network (`proxy`) for reverse proxy access to all service stacks.
Created once: docker network create proxy
Used by: All stacks + Traefik/Nginx reverse proxy

text

---

## Hardware Terms

### Research Hardware
Lab-only components used for architecture validation.
Current: Ryzen 3 2200G + Radeon HD 6750 + single SSD (DEGRADED)
Status: NOT production suitable

text

### Client SKU
Standardized hardware bundle sold to customers.
Basic SKU: i3, 16GB RAM, 256GB SSD + 2TB HDD
Plus SKU: i5, 32GB RAM, 512GB SSD + 4TB HDD + GPU

text

---

## Operational Terms

### Lab Phase
Current research/experimentation stage (v1.0 - v1.5).
Goals: Validate architecture, document patterns, test stacks
Hardware: May use degraded/single-fault components

text

### Production Ready
Customer-facing deployment quality (v2.0+).
Requirements:

ZFS mirror (no single SSD)

VLAN segmentation

Automated backups

99.9% uptime SLA

text

---

## Notes

Terminology evolution:
Lab → Research Hardware → Client SKU
Single VM → Service VM → HaaS Node
Immich stack → Photo Stack → Private Photos

text

Customer-facing terms (avoid technical jargon):
"HaaS Node" → "Your Home Server Box"
"Service Stack" → "Photos App" / "Files App"
"Reference Build" → "Standard Setup"

text

Support ticket categorization:
#hardware → Client SKU failure
#host → Proxmox issues
#vm → Service VM problems
#stack-immich → Photos app issues
#network → Proxy/reverse proxy problems

text

Versioned terms:
v1.x = Lab/Research
v2.x = Reference Build
v3.x = Customer Production

text

---

Alphabetical quick reference:
HaaS Node, Host, Lab Phase, Production Ready, Proxy Network, Reference Build, Research Hardware, Service Stack, Service VM, Client SKU
