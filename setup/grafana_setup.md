# Grafana Setup

## 1. Install Grafana
```bash
wget https://dl.grafana.com/oss/release/grafana_10.4.1_amd64.deb
sudo apt-get install -y adduser libfontconfig1 musl
sudo dpkg -i grafana_10.4.1_amd64.deb
```

## 2. Enable and start service
```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

Access: http://<MONITORING_VM_IP>:3000
Default login: admin / admin (change password on first login)

## 3. Add Prometheus as a data source
- Go to Configuration → Data Sources
- Add Prometheus
- URL: http://localhost:9090

## 4. Import a Prebuilt Node Exporter Dashboard

- Grafana → Dashboards → Import
- Use ID: `1860` (famous Node Exporter dashboard)
- Select Prometheus as data source → Import.
