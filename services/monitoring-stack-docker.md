# Monitoring Stack - Docker

## Overview
Prometheus, Grafana, and Alertmanager running in Docker on dedicated monitoring VM. Provides metrics collection, visualization, and Discord alerting for infrastructure health.

## Infrastructure
- **VM ID:** 203
- **Hostname:** monitoring
- **OS:** Ubuntu 22.04.5 LTS
- **Resources:** 2GB RAM, 2 CPU cores, 30GB disk
- **IP:** 192.168.100.140

## Services

| Service | Port | Purpose | Access |
|---------|------|---------|--------|
| Prometheus | 9090 | Metrics collection | http://192.168.100.140:9090 |
| Grafana | 3000 | Dashboards | http://192.168.100.140:3000 |
| Alertmanager | 9093 | Alert routing | http://192.168.100.140:9093 |
| Node Exporter | 9100 | VM metrics | http://192.168.100.140:9100 |
| Discord Relay | 9094 | Discord webhook | Internal only |

## Directory Structure
```
/home/monitoring/monitoring/
├── docker-compose.yml
├── prometheus/
│   └── config/
│       ├── prometheus.yml
│       └── alerts.yml
└── alertmanager/
    └── alertmanager.yml
```

## Docker Compose

Location: `~/monitoring/docker-compose.yml`

Services: prometheus, grafana, alertmanager, node-exporter, alertmanager-discord

## Monitored Targets

### Proxmox Host
- 192.168.100.77:9100 (node-exporter)

### VMs
- media-stack (192.168.100.139:9100)
- monitoring (node-exporter:9100 - internal)

### LXC Containers
- cockpit (192.168.100.94:9100)
- ollama (192.168.100.105:9100)
- immich (192.168.100.106:9100)
- n8n (192.168.100.107:9100)
- pihole (192.168.100.112:9100)

## Alert Rules

### ServiceDown (Critical)
- **Condition:** Service unreachable for 2 minutes
- **Action:** Discord notification

### HighCPU (Warning)
- **Condition:** CPU usage >80% for 5 minutes
- **Action:** Discord notification

### HighMemory (Warning)
- **Condition:** Memory usage >90% for 5 minutes
- **Action:** Discord notification

### DiskSpaceLow (Warning)
- **Condition:** Disk usage >85% for 5 minutes
- **Action:** Discord notification

## Alert Configuration

Alertmanager sends notifications to Discord via benjojo/alertmanager-discord relay.

Webhook URL configured in docker-compose.yml environment variable.

Alert grouping: 10s wait, 5min interval, 12h repeat interval

## Grafana Setup

- **Datasource:** Prometheus at http://prometheus:9090
- **Dashboards:** Import from Grafana.com (ID: 1860 for Node Exporter)

## Management

### Start all services
```bash
cd ~/monitoring
docker compose up -d
```

### Stop all services
```bash
docker compose down
```

### View logs
```bash
docker compose logs -f [service_name]
```

### Reload Prometheus config
```bash
docker exec prometheus kill -HUP 1
```

### Check alert status
```bash
curl http://localhost:9093/api/v2/alerts
```

### Update containers
```bash
docker compose pull
docker compose up -d
```

## Migration Notes

- Replaced LXC containers (CT 110 Grafana, CT 113 Prometheus)
- Migrated scrape targets from old Prometheus config
- Added Docker-based node-exporter for monitoring VM itself
- Discord integration via alertmanager-discord relay (solves Slack webhook payload incompatibility)
- Added node-exporter to media-stack VM for metrics collection

## Stopped LXC Containers (can be deleted)

- CT 110: grafana (old)
- CT 113: prometheus (old)

## Known Issues

- n8n occasionally spikes CPU during initialization (normal behavior)
- Media disk at 85% usage (expected - active downloads)

## Access

All services accessible via Tailscale VPN only. No public exposure.

## Next Steps
- [ ] Delete old monitoring LXC containers after 1 week stable operation
- [ ] Import custom Grafana dashboards for specific service metrics
- [ ] Add backup for Prometheus data volume
- [ ] Consider Loki for log aggregation
