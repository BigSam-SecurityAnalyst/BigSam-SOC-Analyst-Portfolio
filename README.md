# SOC Level 1 – Project 01: Detecting Network Reconnaissance Using Nmap and TShark

## 1. Project Overview

This project simulated internal network reconnaissance in an isolated VirtualBox lab. Kali Linux was used to perform host discovery with Nmap, while Ubuntu Desktop was used to capture and analyse network traffic with TShark.

## 2. Objective

Identify network reconnaissance activity by examining ARP traffic, determining the source IP address, measuring destination diversity, and identifying active hosts.

## 3. Lab Environment

| Machine | IP Address | Role |
|---|---|---|
| Kali Linux | 192.168.56.101 | Scanning machine |
| Metasploitable 2 | 192.168.56.105 | Vulnerable target |
| Ubuntu Desktop | 192.168.56.111 | Packet analysis |
| Ubuntu Server | 192.168.56.112 | Wazuh Manager |
| VirtualBox Network | 192.168.56.0/24 | Isolated lab subnet |

## 4. Tools Used

- Nmap – Network host discovery.
- TShark – Packet capture and traffic analysis.
- VirtualBox – Lab virtualisation.

## 5. Investigation Methodology

### Step 1: Network Discovery

Executed the following command on Kali Linux:

`nmap -sn 192.168.56.0/24`

The scan identified six active hosts across the subnet.

### Step 2: Packet Capture

Captured ARP and ICMP traffic on Ubuntu Desktop:

`sudo tshark -i enp0s3 -f "arp or icmp" -w /tmp/soc-project-01-nmap-recon.pcapng`

### Step 3: Protocol Analysis

Analysed the saved capture using:

`sudo tshark -r /tmp/soc-project-01-nmap-recon.pcapng -q -z io,phs`

Results:
- Total captured frames: 532
- ARP frames: 532
- ICMP frames: 0

### Step 4: Source Identification

Filtered ARP requests originating from Kali:

`sudo tshark -r /tmp/soc-project-01-nmap-recon.pcapng -Y "arp.opcode == 1 && arp.src.proto_ipv4 == 192.168.56.101" | wc -l`

Result: 509 ARP requests.

### Step 5: Destination Analysis

Counted unique destination IP addresses queried by Kali.

Result: 255 unique destination IP addresses.

## 6. Key Findings

- Kali Linux (192.168.56.101) initiated the host-discovery scan.
- 509 ARP requests originated from Kali.
- The requests covered 255 unique destination IP addresses.
- Nmap identified six active hosts.
- Packet analysis confirmed that Ubuntu Desktop could observe the reconnaissance traffic.

## 7. Security Significance

A workstation generating numerous ARP requests across a subnet may indicate network reconnaissance. In a production environment, this behaviour should be investigated to establish whether the activity was authorised.

## 8. Recommendations

- Monitor unusual ARP request volumes.
- Identify the source endpoint generating the traffic.
- Verify whether network discovery activity is authorised.
- Correlate network activity with endpoint and SIEM logs.
- Escalate unexplained reconnaissance activity for further investigation.

## 9. Lessons Learned

This exercise developed practical experience in Nmap host discovery, packet capture, ARP analysis, traffic filtering, and identifying potential reconnaissance behaviour from a SOC analyst perspective.
