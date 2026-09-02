# SOC Home Lab

This repository documents my personal Security Operations Center (SOC) home lab and the practical cybersecurity exercises I perform in it.

The main purpose of this lab is to understand how security monitoring works in a real environment, including collecting logs, detecting suspicious activity, investigating alerts, and understanding the events generated on an endpoint.

## Lab Environment

The lab is built using VirtualBox and currently consists of three virtual machines:

| Machine |             Role                    | IP Address |
|---                   |---|---|
| Kali Linux | Security testing / attacker machine | 192.168.56.101 |
| Windows 11 | Monitored endpoint | 192.168.56.102 |
| Ubuntu | Wazuh server / SOC monitoring | 192.168.56.20 |

## Tools and Technologies

- Wazuh
- Kali Linux
- Windows 11
- Ubuntu
- VirtualBox
- Nmap
- Windows Event Logs
- Network and security monitoring

## Lab Architecture

The basic workflow of the lab is:

Kali Linux
→ Security Testing
→ Windows 11
→ Wazuh Agent
→ Ubuntu / Wazuh Server
→ Security Events and Alerts
→ Investigation

## What I Practice

The lab is used to practice:

- Security monitoring and hands on experience
- Log collection and analysis
- Windows event investigation
- Network scanning and detection
- Alert analysis
- Basic incident investigation
- Understanding attacker activity from a SOC perspective
- Mapping security activity to relevant MITRE ATT&CK techniques

## Documentation

As I continue working on the lab, I will document the setup, experiments, alerts, investigations, and lessons learned.

### Planned Sections

- Lab setup
- Wazuh configuration
- Windows endpoint monitoring
- Network scanning exercises
- Detection and alert investigation
- Security event analysis
- Incident investigation
- Lessons learned

## Goal

The goal of this project is to build practical SOC skills through hands-on lab work and to understand the complete process from generating an event to detecting, investigating, and responding to it.
