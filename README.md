# x2go Installation Guide

x2go is a free and open-source remote desktop. X2Go provides remote desktop solution for Linux

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
  - RAM: 1GB minimum
  - Storage: 1GB for sessions
  - Network: NX protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 22 (default x2go port)
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

# Install x2go
sudo dnf install -y x2go

# Enable and start service
sudo systemctl enable --now x2go

# Configure firewall
sudo firewall-cmd --permanent --add-port=22/tcp
sudo firewall-cmd --reload

# Verify installation
x2go --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install x2go
sudo apt install -y x2go

# Enable and start service
sudo systemctl enable --now x2go

# Configure firewall
sudo ufw allow 22

# Verify installation
x2go --version
```

### Arch Linux

```bash
# Install x2go
sudo pacman -S x2go

# Enable and start service
sudo systemctl enable --now x2go

# Verify installation
x2go --version
```

### Alpine Linux

```bash
# Install x2go
apk add --no-cache x2go

# Enable and start service
rc-update add x2go default
rc-service x2go start

# Verify installation
x2go --version
```

### openSUSE/SLES

```bash
# Install x2go
sudo zypper install -y x2go

# Enable and start service
sudo systemctl enable --now x2go

# Configure firewall
sudo firewall-cmd --permanent --add-port=22/tcp
sudo firewall-cmd --reload

# Verify installation
x2go --version
```

### macOS

```bash
# Using Homebrew
brew install x2go

# Start service
brew services start x2go

# Verify installation
x2go --version
```

### FreeBSD

```bash
# Using pkg
pkg install x2go

# Enable in rc.conf
echo 'x2go_enable="YES"' >> /etc/rc.conf

# Start service
service x2go start

# Verify installation
x2go --version
```

### Windows

```bash
# Using Chocolatey
choco install x2go

# Or using Scoop
scoop install x2go

# Verify installation
x2go --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/x2go

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
x2go --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable x2go

# Start service
sudo systemctl start x2go

# Stop service
sudo systemctl stop x2go

# Restart service
sudo systemctl restart x2go

# Check status
sudo systemctl status x2go

# View logs
sudo journalctl -u x2go -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add x2go default

# Start service
rc-service x2go start

# Stop service
rc-service x2go stop

# Restart service
rc-service x2go restart

# Check status
rc-service x2go status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'x2go_enable="YES"' >> /etc/rc.conf

# Start service
service x2go start

# Stop service
service x2go stop

# Restart service
service x2go restart

# Check status
service x2go status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start x2go
brew services stop x2go
brew services restart x2go

# Check status
brew services list | grep x2go
```

### Windows Service Manager

```powershell
# Start service
net start x2go

# Stop service
net stop x2go

# Using PowerShell
Start-Service x2go
Stop-Service x2go
Restart-Service x2go

# Check status
Get-Service x2go
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream x2go_backend {
    server 127.0.0.1:22;
}

server {
    listen 80;
    server_name x2go.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name x2go.example.com;

    ssl_certificate /etc/ssl/certs/x2go.example.com.crt;
    ssl_certificate_key /etc/ssl/private/x2go.example.com.key;

    location / {
        proxy_pass http://x2go_backend;
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
    ServerName x2go.example.com
    Redirect permanent / https://x2go.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName x2go.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/x2go.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/x2go.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:22/
    ProxyPassReverse / http://127.0.0.1:22/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend x2go_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/x2go.pem
    redirect scheme https if !{ ssl_fc }
    default_backend x2go_backend

backend x2go_backend
    balance roundrobin
    server x2go1 127.0.0.1:22 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R x2go:x2go /etc/x2go
sudo chmod 750 /etc/x2go

# Configure firewall
sudo firewall-cmd --permanent --add-port=22/tcp
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
sudo systemctl status x2go

# View logs
sudo journalctl -u x2go -f

# Monitor resource usage
top -p $(pgrep x2go)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/x2go"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/x2go-backup-$DATE.tar.gz" /etc/x2go /var/lib/x2go

echo "Backup completed: $BACKUP_DIR/x2go-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop x2go

# Restore from backup
tar -xzf /backup/x2go/x2go-backup-*.tar.gz -C /

# Start service
sudo systemctl start x2go
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u x2go -n 100
sudo tail -f /var/log/x2go/x2go.log

# Check configuration
x2go --version

# Check permissions
ls -la /etc/x2go
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 22

# Test connectivity
telnet localhost 22

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep x2go)

# Check disk I/O
iotop -p $(pgrep x2go)

# Check connections
ss -an | grep 22
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  x2go:
    image: x2go:latest
    ports:
      - "22:22"
    volumes:
      - ./config:/etc/x2go
      - ./data:/var/lib/x2go
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update x2go

# Debian/Ubuntu
sudo apt update && sudo apt upgrade x2go

# Arch Linux
sudo pacman -Syu x2go

# Alpine Linux
apk update && apk upgrade x2go

# openSUSE
sudo zypper update x2go

# FreeBSD
pkg update && pkg upgrade x2go

# Always backup before updates
tar -czf /backup/x2go-pre-update-$(date +%Y%m%d).tar.gz /etc/x2go

# Restart after updates
sudo systemctl restart x2go
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/x2go

# Clean old logs
find /var/log/x2go -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/x2go
```

## Additional Resources

- Official Documentation: https://docs.x2go.org/
- GitHub Repository: https://github.com/x2go/x2go
- Community Forum: https://forum.x2go.org/
- Best Practices Guide: https://docs.x2go.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
