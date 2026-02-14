# Platform Philosophy (HaaS-platformV2)

Purpose:  
Define the core design principles, business constraints, and decision framework guiding the development of **HaaS-platformV2**.

Scope:  
Applies to all technical and business decisions from lab research through client deployments.

This philosophy drives:

- Simple plug-and-play appliances for non-technical residential customers
- Open-source software with commercial hardware + support model
- Privacy-first, subscription-free alternative to Big Tech cloud services
- Standardized, remotely manageable infrastructure

---

## Table of Contents

- [Core Principles](#core-principles)
  - [Simplicity](#simplicity)
  - [Privacy](#privacy)
  - [Ownership](#ownership)
  - [Standardization](#standardization)
- [Business Model](#business-model)
  - [Hardware + Service](#hardware--service)
  - [Customer Scope](#customer-scope)
  - [Support Boundaries](#support-boundaries)
- [Technical Constraints](#technical-constraints)
  - [Open Source Priority](#open-source-priority)
  - [Hardware Standardization](#hardware-standardization)
  - [Automation Requirements](#automation-requirements)
- [Decision Framework](#decision-framework)
  - [Technology Choices](#technology-choices)
  - [Security Posture](#security-posture)
- [Notes](#notes)

---

## Core Principles

### Simplicity

Customers should plug in one box, log in once, and everything works.

Design goals:

- No CLI required from end users
- One portal experience (reverse proxy → service dashboard)
- Standardized onboarding (5–15 minutes maximum)
- Updates should be invisible to customers
- Service recovery must be one-click (or automated)

The customer experience must feel like:

> “I forgot I even have this running.”

---

### Privacy

All customer data stays local.

Privacy requirements:

- No cloud scanning
- No ads
- No tracking
- No vendor data collection
- No external dependency required for normal use

Remote support must be:

- opt-in
- encrypted
- revocable
- transparent

If remote access is enabled, the customer must be able to disable it at any time.

---

### Ownership

Customers own their hardware and their data permanently.

Ownership requirements:

- One-time hardware purchase
- No subscription required for core services
- Optional support contract (updates, monitoring, troubleshooting)
- Easy data export and restore
- No vendor lock-in

The platform must remain usable even if the customer cancels support.

---

### Standardization

A standardized platform is the foundation of supportability.

Standardization requirements:

- Limited hardware SKUs (2–3 maximum)
- Identical software deployment across all clients
- Reproducible from documentation alone
- VM templates + Docker Compose stacks
- Minimal per-customer customization

A system that cannot be reproduced is not production-ready.

---

## Business Model

### Hardware + Service

Revenue model:

- Hardware cost = one-time purchase (covers parts + labor)
- Service contract = recurring revenue (support, updates, monitoring)

Example model (TBD):

- $300–$800 hardware
- $20–$50/month support

---

### Customer Scope

Target customers:

- Non-technical residential households
- Families wanting private photo backup
- Privacy-conscious individuals
- People tired of iCloud / Google Photos / streaming subscriptions

Not target customers:

- Developers
- Enterprise environments
- Homelab enthusiasts
- People requesting custom software builds

This platform is meant to be an appliance, not a project.

---

### Support Boundaries

The support model must be defined clearly.

We support:

- Hardware failure (replacement process)
- Software updates (automated or scheduled)
- Service downtime (remote restart / recovery)
- Data recovery assistance (restore workflow)

We do not support:

- Customer ISP / router problems
- Custom software requests
- Unsupported media format troubleshooting
- User training beyond initial onboarding
- Per-device troubleshooting unrelated to the platform

---

## Technical Constraints

### Open Source Priority

All customer-facing software must be open source.

Allowed examples:

- Immich
- Nextcloud
- Jellyfin
- Vaultwarden
- AdGuard Home / Pi-hole

Not allowed examples:

- Plex Pass (paid lock-in)
- Closed-source SaaS platforms
- Proprietary cloud services disguised as “self-hosted”

Commercial exceptions are allowed only for internal tooling (monitoring, remote support), if justified.

---

### Hardware Standardization

Client hardware must remain simple and quiet.

Target hardware profile:

- Mini-PC or small form factor appliance
- Low power usage
- Low noise (no loud fans)
- Simple drive replacement model

Example SKUs (future standard):

- **Basic SKU**
  - i3-class CPU
  - 16GB RAM
  - 256GB SSD + 2TB HDD
  - iGPU only

- **Plus SKU**
  - i5-class CPU
  - 32GB RAM
  - 512GB SSD + 4TB HDD
  - optional GPU (if justified)

This platform is not designed for rack servers or loud enterprise hardware.

---

### Automation Requirements

Every routine operation must be scripted or documented.

Automation requirements:

- Deploy a new node in ≤ 30 minutes from bare metal
- Update services with minimal downtime
- Restore from backup in ≤ 1 hour
- Failures must be diagnosable remotely

If something requires manual guessing, it is not production-ready.

---

## Decision Framework

### Technology Choices

Accept a technology only if it meets these criteria:

1. Open source license (Apache/MIT/GPL preferred)
2. Stable release track (LTS preferred)
3. Widely adopted in the homelab ecosystem
4. Fits a plug-and-play UX
5. Has clear documentation and community support

Reject a technology if it meets any of these criteria:

- Requires constant CLI babysitting
- Adds unnecessary single points of failure
- Has poor documentation or unclear defaults
- Consumes excessive CPU/RAM for residential hardware
- Creates vendor lock-in

---

### Security Posture

Security must be strong by default.

Baseline security principles:

- default-deny
- least privilege
- minimal exposed ports
- reproducible configuration

Security baseline expectations:

- SSH: keys only, no root login, Fail2Ban enabled
- Docker: non-root containers where possible
- Docker security: `no-new-privileges`, `cap_drop: ALL`
- Reverse proxy: only component exposing 80/443
- Firewall: explicit allow rules only
- Updates: staged testing before rollout to clients

A “secure by default” system is mandatory for a client product.

---

## Notes

### Target Customer Journey

Ideal customer experience:

- **Day 1:** unbox → plug Ethernet → power on → call support (15 minutes)
- **Day 2+:** open browser → `https://home.ar` → click Photos / Files / Movies
- **Month 2+:** system runs silently with no attention required

Success metric:

> Customers should never need to think about infrastructure.

---

### Anti-Patterns to Avoid

The platform must avoid these failures:

- “Works on my machine” deployments
- Per-customer custom configurations
- Manual intervention for routine tasks
- Undocumented CLI hacks
- Breaking changes without rollback plans

Lab validates architecture.  
Production demands reliability with zero excuses.
