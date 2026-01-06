# AI & Automation Stack

AI-powered tools and workflow automation running on Proxmox LXC containers.

## Services Overview

### Ollama (Container 107)
**Purpose:** Local LLM hosting for AI-powered applications

**Specs:**
- 4 CPU cores
- 4GB RAM
- 16GB root disk
- Ubuntu-based
- IP: 192.168.100.105

**Function:**
- Runs Large Language Models locally (no external API costs)
- Currently using llama3.2 model
- Provides AI analysis for system monitoring
- Accessible via REST API on port 11434

**Performance:**
- CPU-based inference (no GPU)
- Response time: 15-30 seconds for analysis tasks
- Runs continuously for instant availability

---

### n8n (Container 109)
**Purpose:** Workflow automation and orchestration

**Specs:**
- 2 CPU cores
- 2GB RAM
- 8GB root disk
- Debian-based

**Function:**
- Visual workflow automation platform
- Connects services via APIs
- Scheduled task execution
- Powers the AI Sysadmin monitoring system

**Web UI:** http://192.168.100.109:5678

---

### Immich (Container 108)
**Purpose:** Self-hosted photo and video management

**Specs:**
- 4 CPU cores
- 4GB RAM
- 16GB root disk
- Debian-based

**Function:**
- Photo backup and organization
- AI-powered facial recognition
- Timeline and album management
- Mobile app sync

---

## AI Sysadmin - Automated Monitoring Project

### Overview

Automated homelab monitoring system that uses AI to analyze system health and send intelligent alerts to Discord every 10 minutes.

**What it monitors:**
- All 20 LXC containers (status, CPU, RAM, disk, uptime)
- Host system resources (hades)
- Resource usage trends
- Stopped containers

**What the AI does:**
- Analyzes metrics for anomalies
- Identifies issues and root causes
- Provides specific recommendations
- Prioritizes actions based on severity

### Architecture
```
Proxmox API → n8n Workflow → Data Formatting
                    ↓
              Ollama (AI Analysis)
                    ↓
           Discord Notification
```

**Data Flow:**
1. **Every 10 minutes:** n8n workflow triggers
2. **API calls:** Fetch container list and host status from Proxmox
3. **Format data:** Parse and structure metrics for AI analysis
4. **AI analysis:** Send to Ollama with context prompt
5. **Generate report:** Format Discord embed with status, top resource users, AI insights
6. **Send alert:** Post to Discord webhook

### Technical Implementation

#### Proxmox API Integration

**Authentication:**
- User: `n8n@pve`
- Role: PVEAuditor (read-only)
- Token-based authentication
- No password required

**Endpoints used:**
```
GET /api2/json/nodes/hades/lxc        # Container metrics
GET /api2/json/nodes/hades/status     # Host metrics
```

**Data collected per container:**
- Status (running/stopped)
- CPU percentage
- Memory usage (used/total/percentage)
- Disk usage (used/total)
- Uptime

#### AI Analysis Prompt

The workflow sends structured data to Ollama:
```
You are an AI system administrator analyzing a Proxmox homelab.

HOST STATUS (hades):
- CPU Usage: X%
- RAM: XGB / XGB (X%)
- Root Disk: X% used
- Uptime: Xd

CONTAINER STATUS:
- container_name (CTXXX): status
  CPU: X% | RAM: X/XGB (X%) | Disk: X/XGB | Uptime: Xd
[repeated for all containers]

Analyze this system health and provide:
1. Overall status (Healthy/Warning/Critical)
2. Any issues detected
3. Specific recommendations
4. Priority actions if needed

Keep response concise and actionable. Use emojis for readability.
```

#### Alert Logic

**Status determination:**
- 🔴 **Critical (Red):** Stopped containers OR host RAM >85% OR 3+ containers >80% RAM
- ⚠️ **Warning (Orange):** Any container >80% RAM OR any container >50% CPU OR host RAM >70%
- ✅ **Healthy (Green):** All containers running, resources normal

#### Discord Output Format

**Embed includes:**
- Overall health status
- Container summary (X running, X stopped)
- Host resources (CPU, RAM, disk, uptime)
- Top 5 CPU consumers
- Top 5 RAM consumers
- Stopped containers list
- AI-generated analysis (up to 1024 characters)

**Example AI analysis:**
```
System Health Analysis
Overall Status: Healthy

Notable Issues:
- High CPU usage on ollama (10.9%)
  Recommendation: Check and optimize workload
- Disk space almost full on immich (75GB/167GB)
  Recommendation: Monitor disk usage, consider expanding storage

No priority actions required. Regularly monitor system to maintain 
optimal performance.
```

### n8n Workflow Details

**Nodes:**
1. **Schedule Trigger:** Runs every 10 minutes
2. **Get Container List:** HTTP request to Proxmox API
3. **Get Host Status:** HTTP request to Proxmox API
4. **Format System Data:** JavaScript code to parse and structure metrics
5. **Ollama AI Analysis:** HTTP POST to Ollama with formatted prompt
6. **Format Discord Message:** JavaScript code to build Discord embed
7. **Send to Discord:** HTTP POST to Discord webhook

**Workflow file:** Available in repository

### Setup Instructions

#### 1. Create Proxmox API Token
```bash
# Create monitoring user
pveum user add n8n@pve --comment "n8n monitoring user"

# Assign read-only role
pveum aclmod / -user n8n@pve -role PVEAuditor

# Generate token
pveum user token add n8n@pve monitoring --privsep 0
```

Save the token value (looks like: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`)

#### 2. Create Discord Webhook

1. Open Discord server settings
2. Go to Integrations → Webhooks
3. Click "New Webhook"
4. Name it "Homelab Monitor"
5. Copy webhook URL

#### 3. Configure n8n Workflow

1. Import workflow JSON into n8n
2. Update these values in workflow nodes:
   - **Proxmox host IP:** 192.168.100.77
   - **API token:** Your generated token
   - **Ollama IP:** 192.168.100.105
   - **Discord webhook URL:** Your webhook URL
   - **Model name:** llama3.2 (or your installed model)

#### 4. Test and Activate

1. Click "Execute Workflow" manually
2. Check Discord for status report
3. Verify all data displays correctly
4. Activate workflow for automatic execution

### Configuration Options

#### Change Monitoring Interval

Edit the "Every 10 minutes" trigger node:
```json
"interval": [
  {
    "field": "minutes",
    "minutesInterval": 10  // Change this value
  }
]
```

#### Adjust Alert Thresholds

In "Format Discord Message" node:
```javascript
// High memory threshold (default 80%)
const highMemContainers = containers.filter(c => parseFloat(c.memPercent) > 80);

// High CPU threshold (default 50%)
const highCPUContainers = containers.filter(c => parseFloat(c.cpuPercent) > 50);

// Host critical threshold (default 85%)
if (parseFloat(host.memPercent) > 85) { color = 15158332; }
```

#### Use Different AI Model

In "Ollama AI Analysis" node, change model parameter:
```json
{
  "name": "model",
  "value": "llama3.2:1b"  // Smaller/faster model
}
```

Available models can be checked with:
```bash
pct exec 107 -- curl http://localhost:11434/api/tags
```

### Performance Considerations

**CPU usage during AI analysis:**
- Ollama container: 10-15% during inference
- n8n container: <1%
- Analysis time: 15-30 seconds

**Network traffic:**
- Proxmox API calls: <10KB per execution
- Ollama request/response: ~5-10KB
- Discord webhook: ~2-5KB

**Total overhead per cycle:** ~30 seconds of increased CPU, minimal network/storage impact

### Troubleshooting

**Issue:** Workflow fails on second run
- **Cause:** Data not properly passed between nodes
- **Fix:** Use `$('NodeName').item.json` to reference previous node data

**Issue:** Host metrics show NaN
- **Cause:** Incorrect data structure parsing
- **Fix:** Verify API response structure with direct curl test

**Issue:** AI response times out
- **Cause:** Ollama container stopped or model not loaded
- **Fix:** Check Ollama container status: `pct status 107`

**Issue:** Discord message doesn't send
- **Cause:** Invalid webhook URL or malformed JSON
- **Fix:** Test webhook with curl, check JSON structure

### Lessons Learned

**API Integration:**
- Proxmox API requires specific token format: `PVEAPIToken=user@realm!tokenname=secret`
- Bash interprets `!` in double quotes—use single quotes for curl commands
- Read-only PVEAuditor role is sufficient for monitoring

**n8n Data Flow:**
- Node references like `$('Format System Data').item.json` maintain data across workflow steps
- Merge nodes add complexity—linear workflows are simpler for sequential processing
- Always add defensive checks for arrays before using `.map()` or `.filter()`

**LLM Integration:**
- Local Ollama avoids external API costs but requires CPU time
- Structured prompts with clear context produce better analysis
- Response truncation needed—Discord embeds limited to 1024 chars per field

**Error Handling:**
- Defensive programming essential in n8n code nodes
- Check if data exists before accessing properties
- Use `Array.isArray()` before array operations

### Future Enhancements

**Historical Analysis:**
- Store metrics in time-series database
- Detect trends and anomalies over time
- "CPU usage 30% higher than usual at this time"

**Auto-Remediation:**
- Restart stopped containers automatically
- Clear logs when disk space critical
- Scale container resources dynamically

**Advanced Alerting:**
- Customizable thresholds per container
- Different alert channels (email, SMS, Slack)
- Escalation for repeated issues

**Predictive Monitoring:**
- Disk space projections ("full in X days")
- Resource usage forecasting
- Maintenance window recommendations

**Integration Expansion:**
- Grafana dashboards for visualization
- Uptime Kuma for service availability tracking
- Backup verification automation

## Skills Demonstrated

- **API Integration:** REST API authentication, endpoint usage, data parsing
- **Workflow Automation:** n8n visual programming, scheduled tasks, error handling
- **AI/LLM Integration:** Local model deployment, prompt engineering, response processing
- **Infrastructure Monitoring:** Metrics collection, alerting logic, status determination
- **System Administration:** Proxmox management, container orchestration, resource optimization
- **Documentation:** Clear technical writing, troubleshooting guides, setup instructions
