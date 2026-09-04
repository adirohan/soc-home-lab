# Reconnaissance

This is the reconnaissance phase of my SOC home lab. I used Kali Linux to perform some basic network scanning against my Windows 11 SOC endpoint and then checked how the activity was visible from the Wazuh side.

## Lab Target

- Attacker/Test Machine: Kali Linux
- Kali IP: `192.168.56.101`
- Target: Windows 11 SOC-WIN01
- Target IP: `192.168.56.102`
- Wazuh Server: Ubuntu `192.168.56.20`

## Tools Used

- Nmap
- Kali Linux
- Windows 11
- Wazuh

---

## 1. Host Discovery

I first wanted to see which machines were active on my lab network.

### Command

```bash
nmap -sn 192.168.56.0/24
Output

The scan showed these active hosts:

192.168.56.1
192.168.56.20
192.168.56.100
192.168.56.101
192.168.56.102

The Windows machine 192.168.56.102 was detected as up.

This confirmed that the Windows endpoint was reachable from my Kali machine.

2. NULL Scan

I also tried a NULL scan to see how the Windows endpoint would respond to TCP packets without any TCP flags.

Command
nmap -sN 192.168.56.0/24

For 192.168.56.102, Nmap reported:

All 1000 scanned ports on 192.168.56.102 are in ignored states.
Not shown: 1000 open|filtered tcp ports (no-response)

The important part here was the no-response result. The scan was not getting the normal responses needed to clearly determine the port state.

3. SYN Scan

Next, I performed a SYN scan directly against the Windows machine.

Command
nmap -sS 192.168.56.102
Result
All 1000 scanned ports on 192.168.56.102 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)

All of the scanned ports were shown as filtered.

This was interesting because the Windows machine itself was reachable, but the TCP ports were not responding to the scan. This suggested that something on the endpoint was filtering the traffic.

4. Checking Common Windows Ports

Instead of scanning only the default 1000 ports, I checked some ports that are commonly associated with Windows services.

Command
nmap -sS -Pn --reason -p 135,139,445,3389,5985,5986,80,443 192.168.56.102
Result
PORT     STATE
80/tcp   filtered
135/tcp  filtered
139/tcp  filtered
443/tcp  filtered
445/tcp  filtered
3389/tcp filtered
5985/tcp filtered
5986/tcp filtered

The ports checked were:

Port	Service
80	HTTP
135	MSRPC
139	NetBIOS
443	HTTPS
445	SMB
3389	RDP
5985	WinRM
5986	WinRM HTTPS

All of them were filtered and Nmap reported no-response.

So, from the Kali side, these services were not directly exposed for enumeration.

5. Service Detection

I then tried Nmap's service detection to see if it could identify the services or their versions.

Command
nmap -sV -Pn --reason -p 135,139,445,3389,5985,5986,80,443 192.168.56.102
Result
PORT     STATE    SERVICE
80/tcp   filtered http
135/tcp  filtered msrpc
139/tcp  filtered netbios-ssn
443/tcp  filtered https
445/tcp  filtered microsoft-ds
3389/tcp filtered ms-wbt-server
5985/tcp filtered wsman
5986/tcp filtered wsmans

Nmap was able to associate the port numbers with their usual service names, but it could not identify service versions because the ports were filtered.

What I Found

The main thing I found during this scan was that the Windows endpoint was reachable, but its TCP ports were not responding normally to the reconnaissance traffic.

The common Windows ports I checked were all reported as filtered.

This also gave me something to investigate from the SOC side. After generating the scanning activity from Kali, I checked Wazuh and found firewall drop events from the Windows endpoint.

Wazuh generated:

Rule ID: 4151

Description: Multiple Firewall drop events from same source.

This was useful because it connected the two sides of the lab:

Kali Linux
192.168.56.101
      |
      | Nmap scanning
      v
Windows 11
192.168.56.102
      |
      | Firewall drops traffic
      v
Wazuh
      |
      v
Rule 4151
Multiple Firewall drop events
from same source
Takeaway

This was my first practical reconnaissance exercise in the lab.

I was able to discover the machines on the network, identify the Windows endpoint, check common Windows ports and see how the endpoint's firewall affected the scan results.

More importantly, I could see the same activity from the SOC side in Wazuh instead of only looking at the Nmap output.
