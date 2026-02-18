---
title: "Simple Homelab Creation Part 2"
categories: [System Design, Network]
toc: true
---

# Single-PC Homelab: Implementing DMZ

This document describes how we added a **DMZ network** to our single-PC VirtualBox homelab to separate “public-facing services” from the trusted Lab LAN and the Vulnerable LAN.

- [Previous Part](https://purinat33.github.io/posts/Simple-Homelab-1/)

---

## Why a DMZ?

We wanted a place to run services (e.g., a web server) with **tighter containment**:

- The DMZ should not be able to pivot into:
  - **Lab LAN** (trusted admin/workstations)
  - **Vulnerable LAN** (intentional targets)
- Admin access into the DMZ should be controlled and limited (e.g., SSH from Lab LAN).

---

## Topology

![Topology](/assets/img/homelab/dmz_topology.png)

### Networks

- **WAN:** VirtualBox NAT `10.0.2.0/24`
- **Lab LAN:** `192.168.1.0/24` (pfSense: `192.168.1.1`)
- **Vulnerable LAN:** `192.168.2.0/24` (pfSense: `192.168.2.1`)
- **DMZ:** `192.168.5.0/24` (pfSense: `192.168.5.1`)

### Hosts

- **Ubuntu Desktop (Lab LAN):** `192.168.1.101`
- **Ubuntu Server (DMZ):** `192.168.5.100`

---

## Implementation Steps (High-Level)

### 1. Add DMZ network interface in pfSense

![Interface](/assets/img/homelab/pre_dmz_1.png)

### 2. Configure DMZ IPv4 + DHCP

![DHCP](/assets/img/homelab/pre_dmz_4.png)

---

## Firewall Policy

### LAN Policy:

- **Allowed**:
  - Lab LAN → Vulnerable LAN
  - Lab LAN → DMZ (SSH)
  - WAN → DMZ (Port Forwarding) (HTTP)

- **Blocked**:
  - Vulnerable LAN → Any
  - DMZ → Vulnerable LAN
  - DMZ → Lab LAN
  - DMZ → WAN

![LAN](/assets/img/homelab/dmz_rule_lan.png)

![DDAMZP](/assets/img/homelab/dmz_rule_dmz.png)

---

## Port Forwarding Configuration

### pfSense

![pfSensePF](/assets/img/homelab/port_forward.png)

### VirtualBox

![VBox](/assets/img/homelab/port_forward_VBOX.png)

---

## Validation

### Server being on the DMZ instead of Lab LAN

![VBox](/assets/img/homelab/new_server_ip.png)

### Server hosting a HTTP server on port 80

![VBox](/assets/img/homelab/http_server.png)

### Ubuntu Desktop reaching the HTTP server

![VBox](/assets/img/homelab/from_lan.png)

### Host PC reaching the HTTP server (Port Forwarding)

![VBox](/assets/img/homelab/http_windows.png)


---

## Summary

With the implementation of DMZ, the public-facing server now lives on a different network (DMZ) than the internal, private network (Lab LAN).


---

## Reference:

* [pfSense Port Forwarding](https://docs.netgate.com/pfsense/en/latest/nat/port-forwards.html)
* [VirtualBox Port Forwarding](https://nsrc.org/workshops/2014/btnog/raw-attachment/wiki/Track2Agenda/ex-virtualbox-portforward-ssh.htm)


