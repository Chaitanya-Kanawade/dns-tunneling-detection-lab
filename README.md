\# DNS Tunneling Detection Lab using Splunk SIEM



\## Overview

This project demonstrates the simulation and detection of DNS tunneling activity in an isolated SOC lab environment using:



\- Kali Linux

\- Windows DNS Server

\- Splunk SIEM

\- Iodine DNS tunneling tool



The objective was to generate tunneling-style DNS traffic and detect suspicious DNS behavior using Splunk detection queries and visualizations.



\---



\## Lab Architecture



Kali Linux (Attacker)

&#x20;       |

&#x20;       v

Windows DNS Server (Logging)

&#x20;       |

&#x20;       v

Splunk SIEM (Detection \& Analysis)



\---



\## Attack Simulation



\### Simulated DNS Tunneling

Random suspicious DNS queries were generated using:

\- nslookup

\- Iodine



Example:

ajd82js92j.tunnel.lab



\---



\## Detection Techniques



\### Long DNS Query Detection



```spl

source=dns.log

| eval query_length=len(_raw)

| where query_length > 120

| table _time _raw query_length

```



\### DNS Traffic Spike Detection



```spl

source="\*dns\*" 

| timechart span=1m count

```



\### High Entropy Detection



```spl

source=dns.log

| regex _raw="\[A-Za-z0-9]{25,}"

```



\---



\## Tools Used



\- Splunk Enterprise

\- Kali Linux

\- Windows Server DNS

\- Iodine

\- VirtualBox



\---



\## MITRE ATT\&CK Mapping



| Technique | ID |

|---|---|

| DNS Protocol | T1071.004 |



\---



\## Key Learning Outcomes



\- DNS tunneling concepts

\- SIEM log analysis

\- Splunk detection engineering

\- DNS anomaly detection

\- SOC monitoring workflow

\- Blue team analysis



\---

## Lab Setup

### Network Configuration
![Kali Linux IP configuration](Screenshot/kali-ip-configuration.png)
*Kali Linux attacker machine IP configuration*

![Windows machine IP configuration](Screenshot/windows-ip-config.png)
*Windows client IP configuration*

![Windows Server IP configuration](Screenshot/windows-server-ip-config.png)
*Windows Server (DNS server) IP configuration*

![VM connectivity test](Screenshot/vm-connectivity-test.png)
*Verifying connectivity between attacker and victim VMs before starting the simulation*

![Isolated lab setup](Screenshot/Isolated_Lab_Setup.png)
*Overall isolated lab environment used for the simulation*

---

## Attack Simulation

![Iodine installation](Screenshot/Iodine_Installation.png)
*Installing Iodine DNS tunneling tool on Kali Linux*

![DNS tunnel listener setup](Screenshot/DNS_tunnel_listener.png)
*Starting the Iodine DNS tunnel listener on the server side*

![Generating DNS tunneling queries](Screenshot/DNS_queries_generate.png)
*Generating tunneling-style DNS queries from the attacker machine*

![Windows DNS debug logs](Screenshot/windows-dns-debug-logs.png)
*DNS debug logging enabled on Windows DNS Server to capture tunneling traffic*

---

## Log Ingestion into Splunk

![Logs generated](Screenshot/Generated_Logs.png)
*Raw DNS logs generated on the Windows DNS Server*

![Logs generated file view](Screenshot/Logs_Generated_file.png)
*DNS log file before ingestion*

![Splunk log injection](Screenshot/Splunk_log_injection.png)
*Ingesting DNS logs into Splunk for analysis*

---

## Detection Results

### Long DNS Query Detection

\`\`\`
source=dns.log
| eval query_length=len(_raw)
| where query_length > 120
| table _time _raw query_length
\`\`\`

![Long DNS query detection results](Screenshot/long-dns-query-detection.png)
*Query results showing DNS requests with abnormally long query lengths, indicative of tunneling*

### DNS Traffic Spike Detection (Timechart)

\`\`\`
source="*dns*"
| timechart span=1m count
\`\`\`

![Timechart of DNS logs](Screenshot/Timechart_of_logs.png)
*Spike in DNS query volume during the simulated tunneling session*

### Tunneling Events Summary

![DNS tunneling events](Screenshot/dns-tunneling-events.png)
*Correlated view of DNS tunneling-related events in Splunk*

---

## Final Dashboard

![Splunk dashboard panel](Screenshot/Splunk_dashboard_panel.png)
*Consolidated Splunk dashboard combining all detection panels for monitoring DNS tunneling activity*



