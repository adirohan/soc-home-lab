# SOC Home Lab Architecture

This page documents the basic architecture of my SOC home lab.

## Lab Components

| Machine | Role | IP Address |
|---|---|---|
| Kali Linux | Security testing / attacker machine | 192.168.56.101 |
| Windows 11 | Monitored endpoint | 192.168.56.102 |
| Ubuntu | Wazuh server / SOC monitoring | 192.168.56.20 |

## Network Flow

```mermaid
flowchart LR
    K["Kali Linux<br/>192.168.56.101<br/>Security Testing"]
    W["Windows 11<br/>192.168.56.102<br/>Wazuh Agent"]
    U["Ubuntu<br/>192.168.56.20<br/>Wazuh Server"]

    K -->|Security Testing| W
    W -->|Logs and Events| U
