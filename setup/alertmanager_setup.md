# Alertmanager Setup

## 1. Download Alertmanager
```bash
cd /tmp
wget https://github.com/prometheus/alertmanager/releases/download/v0.27.0/alertmanager-0.27.0.linux-amd64.tar.gz
tar xvf alertmanager-0.27.0.linux-amd64.tar.gz
sudo mv alertmanager-0.27.0.linux-amd64 /usr/local/bin/alertmanager-files
```

## 2. Create user and directories
```bash
sudo useradd --no-create-home --shell /bin/false alertmanager
sudo mkdir /etc/alertmanager
sudo mkdir /var/lib/alertmanager
sudo chown alertmanager:alertmanager /etc/alertmanager /var/lib/alertmanager
```

## 3. Move binaries
```bash
sudo cp /usr/local/bin/alertmanager-files/alertmanager /usr/local/bin/
sudo cp /usr/local/bin/alertmanager-files/amtool /usr/local/bin/
sudo chown alertmanager:alertmanager /usr/local/bin/alertmanager /usr/local/bin/amtool
```

## 4. Config file
```bash
sudo nano /etc/alertmanager/config.yml
```

Example (Gmail w/ app password):
```yaml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: '<YOUR_EMAIL>'
  smtp_auth_username: '<YOUR_EMAIL>'
  smtp_auth_password: "<YOUR_APP_PASSWORD>'

route:
 receiver: 'email-alert'

receivers:
  - name: 'email-alert'
    email_configs:
     - to: '<YOUR_EMAIL>'
```

## 5. systemd service
```bash
sudo nano /etc/systemd/system/alertmanager.service
```

```ini
[Unit]
Description=Alertmanager
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
ExecStart=/usr/local/bin/alertmanager \
  --config.file=/etc/alertmanager/config.yml \
  --storage.path=/var/lib/alertmanager/
Restart=always

[Install]
WantedBy=multi-user.target
```

## 6. Start service
```bash
sudo systemctl daemon-reexec
sudo systemctl start alertmanager
sudo systemctl enable alertmanager
```

Access: http://<MONITORING_VM_IP>:9093







