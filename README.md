# beanstalkd Installation Guide

beanstalkd is a free and open-source work queue. Beanstalkd provides simple, fast work queue

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
  - CPU: 1 core minimum
  - RAM: 128MB minimum
  - Storage: 1GB for jobs
  - Network: Beanstalk protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 11300 (default beanstalkd port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
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

# Install beanstalkd
sudo dnf install -y beanstalkd

# Enable and start service
sudo systemctl enable --now beanstalkd

# Configure firewall
sudo firewall-cmd --permanent --add-port=11300/tcp
sudo firewall-cmd --reload

# Verify installation
beanstalkd --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install beanstalkd
sudo apt install -y beanstalkd

# Enable and start service
sudo systemctl enable --now beanstalkd

# Configure firewall
sudo ufw allow 11300

# Verify installation
beanstalkd --version
```

### Arch Linux

```bash
# Install beanstalkd
sudo pacman -S beanstalkd

# Enable and start service
sudo systemctl enable --now beanstalkd

# Verify installation
beanstalkd --version
```

### Alpine Linux

```bash
# Install beanstalkd
apk add --no-cache beanstalkd

# Enable and start service
rc-update add beanstalkd default
rc-service beanstalkd start

# Verify installation
beanstalkd --version
```

### openSUSE/SLES

```bash
# Install beanstalkd
sudo zypper install -y beanstalkd

# Enable and start service
sudo systemctl enable --now beanstalkd

# Configure firewall
sudo firewall-cmd --permanent --add-port=11300/tcp
sudo firewall-cmd --reload

# Verify installation
beanstalkd --version
```

### macOS

```bash
# Using Homebrew
brew install beanstalkd

# Start service
brew services start beanstalkd

# Verify installation
beanstalkd --version
```

### FreeBSD

```bash
# Using pkg
pkg install beanstalkd

# Enable in rc.conf
echo 'beanstalkd_enable="YES"' >> /etc/rc.conf

# Start service
service beanstalkd start

# Verify installation
beanstalkd --version
```

### Windows

```bash
# Using Chocolatey
choco install beanstalkd

# Or using Scoop
scoop install beanstalkd

# Verify installation
beanstalkd --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/beanstalkd

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
beanstalkd --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable beanstalkd

# Start service
sudo systemctl start beanstalkd

# Stop service
sudo systemctl stop beanstalkd

# Restart service
sudo systemctl restart beanstalkd

# Check status
sudo systemctl status beanstalkd

# View logs
sudo journalctl -u beanstalkd -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add beanstalkd default

# Start service
rc-service beanstalkd start

# Stop service
rc-service beanstalkd stop

# Restart service
rc-service beanstalkd restart

# Check status
rc-service beanstalkd status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'beanstalkd_enable="YES"' >> /etc/rc.conf

# Start service
service beanstalkd start

# Stop service
service beanstalkd stop

# Restart service
service beanstalkd restart

# Check status
service beanstalkd status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start beanstalkd
brew services stop beanstalkd
brew services restart beanstalkd

# Check status
brew services list | grep beanstalkd
```

### Windows Service Manager

```powershell
# Start service
net start beanstalkd

# Stop service
net stop beanstalkd

# Using PowerShell
Start-Service beanstalkd
Stop-Service beanstalkd
Restart-Service beanstalkd

# Check status
Get-Service beanstalkd
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream beanstalkd_backend {
    server 127.0.0.1:11300;
}

server {
    listen 80;
    server_name beanstalkd.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name beanstalkd.example.com;

    ssl_certificate /etc/ssl/certs/beanstalkd.example.com.crt;
    ssl_certificate_key /etc/ssl/private/beanstalkd.example.com.key;

    location / {
        proxy_pass http://beanstalkd_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName beanstalkd.example.com
    Redirect permanent / https://beanstalkd.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName beanstalkd.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/beanstalkd.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/beanstalkd.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:11300/
    ProxyPassReverse / http://127.0.0.1:11300/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend beanstalkd_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/beanstalkd.pem
    redirect scheme https if !{ ssl_fc }
    default_backend beanstalkd_backend

backend beanstalkd_backend
    balance roundrobin
    server beanstalkd1 127.0.0.1:11300 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R beanstalkd:beanstalkd /etc/beanstalkd
sudo chmod 750 /etc/beanstalkd

# Configure firewall
sudo firewall-cmd --permanent --add-port=11300/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status beanstalkd

# View logs
sudo journalctl -u beanstalkd -f

# Monitor resource usage
top -p $(pgrep beanstalkd)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/beanstalkd"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/beanstalkd-backup-$DATE.tar.gz" /etc/beanstalkd /var/lib/beanstalkd

echo "Backup completed: $BACKUP_DIR/beanstalkd-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop beanstalkd

# Restore from backup
tar -xzf /backup/beanstalkd/beanstalkd-backup-*.tar.gz -C /

# Start service
sudo systemctl start beanstalkd
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u beanstalkd -n 100
sudo tail -f /var/log/beanstalkd/beanstalkd.log

# Check configuration
beanstalkd --version

# Check permissions
ls -la /etc/beanstalkd
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 11300

# Test connectivity
telnet localhost 11300

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep beanstalkd)

# Check disk I/O
iotop -p $(pgrep beanstalkd)

# Check connections
ss -an | grep 11300
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  beanstalkd:
    image: beanstalkd:latest
    ports:
      - "11300:11300"
    volumes:
      - ./config:/etc/beanstalkd
      - ./data:/var/lib/beanstalkd
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update beanstalkd

# Debian/Ubuntu
sudo apt update && sudo apt upgrade beanstalkd

# Arch Linux
sudo pacman -Syu beanstalkd

# Alpine Linux
apk update && apk upgrade beanstalkd

# openSUSE
sudo zypper update beanstalkd

# FreeBSD
pkg update && pkg upgrade beanstalkd

# Always backup before updates
tar -czf /backup/beanstalkd-pre-update-$(date +%Y%m%d).tar.gz /etc/beanstalkd

# Restart after updates
sudo systemctl restart beanstalkd
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/beanstalkd

# Clean old logs
find /var/log/beanstalkd -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/beanstalkd
```

## Additional Resources

- Official Documentation: https://docs.beanstalkd.org/
- GitHub Repository: https://github.com/beanstalkd/beanstalkd
- Community Forum: https://forum.beanstalkd.org/
- Best Practices Guide: https://docs.beanstalkd.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
