# CPU Stress Test 

This test validates that the monitoring and alerting stack detects and alerts on high CPU usage.

## Steps

### 1. Run a CPU Stress Test
On the **web server VM** (where Node Exporter is installed), run:

```bash
sudo apt update
sudo apt install -y stress
```

Run 4 workers stressing CPU for 60 seconds
stress --cpu 4 --timeout 60

### 2. Verify in Prometheus
On the Prometheus VM:
-Open Prometheus at http://localhost:9090/alerts.
Run the query:
-avg(rate(node_cpu_seconds_total{mode!="idle"}[2m]))
You should see values increase above 0.8 during the stress test.

### 3. Check Grafana
-Open Grafana (default: http://<prometheus-vm-ip>:3000).
-Go to the CPU Dashboard.
-Look for a visible spike during the stress test period.

### 4. Confirm Alerting
Prometheus should mark the HighCPUUsage alert as Pending → Firing.
Alertmanager should receive the alert.
If email is configured, you should receive an alert with a subject like:
- [FIRING:1] HighCPUUsage (instance web-server:9100)

### Expected results
CPU stressed in Grafana:
<img width="1846" height="889" alt="Screenshot 2025-08-17 192832" src="https://github.com/user-attachments/assets/994cd8bf-e5b0-463c-b73e-280a3ec4f486" />

Firing in Prometheus:
<img width="1850" height="396" alt="Screenshot 2025-08-18 002325" src="https://github.com/user-attachments/assets/8c8e683d-0ad2-401c-8eeb-a2454d0bef97" />

AlertManager has email to send:
<img width="1412" height="568" alt="Screenshot 2025-08-18 002311" src="https://github.com/user-attachments/assets/b6b760ce-19b1-4d8f-ac4d-2e83c97bf8af" />

Email sent:
<img width="1168" height="578" alt="Screenshot 2025-08-18 002346" src="https://github.com/user-attachments/assets/374354f6-a7e4-47c4-a65e-6242d5506a3c" />

### Notes
If the alert does not fire:
- Ensure Node Exporter is running on the web server (systemctl status node_exporter).
- Confirm Prometheus is scraping Node Exporter (http://<web-server-ip>:9100/metrics).
- Check that the alert rule in prometheus.yml matches the threshold.

