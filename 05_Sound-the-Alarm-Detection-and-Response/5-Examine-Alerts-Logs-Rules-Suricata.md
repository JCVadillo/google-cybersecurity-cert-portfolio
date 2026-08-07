# Examining Alerts, Logs, and Rules with Suricata
*Google Cybersecurity Certificate – Course 05: Sound the Alarm: Detection and Response*
*(Portfolio Activity – Examine Alerts, Logs, and Rules with Suricata)*

---

## 🎯 Lab Summary

In this activity, I worked with **Suricata**, an open-source network intrusion detection and prevention system (IDS/IPS), to configure rules, trigger alerts, and analyze network traffic logs.

The main tasks were to:
- Examine the components of a custom Suricata rule
- Run Suricata against a packet capture (`.pcap`) file using that rule
- Analyze the generated alerts in `fast.log`
- Parse and filter the `eve.json` log file using `jq`

---

## ⚙️ Scenario Overview

As a security analyst, understanding how detection rules are structured and how logs are generated is a critical skill. In this lab, I worked in a Linux environment with a pre-existing `custom.rules` file and a `sample.pcap` file containing network traffic. My goal was to trigger alerts, verify the output logs, and extract meaningful data from Suricata's JSON event log.

---

## 🔍 Task 1 – Examine a Custom Suricata Rule

Before running Suricata, I inspected the rule defined in `custom.rules` using `cat`:

```bash
cat custom.rules
```

**Rule content:**
```
alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
```

### Rule Breakdown

| **Component** | **Value** | **Purpose** |
|---|---|---|
| **Action** | `alert` | Generates an alert when the rule matches |
| **Protocol** | `http` | Applies to HTTP traffic only |
| **Source** | `$HOME_NET any` | Any port on the internal network |
| **Direction** | `->` | Unidirectional: internal → external |
| **Destination** | `$EXTERNAL_NET any` | Any port on external networks |
| **msg** | `"GET on wire"` | Human-readable alert message |
| **flow** | `established,to_server` | Matches established sessions sending data to the server |
| **content** | `"GET"` | Looks for the string `GET` in the packet payload |
| **http_method** | *(modifier)* | Restricts the content match to the HTTP method field specifically |
| **sid** | `12345` | Unique signature ID for this rule |
| **rev** | `3` | Rule revision number |

> The rule is designed to alert on any outbound HTTP GET request from the internal network — a useful baseline for detecting potentially suspicious outbound web traffic.

---

## ⚡ Task 2 – Trigger the Rule and Examine `fast.log`

### Running Suricata

I ran Suricata in offline mode against the sample capture file, applying the custom rules:

```bash
sudo suricata -r sample.pcap -S custom.rules -k none
```

| **Flag** | **Purpose** |
|---|---|
| `-r sample.pcap` | Read packets from a `.pcap` file instead of a live interface |
| `-S custom.rules` | Use only the specified rules file (overrides defaults) |
| `-k none` | Disable checksum validation (common for test captures) |

Suricata processed **200 packets (54,238 bytes)** from the file.

### Verifying Log Output

```bash
ls -l /var/log/suricata/
```

The directory contained: `eve.json`, `fast.log`, `stats.log`, `suricata.log`

### Reading `fast.log`

```bash
cat /var/log/suricata/fast.log
```

```
11/23/2022-12:38:34.624866  [**] [1:12345:3] GET on wire [**] [Classification: (null)] [Priority: 3] {TCP} 172.21.224.2:49652 -> 142.250.1.139:80
11/23/2022-12:38:58.958203  [**] [1:12345:3] GET on wire [**] [Classification: (null)] [Priority: 3] {TCP} 172.21.224.2:58494 -> 142.250.1.102:80
```

### `fast.log` Entry Analysis

| **Field** | **Alert 1** | **Alert 2** |
|---|---|---|
| **Timestamp** | 2022-11-23 12:38:34 | 2022-11-23 12:38:58 |
| **Signature** | `[1:12345:3] GET on wire` | `[1:12345:3] GET on wire` |
| **Protocol** | TCP | TCP |
| **Source IP:Port** | 172.21.224.2:49652 | 172.21.224.2:58494 |
| **Destination IP:Port** | 142.250.1.139:80 | 142.250.1.102:80 |

> Both alerts matched rule `sid:12345` (`GET on wire`), confirming the rule fired correctly on two separate HTTP GET requests in the capture.

---

## 📄 Task 3 – Examine `eve.json` with `jq`

`eve.json` is Suricata's primary, richly structured log — it records detailed metadata about every event.

### Step 1 – Raw output (pretty-printed)

```bash
jq . /var/log/suricata/eve.json
```

This formatted the raw single-line JSON entries into readable structured output, revealing two alert events with full metadata: timestamps, flow IDs, IP addresses, ports, protocol, alert details, HTTP context, and flow statistics.

### Step 2 – Extracting specific fields (compact)

```bash
jq -c "[.timestamp,.flow_id,.alert.signature,.proto,.dest_ip]" /var/log/suricata/eve.json
```

**Output:**
```
["2022-11-23T12:38:34.624866+0000",1234219322472597,"GET on wire","TCP","142.250.1.139"]
["2022-11-23T12:38:58.958203+0000",1750938249499892,"GET on wire","TCP","142.250.1.102"]
```

This confirmed the two triggered alerts, their `flow_id` values, the matching signature, and the destination IPs.

### Step 3 – Filtering by `flow_id`

```bash
jq "select(.flow_id==1234219322472597)" /var/log/suricata/eve.json
```

**Key fields from the filtered event:**

| **Field** | **Value** |
|---|---|
| `timestamp` | `2022-11-23T12:38:34.624866+0000` |
| `event_type` | `alert` |
| `src_ip` | `172.21.224.2` |
| `src_port` | `49652` |
| `dest_ip` | `142.250.1.139` |
| `dest_port` | `80` |
| `proto` | `TCP` |
| `alert.action` | `allowed` |
| `alert.signature_id` | `12345` |
| `alert.signature` | `GET on wire` |
| `alert.severity` | `3` |
| `http.hostname` | `opensource.google.com` |
| `http.url` | `/` |
| `http.http_user_agent` | `curl/7.74.0` |
| `http.http_method` | `GET` |
| `http.status` | `301` |
| `http.redirect` | `https://opensource.google/` |
| `flow.pkts_toserver` | `4` |
| `flow.pkts_toclient` | `3` |
| `flow.bytes_toserver` | `357` |
| `flow.bytes_toclient` | `788` |

> The `eve.json` entry revealed that the alert corresponded to an HTTP GET request made with `curl/7.74.0` targeting `opensource.google.com`, which returned an HTTP `301` redirect to `https://opensource.google/`. The `alert.action: allowed` field indicates Suricata was running in IDS mode — it detected and logged the event but did not block it.

---

## 🧠 Reflections / Notes

- **`fast.log` vs `eve.json`:** `fast.log` is optimized for quick, human-readable alerting — each line is a single alert summary. `eve.json` is the richer, machine-parseable log; it captures full context per event and is the preferred source for SIEM ingestion and deeper forensic analysis.
- **`jq` as a triage tool:** The ability to filter and extract specific fields from JSON logs with `jq` is extremely practical in real-world IR. Rather than scrolling through verbose output, you can pinpoint exactly the event you need.
- **Rule anatomy matters:** Understanding each component of a Suricata rule — action, header, and rule options — is essential for both writing effective detection logic and interpreting why an alert fired.
- **`flow_id` as a correlation key:** The `flow_id` links all log entries belonging to the same network flow, making it a useful pivot point for reconstructing the full picture of a network conversation.
- **IDS vs IPS mode:** Suricata's `alert` action logs the event without blocking. In a production IPS deployment, the action would be `drop` or `reject` to actively prevent the traffic.

---

## 📚 Key Skills Demonstrated

- Configuration and execution of **Suricata** in offline analysis mode
- Interpretation of **Suricata rule syntax** (action, header, rule options)
- Analysis of **`fast.log`** for quick alert triage
- Parsing and querying **`eve.json`** using `jq` for structured log analysis
- Extraction and correlation of network event data (IPs, ports, protocols, HTTP metadata)
- Understanding of **IDS vs IPS** operational modes
- Linux command-line proficiency: `cat`, `ls`, `sudo`, `jq`

---

*This activity demonstrates the ability to work with a network-based intrusion detection system — from understanding detection rule logic to triggering alerts and extracting actionable intelligence from structured log output.*
