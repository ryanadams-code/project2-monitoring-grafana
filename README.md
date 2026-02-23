![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-E6522C?logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-Dashboard-F46800?logo=grafana&logoColor=white)
![Alertmanager](https://img.shields.io/badge/Alertmanager-Alerts-FF6F00)
![Node Exporter](https://img.shields.io/badge/Node_Exporter-Metrics-2E8B57)
![Telegram](https://img.shields.io/badge/Telegram-Notification-26A5E4?logo=telegram&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-Server-FCC624?logo=linux&logoColor=black)

# Monitoring Infrastructure with Prometheus & Grafana

## Overview

This project implements a production-style monitoring stack in a controlled VirtualBox lab environment.

The stack includes:

- **Prometheus** – Metrics collection & alert evaluation
- **Node Exporter** – System-level metrics exposure
- **Grafana** – Dashboard visualization
- **Alertmanager** – Alert routing & grouping
- **Telegram Bot (Webhook-based)** – Real-time alert notifications

The objective is to simulate how monitoring infrastructure is designed, secured, and operated in real-world environments.

This project was built as part of my preparation for **IT Infrastructure / Cloud / DevOps internship roles**.

---

## Network Architecture

### Environment Design

- **Host-Only Network:** `192.168.57.0/24`
- **Admin Server:** `192.168.57.103`
- **Web Server:** `192.168.57.104`
- **NAT Adapter:** Used only for outbound internet access

No monitoring services are publicly exposed.

All monitoring communication occurs over the private Host-Only network.

---

## Architecture Overview

### Servers

### Admin Server (Monitoring Node)
- Prometheus
- Grafana
- Alertmanager
- Telegram Webhook Service (Python Flask)

### Web Server
- Node Exporter
- Nginx

---

## Monitoring Flow

Node Exporter → Prometheus → Alertmanager → Telegram  
Prometheus → Grafana (Visualization)


### Flow Explanation

1. Node Exporter exposes system metrics.
2. Prometheus scrapes metrics every 15 seconds.
3. Alert rules evaluate thresholds.
4. Alertmanager routes alerts by severity.
5. Telegram webhook service formats and sends notifications.
6. Grafana queries Prometheus for visualization.

Architecture diagrams are available in the `diagrams/` folder.

---

## Components

### Prometheus
- Scrape interval: 15s
- Targets:
  - Admin Server (Node Exporter)
  - Web Server (Node Exporter)
- Evaluates alert rules:
  - High CPU Usage (warning)
  - High CPU Usage (critical)

### Node Exporter
- Installed on both VMs
- Exposes:
  - CPU usage
  - Memory usage
  - Disk metrics
  - Load average
  - System uptime

### Grafana
- Connected to Prometheus as data source
- Custom dashboard built for:
  - CPU usage
  - Memory utilization
  - Instance-level monitoring

### Alertmanager
- Routes alerts by severity
- Grouping & repeat interval configured
- Integrated with Telegram webhook

### Telegram Integration
- Custom Python (Flask) webhook service
- Receives Alertmanager payload
- Sends formatted alert message to Telegram Bot

---

## Security Hardening

- UFW firewall enabled on both VMs
- Port `9100` (Node Exporter) restricted to Admin Server only
- SSH access restricted to specific IP
- Grafana anonymous access disabled
- Default Grafana credentials changed
- All services configured with `systemd` for auto-start
- Monitoring services accessible only via private network

---

## Production Considerations

This lab simulates a production-style monitoring pipeline, but does not yet include:

- High availability
- TLS encryption for Prometheus/Grafana
- Persistent storage replication
- Authentication proxy
- Centralized logging stack

These are listed as future improvements.

---

## Screenshots

Available in the `screenshots/` folder:

- Prometheus Targets (UP state)
- Prometheus Alerts (Firing state)
- Grafana Dashboard
- Alertmanager UI
- Telegram alert message
- UFW firewall rules
- Service status (Prometheus, Grafana, Alertmanager)

---

## Challenges & Troubleshooting

- YAML formatting errors in alert rules
- Prometheus service failure due to rule misconfiguration
- Telegram 403 error caused by incorrect chat ID
- Webhook service not running (connection refused)
- Firewall rules initially blocking Node Exporter

Each issue was diagnosed using logs and corrected systematically.

---

## What I Learned

- Designing monitoring-first infrastructure
- Understanding the full metrics lifecycle
- Writing effective Prometheus alert rules
- Alert routing strategies and grouping logic
- Managing Linux services using systemd
- Securing monitoring endpoints
- Integrating infrastructure alerts with external APIs
- Troubleshooting distributed monitoring components

---

## Future Improvements

- Add email integration via Alertmanager
- Deploy stack using Docker Compose
- Add logging stack (Loki or ELK)
- Deploy monitoring stack to cloud VM
- Add TLS encryption to monitoring endpoints
- Implement basic role-based access for Grafana

---

## Author

Ryan Adam  
Aspiring IT Infrastructure & Cloud Engineer  
GitHub: https://github.com/ryanadams-code