---
title: "Simple Homelab Creation"
categories: [System Design, Network]
toc: true
---

# Single-PC Homelab: pfSense Segmentation (Lab LAN + Vulnerable LAN)

## Overview

This project documents a small homelab built on a single Windows 11 PC using VirtualBox.  
The goal is to practice **network segmentation and safe-by-default firewalling** by isolating vulnerable machines from the rest of the lab.

**Key idea:** A dedicated “Vulnerable LAN” contains intentionally vulnerable VMs (e.g., Metasploitable2) and is blocked from reaching the trusted Lab LAN and the internet.

---

## Goals

- Build a homelab using only one physical PC (VirtualBox VMs).
- Create two isolated internal networks:
  - **Lab LAN (trusted/admin)**
  - **Vulnerable LAN (targets only)**
- Enforce simple firewall policy:
  - **ALLOW** Lab LAN → Vulnerable LAN
  - **BLOCK** Vulnerable LAN → Lab LAN
  - **BLOCK** Vulnerable LAN → Internet

---

## Topology

![Homelab Diagram](/assets/img/homelab/homelab_starter.png)

### Networks

- **WAN:** VirtualBox NAT `10.0.2.0/24` (pfSense WAN: `10.0.2.15`)
- **Lab LAN:** `192.168.1.0/24` (pfSense LAN: `192.168.1.1`)
- **Vulnerable LAN:** `192.168.2.0/24` (pfSense OPT1: `192.168.2.1`)

---

## Virtual Machines

| VM              | Purpose                                   | Network                            | Example IP                                                |
| --------------- | ----------------------------------------- | ---------------------------------- | --------------------------------------------------------- |
| pfSense         | Router/firewall, DHCP, policy enforcement | WAN (NAT), Lab LAN, Vulnerable LAN | WAN: `10.0.2.15`, LAN: `192.168.1.1`, OPT1: `192.168.2.1` |
| Ubuntu Server   | Services / general server                 | Lab LAN                            | `192.168.1.100`                                           |
| Ubuntu Desktop  | Admin workstation                         | Lab LAN                            | `192.168.1.101`                                           |
| Kali Linux      | Testing workstation                       | Lab LAN                            | `192.168.1.102`                                           |
| Metasploitable2 | Vulnerable target                         | Vulnerable LAN                     | `192.168.2.100` (DHCP)                                    |

---

## Build Summary (High-Level)

1. Created VirtualBox Internal Networks:
   - `lab-lan` (Lab LAN)
   - `vuln-lan` (Vulnerable LAN)
2. Deployed pfSense with 3 interfaces:
   - WAN attached to VirtualBox NAT
   - LAN attached to `lab-lan`
   - OPT1 attached to `vuln-lan`
3. Configured:
   - Static IPs on LAN/OPT1
   - DHCP on Lab LAN and Vulnerable LAN
4. Implemented firewall policy to isolate targets.

---

## pfSense Configuration

### Interfaces

![Interfaces](/assets/img/homelab/interfaces.png)

### DHCP

![DHCP Labs](/assets/img/homelab/dhcp.png)

### Firewall Rules

**LAN rules (allow Lab → Vuln):**
![LAN Rules](/assets/img/homelab/rules_lan.png)

**Vulnerable LAN rules (block Vuln → Lab and Vuln → Internet):**
![Vuln LAN Rules](/assets/img/homelab/rules_vuln.png)

---

## Future Plans:

- Implementing **DMZ** on the Ubuntu Server
- Allowing VPN access

---

## References:

- [pfSense Installation](https://youtu.be/Y-Dj8lHmXy8)
- [pfSense Rules Configuration](https://youtu.be/Vm98ofYp05g)
