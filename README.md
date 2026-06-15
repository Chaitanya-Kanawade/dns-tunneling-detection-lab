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





