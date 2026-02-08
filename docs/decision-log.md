# Architecture Decision Records (ADRs)

This file records the major technical decisions made during HaaS-platform development. Each ADR follows the format: **Context → Decision → Alternatives → Consequences**.

Status codes:
- **proposed** - Under discussion
- **accepted** - Decision made, implementation in progress  
- **deprecated** - Superseded by newer decision
- **superseded** - Replaced by specific ADR #XXX

---

## ADR-0001: Use Proxmox VE 9.1 as Host Hypervisor [accepted]

**Status**: accepted  
**Date**: 2026-02-07  
**Author**: [Your Name]

### Context
Need a stable, well-documented hypervisor for hosting a single service VM running containerized applications. Must support:
- ZFS storage with snapshots
- VFIO GPU passthrough for VAAPI hardware acceleration
- Simple bridge networking (vmbr0 → LAN)
- Long-term support for production client deployments

### Decision
Use Proxmox VE 9.1.1 (kernel 6.17.2-1-pve) as the bare-metal host hypervisor.

### Alternatives Considered
| Option | Pros | Cons |
|--------|------|------|
| Proxmox VE | Mature ZFS integration, VFIO passthrough, web UI, LTS releases | Learning curve for ZFS/passthrough |
| VMware ESXi | Enterprise features | Licensing costs, less homelab-friendly |
| Ubuntu Server + KVM/libvirt | Most flexible | Manual configuration, no integrated UI |
| XCP-ng | Free Xen-based | Smaller community, less homelab tooling |

### Consequences
- **Positive**: Proven homelab platform with excellent ZFS + VFIO support
- **Negative**: Single SSD storage currently DEGRADED (action item for production)
- **Neutral**: Adds hypervisor layer but enables VM template cloning for clients

---

## ADR-0002: Single Debian 12 VM for Docker Services [accepted]

**Status**: accepted  
**Date**: 2026-02-07  
**Author**: [Your Name]

### Context
Need a container host VM with:
- Predictable base OS for reproducible deployments
- Direct LAN access (no NAT)
- GPU passthrough for media processing workloads
- Template-friendly for client cloning

### Decision
Deploy a single Debian 12 VM (VMID 900) as the Docker service host with:
- q35 machine type + OVMF UEFI
- host CPU passthrough
- VFIO GPU passthrough (01:00.0/01:00.1)
- virtio-scsi-single disk controller
- 2 cores, 2GB RAM baseline

### Alternatives Considered
| Option | Pros | Cons |
|--------|------|------|
| Single Debian VM | Simple, template-friendly, full GPU access | Single point of failure |
| Multiple lightweight LXC containers | Resource efficient | GPU passthrough limitations |
| Bare-metal Docker | Simplest | No VM snapshots/backups |

### Consequences
- **Positive**: VM snapshots work (disk-only), easy to clone for new clients
- **Negative**: No live snapshots with GPU passthrough (documented workaround)
- **Neutral**: Adds minor virtualization overhead (~5% CPU)

---

## ADR-0003: GPU Passthrough to Service VM for VAAPI [accepted]

**Status**: accepted  
**Date**: 2026-02-07  
**Author**: [Your Name]

### Context
Immich requires hardware-accelerated video decode (VAAPI) for photo processing. Lab hardware includes legacy AMD Radeon HD 6750 (Juniper PRO, PCI 1002:68bf).

### Decision
Passthrough full GPU + HDMI audio to Debian service VM:
hostpci0: 01:00.0 (GPU, pcie=1)
hostpci1: 01:00.1 (HDMI audio, pcie=1)

text
Guest detects as `radeon` driver with `/dev/dri/card1` + `renderD128`. Containers mount `/dev/dri` with `LIBVA_DRIVER_NAME=r600`.

### Alternatives Considered
| Option | Pros | Cons |
|--------|------|------|
| GPU passthrough | Full VAAPI decode, matches production needs | No live snapshots, PCIe slot usage |
| Software decode | Simpler, snapshot-friendly | CPU-intensive, slower processing |
| iGPU only | No PCIe usage | Ryzen 3 2200G Vega untested with Immich |

### Consequences
- **Positive**: Hardware acceleration confirmed working (`vainfo`)
- **Negative**: q35 machine required, no live VM snapshots/migrations
- **Neutral**: Lab GPU is decode-only (r600 driver limitation)

---

## Future ADRs (placeholders)
ADR-0004: [Network segmentation / VLANs for client builds] [proposed]
ADR-0005: [Storage redundancy - ZFS mirror minimum] [proposed]
ADR-0006: [Reverse proxy + shared Docker network design] [proposed]

text
undefined
