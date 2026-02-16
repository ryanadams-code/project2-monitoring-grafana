![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-E6522C?logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-Dashboard-F46800?logo=grafana&logoColor=white)
![Alertmanager](https://img.shields.io/badge/Alertmanager-Alerts-FF6F00)
![Node Exporter](https://img.shields.io/badge/Node_Exporter-Metrics-2E8B57)
![Telegram](https://img.shields.io/badge/Telegram-Notification-26A5E4?logo=telegram&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-Server-FCC624?logo=linux&logoColor=black)

# Monitoring & Alerting Infrastructure (Prometheus + Grafana)

## Overview
This project demonstrates a basic monitoring and alerting stack built on a Linux-based infrastructure.

The objective was to implement real-time system monitoring, visualization, and alert notification using open-source tools, while applying secure networking practices.

This project builds on top of the Secure Infrastructure Project and focuses on observability and reliability concepts relevant to IT Infrastructure and Cloud Engineering roles.

---

## Goals
- Install and configure Prometheus
- Collect metrics using Node Exporter
- Visualize system metrics in Grafana
- Configure alert rules
- Send alerts to Telegram
- Apply firewall restrictions
- Implement service auto-start using systemd

---

## Architecture

### Infrastructure Setup
- 2 Ubuntu Server VMs
  - Admin Server (Monitoring Stack)
  - Web Server (Monitored Node)

### Monitoring Stack (Admin Server)
- Prometheus
- Alertmanager
- Grafana
- Telegram Webhook Service (Python + Flask)

### Metrics Collection
- Node Exporter installed on:
  - Admin Server
  - Web Server

---

## Monitoring Flow

1. Node Exporter exposes metrics on port `9100`
2. Prometheus scrapes metrics every 15 seconds
3. Alert rules evaluate system conditions (e.g., high CPU usage)
4. Alertmanager processes alerts
5. Alerts are:
   - Visible in Alertmanager UI
   - Sent to Telegram via webhook

---

## Security Configuration

### Firewall (UFW)

Web Server:
- Allow SSH only from Admin Server
- Allow port 9100 only from Admin Server
- Allow HTTP (80)
- Deny other inbound traffic

Admin Server:
- Allow 9090 (Prometheus)
- Allow 3000 (Grafana)
- Allow internal monitoring traffic
- Restrict unnecessary exposure

---

## Services Configured

- Prometheus (systemd service)
- Node Exporter (systemd service)
- Grafana (systemd service)
- Alertmanager (systemd service)
- Telegram Webhook (Python virtual environment)

All services configured to auto-start on boot.

---

## Alert Rules Implemented

### High CPU Usage (Critical)
Triggered when:
- CPU usage > defined threshold
- For a sustained period

Alert includes:
- alertname
- instance
- severity
- summary
- description

---

## Telegram Integration

A lightweight webhook service was implemented using:

- Python
- Flask
- Requests library
- Virtual environment (venv)

Alertmanager sends alerts to:
http://127.0.0.1:8080/alert

The webhook service forwards alerts to Telegram Bot API.

---


## Repository Structure

project-2-monitoring/
├── diagrams/
├── screenshots/
├── docs/
│ ├── prometheus-config.md
│ ├── alert-rules.md
│ ├── alertmanager-config.md
│ └── troubleshooting.md
└── README.md


---

## Screenshots Included

- Prometheus targets page
- Node Exporter status
- Grafana dashboard
- Alert firing in Prometheus
- Alert visible in Alertmanager
- Telegram notification
- UFW configuration
- Service status (systemctl)

---

## Challenges & Troubleshooting

- Incorrect Prometheus config path caused rule loading failure
- YAML indentation issues in alert rules
- Webhook connection refused (service not running)
- Telegram 403 error due to incorrect chat_id
- Firewall initially blocking port 9100

Each issue was diagnosed using logs and corrected.

---

## Key Learning Outcomes

- Infrastructure monitoring fundamentals
- Metric scraping architecture
- Alert lifecycle understanding
- Service management using systemd
- Secure metric exposure
- Webhook-based alert forwarding
- Debugging production-like failures

---

## Future Improvements

- TLS for Prometheus & Grafana
- Role-based access control in Grafana
- External Alertmanager clustering
- Centralized logging integration
- Cloud deployment simulation

---

## Author
Ryan Adam  
Aspiring IT Infrastructure & Cloud Engineer  
GitHub: https://github.com/ryanadams-code
