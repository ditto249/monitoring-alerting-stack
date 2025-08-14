# monitoring-alerting-stack
Overview

A lightweight monitoring and alerting system to track server health, detect anomalies, and trigger real-time alerts.
Built with Prometheus, Node Exporter, Grafana, and Alertmanager.

Features

Collects metrics from a Linux server (CPU, memory, disk, network)

Real-time visualization with Grafana

Custom alert rules for high CPU/memory usage

Email notifications via Alertmanager

Tested with simulated CPU spikes

Architecture

Setup
1. Install Node Exporter
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-*.linux-amd64.tar.gz
tar xvf node_exporter-*.linux-amd64.tar.gz
sudo cp node_exporter-*/node_exporter /usr/local/bin
sudo useradd --no-create-home --shell /bin/false node_exporter
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
sudo nano /etc/systemd/system/node_exporter.service


Service file:

[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=default.target

sudo systemctl daemon-reload
sudo systemctl enable --now node_exporter

2. Install Prometheus
wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-*.linux-amd64.tar.gz
tar xvf prometheus-*.linux-amd64.tar.gz
sudo mv prometheus-*/prometheus /usr/local/bin/
sudo mv prometheus-*/promtool /usr/local/bin/
sudo mkdir /etc/prometheus /var/lib/prometheus
sudo mv prometheus-*/{consoles,console_libraries} /etc/prometheus/
sudo nano /etc/prometheus/prometheus.yml


Basic config:

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']

sudo useradd --no-create-home --shell /bin/false prometheus
sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
sudo nano /etc/systemd/system/prometheus.service

3. Install Grafana
sudo apt install -y apt-transport-https software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt update
sudo apt install grafana -y
sudo systemctl enable --now grafana-server

4. Install Alertmanager
wget https://github.com/prometheus/alertmanager/releases/latest/download/alertmanager-*.linux-amd64.tar.gz
tar xvf alertmanager-*.linux-amd64.tar.gz
sudo mv alertmanager-*/alertmanager /usr/local/bin/
sudo mv alertmanager-*/amtool /usr/local/bin/
sudo mkdir /etc/alertmanager
sudo nano /etc/alertmanager/alertmanager.yml


Basic config:

route:
  receiver: 'email-alert'
receivers:
- name: 'email-alert'
  email_configs:
  - to: "you@example.com"
    from: "alert@example.com"
    smarthost: "smtp.example.com:587"
    auth_username: "alert@example.com"
    auth_password: "PASSWORD"

Testing

Simulate high CPU load:

sudo apt install stress -y
stress --cpu 4 --timeout 30


Grafana should show CPU spike

Alertmanager should send an email

Screenshots

Grafana dashboard (normal & spike)

Email alert example
