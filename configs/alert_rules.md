# Prometheus Alert Rules

## High CPU Usage
```yaml
- name: cpu-alerts
  rules:
    - alert: HighCPUUsage
      expr: (100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[30s]))* 100)) > 80
      for: 30s
      labels:
        severity: warning
      annotations:
        summary: "High CPU usage detected"
        description: "CPU usage is over 80% for the last 30s."
