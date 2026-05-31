---
name: sysadmin
author: ihgalis
description: Host and container operations skill. Use for OS/network/storage/security, users/SSH/firewall/updates/systemd/DNS/time, Docker host prep, Docker Compose stacks, reverse proxies, container health checks, and deployment operations.
---

# Sysadmin

- **Sysadmin** owns: OS packages/updates, users & SSH, firewalling, DNS/time, disks/filesystems, backups, system services, kernel/sysctl, host security posture, Docker host setup, Docker Compose stacks, reverse proxies, container networking, container health checks, and deployment operations.
- Treat host and container work as one operational surface: check exposure, persistence, logs, backups, and rollback before changing services.

## Examples first

### Example: "Deploy Paperless with docker and make it reachable safely"

Typical sysadmin-owned flow:
- Ensure the host baseline is sane: updates, firewall, LAN-only exposure, storage for volumes, time sync.
- Write or adjust Docker Compose, add health checks, attach internal networks, put HTTP/HTTPS behind a reverse proxy, and verify endpoints.

## Security Deployment (from security-deploy)

Automated security hardening, HTTPS certificate management, firewall configuration, access control, audit reporting, and secure container patterns.

### Cron Jobs
- Auto-renewal: daily at 03:17
- Firewall reload: weekly
- Audit report: weekly on Monday 06:00

## Core workflows

### 1) Host baseline (before deploying anything)

Checklist:
- OS updates applied (security patches)
- Time sync working (chrony/systemd-timesyncd)
- Hostname + local DNS/`/etc/hosts` sanity
- SSH hardened (keys, no password auth if appropriate)
- Firewall configured (UFW/nftables); **avoid 0.0.0.0 binds** for admin UIs
- Storage plan: where Docker volumes live; disk space monitored
- Backups: at least "config + volumes + database dumps" plan

### 2) Docker host preparation

Sysadmin responsibilities:
- Install Docker Engine + Compose plugin
- Add user to `docker` group (or document sudo usage)
- Configure:
  - log rotation / `journald`
  - firewall rules for only intended LAN ports
  - optional kernel/sysctl tuning (only when required)

Verification commands (read-only):
```bash
docker version
docker compose version
docker info
```

### 3) Firewall and exposure rules (LAN-safe defaults)

Rules of thumb:
- Prefer binding admin services to a specific LAN IP, not `0.0.0.0`.
- Prefer a reverse proxy (Caddy/Traefik/nginx) as the single exposed ingress.
- For internal-only backends: use internal Docker networks plus host firewall rules.

### 4) Container stacks and reverse proxies

When managing containerized services, Sysadmin:
- creates or adjusts `docker-compose.yml` / `compose.yml`
- defines volumes, networks, restart policies, health checks, and environment handling
- keeps HTTP/HTTPS services behind Caddy, nginx, Traefik, or the existing reverse-proxy stack
- avoids broad direct port publishing unless Andre explicitly approves it
- verifies with `docker compose config`, `docker compose ps`, service logs, and endpoint checks

### 5) Container build and CI/CD-adjacent operations

Sysadmin may build/update images, install required host or language packages, and prepare deployment scripts when needed. Prefer official package sources, verify downloads when checksums/signatures exist, and document how to reproduce the build.

### 6) Systemd + service lifecycle (non-container services)

When something must run on the host (rare), Sysadmin:
- creates/edits unit files
- sets restart policies
- collects logs via `journalctl`

### 7) Backups and restore drills

Baseline approach:
- **Configs**: Git + encrypted secrets handling
- **Volumes**: snapshot/rsync/tar (service-by-service)
- **Databases**: scheduled dumps + restore test

Sysadmin's job is to make sure restore is actually possible (periodic drill).

## Operational task format

For host or container operations, the orchestrator should route a subtask like:

"Prepare <host|stack>: ensure ports, firewall, storage, DNS/time, Docker/Compose prerequisites, reverse proxy, health checks, and backup/restore assumptions are correct. Return: checklist + evidence commands + any required changes."

Return format:
- status: success|blocked|failed
- evidence: commands/logs/files inspected
- changes: what was changed (if any)
- risks: remaining exposures or open loops
