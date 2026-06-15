# DNS Tunneling Detection Lab using Splunk SIEM

## Overview

This project demonstrates the simulation and detection of DNS tunneling activity in an isolated SOC lab environment using:

- Kali Linux
- Windows DNS Server
- Splunk SIEM
- Iodine DNS tunneling tool

The objective was to generate tunneling-style DNS traffic and detect suspicious DNS behavior using Splunk detection queries and visualizations.

---

## Lab Architecture

```
Kali Linux (Attacker)
        |
        v
Windows DNS Server (Logging)
        |
        v
Splunk SIEM (Detection & Analysis)
```

---

## Lab Setup

[![Kali Linux IP configuration](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/kali-ip-configuration.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/kali-ip-configuration.png)
*Kali Linux attacker machine IP configuration*

[![Windows machine IP configuration](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/windows-ip-config.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/windows-ip-config.png)
*Windows client IP configuration*

[![Windows Server IP configuration](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/windows-server-ip-config.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/windows-server-ip-config.png)
*Windows Server (DNS server) IP configuration*

[![VM connectivity test](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/vm-connectivity-test.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/vm-connectivity-test.png)
*Verifying connectivity between attacker and victim VMs before starting the simulation*

[![Isolated lab setup](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/Isolated_Lab_Setup.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/Isolated_Lab_Setup.png)
*Overall isolated lab environment used for the simulation*

---

## Attack Simulation

Random suspicious DNS queries were generated using `nslookup` and **Iodine**, simulating tunneling-style traffic such as:

```
ajd82js92j.tunnel.lab
```

[![Iodine installation](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/Iodine_Installation.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/Iodine_Installation.png)
*Installing Iodine DNS tunneling tool on Kali Linux*

[![DNS tunnel listener setup](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/DNS_tunnel_listener.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/DNS_tunnel_listener.png)
*Starting the Iodine DNS tunnel listener on the server side*

[![Generating DNS tunneling queries](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/DNS_queries_generate.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/DNS_queries_generate.png)
*Generating tunneling-style DNS queries from the attacker machine*

[![Windows DNS debug logs](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/windows-dns-debug-logs.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/windows-dns-debug-logs.png)
*DNS debug logging enabled on Windows DNS Server to capture tunneling traffic*

---

## Log Ingestion into Splunk

[![Logs generated](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/Generated_Logs.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/Generated_Logs.png)
*Raw DNS logs generated on the Windows DNS Server*

[![Logs generated file view](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/Logs_Generated_file.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/Logs_Generated_file.png)
*DNS log file before ingestion*

[![Splunk log injection](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/Splunk_log_injection.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/Splunk_log_injection.png)
*Ingesting DNS logs into Splunk for analysis*

---

## Detection Results

### Long DNS Query Detection

```
source=dns.log
| eval query_length=len(_raw)
| where query_length > 120
| table _time _raw query_length
```

[![Long DNS query detection results](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/long-dns-query-detection.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/long-dns-query-detection.png)
*Query results showing DNS requests with abnormally long query lengths, indicative of tunneling*

### DNS Traffic Spike Detection (Timechart)

```
source="*dns*"
| timechart span=1m count
```

[![Timechart of DNS logs](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/Timechart_of_logs.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/Timechart_of_logs.png)
*Spike in DNS query volume during the simulated tunneling session*

### High Entropy Subdomain Detection

```
source=dns.log
| regex _raw="[A-Za-z0-9]{25,}"
```

This query flags DNS queries containing long, random-looking alphanumeric strings in the subdomain — a common indicator of encoded tunneling payloads.

### Tunneling Events Summary

[![DNS tunneling events](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/dns-tunneling-events.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/dns-tunneling-events.png)
*Correlated view of DNS tunneling-related events in Splunk*

---

## Final Dashboard

[![Splunk dashboard panel](https://github.com/Chaitanya-Kanawade/dns-tunneling-detection-lab/raw/main/Screenshot/Splunk_dashboard_panel.png)](/Chaitanya-Kanawade/dns-tunneling-detection-lab/blob/main/Screenshot/Splunk_dashboard_panel.png)
*Consolidated Splunk dashboard combining all detection panels for monitoring DNS tunneling activity*

---

## Tools Used

- Splunk Enterprise
- Kali Linux
- Windows Server DNS
- Iodine
- VirtualBox

---

## MITRE ATT&CK Mapping

| Technique | ID |
|---|---|
| Application Layer Protocol: DNS | T1071.004 |

---

## Key Learning Outcomes

- Hands-on understanding of DNS tunneling as a covert data exfiltration / C2 channel
- Building and configuring an isolated SOC lab (Kali Linux, Windows DNS Server, Splunk)
- Writing SPL detection queries for long DNS queries, traffic spikes, and high-entropy subdomains
- Mapping detection logic to the MITRE ATT&CK framework
- End-to-end SOC workflow: simulate → log → ingest → detect → visualize
