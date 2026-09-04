# Lab Environment

This page documents the virtual machines and tools used in my SOC home lab.

## Virtualization

The lab environment is built using VirtualBox. Each machine is used for a specific role in the security monitoring and testing workflow.

## Virtual Machines

### Kali Linux

Role: Security testing and attacker simulation.

Kali Linux is used to generate controlled security activity against the monitored Windows endpoint.

### Windows 11

Role: Monitored endpoint.

The Windows 11 machine runs the Wazuh Agent and generates system and security events that can be monitored from the Wazuh server.

### Ubuntu

Role: SOC monitoring server.

Ubuntu hosts the Wazuh server used to collect and analyze events from the monitored endpoint.

## Tools

- VirtualBox
- Kali Linux
- Windows 11
- Ubuntu
- Wazuh

## Lab Workflow

The basic workflow is:

1. Kali Linux generates controlled security activity.
2. Windows 11 receives the activity and generates relevant events.
3. The Wazuh Agent collects the events.
4. The Wazuh server receives and analyzes the events.
5. Alerts are investigated and documented.

## Purpose

The purpose of this environment is to gain practical experience with security monitoring, log analysis, detection, and alert investigation in a controlled lab.
