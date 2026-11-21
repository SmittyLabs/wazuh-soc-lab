# SOC Lab: Wazuh Detection & Monitoring Home Lab

A home SOC lab built around **Wazuh** (SIEM/XDR). I attack my own intentionally vulnerable targets, then use Wazuh to detect, investigate, and write up what happened, so I practice both sides: bug bounty / offensive testing and SOC-style detection and reporting.

> **Status:** Lab is up and Wazuh is active. Detection write-ups and incident reports are in progress (see [Reports](#reports)).

---

## Goals

- Run a working SIEM/XDR stack at home and keep it healthy
- Generate real attack telemetry from my own lab, then find it in Wazuh
- Practice triage: alert → investigation → verdict → written report
- Build a public portfolio of clear, repeatable write-ups

## Lab Architecture

```
                    +---------------------------+
                    |      Wazuh Server         |
                    |  Manager + Indexer +      |
                    |  Dashboard                |
                    +-------------+-------------+
                                  ^
                  agent logs / syslog / alerts
                 +----------------+----------------+
                 |                                 |
      +----------+-----------+        +------------+-----------+
      | Ubuntu Server #1     |        | Ubuntu Server #2       |
      | - OWASP Juice Shop   |        | - Metasploitable 2     |
      | - crAPI              |        |                        |
      +----------+-----------+        +------------+-----------+
                 ^                                 ^
                 +---------------+-----------------+
                                 | attack traffic
                         +-------+--------+
                         | Kali Linux     |
                         | (attacker)     |
                         +----------------+
```

All machines sit on an isolated host-only network in VirtualBox. Add IPs/hostnames if you want them shown.

## Components

| Component | Role |
|---|---|
| Wazuh Manager / Indexer / Dashboard | SIEM/XDR: log analysis, alerting, dashboards |
| Ubuntu Server #1 | Hosts the web/API targets |
| OWASP Juice Shop | Vulnerable web app target (on Server #1) |
| crAPI | Vulnerable API target (on Server #1) |
| Ubuntu Server #2 | Hosts Metasploitable 2 |
| Metasploitable 2 | Vulnerable network target |
| Kali Linux | Attacker machine, generates attack traffic |

## Setup

> Check the [official Wazuh docs](https://documentation.wazuh.com) for the current version and steps.

**1. Install Wazuh (all-in-one)**

```bash
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

Save the admin credentials the installer prints at the end.

**2. Deploy agents**

In the Wazuh dashboard go to *Agents → Deploy new agent*, pick the OS, and run the generated command on each monitored host.

**3. Verify**

- Agents show as **Active** in the dashboard
- Run a test action (failed SSH login, file change) and confirm an alert fires

**4. Enable useful modules**

- File Integrity Monitoring (FIM)
- Vulnerability Detection
- Security Configuration Assessment (SCA)
- Log collection from target apps ([web server / app logs])

## What This Lab Detects

**What it does**

- Collects logs and security alerts from the lab machines in Wazuh
- Monitors file integrity, system configuration, and known vulnerabilities
- Records attack traffic from Kali against the vulnerable targets so I can find it in the alerts

**What I'm working toward**

- SSH brute force detection
- Web attack detection (SQL injection and XSS against Juice Shop)
- API abuse detection (crAPI)
- Port scan and recon detection
- File integrity change alerts
- Privilege escalation attempts
- Custom Wazuh rules for the attacks above


## Reports

Every exercise gets a short write-up using the same template:

1. **Summary**: what happened in two or three sentences
2. **Timeline**: key events with timestamps
3. **Detection**: Wazuh rule IDs and alert evidence
4. **Analysis**: what the activity was, and impact
5. **Verdict & Severity**
6. **Recommendations**: fixes and detection improvements

Write-ups are in progress.

## Disclaimer

This lab runs on systems I own, on an isolated network. Only attack targets you own or have explicit written authorization to test. The vulnerable machines here must never be exposed to the internet.

## License

[MIT or your choice]
