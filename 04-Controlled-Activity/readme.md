# 04 - Controlled Activity

In this part of the lab, I started generating some controlled activity on the Windows 11 machine from my Kali machine.

The main purpose was to create normal test activity and then check whether it could be seen from the SOC side.

## Lab Machines

- Kali Linux: 192.168.56.101
- Windows 11 endpoint: 192.168.56.102
- Wazuh server: 192.168.56.20

## Objective

The goal of this section is to generate a few controlled activities on the Windows endpoint and observe the logs and alerts produced by them.

I will document the commands I used, what happened on the Windows machine, and what I could see from the SOC/Wazuh side.

---

## 1. Controlled Network Activity

I started with a simple network activity from the Kali machine against the Windows endpoint.

### Command

```bash
ping -c 4 192.168.56.102



 Observation
The purpose of this was just to generate some basic network traffic between the two lab machines.
I then checked the SOC side to see if there was any related activity or log available.
Screenshot
The screenshot for this activity is saved in the screenshots folder.
