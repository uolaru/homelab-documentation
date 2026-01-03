# Homelab Documentation

Self-hosted services on Proxmox for DevOps learning.

## Hardware
- **Host**: Proxmox VE on Intel i7-6700
- **RAM**: 32GB
- **Storage**: 1TB
- **Hostname**: hades

## Services Overview

### Media Stack (Running)
- Jellyfin - Media server
- Jellyseerr - Request management
- qBittorrent - Download client
- Prowlarr - Indexer manager
- Radarr - Movie management
- Sonarr - TV show management

### Monitoring Stack (Running)
- Prometheus - Metrics collection
- Grafana - Visualization dashboards
- Cockpit - System management

### AI/Automation (Running)
- Ollama - Local LLM hosting
- n8n - Workflow automation
- Immich - Photo management

### Infrastructure (Mixed status)
- Unbound - DNS resolver (stopped)
- Pi-hole - Network ad blocker (stopped)
- Twingate - Zero-trust access (stopped)
- Proxmox Backup Server (stopped)

### Development/Gaming (Stopped)
- Crafty Controller
- OpenWebUI
- Syncthing
- Test VMs (twitch, deJoaca)

## Current State
12 containers running, 8 stopped. Some services need password resets and proper documentation. In process of auditing, cleaning up, and documenting all services.

## Documentation
- [Complete Inventory](docs/inventory.md) - All services with access details
- [Cleanup Plan](docs/cleanup-plan.md) - Consolidation and organization strategy

## Learning Progress
- **Week 1**: Service inventory and documentation
