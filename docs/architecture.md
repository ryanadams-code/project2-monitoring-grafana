# Architecture Overview

## Project 2 – Monitoring & Alerting Stack

This project implements a self-hosted monitoring and alerting architecture using Prometheus, Grafana, Alertmanager, and a Telegram webhook integration.

The environment is built inside a segmented Host-Only network using VirtualBox, with strict firewall controls and no public IP exposure.

---

## 1. Network Design

### Host-Only Network
- Subnet: `192.168.57.0/24`
- Isolated private network
- No external inbound access
- Internal communication only between VMs

All monitoring traffic remains inside the private network.

---

## 2. Infrastructure Components

### Admin Server (Monitoring Control Plane)
IP: `192.168.57.104`

Runs:
- Prometheus (9090)
- Grafana (3000)
- Alertmanager (9093)
- Node Exporter (9100)
- Telegram Webhook (5001)

Role:
- Centralized monitoring server
- Alert processing & routing
- Secure bastion SSH access

---

### Web Server (Monitored Node)
IP: `192.168.57.105`

Runs:
- NGINX (80)
- Node Exporter (9100)

Role:
- Application server
- Exposes system metrics to Prometheus

---

## 3. Monitoring Architecture

### Metrics Collection
- Node Exporter exposes metrics on port 9100.
- Prometheus scrapes metrics from:
  - Admin Server
  - Web Server

Scraping occurs over the private Host-Only network.

---

### Visualization Layer
- Grafana queries Prometheus via port 9090.
- Dashboards visualize CPU, memory, and system metrics.

Grafana does not receive pushed data.  
It performs pull-based queries.

---

### Alert Processing
- Prometheus evaluates alert rules.
- When a threshold is exceeded:
  - Alert is sent to Alertmanager.
- Alertmanager routes the alert to the Telegram Webhook service.
- The Flask webhook sends an HTTPS request to Telegram Bot API.

---

## 4. Internet Access Model

The environment has:

- No public IP assigned
- No inbound internet access
- Outbound-only internet via NAT adapter

Internet access is used exclusively for:
- Telegram API (HTTPS 443)
- System updates (apt)

This design prevents exposure of monitoring services to the public internet.

---

## 5. Security Architecture Summary

- Host-only network segmentation
- UFW firewall enabled on both VMs
- Default deny incoming policy
- SSH restricted to Admin IP
- Node Exporter restricted to internal access only
- No public ports opened
- Outbound HTTPS only for alert delivery

---

## Architecture Principles Applied

- Isolation-first design
- Centralized monitoring
- Least privilege access control
- Minimal external exposure
- Controlled outbound communication

---

This architecture simulates a production-style private monitoring environment and demonstrates foundational infrastructure engineering practices.