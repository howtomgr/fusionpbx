# FusionPBX Installation Guide

FusionPBX is a free and open-source PBX. Multi-tenant PBX and voice switch

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 443 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 443 (default fusionpbx port)
- **Dependencies**:
  - freeswitch, postgresql, nginx
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install fusionpbx
sudo dnf install -y fusionpbx freeswitch, postgresql, nginx

# Enable and start service
sudo systemctl enable --now fusionpbx

# Configure firewall
sudo firewall-cmd --permanent --add-service=fusionpbx
sudo firewall-cmd --reload

# Verify installation
fusionpbx --version || systemctl status fusionpbx
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install fusionpbx
sudo apt install -y fusionpbx freeswitch, postgresql, nginx

# Enable and start service
sudo systemctl enable --now fusionpbx

# Configure firewall
sudo ufw allow 443

# Verify installation
fusionpbx --version || systemctl status fusionpbx
```

### Arch Linux

```bash
# Install fusionpbx
sudo pacman -S fusionpbx

# Enable and start service
sudo systemctl enable --now fusionpbx

# Verify installation
fusionpbx --version || systemctl status fusionpbx
```

### Alpine Linux

```bash
# Install fusionpbx
apk add --no-cache fusionpbx

# Enable and start service
rc-update add fusionpbx default
rc-service fusionpbx start

# Verify installation
fusionpbx --version || rc-service fusionpbx status
```

### openSUSE/SLES

```bash
# Install fusionpbx
sudo zypper install -y fusionpbx freeswitch, postgresql, nginx

# Enable and start service
sudo systemctl enable --now fusionpbx

# Configure firewall
sudo firewall-cmd --permanent --add-service=fusionpbx
sudo firewall-cmd --reload

# Verify installation
fusionpbx --version || systemctl status fusionpbx
```

### macOS

```bash
# Using Homebrew
brew install fusionpbx

# Start service
brew services start fusionpbx

# Verify installation
fusionpbx --version
```

### FreeBSD

```bash
# Using pkg
pkg install fusionpbx

# Enable in rc.conf
echo 'fusionpbx_enable="YES"' >> /etc/rc.conf

# Start service
service fusionpbx start

# Verify installation
fusionpbx --version || service fusionpbx status
```

### Windows

```powershell
# Using Chocolatey
choco install fusionpbx

# Or using Scoop
scoop install fusionpbx

# Verify installation
fusionpbx --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/fusionpbx

# Set up basic configuration
sudo tee /etc/fusionpbx/fusionpbx.conf << 'EOF'
# FusionPBX Configuration
max_calls=5000
EOF

# Test configuration
sudo fusionpbx -t || sudo fusionpbx configtest

# Reload service
sudo systemctl reload fusionpbx
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R fusionpbx:fusionpbx /etc/fusionpbx
sudo chmod 750 /etc/fusionpbx

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable fusionpbx

# Start service
sudo systemctl start fusionpbx

# Stop service
sudo systemctl stop fusionpbx

# Restart service
sudo systemctl restart fusionpbx

# Reload configuration
sudo systemctl reload fusionpbx

# Check status
sudo systemctl status fusionpbx

# View logs
sudo journalctl -u fusionpbx -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add fusionpbx default

# Start service
rc-service fusionpbx start

# Stop service
rc-service fusionpbx stop

# Restart service
rc-service fusionpbx restart

# Check status
rc-service fusionpbx status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'fusionpbx_enable="YES"' >> /etc/rc.conf

# Start service
service fusionpbx start

# Stop service
service fusionpbx stop

# Restart service
service fusionpbx restart

# Check status
service fusionpbx status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start fusionpbx
brew services stop fusionpbx
brew services restart fusionpbx

# Check status
brew services list | grep fusionpbx
```

### Windows Service Manager

```powershell
# Start service
net start fusionpbx

# Stop service
net stop fusionpbx

# Using PowerShell
Start-Service fusionpbx
Stop-Service fusionpbx
Restart-Service fusionpbx

# Check status
Get-Service fusionpbx
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/fusionpbx/fusionpbx.conf << 'EOF'
max_calls=5000
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart fusionpbx
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream fusionpbx_backend {
    server 127.0.0.1:443;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name fusionpbx.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name fusionpbx.example.com;

    ssl_certificate /etc/ssl/certs/fusionpbx.example.com.crt;
    ssl_certificate_key /etc/ssl/private/fusionpbx.example.com.key;

    location / {
        proxy_pass http://fusionpbx_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName fusionpbx.example.com
    Redirect permanent / https://fusionpbx.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName fusionpbx.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/fusionpbx.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/fusionpbx.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:443/
    ProxyPassReverse / http://127.0.0.1:443/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:443/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend fusionpbx_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/fusionpbx.pem
    redirect scheme https if !{ ssl_fc }
    default_backend fusionpbx_backend

backend fusionpbx_backend
    balance roundrobin
    option httpchk GET /health
    server fusionpbx1 127.0.0.1:443 check
    server fusionpbx2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R fusionpbx:fusionpbx /etc/fusionpbx
sudo chmod 750 /etc/fusionpbx

# Configure firewall
sudo firewall-cmd --permanent --add-service=fusionpbx
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/fusionpbx.conf << 'EOF'
[fusionpbx]
enabled = true
port = 443
filter = fusionpbx
logpath = /var/log/fusionpbx/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/fusionpbx.key \
    -out /etc/ssl/certs/fusionpbx.crt

# Configure SSL in fusionpbx
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE fusionpbx_db;
CREATE USER fusionpbx_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE fusionpbx_db TO fusionpbx_user;
EOF

# Configure fusionpbx to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE fusionpbx_db;
CREATE USER 'fusionpbx_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON fusionpbx_db.* TO 'fusionpbx_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# FusionPBX specific tuning
max_calls=5000
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
fusionpbx soft nofile 65535
fusionpbx hard nofile 65535
fusionpbx soft nproc 32768
fusionpbx hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'fusionpbx'
    static_configs:
      - targets: ['localhost:443']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet fusionpbx; then
    echo "FusionPBX is running"
    exit 0
else
    echo "FusionPBX is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/fusionpbx << 'EOF'
/var/log/fusionpbx/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 fusionpbx fusionpbx
    postrotate
        systemctl reload fusionpbx > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# FusionPBX backup script
BACKUP_DIR="/backup/fusionpbx"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop fusionpbx

# Backup configuration
tar -czf "$BACKUP_DIR/fusionpbx-config-$DATE.tar.gz" /etc/fusionpbx

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/fusionpbx-data-$DATE.tar.gz" /var/lib/fusionpbx

# Start service
systemctl start fusionpbx

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop fusionpbx

# Restore configuration
sudo tar -xzf /backup/fusionpbx/fusionpbx-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/fusionpbx/fusionpbx-data-*.tar.gz -C /

# Set permissions
sudo chown -R fusionpbx:fusionpbx /etc/fusionpbx
sudo chown -R fusionpbx:fusionpbx /var/lib/fusionpbx

# Start service
sudo systemctl start fusionpbx
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u fusionpbx -n 100
sudo tail -f /var/log/fusionpbx/*.log

# Check configuration
sudo fusionpbx -t || sudo fusionpbx configtest

# Check permissions
ls -la /etc/fusionpbx
ls -la /var/lib/fusionpbx
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 443
sudo netstat -tlnp | grep 443

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 443
nc -zv localhost 443
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep php-fpm)
htop -p $(pgrep php-fpm)

# Check connections
ss -ant | grep :443 | wc -l

# Monitor I/O
iotop -p $(pgrep php-fpm)
```

### Debug Mode

```bash
# Run in debug mode
sudo fusionpbx -d
# or
sudo fusionpbx debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  fusionpbx:
    image: fusionpbx:latest
    container_name: fusionpbx
    ports:
      - "443:443"
    volumes:
      - ./config:/etc/fusionpbx
      - ./data:/var/lib/fusionpbx
    environment:
      - fusionpbx_CONFIG=/etc/fusionpbx/fusionpbx.conf
    restart: unless-stopped
    networks:
      - fusionpbx_net

networks:
  fusionpbx_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fusionpbx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: fusionpbx
  template:
    metadata:
      labels:
        app: fusionpbx
    spec:
      containers:
      - name: fusionpbx
        image: fusionpbx:latest
        ports:
        - containerPort: 443
        volumeMounts:
        - name: config
          mountPath: /etc/fusionpbx
      volumes:
      - name: config
        configMap:
          name: fusionpbx-config
---
apiVersion: v1
kind: Service
metadata:
  name: fusionpbx
spec:
  selector:
    app: fusionpbx
  ports:
  - port: 443
    targetPort: 443
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure FusionPBX
  hosts: all
  become: yes
  tasks:
    - name: Install fusionpbx
      package:
        name: fusionpbx
        state: present
    
    - name: Configure fusionpbx
      template:
        src: fusionpbx.conf.j2
        dest: /etc/fusionpbx/fusionpbx.conf
        owner: fusionpbx
        group: fusionpbx
        mode: '0640'
      notify: restart fusionpbx
    
    - name: Start and enable fusionpbx
      systemd:
        name: fusionpbx
        state: started
        enabled: yes
  
  handlers:
    - name: restart fusionpbx
      systemd:
        name: fusionpbx
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update fusionpbx

# Debian/Ubuntu
sudo apt update && sudo apt upgrade fusionpbx

# Arch Linux
sudo pacman -Syu fusionpbx

# Alpine Linux
apk update && apk upgrade fusionpbx

# openSUSE
sudo zypper update fusionpbx

# FreeBSD
pkg update && pkg upgrade fusionpbx

# Always backup before updates
tar -czf /backup/fusionpbx-pre-update-$(date +%Y%m%d).tar.gz /etc/fusionpbx

# Restart after updates
sudo systemctl restart fusionpbx
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/fusionpbx -name "*.log" -mtime +30 -delete

# Verify integrity
sudo fusionpbx --verify || sudo fusionpbx check

# Update databases (if applicable)
sudo fusionpbx-update-db

# Optimize performance
sudo fusionpbx-optimize

# Check for security updates
sudo fusionpbx --security-check
```

## Additional Resources

- Official Documentation: https://docs.fusionpbx.org/
- GitHub Repository: https://github.com/fusionpbx/fusionpbx
- Community Forum: https://forum.fusionpbx.org/
- Wiki: https://wiki.fusionpbx.org/
- Comparison vs FreePBX, Asterisk, 3CX, Elastix: https://docs.fusionpbx.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
