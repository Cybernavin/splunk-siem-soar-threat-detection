# MITRE ATT&CK & D3FEND Mapping — RDP Brute Force Response

## Overview

This document maps the RDP Brute Force detection and automated response playbook to the MITRE ATT&CK framework and the MITRE D3FEND defensive framework.

---

## MITRE ATT&CK Mapping

| Tactic            | Technique                              | ID            |
| ----------------- | -------------------------------------- | ------------- |
| Credential Access | Password Guessing                      | **T1110.001** |
| Initial Access    | External Remote Services (RDP)         | **T1133**     |
| Defense Evasion   | Valid Accounts (attempted login abuse) | **T1078**     |

### Detection Logic

* **Event Source:** Windows Security Logs
* **Event ID:** 4625 (Failed Logon)
* **Logon Type:** 10 (Remote Desktop Protocol)
* **Detection:** Multiple failed RDP login attempts from the same source IP within a short time window.

---

## MITRE D3FEND Mapping

| Defensive Technique           | D3FEND ID  | Used In Playbook                          |
| ----------------------------- | ---------- | ----------------------------------------- |
| IP Address Analysis           | **D3-IPA** | VirusTotal IP reputation lookup           |
| Network Traffic Filtering     | **D3-NTF** | Block malicious IP using Windows Firewall |
| Indicator Reputation Analysis | **D3-IRA** | Evaluate IP reputation before response    |
| Firewall Rule Management      | **D3-FRM** | Create inbound firewall block rule        |

---

## Detection and Response Flow

1. Splunk Enterprise detects repeated RDP failed logins.
2. Splunk SOAR receives the alert artifact.
3. VirusTotal checks the source IP reputation.
4. If the IP is malicious or suspicious, SOAR creates a Windows Firewall rule to block it.
5. The action is recorded in the SOAR investigation timeline.

---

## Mapping Summary

| Detection Stage           | Framework Mapping               |
| ------------------------- | ------------------------------- |
| RDP Brute Force Detection | ATT&CK **T1110.001**, **T1133** |
| IP Reputation Enrichment  | D3FEND **D3-IPA**, **D3-IRA**   |
| Automatic Firewall Block  | D3FEND **D3-NTF**, **D3-FRM**   |
