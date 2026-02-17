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
