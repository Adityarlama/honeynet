# 🐝 Honeynet Project on Microsoft Azure

## Global Attack Monitoring & Threat Visualization with Microsoft Sentinel

### 📌 Overview

This project demonstrates the creation of a **honeynet**—a deliberately vulnerable cloud-hosted environment used to attract, observe, and analyze real-world cyberattacks.
Using **Microsoft Azure** as the virtual host platform and **Microsoft Sentinel** as the **SIEM**, I exposed a Windows 10 VM to the internet, collected large volumes of security events, enriched the logs with geolocation data, and visualized global attack patterns using an attack map.

Over a 24-hour period, the honeynet recorded tens of thousands of _Event ID 4625_ (failed logins) originating from hundreds of regions across the world.

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

Azure Subscription </br>
│ </br>
└── Resource Group (Isolated Sandbox)</br>
    │
    ├── Windows 10 Virtual Machine (honeypot)
    │     ├── Public IP
    │     ├── All firewalls disabled
    │     └── NSG inbound “Allow Any” rule
    │
    ├── Virtual Network (VNet) + Subnet
    │
    ├── Log Analytics Workspace (LAW)
    │
    └── Microsoft Sentinel
          ├── Windows Security Events via AMA
          ├── Watchlist (GeoIP Database)
          └── Attack Map Workbook

