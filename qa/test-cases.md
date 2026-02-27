✅ TEST CASE 1
Test Case ID: TC-001
Test Case Name: CPU Alert Trigger – Warning Level

Objective:
To verify that the monitoring system triggers a warning alert when CPU usage exceeds the defined threshold.

Precondition:
Prometheus and Alertmanager are running
CPU warning threshold configured at 70%

Test Steps:
Simulate high CPU usage on monitored server.
Wait for Prometheus scrape interval.
Observe alert status in Prometheus.
Check Alertmanager notification logs.
Verify Telegram webhook delivery.

Expected Result:
Warning alert is triggered.
Alert is routed correctly to Alertmanager.
Telegram notification is successfully received.

✅ TEST CASE 2

Test Case ID: TC-002
Test Case Name: CPU Alert Trigger – Critical Level

Objective:
To verify that a critical alert is triggered when CPU usage exceeds critical threshold.

Precondition:
Critical threshold configured at 85%

Test Steps:
Simulate CPU usage above 85%.
Observe alert firing in Prometheus.
Verify severity routing in Alertmanager.
Check Telegram notification content.

Expected Result:
Critical alert is triggered.
Alert severity is labeled correctly.
Telegram notification reflects "critical" severity.

✅ TEST CASE 3

Test Case ID: TC-003
Test Case Name: Alert Routing Verification

Objective:
To verify that alerts are routed to correct receivers based on severity.

Precondition:
Alertmanager routing rules configured

Test Steps:
Trigger warning-level alert.
Trigger critical-level alert.
Observe Alertmanager routing behavior.

Expected Result:
Warning alert sent to warning receiver.
Critical alert sent to critical receiver.
No misrouting occurs.

✅ TEST CASE 4

Test Case ID: TC-004
Test Case Name: Webhook Failure Handling

Objective:
To verify system behavior when Telegram webhook service is unavailable.

Precondition:
Stop webhook service manually

Test Steps:
Trigger CPU alert.
Observe Alertmanager logs.
Restart webhook service.

Expected Result:
Alertmanager logs connection error.
System does not crash.
After restart, notification resumes normally.

✅ TEST CASE 5

Test Case ID: TC-005
Test Case Name: Configuration Validation – YAML Syntax Error

Objective:
To verify system behavior when Prometheus configuration contains invalid YAML.

Precondition:
Introduce minor YAML indentation error in config file

Test Steps:
Restart Prometheus service.
Observe service status.
Check error logs.

Expected Result:
Service fails to start.
Error message clearly indicates configuration issue.
After correction, service runs normally.

✅ DATABASE TEST CASE 1

Test Case ID: TC-006
Test Case Name: Database Insert Validation

Objective:
To verify that data is correctly inserted into PostgreSQL database.

Precondition:
Database connection established
Table alerts_log exists

Test Steps:
Insert a new alert record into alerts_log.
Execute SELECT query to retrieve inserted record.
Compare inserted values with retrieved data.

Expected Result:
Record is successfully stored in database.
Retrieved values match inserted values.
No data truncation or mismatch occurs.

✅ DATABASE TEST CASE 2

Test Case ID: TC-007
Test Case Name: Invalid Data Input Handling

Objective:
To verify system behavior when invalid data is inserted into database.

Precondition:
Table has NOT NULL constraint on severity column

Test Steps:
Attempt to insert record with NULL value in severity.
Execute insert query.

Expected Result:
Database rejects the insert operation.
Proper error message is returned.
No corrupted data stored.