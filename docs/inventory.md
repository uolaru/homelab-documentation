# Service Inventory

## Running LXC Containers

### Media Stack
| ID | Name | OS | Purpose | Access | Status |
|----|------|----|---------| -------|--------|
| 101 | jellyfin | Debian/Ubuntu | Media streaming | http://192.168.100.99:8096 | ✓ Running |
| 102 | jellyseerr | Debian/Ubuntu | Media requests | http://192.168.100.100:5055 | ✓ Running |
| 103 | qbittorrent | Debian/Ubuntu | Downloads | http://192.168.100.101:8090 | ✓ Running |
| 104 | prowlarr | Debian/Ubuntu | Indexer management | http://192.168.100.102:9696 | ✓ Running |
| 105 | radarr | Debian/Ubuntu | Movie automation | http://192.168.100.103:7878 | ✓ Running |
| 106 | sonarr | Debian/Ubuntu | TV automation | http://192.168.100.104:8989 | ✓ Running |

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

## Stopped Containers (Need Review)
| ID | Name | Purpose | Decision |
|----|------|---------|----------|
| 111 | unbound | DNS resolver | Evaluate vs Pi-hole |
| 112 | pihole | Ad blocker | Restart or remove |
| 114 | twingate-connector | VPN access | Document or remove |
| 115 | proxmox-backup-server | Backups | Critical - restart |
| 117 | syncthing | File sync | Evaluate usage |
| 118 | openwebui | LLM interface | Redundant with Ollama? |
| 120 | crafty-controller | Minecraft management |

## VMs (Stopped)
| ID | Name | Purpose | Status |
|----|------|---------|--------|
| 121 | twitch | Twitch Drops | Stopped |
| 122 | deJoaca | Testing | Stopped |

## Issues to Address
- [ ] Password resets needed for multiple containers
- [ ] Document actual IPs and access methods
- [ ] Decide which stopped services to keep
- [ ] Remove/consolidate redundant services
- [ ] Set up proper backup strategy
- [ ] Document network configuration
