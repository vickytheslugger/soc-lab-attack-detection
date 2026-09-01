# SOC Lab — Network Architecture & Configuration

## 1. What this project is

This is a home-built Security Operations Center (SOC) lab. It simulates a small company network with a firewall, a monitored target zone, and a separated attacker zone. The goal is to generate attacks from the Kali machine, watch how they appear as alerts in Wazuh, and use that to practice SOC Analyst (Tier 1) skills: monitoring, detection, and investigation.

## 2. Machines used

1. **pfSense** — firewall/router. Splits the lab into separate network zones and controls traffic between them.
2. **Ubuntu Server** — runs Wazuh Manager + Indexer + Dashboard and acts as the SIEM server.
3. **Windows 10** — target machine monitored using a Wazuh Agent + Sysmon.
4. **Kali Linux** — attacker machine used to simulate attacks against the Windows target.

## 3. Network zones

| Zone | Purpose | Subnet | VMs |
|---|---|---|---|
| WAN | Internet access for updates/downloads | `10.0.2.0/24` | pfSense |
| LAN | Protected target/server zone | `192.168.10.0/24` | Ubuntu Server, Windows 10 |
| OPT1 / DMZ | Isolated attacker zone | `192.168.20.0/24` | Kali Linux |

## 4. Topology

```text
Internet
   |
   v
[ WAN — 10.0.2.0/24 ]
   |
[ pfSense Firewall/Router ]
   |-------------------------------|
   v                               v
[ LAN — 192.168.10.0/24 ]     [ OPT1 / DMZ — 192.168.20.0/24 ]
  • Ubuntu Server                 • Kali Linux (Attacker)
    192.168.10.10                  192.168.20.30
  • Windows 10 (Target)
    192.168.10.20
```

pfSense sits in the middle of the relevant paths. Kali can reach the LAN for the attack simulations through a specific OPT1 firewall rule.

## 5. IP address table

| Device | Role | Zone | IP |
|---|---|---|---|
| pfSense | Firewall / Router | WAN | `10.0.2.15/24` (DHCP) |
| pfSense | Firewall / Router | LAN | `192.168.10.1/24` |
| pfSense | Firewall / Router | OPT1 | `192.168.20.1/24` |
| Ubuntu Server | Wazuh Manager / Indexer / Dashboard | LAN | `192.168.10.10/24` |
| Windows 10 | Target / monitored endpoint | LAN | `192.168.10.20/24` |
| Kali Linux | Attacker | OPT1 | `192.168.20.30/24` |

## 6. pfSense rule

The lab documentation records an OPT1 rule that allows the attacker network to reach the required destination for attack simulations, with logging enabled.

| Interface | Action | Source | Destination | Logging |
|---|---|---|---|---|
| OPT1 | Pass | OPT1 net | Any (LAN + Internet) | Enabled |

## 7. Security data flow

1. Windows 10 generates Windows and Sysmon events.
2. The Wazuh Agent forwards security data to the Wazuh Manager on Ubuntu Server.
3. Wazuh Manager analyzes the raw logs and raises alerts using detection rules.
4. Filebeat ships the alerts to the Wazuh Indexer.
5. Wazuh Dashboard reads from the Indexer and presents the events and alerts visually.

## 8. Sysmon

Sysmon adds detailed endpoint telemetry beyond the basic Windows event logs. The documented setup uses the `Microsoft-Windows-Sysmon/Operational` event channel and forwards it through the Wazuh Agent.

## 9. SOC relevance

This lab demonstrates:

- Network segmentation
- Centralized security logging
- Endpoint visibility
- Firewall logging
- Security alert monitoring and investigation

It is intended as the foundation for future attack simulations, custom detection rules, and automated response.
