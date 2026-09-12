# SOC Design & XDR Threat Detection with Wazuh

> **Context:** This repository showcases my individual contribution to a 4-person **group project** for the course *IT8510 – Threat Intelligence & Threat Hunting* (Bahrain Polytechnic). The full assignment required a group to design a SOC architecture and build a complete SIEM/XDR/SOAR environment. **This repo contains only the two components I personally designed and implemented**, documented here for my own portfolio.

## My Role in the Project

The group built a SOC environment centered on **Wazuh**, integrating Windows, Linux, and network devices, with threat intelligence, XDR detection/response, and SOAR automation layered on top. My individual responsibility covered:

| # | Task | Deliverable |
|---|------|-------------|
| 1 | **SOC Architecture Design** (Part A) | Designed the full 8-component SOC architecture diagram, data-flow logic, the SOC workflow, and defined 5 SOC roles |
| 2 | **XDR Detection & Automated Response** (Part B) | Simulated 3 attack types, wrote custom Wazuh detection rules, and configured automated active responses for each |
| 3 | **SOAR Automation Using Shuffle** (Part B) | Built an end-to-end SOAR pipeline: Wazuh webhook → Shuffle → VirusTotal enrichment → automated email alert |

## Repository Structure

```
├── task-1-soc-architecture-design/   # SOC architecture diagram + design rationale
├── task-2-xdr-detection-response/    # 3 attacks: SSH brute force, port scan, malware (VirusTotal + FIM)
└── task-3-soar-automation-shuffle/   # Wazuh + Shuffle + VirusTotal automated response pipeline
```

Each folder has its own README with full write-up and supporting screenshots.

## Tech Stack

`Wazuh` `SIEM` `XDR` `SOAR` `Shuffle` `Kali Linux` `Medusa` `Nmap` `UFW` `VirusTotal API` `FIM (Syscheck)` `Python / PyInstaller` `iptables`

## Note on Scope

This was a group assessment; other members were responsible for the Wazuh deployment/agent integration and threat intelligence source integration into Wazuh. I understand the full project end-to-end (it was assessed individually via viva voce), but I'm only showcasing the parts I built.

## A Note on Screenshots

Some screenshots in this repo (flagged `-REDACTED` in the filename) have had live secrets — a webhook URL, personal email, and account name — blacked out before publishing. 
