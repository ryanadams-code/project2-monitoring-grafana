![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-E6522C?logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-Dashboard-F46800?logo=grafana&logoColor=white)
![Alertmanager](https://img.shields.io/badge/Alertmanager-Alerts-FF6F00)
![Node Exporter](https://img.shields.io/badge/Node_Exporter-Metrics-2E8B57)
![Telegram](https://img.shields.io/badge/Telegram-Notification-26A5E4?logo=telegram&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-Server-FCC624?logo=linux&logoColor=black)

# Monitoring Infrastructure with Prometheus & Grafana

## Overview

This project implements a complete monitoring stack using:

- Prometheus (metrics collection)
- Node Exporter (system metrics)
- Grafana (dashboard visualization)
- Alertmanager (alert routing)
- Telegram Bot (real-time notifications)

The goal of this project is to simulate a production-style monitoring environment for Linux servers in a controlled VirtualBox lab environment.

This project was built as part of my preparation for IT Infrastructure / Cloud / DevOps internship roles.

---

## Architecture

The monitoring architecture consists of:

- 1 Admin Server (Monitoring Server)
  - Prometheus
  - Grafana
  - Alertmanager
  - Telegram Webhook Service

- 1 Web Server
  - Node Exporter
  - Nginx

Metrics flow:

Node Exporter → Prometheus → Alertmanager → Telegram  
Prometheus → Grafana (Visualization)

Architecture diagrams are available in the `diagrams/` folder.

---

## Components

### Prometheus
- Scrapes metrics every 15 seconds
- Collects metrics from:
  - Admin Server
  - Web Server
- Evaluates alert rules

### Node Exporter
- Runs on both VMs
- Exposes:
  - CPU usage
  - Memory usage
  - Disk metrics
  - System load

### Grafana
- Connected to Prometheus as a data source
- Custom dashboard created for:
  - CPU usage
  - Memory usage
  - Instance monitoring

### Alertmanager
- Routes alerts based on severity
- Separates:
  - warning
  - critical
- Sends alerts to Telegram via webhook service

### Telegram Integration
- Custom webhook service built using Python (Flask)
- Receives Alertmanager payload
- Sends formatted message to Telegram Bot

---

## Implementation Details

### 1. Prometheus Configuration

- Configured `scrape_configs` for:
  - localhost (Prometheus)
  - Admin Server (Node Exporter)
  - Web Server (Node Exporter)

- Alert rules created:
  - High CPU Usage (warning)
  - High CPU Usage Critical

### 2. Alert Rules Example

Alerts trigger when CPU usage exceeds defined thresholds for a sustained period.

Severity labels:
- warning
- critical

### 3. Alert Flow

1. Prometheus detects threshold breach.
2. Alert is marked as firing.
3. Alertmanager receives alert.
4. Alert is routed based on severity.
5. Webhook service processes alert.
6. Telegram bot sends real-time notification.

---

## Security Hardening

- UFW firewall enabled on both VMs
- Port 9100 (Node Exporter) restricted to Admin Server only
- SSH access restricted
- Grafana anonymous access disabled
- Default Grafana credentials changed
- Services configured with systemd for auto-start

---

## Screenshots

Available in the `screenshots/` folder:

- Prometheus targets page
- Prometheus alerts (firing state)
- Grafana dashboard
- Alertmanager UI
- Telegram alert message
- Firewall rules
- Service status (Prometheus, Grafana, Alertmanager)

---

## Challenges & Troubleshooting

- YAML formatting issues in alert rules
- Prometheus service failed due to rule misconfiguration
- Telegram bot returned 403 due to incorrect chat ID
- Webhook service initially not running (connection refused)
- Firewall rules blocking Node Exporter

Each issue was diagnosed using logs and corrected accordingly.

---

## What I Learned

- Designing monitoring-first infrastructure
- Understanding metrics lifecycle
- Writing Prometheus alert rules
- Alert routing strategies
- Production-style service management with systemd
- Securing monitoring endpoints
- Integrating infrastructure alerts with external messaging platforms

---

## Future Improvements

- Add Alertmanager email integration
- Deploy stack using Docker Compose
- Add logging stack (Loki or ELK)
- Deploy monitoring on cloud VM
- Add SSL/TLS for Grafana and Prometheus

---

## Author

Ryan Adam  
Aspiring IT Infrastructure & Cloud Engineer  
GitHub: https://github.com/ryanadams-code
