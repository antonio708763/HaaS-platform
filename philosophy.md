Purpose:
Core design principles, business constraints, and decision framework guiding HaaS-platform development.

Scope:
Applies to all technical and business decisions from lab research through client deployments.

This philosophy drives:
- Simple plug-and-play appliances for non-technical residential customers
- Open-source software with commercial hardware+service model
- Privacy-first, subscription-free alternative to Big Tech cloud services
- Standardized, remotely manageable infrastructure

---

## Table of Contents

### Core Principles
- [Simplicity](#simplicity)
- [Privacy](#privacy)
- [Ownership](#ownership)
- [Standardization](#standardization)

### Business Model
- [Hardware + Service](#hardware--service)
- [Customer Scope](#customer-scope)
- [Support Boundaries](#support-boundaries)

### Technical Constraints
- [Open Source Priority](#open-source-priority)
- [Hardware Standardization](#hardware-standardization)
- [Automation Requirements](#automation-requirements)

### Decision Framework
- [Technology Choices](#technology-choices)
- [Security Posture](#security-posture)

### Notes
- [Notes](#notes)

---

## Core Principles

### Simplicity
Customers plug in one box. One login. Services "just work."
- No CLI required from end users
- Single web portal (reverse proxy → app dashboard)
- Standardized onboarding (5-minute setup call maximum)
- Updates invisible to customers

### Privacy
All data stays local. No cloud scanning, no ads, no tracking.
- Self-hosted = fully client-owned data
- No phoning home to our servers
- Encrypted remote support only when requested
- Transparent remote access (opt-in, revocable)

### Ownership
Customers own hardware and data forever. No subscriptions to core services.
- One-time hardware purchase
- Optional service contract for updates/support
- Easy data export/restore
- No vendor lock-in

### Standardization
One SKU → One software stack → One support process.
- Limited hardware choices (2-3 SKUs maximum)
- Identical software across all deployments
- Reproducible from documentation alone
- VM templates + automated stack deployment

---

## Business Model

### Hardware + Service
Hardware Cost → One-time purchase (break-even on parts + labor)
Service Contract → Recurring revenue (updates, monitoring, support)

text

Model: $300-800 hardware + $20-50/month support (TBD)

### Customer Scope
Target: Non-technical residential households
- Families wanting private photo backup
- Privacy-conscious individuals
- Tired of iCloud/Google Photos/Netflix subscriptions
- NOT: Developers, enterprise, homelab enthusiasts

### Support Boundaries
We fix:
- Hardware failure → replacement
- Software updates → automated
- Service downtime → remote restart

We don't fix:
- Customer internet problems
- Custom software requests
- Media format compatibility
- User training beyond 1-hour onboarding

---

## Technical Constraints

### Open Source Priority
Everything customer-facing must be FOSS.
ALLOWED: Nextcloud, Jellyfin, Immich, Vaultwarden, AdGuard
NOT ALLOWED: Plex Pass, OnlyOffice Enterprise, proprietary SaaS

text

Commercial exceptions only for remote management/monitoring.

### Hardware Standardization
2-3 SKUs maximum, all passively cooled mini-PCs.
Basic: 16GB RAM, 256GB SSD + 2TB HDD, iGPU
Plus: 32GB RAM, 512GB SSD + 4TB HDD, discrete GPU

text

No servers, rackmount, or loud fans.

### Automation Requirements
All operations must be scripted/documented.
Deploy new node: 30 minutes maximum from bare metal
Software update: 1 command, zero downtime
Backup restore: 1 hour maximum RTO

text

---

## Decision Framework

### Technology Choices
Accept if:
1. Open source (Apache/MIT/GPL)
2. Documented, stable (LTS preferred)
3. Homelab community adoption
4. Fits "plug-and-play" UX

Reject if:
1. Requires daily CLI babysitting
2. Single points of failure
3. Undocumented configuration
4. Heavy CPU/RAM (residential hardware limits)

### Security Posture
Default-deny, least privilege, automation-friendly.
SSH: Keys only, no root login, fail2ban
Docker: Non-root, no-new-privileges, cap_drop ALL
Firewall: Explicit ports only
Updates: Automated, tested on reference builds first

text

---

## Notes

Target customer journey:
Day 1: Unbox → plug Ethernet → power on → call support (15min)
Day 2+: https://home.mylastname.local → Files/Photos/Movies dashboard
Month 2+: "I forgot I even have this running"

Success metric: Customer can't tell if it's working (because it always is)

Anti-patterns to avoid:

text
- "Works on my machine" syndrome
- Per-customer customizations  
- Manual intervention for routine tasks
- Undocumented CLI hacks
Lab validates business viability. Production demands zero-excuses reliability.
