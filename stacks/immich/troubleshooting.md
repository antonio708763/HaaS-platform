Purpose:
Common issues, error messages, and resolution steps for Immich stack deployment and operation.

Scope:
Lab/research deployment troubleshooting. Covers container startup, GPU acceleration, database, and networking issues.

This document addresses:
- Container failures and restart loops
- VAAPI/GPU passthrough problems
- Database initialization errors
- Reverse proxy connectivity issues

---

## Table of Contents

### Container Issues
- [Service Won't Start](#service-wont-start)
- [Restart Loops](#restart-loops)
- [Permission Denied](#permission-denied)

### GPU/VAAPI Issues
- [No Hardware Acceleration](#no-hardware-acceleration)
- [vainfo Empty](#vainfo-empty)
- [Permission on /dev/dri](#permission-on-devdri)

### Database Issues
- [Postgres Won't Start](#postgres-wont-start)
- [Connection Refused](#connection-refused)
- [pgvector Extension Missing](#pgvector-extension-missing)

### Networking Issues
- [Proxy Network Missing](#proxy-network-missing)
- [Can't Reach Immich](#cant-reach-immich)
- [Health Check Fails](#health-check-fails)

### Notes
- [Notes](#notes)

---

## Container Issues

### Service Won't Start
Symptom: `docker compose up -d` fails, container exits immediately

Check:
docker compose logs immich-server

Common fixes:
Missing .env file
cp .env.example .env

Database not ready (race condition)
docker compose down
docker compose up database redis
sleep 30
docker compose up -d

text

### Restart Loops
Symptom: Container constantly restarts (check `docker ps`)

Check:
docker compose logs --tail=50 immich-server

Common causes:
Database password mismatch in .env
DB_PASSWORD=your_secure_password # Must match all services

Redis unavailable
docker compose logs redis

text

### Permission Denied
Symptom: `EACCES: permission denied` on ./library path

Fix:
sudo chown -R 1000:1000 ./library
sudo chmod 755 ./library

text

---

## GPU/VAAPI Issues

### No Hardware Acceleration
Symptom: `LIBVA_DRIVER_NAME=r600` ignored, CPU fallback

Inside Debian VM, verify:
lspci -nnk | grep VGA

Expect: radeon driver on card1
ls -l /dev/dri

Expect: card1 owned by root:video, renderD128 by root:render
text

Fix (inside VM):
sudo usermod -aG video,render $USER
newgrp video render

text

### vainfo Empty
Symptom: `vainfo` shows no profiles

Check:
export LIBVA_DRIVER_NAME=r600
vainfo

Expect: VAProfileH264... VAProfileMPEG2...
text

### Permission on /dev/dri
Symptom: Container can't access GPU devices

Verify host groups:
getent group video # Should return 44
getent group render # Should return 105

text

docker-compose.yml already maps:
group_add:

"44" # video

"105" # render

text

---

## Database Issues

### Postgres Won't Start
Symptom: `immich-postgres` container crashes on startup

Check logs:
docker compose logs database

Common fix:
docker compose down
docker volume rm haas-platform_pgdata
docker compose up -d

Fresh database initialization
text

### Connection Refused
Symptom: `immich-server` can't connect to `database`

Verify network:
docker network inspect haas-platform_immich

All services must show under same network
text

### pgvector Extension Missing
Symptom: ML features fail silently

Image already includes pgvector (`pgvector/pgvector:pg14`). No action needed.

---

## Networking Issues

### Proxy Network Missing
Symptom: `proxy` network doesn't exist

Fix:
docker network create proxy
docker compose up -d

text

### Can't Reach Immich
Symptom: Reverse proxy 502/404 or connection refused

Check:
docker compose ps # All services "Up"
curl immich-server:3001/health # From inside proxy network

text

### Health Check Fails
Symptom: `curl http://immich-server:3001/health` fails

Status codes:
200 OK     → Healthy
502 Bad Gateway → Proxy misconfigured
Connection refused → Container down

---

## Notes

- Always check logs first: `docker compose logs -f [service]`
- Restart order matters: `database → redis → immich-server → microservices → ml`
- GPU issues usually group permissions (`video/render`) or driver mismatch
- Database volume corruption requires `docker volume rm pgdata`
- Use `docker compose down -v` only as last resort (wipes data)

Common log patterns:
Database ready
"database system is ready to accept connections"

Immich healthy
"Immich has started successfully"

VAAPI working
"Using VAAPI driver: r600"

text

---

Quick debug checklist:
[ ] docker compose ps (all Up?)
[ ] docker compose logs (recent errors?)
[ ] docker network ls (proxy + immich exist?)
[ ] ls -l /dev/dri (permissions correct?)
[ ] cat .env (passwords match?)
