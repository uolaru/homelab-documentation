# Homelab Infrastructure Documentation

Production homelab running 20+ self-hosted services on Proxmox for DevOps learning and automation.

## Featured Projects

### 🤖 AI Sysadmin Monitor
Automated infrastructure monitoring using Proxmox API, Ollama LLM, and n8n workflow automation.

**Tech stack:** Proxmox REST API • Ollama (llama3.2) • n8n • Discord webhooks

**What it does:**
- Monitors 20 LXC containers + host metrics every 10 minutes
- AI analyzes system health and identifies issues
- Sends intelligent alerts to Discord with recommendations
- Zero external API costs (local LLM)

[→ View full implementation](services/ai-tools.md#ai-sysadmin---automated-monitoring-project)

### 📺 Media Automation Stack
Complete automated media management from request to streaming.

**Tech stack:** Jellyfin • *arr suite (Radarr/Sonarr/Prowlarr) • qBittorrent • Jellyseerr

**Architecture:** User request → Jellyseerr → Radarr/Sonarr → Prowlarr → qBittorrent → Jellyfin

[→ View full documentation](services/media-stack.md)

## Hardware

- **Platform:** Proxmox VE on Intel i7-6700 (8 cores @ 3.40GHz)
- **RAM:** 32GB DDR4
- **Storage:** 1TB + external HDD
- **Network:** Tailscale VPN for secure remote access
- **Hostname:** hades

## Active Services

### Media Stack (6 containers)
Jellyfin • Jellyseerr • Radarr • Sonarr • Prowlarr • qBittorrent

[Full documentation →](services/media-stack.md)

### AI & Automation (3 containers)
Ollama • n8n • Immich

[Full documentation →](services/ai-tools.md)

### Monitoring (3 containers)
Prometheus • Grafana • Cockpit

### Infrastructure (8 containers - mixed status)
Pi-hole • Unbound • Twingate • Proxmox Backup Server • Others

[Complete inventory →](docs/inventory.md)

## Skills Demonstrated

**Infrastructure:**
- Proxmox virtualization (LXC containers)
- Shared storage configuration
- Network segmentation and VPN setup

**Automation:**
- Workflow orchestration (n8n)
- REST API integration and authentication
- JavaScript for automation logic
- Error handling and defensive programming

**Monitoring:**
- Metrics collection (Prometheus)
- Dashboard creation (Grafana)
- Custom alerting logic
- AI-powered analysis

**AI/ML:**
- Local LLM deployment (Ollama)
- Prompt engineering
- AI-driven system analysis

**Documentation:**
- Technical writing
- Architecture documentation
- Troubleshooting guides
- Setup instructions

## Learning Path

**Completed:**
- Linux Foundation LFS101x (Chapter 5)
- Cisco NDG Linux Essentials (Certified Dec 2024)
- Media automation stack deployment
- AI monitoring system implementation

**In Progress:**
- LPIC-1 certification preparation
- Docker deep dive
- Ansible for infrastructure automation

**Next:**
- CI/CD pipeline with GitHub Actions
- Terraform infrastructure as code
- Kubernetes homelab cluster

## Repository Structure
```
├── services/           # Detailed service documentation

## Contact

Building this infrastructure for hands-on DevOps learning. Open to feedback and collaboration.

[LinkedIn](https://linkedin.com/in/andrei-paul-olaru) • [GitHub](https://github.com/uolaru)# Homelab Infrastructure Documentation

Production homelab running 20+ self-hosted services on Proxmox for DevOps learning and automation.

## Featured Projects

### 🤖 AI Sysadmin Monitor
Automated infrastructure monitoring using Proxmox API, Ollama LLM, and n8n workflow automation.

**Tech stack:** Proxmox REST API • Ollama (llama3.2) • n8n • Discord webhooks

**What it does:**
- Monitors 20 LXC containers + host metrics every 10 minutes
- AI analyzes system health and identifies issues
- Sends intelligent alerts to Discord with recommendations
- Zero external API costs (local LLM)

[→ View full implementation](services/ai-tools.md#ai-sysadmin---automated-monitoring-project)

### 📺 Media Automation Stack
Complete automated media management from request to streaming.

**Tech stack:** Jellyfin • *arr suite (Radarr/Sonarr/Prowlarr) • qBittorrent • Jellyseerr

**Architecture:** User request → Jellyseerr → Radarr/Sonarr → Prowlarr → qBittorrent → Jellyfin

[→ View full documentation](services/media-stack.md)

## Hardware

- **Platform:** Proxmox VE on Intel i7-6700 (8 cores @ 3.40GHz)
- **RAM:** 32GB DDR4
- **Storage:** 1TB + external HDD
- **Network:** Tailscale VPN for secure remote access
- **Hostname:** hades

## Active Services

### Media Stack (6 containers)
Jellyfin • Jellyseerr • Radarr • Sonarr • Prowlarr • qBittorrent

[Full documentation →](services/media-stack.md)

### AI & Automation (3 containers)
Ollama • n8n • Immich

[Full documentation →](services/ai-tools.md)

### Monitoring (3 containers)
Prometheus • Grafana • Cockpit

### Infrastructure (8 containers - mixed status)
Pi-hole • Unbound • Twingate • Proxmox Backup Server • Others

[Complete inventory →](docs/inventory.md)

## Contact

Building this infrastructure for hands-on DevOps learning. Open to feedback and collaboration.

[LinkedIn](https://linkedin.com/in/andrei-paul-olaru) • [GitHub](https://github.com/uolaru)
