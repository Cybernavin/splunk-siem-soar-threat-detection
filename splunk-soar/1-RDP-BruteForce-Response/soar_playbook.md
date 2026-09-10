# Splunk SOAR Playbook — RDP Brute Force Response

## Overview

This playbook automates the response to an RDP brute-force attack detected in Splunk Enterprise. It enriches the attacker IP using VirusTotal and blocks the IP on the Windows host if it is identified as malicious.

---

## Workflow

```text
RDP Brute Force Alert
        │
        ▼
VirusTotal IP Reputation
        │
        ▼
Decision (Malicious/Suspicious?)
        │
   ┌────┴────┐
   │         │
  Yes        No
   │         │
   ▼         ▼
Block IP    End
```

---

## Playbook Steps

### 1. Trigger

* Source: Splunk Enterprise alert.
* Detection: Windows Security Event ID **4625** (RDP failed login).
* Input IP: `artifact:*.cef.sourceAddress`

### 2. IP Reputation

* App: **VirusTotal v3**
* Checks the reputation of the source IP.

### 3. Decision

Block the IP if:

* `Malicious > 1`, or
* `Suspicious > 1`

### 4. Block IP

* App: **Windows Remote Management (WinRM)**
* Creates a Windows Firewall rule named **SOAR_RDP_Block** to block the attacker's IP.

---

## Assets Used

| Asset                     | Purpose                      |
| ------------------------- | ---------------------------- |
| VirusTotal v3             | IP reputation lookup         |
| Windows Remote Management | Create Windows Firewall rule |

---

## Result

* Malicious IP is automatically blocked on the Windows host.
* The action is recorded in the Splunk SOAR investigation timeline.
* Analysts can verify the firewall rule and continue the investigation if needed.
