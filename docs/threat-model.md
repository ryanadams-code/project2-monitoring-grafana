# Threat Model

## Project 2 – Monitoring & Alerting Infrastructure

This document identifies potential threats against the monitoring environment and the mitigation strategies implemented.

---

# 1. Asset Identification

Critical assets:

- Prometheus metrics data
- Alert rules and configurations
- Grafana dashboards
- Telegram bot credentials
- SSH access credentials

---

# 2. Threat Categories

This model uses a simplified STRIDE-style approach.

---

# 2.1 Unauthorized Access

Threat:
Unauthorized SSH access to Admin or Web server.

Mitigation:
- SSH restricted by IP
- Root login disabled
- UFW firewall enabled
- No public IP exposure

---

# 2.2 Service Exposure

Threat:
Prometheus, Grafana, or Node Exporter exposed publicly.

Mitigation:
- Host-Only private network
- No inbound internet access
- Firewall rules explicitly scoped

---

# 2.3 Data Tampering

Threat:
Modification of alert rules or configuration files.

Mitigation:
- Limited access to Admin server
- Controlled service users
- Configuration stored locally
- systemd-managed service control

Future improvement:
- Role-based access control
- Version-controlled configuration

---

# 2.4 Alert Suppression

Threat:
Alerts fail to deliver due to webhook failure.

Observed scenario:
- Incorrect webhook port caused delivery failure.

Mitigation:
- Log-based troubleshooting
- Service monitoring
- Validation via manual curl tests

Future improvement:
- Secondary alert channel
- Alertmanager HA

---

# 2.5 Credential Leakage

Threat:
Telegram bot token exposure.

Risk:
If token is leaked, attacker can send fake messages.

Mitigation:
- Token stored inside controlled script
- No public exposure
- No public repository inclusion

Future improvement:
- Use environment variables
- Secret management solution

---

# 2.6 Denial of Service

Threat:
High load causing Prometheus or Alertmanager crash.

Current limitation:
- No HA
- No auto-scaling

Mitigation:
- Minimal lab environment exposure
- Controlled private traffic

Future improvement:
- Horizontal scaling
- Resource monitoring thresholds

---

# 3. Attack Surface Summary

| Component | Exposure Level |
|-----------|----------------|
| Web Server | Private Only |
| Prometheus | Private Only |
| Grafana | Private Only |
| Alertmanager | Private Only |
| Telegram Webhook | Localhost Only |

No component is directly exposed to the public internet.

---

# 4. Residual Risk

Remaining risks include:

- Single point of failure
- Lack of encryption
- Manual configuration management
- No centralized secrets storage

---

# 5. Security Posture Summary

The monitoring stack demonstrates:

- Segmented private network design
- Controlled port exposure
- Internal-only monitoring services
- Explicit firewall enforcement
- Alert routing validation

While simplified for lab purposes, the environment reflects real-world security design considerations for monitoring infrastructure.