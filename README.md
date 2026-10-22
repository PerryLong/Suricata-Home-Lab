# Suricata Home Lab 

# Building an Active Defense & Intrusion Detection Lab From Scratch

Hey whoever's reading this, I’m a senior IT undergraduate at Florida State  University working toward breaking into cybersecurity. 

I wanted to do more than just read about defense but rather and build a home lab in VirtualBox to see how attacks actually look and how they are detected. This project is an automated detection and active defense system that inspects live traffic using **Suricata IDS**, detects rapid TCP SYN port scans , and automatically blocks the attacker in the Linux kernel using **iptables**.

I am still learning how to write code in Python so I used AI to help create the logic for the detection engine, and then had it rewrite in Go to learn more about programming on the systems level.

---
## Lab Architecture & Network Setup

To make sure no attack traffic ever leaked onto my home Wifi or router, I isolated both VMs inside VirtualBox using an Internal Network (`cyber-lab`) with manual static IPs:

- Attacker VM: Kali Linux (192.168.100.20) 
- Defender VM: Ubuntu Server 24.04 (192.168.100.10)
  
## What It Does & How It Works

1. **Reconnaissance Simulation:** An attacker VM (Kali Linux) launches a fast TCP SYN scan (`nmap -sS`) against an isolated target server.
2. **Packet Inspection & Telemetry:** Suricata runs on the defender VM (Ubuntu Server) and writes parsed network flow events directly to `/var/log/suricata/eve.json`.
3. **Threshold Detection:** A custom script tails `eve.json` in real time. It keeps a sliding window tracker of how many distinct destination ports a single IP attempts to reach within a short time frame.
4. **Automated Kernel Mitigation:** Once an IP exceeds the threshold (15 unique ports within 10 seconds), the script automatically executes an `iptables` rule inserting a `DROP` command for that source IP, immediately stopping the scan.

## My Steps
1. Created and configured Attacker and Defender Virtual Machines(VMs).
2. Troubleshooted and set the static Ip of both VMs on an internal network.
3. Used Python Detection Engine.
4. Through the Attacker VM, I launched a TCP SYN scan on the IP of the Defender VM.
5. Once the detection was successful, I updated the Detection Engine with automated firewall blocking.
6. Then I ran a continuous ping and TCP SYN scan from the Attacker VM onto the Defender VM.
7. The Defender VM successfully stopped all traffic coming in and banned the Attacker VM while resetting the firewall rule.
8. Rewrote the Python Detection Engine in Go and ran a TCP SYN scan once again and it was successful. 
