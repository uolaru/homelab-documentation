# Media Stack - Docker Migration

## Overview
Migrated from individual LXC containers to Docker Compose running in Ubuntu 22.04 VM. All services now run as containers orchestrated via docker-compose.

## Infrastructure
- **VM ID:** 201
- **Hostname:** media-stack
- **OS:** Ubuntu 22.04.5 LTS
- **Resources:** 8GB RAM, 4 CPU cores, 50GB disk
- **IP:** 192.168.100.139
- **Media Storage:** /dev/sdb (931.5GB) mounted at /mnt/hdd

## Services

| Service | Port | Container Path | Purpose |
|---------|------|----------------|---------|
| Jellyfin | 8096 | /media | Media streaming |
| qBittorrent | 8090 | /downloads | Torrent client |
| Prowlarr | 9696 | - | Indexer manager |
| Radarr | 7878 | /movies, /downloads | Movie automation |
| Sonarr | 8989 | /tv, /downloads | TV automation |
| Jellyseerr | 5055 | - | Media requests |
| Node Exporter | 9100 | /host | Prometheus metrics |

## Directory Structure
```
/home/media/
├── jellyfin/
│   ├── docker-compose.yml
│   ├── config/
│   └── cache/
└── arr-stack/
    ├── docker-compose.yml
    ├── qbittorrent/config/
    ├── prowlarr/config/
    ├── radarr/config/
    ├── sonarr/config/
    └── jellyseerr/config/

/mnt/hdd/
├── movies/
├── shows/
└── downloads/
```

## Docker Compose Files

### Jellyfin
Location: `~/jellyfin/docker-compose.yml`
```yaml
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    restart: unless-stopped
    ports:
      - "8096:8096"
    volumes:
      - ./config:/config
      - ./cache:/cache
      - /mnt/hdd:/media:ro
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Bucharest
```

### Media Automation Stack
Location: `~/arr-stack/docker-compose.yml`

Services: qBittorrent, Prowlarr, Radarr, Sonarr, Jellyseerr
[Full compose file in repository]

## Access
All services accessible via Tailscale VPN:
- Jellyfin: http://192.168.100.139:8096
- qBittorrent: http://192.168.100.139:8090
- Prowlarr: http://192.168.100.139:9696
- Radarr: http://192.168.100.139:7878
- Sonarr: http://192.168.100.139:8989
- Jellyseerr: http://192.168.100.139:5055

## Automation Workflow
1. User requests media via Jellyseerr
2. Jellyseerr sends request to Radarr (movies) or Sonarr (TV)
3. Radarr/Sonarr searches via Prowlarr indexers
4. Download sent to qBittorrent
5. Completed files moved to /mnt/hdd/movies or /mnt/hdd/shows
6. Jellyfin automatically detects new content
7. Discord notification sent via Prowlarr webhook

## Management

### Start all services
```bash
cd ~/jellyfin && docker compose up -d
cd ~/arr-stack && docker compose up -d
```

### Stop all services
```bash
cd ~/jellyfin && docker compose down
cd ~/arr-stack && docker compose down
```

### View logs
```bash
docker compose logs -f [service_name]
```

### Update containers
```bash
docker compose pull
docker compose up -d
```

## Migration Notes
- Previous setup: 6 individual LXC containers (CT 101-106)
- Path changes: `/mnt/hddextern` → `/mnt/hdd` on VM
- Docker containers see simplified paths: `/movies`, `/tv`, `/downloads`
- External HDD mounted via fstab (UUID: e7f7ee75-60df-4ee5-9ac9-d8340df44b79)
- All services run as UID/PGID 1000 (media user)
- Node exporter added for Prometheus monitoring

## Stopped LXC Containers (can be deleted)
- CT 101: jellyfin
- CT 102: jellyseerr  
- CT 103: qbittorrent
- CT 104: prowlarr
- CT 105: radarr
- CT 106: sonarr

## Next Steps
- [ ] Delete old LXC containers after 1 week of stable operation
- [ ] Set up automatic container updates
- [x] Document Prometheus scraping for Docker containers
- [ ] Add backup strategy for Docker volumes
