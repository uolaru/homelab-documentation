# Service Inventory

## VMs

| ID | Name | OS | Purpose | IP | Status |
|----|------|----|---------|----|--------|
| 201 | media-stack | Ubuntu 22.04 | Docker host - media services | 192.168.100.139 | ✓ Running |

## Running LXC Containers

### Monitoring
| ID | Name | OS | Purpose | Access | Status |
|----|------|----|---------| -------|--------|
| 100 | cockpit | Debian/Ubuntu | System management | http://192.168.100.94:9090 | ✓ Running |
| 110 | grafana | Debian/Ubuntu | Dashboards | http://192.168.100.108:3000 | ✓ Running |
| 113 | prometheus | Debian/Ubuntu | Metrics | http://192.168.100.113:9090 | ✓ Running |

### AI/Automation
| ID | Name | OS | Purpose | Access | Status |
|----|------|----|---------| -------|--------|
| 107 | ollama | Debian/Ubuntu | Local LLM | http://192.168.100.105:11434 | ✓ Running |
| 108 | immich | Debian/Ubuntu | Photo management | http://192.168.100.106:2283 | ✓ Running |
| 109 | n8n | Debian/Ubuntu | Workflow automation | http://192.168.100.107:5678 | ✓ Running |

### Network Services
| ID | Name | OS | Purpose | Access | Status |
|----|------|----|---------| -------|--------|
| 112 | pihole | Debian/Ubuntu | Ad blocker | http://192.168.100.XXX | ✓ Running |

### Other
| ID | Name | OS | Purpose | Status |
|----|------|----|---------| -------|
| 115 | proxmox-backup-server | Debian/Ubuntu | Backups | ✓ Running |
| 118 | openwebui | Debian/Ubuntu | LLM interface | ✓ Running |

## Deprecated LXC Containers (Migrated to Docker)

| ID | Name | Purpose | Migration Status |
|----|------|---------|------------------|
| 101 | jellyfin | Media streaming | → VM 201 (Docker) |
| 102 | jellyseerr | Media requests | → VM 201 (Docker) |
| 103 | qbittorrent | Downloads | → VM 201 (Docker) |
| 104 | prowlarr | Indexer management | → VM 201 (Docker) |
| 105 | radarr | Movie automation | → VM 201 (Docker) |
| 106 | sonarr | TV automation | → VM 201 (Docker) |

**Action:** Delete after 1 week of stable VM 201 operation

## Docker Services (VM 201)

| Service | Port | Purpose | Access |
|---------|------|---------|--------|
| Jellyfin | 8096 | Media streaming | http://192.168.100.139:8096 |
| qBittorrent | 8090 | Torrent client | http://192.168.100.139:8090 |
| Prowlarr | 9696 | Indexer manager | http://192.168.100.139:9696 |
| Radarr | 7878 | Movie automation | http://192.168.100.139:7878 |
| Sonarr | 8989 | TV automation | http://192.168.100.139:8989 |
| Jellyseerr | 5055 | Media requests | http://192.168.100.139:5055 |

## Stopped Services (Need Review)

| ID | Name | Purpose | Decision |
|----|------|---------|----------|
| 111 | unbound | DNS resolver | Evaluate vs Pi-hole |
| 114 | twingate-connector | VPN access | Document or remove |
| 117 | syncthing | File sync | Evaluate usage |
| 120 | crafty-controller | Minecraft management | Remove |

## Stopped VMs

| ID | Name | Purpose | Status |
|----|------|---------|--------|
| 121 | twitch | Twitch Drops | Stopped |
| 122 | deJoaca | Testing | Stopped |

## Infrastructure Summary

### Resource Allocation
- **Proxmox Host:** i7-6700, 32GB RAM total
- **VM 201 (media-stack):** 8GB RAM, 4 cores
- **LXC Containers:** ~8GB total
- **Available:** ~13GB RAM

### Storage
- **Primary:** 912GB local storage
- **Media:** 931.5GB external HDD (/dev/sdb1)
  - Mounted at `/mnt/pve/hddextern` on host
  - Mounted in VM 201 at `/mnt/hdd`
  - 668GB used, 203GB available

## Next Actions
- [ ] Monitor VM 201 stability for 1 week
- [ ] Delete deprecated LXC containers (101-106)
- [ ] Create monitoring VM (VM 202)
- [ ] Document monitoring stack
- [ ] Review stopped services - keep or delete
- [ ] Set up backup strategy for Docker volumes
