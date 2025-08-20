# Alertmanager Configuration

## Global SMTP Settings
```yaml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: '<YOUR_EMAIL>'
  smtp_auth_username: '<YOUR_EMAIL>'
  smtp_auth_password: "<YOUR_APP_PASSWORD>'
```
## Routing
```yaml
route:
 receiver: 'email-alert'

receivers:
  - name: 'email-alert'
    email_configs:
     - to: '<YOUR_EMAIL>'
