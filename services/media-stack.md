# Media Automation Stack

Complete automated media management system running on Proxmox LXC containers with shared storage.

## Architecture Overview

Six interconnected LXC containers work together to automate media downloading, organization, and streaming:

**User requests → Jellyseerr → Radarr/Sonarr → Prowlarr → qBittorrent → Jellyfin**

## Shared Storage

All containers mount the same external storage for seamless file access:

- **Host mount point:** `/mnt/pve/hddextern`
- **Container mount point:** `/mnt/hddextern`
- **Purpose:** Central storage for downloads, media library, and configuration persistence

This shared mount allows:
- qBittorrent to download files accessible by all services
- Radarr/Sonarr to move completed files to media folders
- Jellyfin to read media without file copying

## Container Details

### Jellyfin (Container 101)
**Purpose:** Media streaming server (movies, TV shows)

**Specs:**
- 2 CPU cores
- 2GB RAM
- 8GB root disk
- Ubuntu-based

**Special features:**
- GPU passthrough enabled (dev0: /dev/dri/card1, dev1: /dev/dri/renderD128)
- Hardware transcoding for efficient video streaming
- Shared storage access at /mnt/hddextern

**Installation method:** Community Scripts (Helper-Scripts.com)

---

### Jellyseerr (Container 102)
**Purpose:** User request management interface

**Specs:**
- 4 CPU cores
- 4GB RAM
- 8GB root disk
- Debian-based

**Function:**
- Users request movies/TV shows through web interface
- Automatically sends requests to Radarr (movies) or Sonarr (TV shows)
- Tracks request status and notifications

**Installation method:** Community Scripts

---

### qBittorrent (Container 103)
**Purpose:** Download client

**Specs:**
- 2 CPU cores
- 2GB RAM
- 8GB root disk
- Debian-based

**Function:**
- Downloads torrents sent by Radarr/Sonarr
- Saves completed files to shared storage
- Provides download status back to *arr apps

**Installation method:** Community Scripts

---

### Prowlarr (Container 104)
**Purpose:** Indexer manager

**Specs:**
- 2 CPU cores
- 1GB RAM
- 4GB root disk
- Debian-based

**Function:**
- Central indexer management for Radarr and Sonarr
- Searches multiple torrent sites/indexers
- Syncs indexer configuration to all *arr apps
- Single point of configuration for all search sources

**Installation method:** Community Scripts

---

### Radarr (Container 105)
**Purpose:** Movie management

**Specs:**
- 2 CPU cores
- 1GB RAM
- 4GB root disk
- Debian-based

**Function:**
- Monitors for requested movies from Jellyseerr
- Searches for releases via Prowlarr
- Sends torrents to qBittorrent
- Hardlinks, renames and organizes completed downloads
- Updates Jellyfin library

**Installation method:** Community Scripts

---

### Sonarr (Container 106)
**Purpose:** TV show management

**Specs:**
- 2 CPU cores
- 1GB RAM
- 4GB root disk
- Debian-based

**Function:**
- Monitors for requested TV shows from Jellyseerr
- Tracks episode releases automatically
- Searches for episodes via Prowlarr
- Sends torrents to qBittorrent
- Hardlinks, renames and organizes completed downloads
- Updates Jellyfin library

**Installation method:** Community Scripts

---

## Networking

All containers use:
- Bridge: vmbr0
- IP assignment: DHCP
- Network type: veth (virtual ethernet)

Each container gets its own IP address from DHCP server. Services communicate via their web APIs on assigned ports.

## Permissions

All containers use unprivileged LXC with specific UID/GID mapping:
- UID 1005 on host mapped to UID 1005 in container
- Ensures shared storage permissions work correctly across all services
- Prevents permission errors when moving/reading files

## Automation Flow

1. **User requests content** via Jellyseerr web interface
2. **Jellyseerr sends request** to Radarr (movies) or Sonarr (TV shows)
3. **Radarr/Sonarr search** for releases using Prowlarr indexers
4. **Best release selected** based on quality profiles
5. **Torrent sent** to qBittorrent for downloading
6. **Download completes** to /mnt/hddextern
7. **Radarr/Sonarr hardlink and rename** files to proper media folders (same file, two directory entries, no space duplication)
8. **Jellyfin library updates** automatically
9. **User notification** sent when content is available

## Key Benefits

- **Almost zero manual intervention:** Request to streaming fully automated (occasional manual file selection needed for titles with special characters or naming issues)
- **Quality control:** Radarr/Sonarr enforce quality profiles
- **Organized library:** Automatic renaming and folder structure
- **Efficient storage:** Hardlinks used instead of copying—files exist in both downloads and media folders without duplication
- **Hardware transcoding:** GPU acceleration in Jellyfin for smooth streaming

## Management

All services managed via:
- Web interfaces (each container runs its own web UI)
- Proxmox LXC commands: `pct start/stop/restart <container_id>`
- Services set to auto-start on boot (onboot: 1)
