# Deployment Guide
# Cloud SOC Automation — Step by Step Setup

---

## Prerequisites

- AWS account with EC2 access and VPC permissions
- Splunk Enterprise free trial licence (splunk.com)
- n8n instance — self-hosted via Docker or n8n Cloud
- Slack workspace with incoming webhook configured
- VirusTotal API key (free tier — virustotal.com)

---

## Step 1 — AWS Infrastructure Setup

### Create VPC
1. Go to AWS VPC console
2. Create VPC with CIDR block `10.0.0.0/16`
3. Create public subnet `10.0.1.0/24`
4. Attach Internet Gateway
5. Configure route table to point `0.0.0.0/0` to Internet Gateway

### Launch EC2 Instances

| Instance | OS | Type | Purpose |
|----------|-----|------|---------|
| Splunk Server | Ubuntu 22.04 | t2.medium | SIEM |
| Windows AD | Windows Server 2019 | t2.medium | Domain Controller |
| Windows Victim | Windows 10 | t2.small | Target endpoint |
| n8n SOAR | Ubuntu 22.04 | t2.small | Automation |
| Kali Linux | Kali Linux | t2.small | Attack simulation |

### Assign Elastic IPs
Assign a static Elastic IP to each instance for consistent addressing.

---

## Step 2 — Splunk Setup

```bash
# Download and install Splunk on Ubuntu
wget -O splunk.tgz 'https://download.splunk.com/products/splunk/releases/9.0.0/linux/splunk-9.0.0-6818ac46f2ec-Linux-x86_64.tgz'
tar xvzf splunk.tgz -C /opt
/opt/splunk/bin/splunk start --accept-license
/opt/splunk/bin/splunk enable boot-start
```

Configure receiving port 9997 in Splunk Settings > Forwarding and Receiving.

---

## Step 3 — Active Directory Setup

1. Install AD DS role on Windows Server via Server Manager
2. Promote to Domain Controller
3. Create test user accounts for simulation
4. Configure Group Policy for auditing (Security Events 4624, 4625, 4672, 4673)

---

## Step 4 — Sysmon Installation (Windows Victim VM)

```powershell
# Download Sysmon
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "Sysmon.zip"
Expand-Archive -Path "Sysmon.zip" -DestinationPath "C:\Sysmon"

# Install with config
cd C:\Sysmon
.\Sysmon64.exe -accepteula -i ..\config\sysmon-config.xml
```

---

## Step 5 — Splunk Universal Forwarder (Windows VMs)

```powershell
# Download and install Splunk Universal Forwarder
# Copy inputs.conf and outputs.conf from /config/ directory
# Update SPLUNK_INDEXER_IP in outputs.conf

$forwarderPath = "C:\Program Files\SplunkUniversalForwarder\etc\system\local\"
Copy-Item "splunk-forwarder-inputs.conf" -Destination ($forwarderPath + "inputs.conf")
Copy-Item "splunk-forwarder-outputs.conf" -Destination ($forwarderPath + "outputs.conf")

# Restart forwarder
Restart-Service SplunkForwarder
```

---

## Step 6 — n8n Setup

```bash
# Install n8n via Docker
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -e N8N_BASIC_AUTH_ACTIVE=true \
  -e N8N_BASIC_AUTH_USER=admin \
  -e N8N_BASIC_AUTH_PASSWORD=yourpassword \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

Import playbook JSON files from `/playbooks/` directory via n8n UI.

---

## Step 7 — Splunk Alert Webhook Configuration

For each detection rule in `/detection-rules/`:
1. Run the SPL query in Splunk
2. Save as Alert with trigger condition
3. Add Webhook action pointing to n8n webhook URL
4. Set payload to include: `src_ip`, `user`, `count`, `alert_type`, `timestamp`

---

## Step 8 — Slack Configuration

1. Create Slack App at api.slack.com
2. Enable Incoming Webhooks
3. Add webhook URL to n8n Slack nodes in each playbook
4. Create dedicated `#soc-alerts` channel

---

## Step 9 — Test the System

Run attack simulations from `/scripts/` directory and verify:
- Splunk detects and fires alert
- n8n receives webhook and executes playbook
- Response actions complete successfully
- Slack notification received with enriched data

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Splunk not receiving logs | Check forwarder outputs.conf IP and port 9997 open in security group |
| n8n webhook not triggering | Verify Splunk alert webhook URL and n8n port 5678 accessible |
| VirusTotal returning errors | Check API key and rate limit (4 requests/minute on free tier) |
| Sysmon not logging | Verify service running: `Get-Service Sysmon64` |
