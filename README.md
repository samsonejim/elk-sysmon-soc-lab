# ELK Stack + Sysmon + Wazuh — Full SOC Home Lab

**Author:** Samson Ejim | Cybersecurity Enthusiast | Czechia
**Platform:** Google Cloud Platform | VirtualBox
**Stack:** Elasticsearch 8.19 · Logstash · Kibana · Sysmon 15 · Winlogbeat 8.19 · Wazuh 4.7.5

---

## Project Overview

A production-grade Security Operations Centre (SOC) home lab built on Google Cloud Platform,
combining the ELK Stack, Microsoft Sysmon, and Wazuh SIEM. The lab ingests Windows endpoint
telemetry in real time, runs adversary attack simulations using Atomic Red Team, and detects
threats using custom KQL detection rules mapped to the MITRE ATT&CK framework.

This project demonstrates hands-on skills in SIEM engineering, endpoint detection,
threat hunting, detection-as-code, and incident response — directly applicable to
SOC Tier 1/2 and Detection Engineering roles.

---

## Architecture

    Windows VM (VirtualBox) → Sysmon → Winlogbeat 8.19
               ↓
    GCP elk-server → Logstash → Elasticsearch 8.19 → Kibana
               ↑
    Wazuh Server (GCP) → Python Shipper → Logstash

### Infrastructure

| Component | Spec | Role |
|---|---|---|
| elk-server (GCP) | e2-standard-4, 16GB RAM, 100GB SSD | Elasticsearch + Logstash + Kibana |
| wazuh-server (GCP) | e2-standard-2, 8GB RAM, 50GB SSD | Wazuh Manager + Wazuh Indexer |
| windows-endpoint (VirtualBox) | 4GB RAM, 60GB disk | Sysmon + Winlogbeat + Attack simulation |
| ubuntu-endpoint (GCP) | e2-medium, 4GB RAM | Suricata IDS + Wazuh Agent |

---

## Technologies Used

- **Elasticsearch 8.19.15** — Log storage and indexing with security enabled
- **Logstash** — Log parsing and pipeline management (ports 5044 + 5045)
- **Kibana 8.19.15** — SIEM dashboards and detection rules engine
- **Microsoft Sysmon 15.x** — Endpoint telemetry (SwiftOnSecurity config)
- **Winlogbeat 8.19.0** — Windows log shipping to Logstash
- **Wazuh 4.7.5** — Host-based IDS, FIM, compliance monitoring
- **Atomic Red Team** — Adversary attack simulation (336 techniques)
- **Google Cloud Platform** — Cloud infrastructure

---

## Detection Rules Built

10 custom KQL detection rules written from scratch, all mapped to MITRE ATT&CK:

| Rule Name | MITRE Technique | Severity | Risk Score |
|---|---|---|---|
| LSASS Memory Access - Credential Dumping | T1003.001 | Critical | 95 |
| PowerShell Encoded Command Execution | T1059.001 | High | 75 |
| Persistence via Registry Run Key | T1547.001 | High | 73 |
| Suspicious Scheduled Task Creation | T1053.005 | High | 73 |
| PowerShell Outbound Network Connection | T1071.001 | High | 75 |
| Brute Force Login Failures Detected | T1110.001 | Medium | 60 |
| Suspicious WMI Child Process Execution | T1047 | High | 73 |
| PsExec Lateral Movement Detected | T1569.002 | High | 75 |
| Windows Security Event Log Cleared | T1070.001 | Critical | 90 |
| DLL Injection via CreateRemoteThread | T1055.001 | Critical | 85 |

Plus 1,677 Elastic prebuilt detection rules installed and enabled.

---

## Attack Simulations

Ran 8 Atomic Red Team attack simulations — confirmed detected by custom rules:

| Attack | Technique | Tool | Detected |
|---|---|---|---|
| LSASS Memory Dump | T1003.001 | ProcDump | ✅ Rule fired |
| Credential Dumping | T1059.001 | Mimikatz 2.2.0 | ✅ Rule fired |
| Registry Run Key Persistence | T1547.001 | Atomic Red Team | ✅ Rule fired |
| Scheduled Task Creation | T1053.005 | schtasks.exe | ✅ Rule fired |
| System Information Discovery | T1082 | systeminfo.exe | Logged |
| Brute Force Login | T1110.001 | Atomic Red Team | ✅ 5 alerts fired |
| Rundll32 Bypass | T1218.011 | rundll32.exe | Logged |
| Event Log Clearing | T1070.001 | wevtutil | ✅ Critical alert fired |

**Total alerts generated: 29 (1 Critical, 23 High, 5 Medium)**

---

## Kibana Dashboards

Three custom SOC dashboards built:

**1. SOC Overview Dashboard**
- Windows event volume by Event ID (stacked bar chart)
- Top 10 processes launched (treemap)
- Top source IPs — network connections (table)
- Windows login activity — success vs failure (bar chart)
- Sysmon network connections by destination IP/port (table)
- Critical Sysmon events timeline (line chart)

**2. Threat Alerts and Detections Dashboard**
- LSASS access attempts (table)
- PowerShell executions with command lines (table)
- Failed login attempts over time (bar chart)
- New services created (timeline)

**3. Threat Hunting Dashboard**
- Process parent-child relationships (table)
- DNS query analysis including attack tool downloads (table)
- Registry modifications (table)

---

## Key Skills Demonstrated

- SIEM deployment and configuration (Elasticsearch + Logstash + Kibana)
- Endpoint telemetry engineering (Sysmon + Winlogbeat)
- KQL detection rule writing mapped to MITRE ATT&CK
- Threat hunting using dashboard analysis
- Adversary simulation with Atomic Red Team
- Incident response — alert triage and investigation
- Cloud infrastructure on GCP
- Log pipeline engineering (Beats → Logstash → Elasticsearch)

---

## Repository Structure

    elk-sysmon-soc-lab/
    ├── configs/
    │   ├── sysmonconfig.xml
    │   ├── winlogbeat.yml
    │   └── logstash/
    │       ├── winlogbeat.conf
    │       └── wazuh.conf
    ├── detections/
    │   └── kql/
    │       ├── lsass_credential_dumping.kql
    │       ├── powershell_encoded_command.kql
    │       ├── persistence_run_key.kql
    │       ├── scheduled_task_creation.kql
    │       ├── powershell_network_connection.kql
    │       ├── brute_force_login.kql
    │       ├── wmi_child_process.kql
    │       ├── psexec_lateral_movement.kql
    │       ├── event_log_cleared.kql
    │       └── dll_injection.kql
    ├── screenshots/
    │   ├── kibana_home.png
    │   ├── security_alerts_29.png
    │   ├── detection_rules_10_custom.png
    │   ├── soc_overview_dashboard.png
    │   ├── threat_hunting_dashboard.png
    │   ├── threat_alerts_dashboard.png
    │   ├── winlogbeat_discover.png
    │   ├── wazuh_alerts_discover.png
    │   └── expanded_alert_mitre.png
    └── README.md

---

## Previous Projects in This Series

1. [Wazuh + VirusTotal on GCP](https://github.com/samsonejim/wazuh-virustotal-soc-lab) — File integrity monitoring + threat intelligence
2. [Suricata IDS + Wazuh on GCP](https://github.com/samsonejim/suricata-ids-wazuh-integration) — Network intrusion detection
3. [Velociraptor DFIR + Wazuh on GCP](https://github.com/samsonejim/velociraptor-dfir-wazuh-lab) — Digital forensics + incident response

---

## Author

**Samson Ejim**
Cybersecurity Enthusiast | SOC Analyst in Training | Czechia
Building toward SOC Tier 1/2 and Detection Engineering roles

[LinkedIn](https://www.linkedin.com/in/samsonejim/) | [GitHub](https://github.com/samsonejim)
