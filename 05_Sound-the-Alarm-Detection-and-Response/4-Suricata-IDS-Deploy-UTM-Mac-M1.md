# Suricata IDS Deployment on UTM – Mac M1 (ARM)
*Google Cybersecurity Certificate – Course 05: Sound the Alarm: Detection and Response*
*(Portfolio Activity – Deploying and Configuring Suricata via UTM)*

---

## 🎯 Lab Summary

In this activity, I deployed **Suricata 8.0.6** as a network-based **Intrusion Detection System (IDS)** on a dedicated Ubuntu 26.04 LTS virtual machine running on **UTM (QEMU v10.0.2)** on a Mac M1.

The main objectives were to:
- Create and configure a dedicated VM for Suricata following security best practices
- Install Suricata from the official **OISF** repository
- Configure Suricata to monitor the correct network interface
- Download and validate detection rules from **Emerging Threats Open**
- Write and test a custom detection rule
- Verify live alert generation via CLI log analysis

---

## ⚙️ Environment

| Component | Details |
|---|---|
| Host Machine | MacBook (Apple M1 – ARM64) |
| Hypervisor | UTM (QEMU v10.0.2) |
| Virtualization Mode | Virtualize (native ARM64) |
| Guest OS | Ubuntu Server 26.04 LTS (ARM64) |
| VM Hostname | `suricata` |
| VM Username | `suricata` |
| VM IP Address | `192.168.64.6` |
| Network Interface | `enp0s1` |
| Suricata Version | 8.0.6 RELEASE |
| Deployment Mode | IDS (Intrusion Detection System) |

---

## 🏗️ Architecture Decision: Dedicated VM

Suricata was deployed on a **separate VM** from the existing Wazuh installation, following the principle of **least functionality**:

- **Separation of responsibilities** — Wazuh is a SIEM/EDR; Suricata is a network IDS/IPS. Each tool should run independently.
- **Resource isolation** — Suricata performs real-time packet inspection and is CPU/RAM intensive. Sharing resources with Wazuh could degrade both services.
- **Cleaner troubleshooting** — Issues can be isolated to a specific host without cross-tool interference.
- **Stronger portfolio architecture** — Demonstrates understanding of security tool deployment principles, not just installation.

---

## 🖥️ VM Configuration (UTM)

| Parameter | Value | Reason |
|---|---|---|
| Virtualization Engine | Virtualize | Host (M1) and guest (ARM64) share the same architecture — no emulation needed |
| OS Type | Linux | Ubuntu is a Linux distribution |
| Memory | 2048 MB | Sufficient for OS + Suricata in a lab environment |
| CPU Cores | 2 | Suricata supports multi-threading; 2 cores adequate for lab traffic |
| Storage | 64 GB | Provides space for OS, Suricata, rules, and months of logs |
| Display | Enabled, no OpenGL | Ubuntu Server is headless; hardware acceleration unnecessary and potentially unstable |
| Shared Directory | Disabled | VM isolation is best practice for a security tool |
| OpenSSH | Installed | Enables SSH access for copy/paste and professional terminal workflow |

---

## 🚀 Deployment Steps

### Step 1 — System Update

```bash
sudo apt update && sudo apt upgrade -y
```

Updated all system packages before installation to ensure a clean, patched baseline.

---

### Step 2 — Add Official OISF Repository

```bash
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update
```

Suricata is installed from the **OISF (Open Information Security Foundation)** official repository rather than the default Ubuntu repos, which may contain outdated versions.

---

### Step 3 — Install Suricata

```bash
sudo apt install suricata -y
```

Installed Suricata and all dependencies from the OISF repository.

**Verification:**

```bash
suricata --version
```

```
Suricata version 8.0.6 RELEASE
```

---

### Step 4 — Configure Network Interface

Suricata must be told which network interface to monitor. The interface was identified during OS installation as `enp0s1`.

```bash
sudo nano /etc/suricata/suricata.yaml
```

Located the `af-packet` section (`Ctrl+W` → search `af-packet`) and updated:

```yaml
af-packet:
  - interface: enp0s1
```

> **Why this matters:** If the wrong interface is specified, Suricata captures no traffic and generates no alerts — the most common configuration mistake.

---

### Step 5 — Update Detection Rules

```bash
sudo suricata-update
```

Downloaded the **Emerging Threats Open** ruleset — a free, industry-respected set of detection signatures maintained by Proofpoint. These rules define what traffic patterns Suricata will flag as suspicious or malicious.

**Configuration validation:**

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml -v
```

```
Notice: suricata: Configuration provided was successfully loaded. Exiting.
```

---

### Step 6 — Enable and Start Service

```bash
sudo systemctl start suricata
sudo systemctl enable suricata
sudo systemctl restart suricata
```

| Command | Purpose |
|---|---|
| `start` | Starts the service immediately |
| `enable` | Configures Suricata to start automatically on every boot |
| `restart` | Reloads the service after configuration changes |

---

### Step 7 — Verify Packet Capture

```bash
sudo suricatasc -c "iface-stat enp0s1"
```

```json
{"message":{"pkts":54048,"invalid-checksums":0,"drop":0,"bypassed":0},"return":"OK"}
```

Confirmed Suricata was actively capturing **54,048 packets** with **zero drops** on `enp0s1`.

---

### Step 8 — Create and Test a Custom Rule

A custom rule was written to validate the detection pipeline end-to-end:

```bash
sudo mkdir -p /etc/suricata/rules
sudo nano /etc/suricata/rules/test.rules
```

```
alert icmp any any -> any any (msg:"ICMP Ping Detectado"; sid:9000001; rev:1;)
```

**Rule breakdown:**

| Field | Value | Meaning |
|---|---|---|
| `alert` | action | Generate an alert when this rule matches |
| `icmp` | protocol | Monitor ICMP traffic (used by ping) |
| `any any -> any any` | source/destination | Match any IP and port in any direction |
| `msg` | `"ICMP Ping Detectado"` | Human-readable alert message |
| `sid` | `9000001` | Unique rule identifier |
| `rev` | `1` | Rule version |

The rule file was registered in `suricata.yaml`:

```yaml
rule-files:
  - suricata.rules
  - /etc/suricata/rules/test.rules
```

Service was restarted to load the new rule:

```bash
sudo systemctl restart suricata
```

---

## ✅ Alert Verification

Monitored the alert log in real time:

```bash
sudo tail -f /var/log/suricata/fast.log
```

Triggered the custom rule with a ping:

```bash
ping -c 4 8.8.8.8
```

**Alert output:**

```
08/06/2026-09:59:39.102698 [**] [1:9000001:1] ICMP Ping Detectado [**]
[Classification: (null)] [Priority: 3] {ICMP} 192.168.64.6:8 -> 8.8.8.8:0
```

Additionally, Emerging Threats rules generated alerts for background traffic, confirming the full ruleset was active and operational.

**Reading the alert:**

| Field | Value | Meaning |
|---|---|---|
| Timestamp | `08/06/2026-09:59:39` | When the event was detected |
| Rule ID | `[1:9000001:1]` | Generator:SID:Revision — matches our custom rule |
| Message | `ICMP Ping Detectado` | The `msg` field from our rule |
| Protocol | `{ICMP}` | Traffic type that triggered the alert |
| Source | `192.168.64.6` | Our Suricata VM |
| Destination | `8.8.8.8` | Google's public DNS |

---

## 📁 Key File Locations

| File/Directory | Purpose |
|---|---|
| `/etc/suricata/suricata.yaml` | Main Suricata configuration file |
| `/var/lib/suricata/rules/suricata.rules` | Emerging Threats ruleset (downloaded via suricata-update) |
| `/etc/suricata/rules/test.rules` | Custom rules directory |
| `/var/log/suricata/fast.log` | Human-readable alert log |
| `/var/log/suricata/eve.json` | Full event log in JSON format (flows, stats, alerts) |

---

## 🧠 Reflections / Notes

- **`fast.log` vs `eve.json`** — Suricata 8.x logs all events (flows, stats, alerts) to `eve.json`. The `fast.log` is specifically for alerts in a concise, human-readable format. For SOC work, `eve.json` is the source of truth, while `fast.log` is useful for quick triage.
- **Rules are the brain of Suricata** — Without rules, Suricata captures traffic but generates no alerts. Understanding rule syntax (`action protocol src -> dst (options)`) is fundamental to both writing detections and understanding false positives.
- **Interface configuration is critical** — The most common Suricata misconfiguration is pointing to the wrong network interface. Always verify the interface name with `ip a` before editing `suricata.yaml`.
- **`suricatasc`** allows real-time interaction with a running Suricata instance — useful for verifying packet capture stats without restarting the service.
- **Virtualize vs Emulate** — When the host and guest share the same CPU architecture (ARM64 in this case), Virtualize always outperforms Emulate, which must translate instructions at runtime.
- **Dedicated VMs per tool** — Running Wazuh and Suricata on separate VMs is not just best practice — it reflects how real SOC environments are architected, with purpose-built sensors and collectors deployed independently.

---

## 📚 Key Skills Demonstrated

- Deployment of a **network IDS** using Suricata 8.0.6 in a virtualized ARM64 environment
- VM creation and hardware configuration in **UTM** following resource allocation best practices
- Installation from an **official vendor repository (OISF)** rather than default OS repos
- Network interface identification and **Suricata configuration** via YAML
- **Rule management** using `suricata-update` and Emerging Threats Open ruleset
- **Custom Suricata rule authoring** using the standard rule syntax
- Real-time alert monitoring and log analysis via **CLI** (`tail`, `jq`, `grep`)
- Understanding of `fast.log` vs `eve.json` log formats
- Application of **least functionality** and **separation of responsibilities** principles in VM architecture

---

*This activity demonstrates the ability to deploy, configure, and validate a network intrusion detection system from scratch in a virtualized environment, write custom detection rules, and analyze alerts using CLI-based log analysis tools — core competencies for a SOC analyst role.*
