# Architecture Decision Records (ADRs)

Records major technical decisions for HaaS-platform. Format: **Context → Decision → Alternatives → Consequences**.

Status: proposed | accepted | deprecated | superseded

---

## ADR-0001: Proxmox VE 9.1 as Host Hypervisor [accepted]
**Date:** 2026-02-07  
**Context:** Stable hypervisor with ZFS, VFIO GPU passthrough, simple bridge networking for labcore01 (192.168.1.123).  
**Decision:** Proxmox VE 9.1.1 (kernel 6.17.2-1-pve).  
**Alternatives:** ESXi (paid), Ubuntu+KVM (manual), XCP-ng (Xen).  
**Consequences:** +Proven platform, -Single SSD DEGRADED (lab only).

## ADR-0002: Single Debian 12 VM for Docker Services [accepted]
**Date:** 2026-02-07  
**Context:** Container host VM (debian12-base, 192.168.1.50) with GPU passthrough, template-friendly.  
**Decision:** Debian 12 VM (VMID 900) with q35, host CPU, VFIO GPU (01:00.0/01:00.1).  
**Alternatives:** LXC (no GPU), bare-metal Docker (no snapshots).  
**Consequences:** +VM cloning easy, -No live snapshots (GPU limitation).

## ADR-0003: GPU Passthrough for VAAPI [accepted]
**Date:** 2026-02-07  
**Context:** Immich needs VAAPI decode. Lab GPU: Radeon HD 6750 (PCI 1002:68bf).  
**Decision:** Passthrough GPU+audio to VM900, r600 driver, /dev/dri mount.  
**Alternatives:** Software decode (CPU heavy), iGPU (untested).  
**Consequences:** +VAAPI working, -q35 required, decode-only.

## ADR-0004: SSH Key-Only Authentication [accepted]
**Date:** 2026-02-09  
**Context:** Eliminate password brute-force on labcore01 + debian12-base.  
**Decision:** Ed25519 keys (`~/.ssh/haas_proxmox.pub`). Disable root login, PasswordAuthentication=no.  
**Alternatives:** Password+fail2ban, key+password hybrid.  
**Consequences:** +Zero password risk, -Key management required. Users: haas-admin@labcore01, user1default@debian12-base.

## ADR-0005: Proxmox Firewall Baseline [accepted]
**Date:** 2026-02-09  
**Context:** Restrict labcore01 to SSH(22)+WebUI(8006) only.  
**Decision:** Datacenter firewall: ACCEPT 22/tcp, 8006/tcp → DROP all.  
**Alternatives:** Host-only firewall, ufw (Debian-only).  
**Consequences:** +Minimized attack surface, -Rule maintenance needed.

## ADR-0006: UFW Firewall on Debian VM [accepted]
**Date:** 2026-02-09  
**Context:** Secure debian12-base before reverse proxy deployment.  
**Decision:** ufw default deny → allow 22/ssh, 3001/immich, 80/443 proxy.  
**Alternatives:** nftables (complex), iptables (manual).  
**Consequences:** +Simple ruleset, -Refine after proxy stacks deployed.

---

## Future ADRs
- ADR-0007: Reverse proxy (Traefik/Nginx) [proposed]
- ADR-0008: ZFS mirror for v2-client [proposed]  
- ADR-0009: Static IP reservations + VLANs [proposed]
