# Installation and Setup Guide

## Overview

This guide explains how to set up the SIEM monitoring lab used in this project.

The environment collects security telemetry from Windows and Linux systems and forwards the logs to Splunk for monitoring, analysis, and threat detection.

## Lab Components

* Splunk Free / Splunk Enterprise
* Splunk Universal Forwarder
* Windows Security Event Logs
* Sysmon
* Microsoft Defender Operational Logs
* Linux Authentication Logs
* Linux System Logs

## Architecture

Windows and Linux systems generate security events that are collected by the Splunk Universal Forwarder and sent to the Splunk server.

The collected events are indexed in Splunk and analyzed using SPL searches. Dashboard Studio visualizations provide centralized monitoring and investigation capabilities.

## Splunk Server Setup

Install Splunk on the system that will act as the SIEM server.

After installation, start Splunk and access the Splunk Web interface.

Default Splunk Web port:

```text
8000
```

Configure the Splunk server to receive data from Splunk Universal Forwarders.

Default receiving port:

```text
9997
```

## Windows Log Collection

Install Splunk Universal Forwarder on the Windows system.

Configure the forwarder to collect Windows Security Event Logs.

The project monitors authentication events including:

* Event ID 4624 - Successful Logon
* Event ID 4625 - Failed Logon
* Event ID 4672 - Special Privileges Assigned
* Event ID 4740 - Account Lockout

## Sysmon Configuration

Install Microsoft Sysmon on the Windows endpoint.

Sysmon Process Creation events are used to monitor process execution.

The primary Sysmon event used in this project is:

```text
Event ID 1 - Process Creation
```

Collected fields include:

* User
* Computer
* Image
* CommandLine
* ParentImage
* ProcessGuid
* ParentProcessGuid

## Microsoft Defender Log Collection

Microsoft Defender Operational logs are collected for malware and security alert monitoring.

The project monitors the following Defender Event IDs:

* Event ID 1116 - Malware Detected
* Event ID 1117 - Remediation Action
* Event ID 1118 - Remediation Action Failed

## Linux Log Collection

Install Splunk Universal Forwarder on the Linux system.

The following Linux logs are monitored:

```text
/var/log/auth.log
/var/log/syslog
```

The Linux dashboard monitors:

* Successful SSH logins
* Failed SSH logins
* Root sessions
* User account creation
* Sudo activity

## SPL Queries

The SPL searches used by the dashboards are available in the `spl/` directory.

```text
spl/
├── authentication_queries.spl
├── sysmon_queries.spl
├── linux_queries.spl
└── threat_detection_queries.spl
```

## Dashboard Import

The project dashboards were created using Splunk Dashboard Studio.

Dashboard exports and screenshots are available in the repository for reference.

## Verification

After configuring log forwarding, verify that events are reaching Splunk.

Windows events:

```text
index=main sourcetype="WinEventLog:Security"
```

Sysmon events:

```text
index=main EventCode=1
```

Linux authentication logs:

```text
index=main source="/var/log/auth.log"
```

If events are returned, log collection is working correctly.

## Future Improvements

Future versions of this project will include:

* MITRE ATT&CK mapping
* Improved behavioral detection logic
* Brute-force detection
* Persistence monitoring
* Lateral movement detection
* Risk-based alerting
* Detection rule documentation
