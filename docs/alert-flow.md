# Alert Flow Documentation

## Project 2 – Monitoring & Alert Delivery Pipeline

This document explains the end-to-end alert lifecycle within the monitoring stack.

The architecture follows a pull-based metrics model and an event-driven alerting model.

---

# 1. Metrics Collection Flow

### Step 1 – Metrics Exposure

Each server runs **Node Exporter (port 9100)**:

- Admin Server → 192.168.57.104:9100  
- Web Server → 192.168.57.105:9100  

Node Exporter exposes:
- CPU usage
- Memory usage
- Disk usage
- Load average
- Network statistics

Metrics are available via:
http://<server-ip>:9100/metrics

---

### Step 2 – Prometheus Scraping

Prometheus (running on Admin Server) performs pull-based scraping:

- Scrapes Web Server metrics
- Scrapes Admin Server metrics

Scrape interval: 15s  
Protocol: HTTP  
Network: Host-Only (private)

All metrics traffic remains inside `192.168.57.0/24`.

---

# 2. Alert Evaluation Flow

### Step 3 – Rule Evaluation

Prometheus continuously evaluates alert rules defined in:
/etc/prometheus/alert_rules.yml

Example condition:
- High CPU usage (> 80%)
- Sustained for defined duration (e.g., 1m)

If condition becomes TRUE:
- Alert status → `FIRING`
- Alert object is generated

---

### Step 4 – Alert Dispatch to Alertmanager

Prometheus sends alert payload to:
/etc/prometheus/alert_rules.yml

Example condition:
- High CPU usage (> 80%)
- Sustained for defined duration (e.g., 1m)

If condition becomes TRUE:
- Alert status → `FIRING`
- Alert object is generated

---

### Step 4 – Alert Dispatch to Alertmanager

Prometheus sends alert payload to:
Alertmanager (Port 9093)


Alertmanager responsibilities:
- Group alerts
- Deduplicate alerts
- Apply routing rules
- Handle severity levels (warning / critical)

---

# 3. Alert Routing Flow

### Step 5 – Webhook Forwarding

For critical alerts:

Alertmanager sends HTTP POST request to:
Telegram Webhook Service (Flask App)
Port: 5001
Endpoint: /alert


This occurs inside the private network.

---

### Step 6 – Telegram API Integration

The Flask webhook:

1. Receives JSON alert payload
2. Formats alert message
3. Sends HTTPS request to:
https://api.telegram.org/bot<TOKEN>/sendMessage


This is the only external communication.

Protocol: HTTPS (443)  
Outbound via NAT adapter  
No inbound internet allowed.

---

# 4. Full Alert Lifecycle Summary

1. Metric changes (CPU spike)
2. Prometheus scrapes metric
3. Alert rule condition becomes TRUE
4. Prometheus marks alert as FIRING
5. Alert sent to Alertmanager
6. Alertmanager routes to webhook
7. Webhook sends message to Telegram
8. Telegram delivers alert to chat

---

# 5. Security Controls in Alert Flow

- All metrics scraping is internal only
- Alertmanager not publicly exposed
- Webhook not publicly exposed
- No public IP assigned to VMs
- Only outbound HTTPS allowed
- UFW default deny inbound policy

---

# 6. Architecture Characteristics

| Component    | Communication Type | Direction     |
|--------------|--------------------|---------------|
| Prometheus   | Pull (Scrape)      | Internal      |
| Grafana      | Query (Pull)       | Internal      |
| Alertmanager | Push               | Internal      |
| Telegram API | HTTPS Push         | Outbound Only |

---

# 7. Design Principles Demonstrated

- Pull-based monitoring model
- Event-driven alerting pipeline
- Internal network isolation
- Secure outbound-only integrations
- Centralized monitoring architecture

---

This monitoring pipeline reflects production-style monitoring concepts while operating in an isolated lab environment.