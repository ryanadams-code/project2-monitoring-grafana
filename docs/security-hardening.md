# Security Hardening & Defensive Controls

## Project 2 – Monitoring Infrastructure

This document explains the security architecture, hardening decisions, and risk mitigation strategies applied to the monitoring stack.

The goal of this project is not only to deploy monitoring tools, but to implement them using secure infrastructure principles similar to production environments.

---

# 1. Security Design Philosophy

This monitoring infrastructure follows:

- Principle of Least Privilege
- Defense-in-Depth
- Network Segmentation
- Service Exposure Minimization
- Controlled External Integration

The monitoring stack is fully isolated inside a private network and does not expose any management services publicly.

---

# 2. Network Isolation

## Private Network (Host-Only)

All internal services communicate within:

192.168.57.0/24

This network acts as a simulated private subnet.

Characteristics:

- No public IP assigned to any VM
- No inbound internet access
- No port forwarding configured
- Internal-only service communication

This mimics:
- Cloud VPC private subnet
- Zero-trust internal segmentation

---

# 3. Firewall Enforcement (UFW)

Default firewall policy on both VMs:

- Default deny incoming
- Allow only required ports
- Explicit rule-based access control

---

## Admin Server Rules

Services hosted:

- Prometheus (9090)
- Grafana (3000)
- Alertmanager (9093)
- Node Exporter (9100)
- Telegram Webhook (5001)
- SSH (22)

Firewall Policy:

| Port | Service | Access Control |
|------|---------|---------------|
| 22 | SSH | Restricted (Lab Access Only) |
| 9090 | Prometheus | Internal Only |
| 3000 | Grafana | Internal Only |
| 9093 | Alertmanager | Internal Only |
| 9100 | Node Exporter | Internal Only |
| 5001 | Webhook | Internal Only |
| All others | — | Deny |

No service is exposed to public internet.

---

## Web Server Rules

Services hosted:

- Nginx (80)
- Node Exporter (9100)
- SSH (22)

Firewall Policy:

| Port | Service | Access Control |
|------|---------|---------------|
| 80 | HTTP | Allowed (Lab Testing) |
| 22 | SSH | Admin Server Only |
| 9100 | Node Exporter | Admin Server Only |
| All others | — | Deny |

This prevents unauthorized lateral movement between hosts.

---

# 4. SSH Hardening

SSH configuration hardened with:

- Root login disabled
- Limited access via firewall
- Controlled administrative entry point
- No public SSH exposure

This simulates:

- Bastion access model
- Controlled administrative channel
- Reduced brute-force exposure

---

# 5. Service Exposure Model

Monitoring services (Prometheus, Grafana, Alertmanager):

- Bound to private interfaces
- Not publicly routable
- Access limited to internal network

This reduces:

- Remote exploitation risk
- Automated internet scanning exposure
- Attack surface of observability stack

---

# 6. Outbound Communication Control

Internet access is provided only through:

NAT Adapter (Outbound-only)

Used exclusively for:

- Telegram API HTTPS calls (port 443)

No inbound NAT forwarding configured.

This enforces:

- One-way external integration
- No externally reachable webhook endpoint
- Minimal external dependency exposure

---

# 7. Telegram Integration Security

Telegram alerting is implemented via:

Internal webhook service (localhost binding)

Security considerations:

- Webhook runs locally
- Not exposed externally
- Only Alertmanager can access it
- No public API endpoint

Sensitive credentials (Bot Token) are stored securely and not committed to repository.

---

# 8. Attack Surface Reduction

Mitigations implemented:

- No public monitoring UI exposure
- No open management ports
- Minimal open service set
- Explicit firewall rules
- No unused services running
- No unnecessary listening interfaces

---

# 9. Threat Mitigation Summary

| Threat | Mitigation |
|--------|-----------|
| Port scanning | No public exposure |
| Brute-force SSH | Root disabled + restricted access |
| Lateral movement | Firewall isolation |
| Unauthorized metric scraping | Source-restricted port 9100 |
| Monitoring stack exploitation | Private network only |
| External webhook abuse | Local-only binding |

---

# 10. Production-Level Concepts Simulated

Although deployed in a local lab, this architecture simulates:

- Cloud private subnet design
- Security group enforcement
- Bastion-style SSH access
- Internal-only observability stack
- Controlled third-party integration

---

# 11. Security Maturity Outcome

This project demonstrates:

- Infrastructure-first security mindset
- Proper monitoring stack isolation
- Layered defensive controls
- Practical firewall enforcement
- Secure service architecture design

The monitoring system is functional, isolated, and hardened following foundational infrastructure security principles.