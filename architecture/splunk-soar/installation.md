# Splunk SOAR Installation (WSL RHEL 9) + Splunk Enterprise Integration

## Overview

This setup installs **Splunk SOAR 8.5** inside **WSL (RHEL 9)** and connects it with **Splunk Enterprise** running on Windows.

**Environment**

| Component          | Value                         |
| ------------------ | ----------------------------- |
| OS                 | Windows 10 + WSL2             |
| Linux Distribution | RHEL 9                        |
| Splunk Enterprise  | Windows Host                  |
| Splunk SOAR        | WSL (RHEL 9)                  |
| Communication      | HTTPS (8443), REST API (8089) |

---

## Installation Steps

### 1. Install WSL and RHEL 9

```bash
wsl --install
```

Import or install the RHEL 9 image and update packages.

```bash
sudo dnf update -y
```

---

### 2. Install Splunk SOAR

Install the Splunk SOAR package inside RHEL 9.

```bash
sudo dnf install ./splunk_soar*.rpm
```

Initialize SOAR.

```bash
sudo /opt/phantom/bin/register_phantom.sh
```

Start services.

```bash
sudo systemctl start phantom
sudo systemctl enable phantom
```

Access the SOAR web interface.

```
https://localhost:8443
```

---

### 3. Install Splunk Enterprise (Windows)

Install Splunk Enterprise normally on Windows.

Verify services.

```
Splunk Web : http://localhost:8000
Management Port : 8089
Receiving Port : 9997
```

---

### 4. Enable SOAR Integration in Splunk Enterprise

Go to:

```
Settings → Data Inputs → HTTP Event Collector
```

Enable HEC and create a token.

Example:

```
HEC Port : 8088
Index : main
```

---

### 5. Add Splunk Enterprise Asset in SOAR

Navigate:

```
SOAR → Assets → New Asset
```

Choose **Splunk App**.

Configure:

| Field      | Value              |
| ---------- | ------------------ |
| Host       | Windows IP Address |
| Port       | 8089               |
| Username   | Splunk Admin       |
| Password   | Splunk Password    |
| Verify SSL | Disabled (Lab)     |

Save and run **Test Connectivity**.

Expected result:

```
Connectivity Successful
```

---

### 6. Create Ingest Automation

In SOAR:

```
Administration → Event Settings → Ingest Settings
```

Enable event ingestion from Splunk Enterprise.

Configure the event label.

Example:

```
Label = events
```

---

### 7. Verify Connection

Run an SPL search inside Splunk Enterprise.

```spl
index=main EventCode=4625
| head 5
```

Send results to SOAR.

Confirm a new event appears under:

```
SOAR → Investigations
```

---

## Connection Flow

```text
Windows Security Logs
        │
        ▼
Splunk Universal Forwarder
        │
        ▼
Splunk Enterprise
 (Index + Detection)
        │ REST API / HEC
        ▼
Splunk SOAR
 (Playbooks & Automation)
```

---

## Ports Used

| Port | Purpose                            |
| ---- | ---------------------------------- |
| 8443 | Splunk SOAR Web UI                 |
| 8089 | Splunk Enterprise Management API   |
| 8088 | HTTP Event Collector (HEC)         |
| 9997 | Universal Forwarder Receiving Port |

---

## Verification Checklist

* WSL RHEL 9 running.
* Splunk SOAR accessible on `https://localhost:8443`.
* Splunk Enterprise running on `http://localhost:8000`.
* Splunk asset connectivity successful in SOAR.
* Events successfully ingested from Splunk Enterprise into SOAR.
* Playbooks execute automatically on incoming events.
