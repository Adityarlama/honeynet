# 🐝 Honeynet Project on Microsoft Azure

## Global Attack Monitoring & Threat Visualization with Microsoft Sentinel

### 📌 Overview

This project documents a fully deployed honeynet built on Microsoft Azure, designed to attract real-world attackers and analyze global malicious activity.
I deployed an intentionally vulnerable Windows 10 VM, exposed it to the internet, collected security events using Microsoft Sentinel, enriched the attack data with geolocation metadata, and visualized global threat patterns through an interactive attack map.

Over a 24-hour period, the honeynet generated tens of thousands of failed login attempts, providing a real dataset for SIEM analysis, KQL querying, and threat visualization.

--- 

## 🧠 What is a Honeynet?

A **honeynet** is a decoy network designed to mimic a real environment.
It's a network with intentionally disabled security controls (like firewalls) so attackers are encouraged to interact with it.

- This allows security analysts to:
- Observe malicious behavior safely
- Practice log analysis
- Study attacker patterns
- Understand common brute-force and scanning activity
- Build SIEM queries, dashboards, and visualizations

---

## 🏗️ Architecture Diagram

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
- Azure Virtual Machines
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


