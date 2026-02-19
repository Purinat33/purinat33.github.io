---
title: "Simple Homelab Creation Part 3"
categories: [System Design, Network]
toc: true
---

# Single-PC Homelab: OpenVPN Server for Host to Lab Access

This document explains how we set up **OpenVPN Remote Access** on pfSense so the **Windows host PC** can connect into the lab and reach the pfSense GUI at **`192.168.1.1`**.

- [Previous Part](https://purinat33.github.io/posts/Simple-Homelab-2/)

## Topology

![topology](/assets/img/homelab/vpn_archi.png)

> Key outcome: The host PC connects via OpenVPN and receives a VPN IP in **`10.4.0.0/24`**, then can browse to **`https://192.168.1.1`** (pfSense web UI).

## Why OpenVPN in this Homelab?

Our lab networks (Lab LAN / DMZ / Vulnerable LAN) live behind VirtualBox **Internal Networks**, so the host PC normally cannot directly “join” those networks.

OpenVPN gives us a clean “remote access” path:

- Host PC → VPN tunnel → pfSense → Lab LAN resources (including pfSense GUI)

## Implementation Summary (High-Level)

![VPN_2](/assets/img/homelab/vpn_2.png)

### 1) OpenVPN Server (Wizard)

We used **OpenVPN Remote Access Server Setup** with:

- **Authentication backend**: `Local User Access`
- **Server mode**: Remote Access (SSL/TLS + User Auth)
- **Protocol/Port**: `UDP / 1194`
- **Tunnel Network**: `10.4.0.0/24`
- **Interface**: `WAN`

Along with a Certificate Authority

![VPN_3](/assets/img/homelab/vpn_3.png)

### 2) Create a Non-Admin VPN User + User Certificate

Created a dedicated user (e.g., `test`) and issued a user certificate for that account.

![VPN_5](/assets/img/homelab/vpn_5.png)

### 3) Firewall Rules

- OpenVPN-related rules under **Firewall → Rules → OpenVPN**
- WAN listening on UDP/1194 (pfSense-side)

![Port](/assets/img/homelab/vpn_6.png)

### 4) Configure Port Forwarding (VirtualBox)

Because pfSense WAN sits behind **VirtualBox NAT**, the host PC can’t directly connect to `10.0.2.15:1194` as a normal routed address from outside the VM network.

So we used **VirtualBox NAT Port Forwarding** to expose the VPN service to the host.

In our current setup, the VirtualBox NAT port forward is effectively exposed to the **host itself**.

That means the OpenVPN client on the host connects to:

- **`127.0.0.1:1194`** (the host loopback), which VirtualBox forwards into:
- **pfSense WAN (`10.0.2.15:1194`)**

So the VPN “server public IP” shows as `127.0.0.1` because:

- the host is dialing its **own** forwarded port (loopback)
- VirtualBox then relays it to the pfSense VM

---

## Testing

![Port](/assets/img/homelab/vpn_10.png)

![Port](/assets/img/homelab/vpn_11.png)

The Host PC successfully received the VPN client's IP and is able to ping and access pfSense GUI.

--

## References:

- [pfSense OpenVPN Setup](https://docs.netgate.com/pfsense/en/latest/recipes/openvpn-ra.html)
