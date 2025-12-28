---
title: "Distributed SIEM with Wazuh (Multi-Site Deployment)"
categories: [Cybersecurity, SIEM]
pin: True
---

# Distributed SIEM with Wazuh: Multi-Site Monitoring with Local Autonomy

**Graduation (Senior) Project — B.Sc. ICT, Mahidol University (2024)**  
This project builds a **distributed, multi-site SIEM** using **Wazuh** to support organizations operating across multiple geographical locations. Each site runs its own Wazuh stack (server + indexer + agents) for **local log collection and processing**, while a **central dashboard** provides a unified view across all sites.

## At a glance

- **What we built:** A **multi-site Wazuh architecture** where each site is independent, but security visibility is centralized through one dashboard.
- **Why it matters:** Traditional centralized SIEM can cause **network congestion**, high resource usage, and operational complexity when sites are geographically separated. This design keeps traffic local and still provides global monitoring.
- **How we deployed:** Automated installation and configuration using **Ansible + Jinja2 templates** (repeatable, scalable site onboarding).

## Problem

Centralized SIEM deployments can become inefficient for multi-site organizations: logs must travel across the network to a single point, increasing congestion and making operations harder to manage at scale. Our goal was to design a SIEM that supports **site-level independence** while still allowing a security team to monitor everything from one place.

## Solution

We deployed **independent Wazuh SIEM clusters per site**, then connected them to a **unified Wazuh Dashboard**:

- **Local collection + local processing:** agents connect to their nearest site server
- **Central visibility:** one dashboard aggregates alerts and site health via the Wazuh RESTful API
- **Data redundancy:** indexed data is replicated across sites (resilience and recovery)
- **Site identity:** each site uses distinct index patterns to label alert origin cleanly

## System architecture

Each “site” contains:

- **Wazuh Server** (analysis + management)
- **Wazuh Indexer** (storage + search)
- **Wazuh Agents** (endpoint monitoring)

Additionally:

- A **central Dashboard** connects to all sites for monitoring.
- A **router / network segmentation** simulates geographic separation (virtual implementation).
- A **load balancer (NGINX)** can be added per site to distribute agents across multiple server nodes when scaling.

## Automation (what made it scalable)

To avoid manually configuring every node, we used:

- **Ansible** for remote installation/configuration
- **Jinja2 templates** to generate per-host configs (e.g., dashboard config that lists indexers, indexer cluster configs listing peer nodes, Filebeat configs per site)

This allowed us to expand the system by:

1. adding hosts to the inventory using a naming convention
2. re-running playbooks to install new components and update existing nodes consistently

## Validation and experiments

We tested the design in both a **virtualized environment** (VMs + subnets) and a **physical environment** (separate machines + VLANs).

Key experiments included:

### Event isolation testing

Confirmed that alerts from Site A do **not** appear in Site B views (site separation works).

### Site isolation testing

Brought Site B components offline and verified:

- Site A remains operational and visible
- Site B becomes unavailable as expected (no cross-site dependency for operations)

### Scalability testing

- Added extra server nodes in a site and used a load balancer to distribute agent connections.
- Added new indexers and verified cluster behavior.
- Added an entirely new site and confirmed it appears in the dashboard without disrupting existing sites.

### Offline server alert behavior

Simulated an attack while servers were offline and verified that once servers returned, agent logs were processed and alerts appeared (visibility is delayed, but data isn’t lost).

## Results

What the final implementation demonstrated:

- **Centralized dashboard visibility** across sites
- **Reduced network congestion** by keeping agent-to-server traffic local
- **Independent operations per site** (fault containment)
- **Repeatable deployment** using automation
- **Low cost** by relying on open-source tooling (Wazuh + Linux + automation)

## Limitations

- **No automatic master failover:** if a site’s master server used by the dashboard API goes down, that site becomes temporarily unavailable in the dashboard view.
- **New site onboarding still needs inventory edits:** automation helps a lot, but adding entirely new sites still requires manual inventory updates and reruns.
- **Some performance metrics not measured:** ingestion time, bandwidth utilization, and reliability metrics were not fully quantified in evaluation.

## What I learned

- How SIEM pipelines work end-to-end (agent → server analysis → indexer → dashboard/API)
- Designing for **fault isolation** and **scalability** in distributed systems
- Turning a “one-off setup” into a **repeatable deployment** using automation (Ansible + templates)
- Practical trade-offs: availability, operational complexity, and where “single points of failure” still exist

## Repository:

- [Repo](https://github.com/Purinat33/ICTSP-18-Ansible)

## Example Screenshots:

1. Architecture

![Architecture](/assets/img/senior/architecture.png)

2. Dashboard showing multiple Site connection

![Dashboard](/assets/img/senior/dashboard.png)
