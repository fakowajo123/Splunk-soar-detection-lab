# Cloud SOC Automation — Automated Threat Detection & Incident Response

> MSc Dissertation Project | Kingston University London | Networks and Information Security  
> **Result: Distinction (Expected)**

---

## Overview

A fully cloud-hosted Security Operations Centre (SOC) simulation built on AWS, integrating Splunk SIEM, n8n SOAR, Sysmon telemetry, and Slack for real-time analyst collaboration. The system automatically detects, classifies, enriches, and responds to cyber threats — with measurable, benchmarked results.

This project bridges the gap between academic research and real-world SOC operations by demonstrating how automation and orchestration can be ethically and effectively integrated into modern cybersecurity environments.

---

## Key Results

| Metric | Result | Industry Benchmark |
|--------|--------|--------------------|
| Detection Accuracy | 95% | 85-90% |
| Mean Time to Detect (MTTD) | < 10 seconds | 2-7 minutes |
| Mean Time to Respond (MTTR) | < 10 seconds | 15-60 minutes |
| Alert Classification Accuracy | 95% | ~80% |
| Analyst Workload Reduction | ~70% | 40-60% |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        AWS Cloud (VPC)                       │
│                                                             │
│  ┌─────────────┐    ┌──────────────┐    ┌───────────────┐  │
│  │  Windows AD  │───▶│ Splunk SIEM  │───▶│   n8n SOAR    │  │
│  │   Domain     │    │ (Log Ingest  │    │ (Playbooks &  │  │
│  │  Controller  │    │  & Alerts)   │    │  Automation)  │  │
│  └─────────────┘    └──────────────┘    └───────────────┘  │
│         │                                       │           │
│  ┌─────────────┐                       ┌───────────────┐   │
│  │  Windows 10  │                       │     Slack     │   │
│  │  Victim VM   │                       │  (Analyst     │   │
│  │  (Sysmon)    │                       │   Alerts)     │   │
│  └─────────────┘                       └───────────────┘   │
│         │                                                   │
│  ┌─────────────┐                                           │
│  │   Kali Linux │                                           │
│  │  (Attack VM) │                                           │
│  └─────────────┘                                           │
└─────────────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Component | Tool | Purpose |
|-----------|------|---------|
| Cloud Infrastructure | AWS (EC2, VPC, Elastic IP) | Hosting all VMs |
| SIEM | Splunk Enterprise | Log ingestion, correlation, alerting |
| SOAR | n8n | Playbook automation and orchestration |
| Telemetry | Sysmon + Splunk Universal Forwarder | Endpoint event collection |
| Identity | Windows Active Directory | Enterprise environment simulation |
| Attack Simulation | Atomic Red Team, custom scripts | MITRE ATT&CK threat simulation |
| Threat Intelligence | VirusTotal API | IOC enrichment |
| Collaboration | Slack | Real-time analyst notifications |
| OS Environments | Windows Server, Windows 10, Kali Linux | Multi-platform simulation |

---

## Attack Scenarios Simulated

| Attack Type | MITRE ATT&CK Technique | Detection Method |
|-------------|------------------------|-----------------|
| Brute Force / RDP Login Attempts | T1110 | Splunk SPL — high frequency auth failures |
| Privilege Escalation | T1068 | Sysmon Event ID 4672 correlation |
| Data Exfiltration / File Ingress | T1041 | File hash analysis + VirusTotal enrichment |
| Remote Command Execution | T1059 | Suspicious process execution detection |

---

## Automated Response Workflow

```
Sysmon Event
     │
     ▼
Splunk Detection (SPL Alert)
     │
     ▼
n8n Webhook Trigger
     │
     ▼
Alert Classification (JavaScript node)
     │
     ├──▶ Brute Force ──▶ Block IP + Disable Account + Slack Alert
     │
     ├──▶ File Ingress ──▶ Hash File + VirusTotal Check + Quarantine + Slack Alert
     │
     └──▶ Privilege Esc ──▶ Isolate Host + Slack Alert + Escalate to Analyst
```

---

## Repo Structure

```
cloud-soc-automation/
│
├── README.md                          # This file
├── architecture/                      # Infrastructure diagrams
│   ├── high-level-workflow.png
│   ├── cloud-environment.png
│   └── n8n-automated-workflow.png
│
├── detection-rules/                   # Splunk SPL queries
│   ├── brute-force-detection.spl
│   ├── file-ingress-detection.spl
│   ├── privilege-escalation.spl
│   └── suspicious-process.spl
│
├── playbooks/                         # n8n automation workflows
│   ├── brute-force-response.json
│   ├── file-ingress-response.json
│   └── privilege-escalation-response.json
│
├── config/                            # Configuration files
│   ├── sysmon-config.xml
│   ├── splunk-forwarder-inputs.conf
│   └── splunk-forwarder-outputs.conf
│
├── scripts/                           # Attack simulation scripts
│   ├── brute-force-simulation.ps1
│   ├── file-ingress-simulation.ps1
│   └── atomic-red-team-commands.md
│
├── results/                           # Benchmarking and findings
│   ├── performance-results.md
│   └── comparative-benchmarks.md
│
└── docs/                              # Additional documentation
    ├── deployment-guide.md
    └── ethical-considerations.md
```

---

## Detection Rules

See `/detection-rules/` for full Splunk SPL queries. Example — Brute Force Detection:

```spl
index=main source="WinEventLog:Security" EventCode=4625
| stats count by src_ip, user, _time
| where count > 5
| eval alert_type="Brute Force Attempt"
| table _time, src_ip, user, count, alert_type
```

---

## Setup and Deployment

See `/docs/deployment-guide.md` for full step-by-step instructions to replicate this environment.

**Prerequisites:**
- AWS account with EC2 access
- Splunk Enterprise free trial licence
- n8n instance (self-hosted or cloud)
- Slack workspace and webhook URL
- VirusTotal API key (free tier)

---

## Ethical Considerations

All attack simulations were conducted in an isolated, self-contained AWS environment with no connection to external networks or real infrastructure. No real systems, users, or data were targeted. The project complies with Kingston University's research ethics guidelines and UK Computer Misuse Act 1990.

---

## Author

**Oluwabukunmi Fakowajo**  
MSc Networks and Information Security — Kingston University London  
[LinkedIn](https://www.linkedin.com/in/fakowajo-oluwabukunmi-5144381a9/) | [GitHub](https://github.com/fakowajo123/)

---

## Acknowledgements

Supervised by Dr. Usman Rehan, Kingston University London.
