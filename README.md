# 🐝🕸 Honeynet Project on Microsoft Azure

## Global Attack Monitoring & Threat Visualization with Microsoft Sentinel

### 📌 Overview

This project documents a fully deployed honeynet built on Microsoft Azure, designed to attract real-world attackers and analyze global malicious activity.
I deployed an intentionally vulnerable Windows 10 VM, exposed it to the internet, collected security events using Microsoft Sentinel, enriched the attack data with geolocation metadata, and visualized global threat patterns through an interactive attack map.

Over a 24-hour period, the honeynet generated tens of thousands of failed login attempts, providing a real dataset for SIEM analysis, KQL querying, and threat visualization.

--- 

## What is a Honeynet?

A **honeynet** is a decoy network designed to mimic a real environment.
It's a network with intentionally disabled security controls (like firewalls) so attackers are encouraged to interact with it.

- This allows security analysts to:
- Observe malicious behavior safely
- Practice log analysis
- Study attacker patterns
- Understand common brute-force and scanning activity
- Build SIEM queries, dashboards, and visualizations

---

## Architecture Diagram

```
Isolated Resource Group (Azure)
│
├── Windows 10 Honeypot VM (Firewalls Disabled)
│     ├── Public IP Address
│     ├── NSG Rule: Allow All Inbound Traffic
│     └── Event ID 4625 generation at scale
│
├── Azure Virtual Network + Subnet
│
├── Log Analytics Workspace (Central Log Store)
│
└── Microsoft Sentinel
      ├── Windows Security Events via AMA
      ├── 55K-row GeoIP Watchlist
      └── Attack Map Workbook

```

This structure ensured:
- Controlled isolation
- Safe exposure
- Accurate log ingestion
- Comprehensive threat analysis

---

## 🧰 Technologies Used

### Cloud Infrastructure

- Microsoft Azure
- Azure Virtual Network (VNet)
- Network Security Groups (NSG)
- Azure Resource Groups

### Security & Monitoring

- Microsoft Sentinel (SIEM)
- Log Analytics Workspace (LAW)
- Windows Security Events via AMA
- Watchlists (GeoIP enrichment)
- Kusto Query Language (KQL)

### Data Tools

- CSV-based geolocation mapping
- Sentinel Attack Map Workbook
- map.json (Sentinel workbook import)

---

## 🎯 Objectives

The goals of this project were to:

- Deploy a cloud-hosted environment vulnerable by design
- Capture authentic attacker behavior
- Analyze logs at scale using Kusto Query Language (KQL)
- Enrich raw logs with geographic data
- Produce visual insights into global threat activity
- Practice SIEM workflows used in real SOC operations

## 🧪 Environment Setup & Exposure

To generate real attack traffic, I deployed a Windows 10 Virtual Machine on Azure, placed it inside a dedicated resource group, and configured it as a honeypot:

Key configurations applied:

- All inbound traffic allowed in the Network Security Group
- Windows Firewall disabled (Public, Private, Domain)
- Public-facing RDP enabled
- Machine left exposed for 24 hours without user activity
- Immediately after exposure, automated scanners and brute-force bots began interacting with the machine.

---

## 📝 Log Collection & Forwarding

All security logs were forwarded to a Log Analytics Workspace and processed through Microsoft Sentinel.

Sentinel configuration included:

- Enabling Windows Security Events via AMA
- Creating a Data Collection Rule (DCR)
- Connecting the VM as a data source
- Validating ingestion of:
  - Event ID 4625 (failed logins)
  - Event ID 4624 (successful logins)
  - Authentication attempts
  - Network traffic events

KQL was used throughout to extract, filter, and analyze event data.

The KQL query I used:

```
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Account, Computer, EventID, Activity , IPAddress
```

---

## 🌍 Log Enrichment & GeoIP Integration

Raw logs do not include geographic information, so I enriched attacker IP addresses using a 55,000-row GeoIP watchlist imported into Sentinel.

This allowed me to:

- Map each attacker to a specific city, region, and country
- Identify global attack patterns
- Group high-activity IP ranges
- Join geolocation data directly into KQL queries

KQL Querry that I used for enrichment: 

```
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
```
This transformed raw logs into meaningful threat intelligence.

---

## 🗺️ Visualization — Global Attack Map

To visualize attack patterns, I built a custom Sentinel Workbook using a JSON-based map configuration.
The map plotted each failed login attempt geographically, with clustering based on event volume.

This visualization made it immediately clear:

- Which regions were the most aggressive
- The distribution of global brute-force activity
- The intensity of scanning and probing within hours of exposure



<p align="center">
  <img src="https://i.imgur.com/z9M6QIE.png" alt="Page 1" />
</p>

---

## 📊 Key Findings from 24 Hours of Logs

The honeynet recorded tens of thousands of brute-force attempts worldwide.
Below are the top regions by unsuccessful login attempts (Event ID 4625):

| Region                     | Attempts |
| -------------------------- | -------- |
| Gwangmyeong (South Korea)  | 3.98K    |
| Bangshal (Bangladesh)      | 3.13K    |
| Akola (India)              | 3.14K    |
| Brussels (Belgium)         | 3.12K    |
| Ciamis (Indonesia)         | 3.11K    |
| United States (Various)    | 3.10K    |
| Central (Hong Kong)        | 3.10K    |
| Contoocook (United States) | 2.31K    |
| Paignton (United Kingdom)  | 801      |
| Other                      | 2.72K    |


Additional Observations
- Attack activity begins within minutes of exposure
- RDP brute-force attempts dominate
- Traffic originates from compromised global hosts, not just localized scanners
- Automated bots attack newly created VMs extremely quickly
- The volume of logs rapidly becomes large enough for meaningful SIEM practice

--- 

## 🧠 Skills Demonstrated

### Cloud Security
- Azure architecture design
- Network Security Groups
- Resource isolation and sandboxing

### Threat Analysis
- Identification of brute-force and scanning activity
- Understanding attacker behavior in the wild
  
### SIEM & Log Analytics
- Microsoft Sentinel configuration
- Log Analytics Workspace management
- Watchlist creation and lookup operations
- KQL proficiency (joins, enrichments, aggregations)

### Visualization & Reporting
- Custom workbook building
- Attack map visualization
- Data correlation and summarization

---

## 📌 Summary

This project successfully demonstrates the end-to-end deployment of a honeynet, real-world data collection, security event enrichment, SIEM analysis, and threat visualization.
It replicates a realistic SOC workflow, involving:
- Log ingestion
- Querying
- Enrichment
- Interpretation
- Reporting

The final dataset and attack map provide a clear, visual story of global attack behavior observed in a 24-hour window.
