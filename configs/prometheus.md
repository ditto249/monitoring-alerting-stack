# Prometheus Configuration

## prometheus.yml
```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['<TARGETS_IP_ADDRESS>:9100']

  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']

rule_files:
  - "/etc/prometheus/alert.rules.yml"
