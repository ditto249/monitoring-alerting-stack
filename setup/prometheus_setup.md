# Prometheus Setup

## 1. Download and extract Prometheus
```bash
cd /tmp
wget https://github.com/prometheus/prometheus/releases/download/v2.54.1/prometheus-2.54.1.linux-amd64.tar.gz
tar xvf prometheus-2.54.1.linux-amd64.tar.gz
sudo mv prometheus-2.54.1.linux-amd64 /usr/local/bin/prometheus-files
```

## 2. Create Prometheus user and directories
```bash
sudo useradd --no-create-home --shell /bin/false prometheus
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo chown prometheus:prometheus /etc/prometheus /var/lib/prometheus
```

## 3. Move binaries
```bash
sudo cp /usr/local/bin/prometheus-files/prometheus /usr/local/bin/
sudo cp /usr/local/bin/prometheus-files/promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool
```

## 4. Move console files
```bash
sudo cp -r /usr/local/bin/prometheus-files/consoles /etc/prometheus
sudo cp -r /usr/local/bin/prometheus-files/console_libraries /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles /etc/prometheus/console_libraries
```
## 5. Prometheus config
```bash
sudo nano /etc/prometheus/prometheus.yml
```

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
```
## 6. Create systemd service
```bash
sudo nano /etc/systemd/system/prometheus.service
```

```ini
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus/
Restart=always

[Install]
WantedBy=multi-user.target
```

## 7. Start and enable Prometheus
```bash
sudo systemctl daemon-reexec
sudo systemctl start prometheus
sudo systemctl enable prometheus
```

Access: http://<MONITORING_VM_IP>:9090

