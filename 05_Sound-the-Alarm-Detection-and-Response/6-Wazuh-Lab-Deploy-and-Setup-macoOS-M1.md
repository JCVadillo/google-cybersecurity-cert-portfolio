# Wazuh SIEM Lab Setup on macOS Apple Silicon (M1 Max)
*Google Cybersecurity Certificate – Course 05: Sound the Alarm: Detection and Response*
*(Portfolio Activity – Adapted for Apple Silicon / ARM64)*

---

## 🎯 Lab Summary

In this lab, I deployed a fully functional **Wazuh SIEM (Security Information and Event Management)** environment on a **macOS M1 Max** machine — a setup that required adapting the original guide, which was designed exclusively for x86_64 architecture and incompatible with Apple Silicon.

The main tasks were to:
- Identify and solve the ARM64 compatibility issue with the official Wazuh OVA
- Deploy an Ubuntu Server ARM64 virtual machine using UTM
- Install Wazuh (Indexer, Manager, and Dashboard) via the official assisted installer
- Transfer and ingest log data (`tutorialdata`) into Wazuh using Filebeat
- Create a Filebeat index pattern and verify the ingested events in Wazuh Discover

---

## ⚙️ Environment Overview

| Component | Details |
|---|---|
| **Host Machine** | MacBook Pro M1 Max – 10 cores, 32 GB RAM, 1 TB SSD |
| **Host OS** | macOS (Apple Silicon / ARM64) |
| **Hypervisor** | UTM (native ARM64 virtualization) |
| **Guest OS** | Ubuntu Server 24.04 LTS (ARM64) |
| **VM Resources** | 6 vCPUs, 12 GB RAM, 80 GB disk |
| **Wazuh Version** | 4.13 (single-node deployment) |
| **VM IP Address** | `192.168.64.4` |

---

## 🚧 The Problem: OVA Incompatibility with Apple Silicon

The original lab guide instructs students to import a **Wazuh OVA** into VirtualBox. This OVA is built for **x86_64/AMD64** architecture only, as confirmed by the [official Wazuh documentation](https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html).

> *"This VM runs only on 64-bit systems with x86_64/AMD64 architecture."*

Attempting to run it on an M1 Mac results in:

```
This virtual machine cannot be powered on because it is incompatible with this ARM machine architecture host.
```

**Solution:** Instead of importing the OVA, I created a native ARM64 Ubuntu Server VM using **UTM** and installed Wazuh from scratch using the official assisted installation script.

---

## 🛠️ Setup Steps

### Step 1 — Create the VM in UTM

1. Opened UTM → **Create a New Virtual Machine**
2. Selected **Virtualize** *(native ARM64, not emulation)*
3. Selected **Linux** and loaded the Ubuntu Server ARM64 ISO
4. Configured resources:
   - CPU Cores: `6`
   - Memory: `12288` MB (12 GB)
   - Storage: `80` GB
5. Named the VM: `wazuh-lab` → **Save**

---

### Step 2 — Install Ubuntu Server

Booted the VM and completed the Ubuntu Server installer:

| Setting | Value |
|---|---|
| **Install type** | Ubuntu Server (default) |
| **Storage** | Use entire disk |
| **Username** | `wazuh` |
| **Server name** | `wazuh-server` |
| **OpenSSH** | ✅ Enabled |
| **Featured Snaps** | None selected |

> ⚠️ After installation, the ISO must be ejected manually in UTM (VM Settings → CD/DVD → Clear) to prevent the installer from running again on reboot.

After confirming the VM booted to the login prompt, SSH access was enabled from the Mac terminal for easier command execution (copy/paste):

```bash
ssh wazuh@192.168.64.4
```

> 💡 Copy/paste between macOS and the UTM console does not work natively even with `spice-vdagent`. SSH from the Mac terminal is the recommended workaround.

---

### Step 3 — Install Wazuh (Single-Node)

#### 3.1 — Update the system

```bash
sudo apt update && sudo apt upgrade -y
```

#### 3.2 — Download the Wazuh installer

```bash
curl -sO https://packages.wazuh.com/4.13/wazuh-install.sh
curl -sO https://packages.wazuh.com/4.13/config.yml
```

#### 3.3 — Configure `config.yml`

Edited the file to set the VM's IP for all nodes (single-node deployment):

```yaml
nodes:
  indexer:
    - name: node-1
      ip: "192.168.64.4"
  server:
    - name: wazuh-1
      ip: "192.168.64.4"
  dashboard:
    - name: dashboard
      ip: "192.168.64.4"
```

#### 3.4 — Generate certificates and install components

```bash
sudo bash wazuh-install.sh -g
sudo bash wazuh-install.sh --wazuh-indexer node-1
sudo bash wazuh-install.sh --wazuh-server wazuh-1
sudo bash wazuh-install.sh --wazuh-dashboard dashboard
```

> ⚠️ **Issue encountered:** The dashboard installation failed on the first attempt because the indexer cluster had not been initialized. Fix:

```bash
sudo bash wazuh-install.sh --start-cluster
sudo bash wazuh-install.sh --wazuh-dashboard dashboard
```

> ⚠️ **Second issue:** The `--wazuh-server` command failed silently during the first run, leaving the Wazuh Manager uninstalled. Detected by running:

```bash
sudo systemctl list-units | grep wazuh
```

> Resolved by re-running the server installation command independently.

#### 3.5 — Verify all services are running

```bash
sudo systemctl status wazuh-indexer wazuh-manager wazuh-dashboard
```

All three services confirmed as `active (running)` ✅

---

### Step 4 — Access the Wazuh Dashboard

Navigated to `https://192.168.64.4` in the browser, accepted the self-signed certificate warning, and logged in with the `admin` credentials generated by the installer at the end of the dashboard installation.

> 💡 The self-signed SSL certificate triggers a browser security warning. This is expected and safe in a local lab environment.

---

### Step 5 — Ingest Log Data (tutorialdata)

#### 5.1 — Transfer the data to the VM

From the Mac terminal (navigating first to the folder containing the file):

```bash
scp tutorialdata.zip wazuh@192.168.64.4:~
```

#### 5.2 — Extract on the VM

```bash
unzip tutorialdata.zip
```

This extracted the following log directories to the home folder:

| Directory | Content |
|---|---|
| `mailsv/` | Mail server logs |
| `vendor_sales/` | Vendor sales logs |
| `www1/`, `www2/`, `www3/` | Web server logs |

#### 5.3 — Create the Filebeat ingestion config

```bash
nano ingest.yml
```

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /home/wazuh/www1/*.log
    - /home/wazuh/www2/*.log
    - /home/wazuh/www3/*.log
    - /home/wazuh/mailsv/*.log
    - /home/wazuh/vendor_sales/*.log

output.elasticsearch:
  hosts: ["https://192.168.64.4:9200"]
  username: "admin"
  password: "<admin-password>"
  ssl.verification_mode: none

setup.ilm.enabled: false
```

#### 5.4 — Fix file permissions (required by Filebeat)

```bash
sudo chown root:root ingest.yml
sudo chmod go-w /home/wazuh/ingest.yml
```

#### 5.5 — Run the ingestion

```bash
sudo /usr/share/filebeat/bin/filebeat -c ingest.yml -e
```

Filebeat processed **109,872 events** (`added: 109872, done: 109872`). Interrupted with `Ctrl+C` once processing stabilized.

---

### Step 6 — Create Index Pattern and Verify in Discover

Since Filebeat sends data to a separate index from Wazuh's native alerts, a new index pattern was required:

1. Dashboard → ☰ Menu → **Dashboards Management → Index Patterns**
2. **Create index pattern** → `filebeat-*`
3. Time field: `@timestamp` → **Create**

Navigated to **Explore → Discover**, selected the `filebeat-*` index, set the time range to **Absolute from January 1, 2000**, searched `*`.

**Result: 109,864 hits** ✅ *(above the minimum 100 required by the activity)*

---

## 🐛 Errors & Fixes Summary

| Error | Cause | Fix |
|---|---|---|
| OVA incompatible with M1 | x86_64-only OVA | Used UTM + Ubuntu ARM64 + manual Wazuh install |
| VM rebooted into installer again | ISO not ejected after install | Ejected ISO in UTM VM settings (CD/DVD → Clear) |
| Dashboard install failed | Indexer cluster not initialized | Ran `--start-cluster` before reinstalling dashboard |
| `wazuh-manager` not found | Server install failed silently | Detected with `systemctl list-units`, re-ran `--wazuh-server` |
| Filebeat permission errors | File not owned by root | Applied `chown root` and `chmod go-w` |
| Only 90 hits in Discover | Wrong index selected (`wazuh-alerts-*`) | Created `filebeat-*` index pattern |
| Copy/paste not working in UTM | `spice-vdagent` unreliable on ARM | Used SSH from Mac terminal instead |

---

## 🧠 Reflections / Notes

- The OVA incompatibility with Apple Silicon is a real-world constraint that required researching an alternative deployment path — a valuable exercise in **adapting procedures to the available environment**.
- Running a full SIEM stack (Indexer + Manager + Dashboard) locally gave hands-on exposure to what enterprise security infrastructure looks like under the hood.
- **SSH as a management interface** proved to be more reliable and productive than the VM console — a common pattern in real server administration.
- Troubleshooting the failed Manager installation reinforced the importance of **verifying each component** rather than assuming a command succeeded because it ran without an obvious error message.
- The difference between `wazuh-alerts-*` and `filebeat-*` index patterns highlighted how log ingestion pipelines work and why **index management** matters in a SIEM.

---

## 📚 Key Skills Demonstrated

- Virtualization with **UTM** on Apple Silicon (ARM64 native)
- **Linux server administration** (Ubuntu Server, systemctl, file permissions, networking)
- **Wazuh SIEM** single-node deployment from scratch
- **Filebeat** configuration and log ingestion pipeline
- **SSH** remote server management
- Troubleshooting installation errors in a Linux environment
- **OpenSearch/Elasticsearch** index pattern management
- Adapting official lab guides to non-standard hardware environments

---

*This lab demonstrates the ability to deploy and operate a SIEM environment from scratch on non-standard hardware, troubleshoot real installation errors, and ingest and query security log data — core skills for a SOC analyst or security engineer.*
