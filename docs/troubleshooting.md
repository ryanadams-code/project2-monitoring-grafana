# Troubleshooting Log

## Project 2 – Monitoring & Alerting Stack

This document records real issues encountered during the deployment of Prometheus, Node Exporter, Grafana, Alertmanager, and Telegram integration.

The objective is to demonstrate practical troubleshooting methodology and root cause analysis during infrastructure implementation.

---

# 1. Prometheus Service Failing to Start

## Problem

Prometheus service repeatedly failed:

systemctl status prometheus  
Result: exit-code

Manual execution showed:

yaml: unmarshal errors:
field alert not found in type rulefmt.RuleGroup

## Root Cause

Incorrect YAML structure inside alert rule file.

Fields such as:
- alert
- expr
- for
- annotations

Were placed outside the required `groups:` structure.

## Resolution

Rewrote alert rule file with proper structure:

```yaml
groups:
  - name: cpu-alerts
    rules:
      - alert: HighCPUUsageCritical
        expr: 100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[1m])) * 100) > 80
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "High CPU usage"
          description: "CPU usage above 80% for 1 minute."
```

Restarted Prometheus successfully.

---

# 2. Node Exporters Not Appearing in Prometheus

## Problem

Only Prometheus job appeared as UP in the Targets page.

Node exporters were missing.

## Root Cause

Edited the wrong configuration file.

Prometheus was using:

/etc/prometheus/prometheus.yml

But changes were made inside:

~/prometheus-3.x.x/prometheus.yml

## Resolution

Updated the correct config file:

```yaml
- job_name: "node_exporters"
  static_configs:
    - targets:
      - "192.168.57.103:9100"
      - "192.168.57.104:9100"
```

Restarted Prometheus.  
Targets appeared as UP.

---

# 3. Wildcard Download Failing

## Problem

Using:

wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-*.linux-amd64.tar.gz

Resulted in:

Warning: wildcards not supported in HTTP  
404 Not Found

## Root Cause

wget does not support wildcard expansion in HTTP URLs.

## Resolution

Used exact version URL:

wget https://github.com/prometheus/prometheus/releases/download/v3.8.1/prometheus-3.8.1.linux-amd64.tar.gz

Download successful.

---

# 4. Alertmanager Service Failure

## Problem

Alertmanager failed to start after Telegram integration was added.

Error:

Loading configuration file failed

## Root Cause

Invalid YAML formatting and incorrect webhook configuration.

## Resolution

Simplified receiver configuration.

Validated config before restart:

amtool check-config /etc/alertmanager/alertmanager.yml

Restarted service successfully.

---

# 5. Telegram Bot Returning 403

## Problem

Direct Python test returned:

<Response [403]>

## Root Cause

Incorrect chat_id was used (bot ID instead of actual chat ID).

## Resolution

Sent a manual message to the bot.

Queried:
https://api.telegram.org/bot<TOKEN>/getUpdates

Extracted correct chat_id from response.

Retested message.

Response returned HTTP 200.

---

# 6. Alertmanager Webhook Connection Refused

## Problem

Logs showed:

dial tcp 127.0.0.1:8080: connect: connection refused

## Root Cause

Webhook service was not running or port mismatch.

## Resolution

Verified webhook binding:

app.run(host="0.0.0.0", port=5001)

Updated Alertmanager receiver URL:

http://127.0.0.1:5001/alert

Restarted services.

Alert successfully delivered.

---

# 7. Python PEP 668 Installation Error

## Problem

externally-managed-environment error when running:

pip3 install flask

## Root Cause

Ubuntu enforces system-managed Python packages.

## Resolution

Created virtual environment:

python3 -m venv venv  
source venv/bin/activate  
pip install flask requests

Dependencies installed successfully.

---

# 8. Firewall Blocking Node Exporter

## Problem

Prometheus could not scrape web server metrics.

## Root Cause

Port 9100 not allowed in UFW.

## Resolution

Allowed access from Admin server only:

sudo ufw allow from 192.168.57.103 to any port 9100

Metrics scraped successfully.

---

# 9. Alert Delay Confusion

## Observation

Alerts did not fire immediately.

## Explanation

Alert rule used:

for: 1m

This requires sustained threshold violation before firing, preventing false positives.

---

# Troubleshooting Methodology Used

For each issue:

- Check service status (systemctl status)
- Inspect logs (journalctl -u <service>)
- Validate YAML configuration
- Verify network connectivity (ping, curl)
- Check firewall rules (ufw status)
- Confirm listening ports (ss -tulnp)
- Test integrations manually before automation

# Engineering Takeaway

This project strengthened:

- YAML debugging
- Linux service management
- Firewall troubleshooting
- Alert flow validation
- External API integration debugging
- Structured root cause analysis

The monitoring stack required iterative debugging and reflects real-world infrastructure troubleshooting scenarios.