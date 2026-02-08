Purpose:
Document the Immich photo management stack deployment, configuration, and prerequisites for the HaaS-platform service VM.

Scope:
Lab/research deployment on Debian 12 VM (VMID 900). Uses GPU passthrough for VAAPI hardware acceleration.

This stack provides:
- Private photo/video backup and organization
- Machine learning face recognition and search
- Hardware-accelerated video processing (AMD radeon driver)

---

## Table of Contents

### Stack Overview
- [Services](#services)
- [Prerequisites](#prerequisites)
- [Networking](#networking)

### Deployment
- [Deploy Commands](#deploy-commands)
- [Configuration](#configuration)
- [Validation](#validation)

### Hardware Acceleration
- [GPU Requirements](#gpu-requirements)
- [VAAPI Setup](#vaapi-setup)

### Notes
- [Notes](#notes)

---

## Stack Overview

### Services
Immich-server          ghcr.io/immich-app/immich-server:release
Immich-microservices   ghcr.io/immich-app/immich-server:release  
Immich-machine-learning ghcr.io/immich-app/immich-machine-learning:release
Redis                  redis:6
Database               pgvector/pgvector:pg14

### Prerequisites
- Debian 12 service VM with Docker + docker-compose
- Shared proxy network (`docker network create proxy`)
- GPU passthrough configured (`/dev/dri` accessible)
- Persistent storage: `./library` directory + postgres volume

### Networking
- proxy network    External (reverse proxy access)
- immich network   Internal (service communication)

---

## Deployment

### Deploy Commands

cd /srv/HaaS-platform/stacks/immich
cp .env.example .env
# Edit .env (required: DB_PASSWORD, etc.)
docker compose up -d

### Configuration
.env file required. See .env.example for template.
Required variables:
DB_PASSWORD=your_secure_password
REDIS_HOSTNAME=redis
DB_HOSTNAME=database

Storage paths:
./library → /usr/src/app/upload (photos/videos)

### Validation
docker compose logs -f immich-server
curl http://immich-server:3001/health
Access via reverse proxy: https://photos.lab.local

---

## Hardware Acceleration

### GPU Requirements
- AMD Radeon HD 6750 (Juniper PRO, PCI 1002:68bf)
- VFIO passthrough to Debian VM
- Host groups: video(44), render(105)

### VAAPI Setup
LIBVA_DRIVER_NAME=r600 (Mesa r600 driver)
Container mounts: /dev/dri:/dev/dri
Container groups: 44(video), 105(render)

Validation (inside VM):
lspci -nnk | grep VGA
vainfo
ls -l /dev/dri

Expected:
card1 → radeon driver
vainfo → radeon decode profiles

---

## Notes

- Security: Non-root user(1000:1000), no-new-privileges, cap_drop ALL
- Logging: JSON-file, 10m x 3 files rotation
- Storage: Single volume pgdata persists database
- Reverse proxy: Expects external proxy network (Traefik/Nginx)
- Lab only: Uses research GPU. Client builds need modern VAAPI GPUs

Health endpoints:
GET /health → Immich server status
GET /api/server/info → System information

---

Next steps: Reverse proxy configuration → HTTPS certificates → Additional stacks (Nextcloud, Jellyfin)
