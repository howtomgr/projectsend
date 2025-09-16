# projectsend Installation Guide

projectsend is a free and open-source file sharing with clients. ProjectSend provides file sharing focused on client interactions and project management

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
  - RAM: 256MB minimum
  - Storage: 1GB for files
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default projectsend port)
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

# Install projectsend
sudo dnf install -y projectsend

# Enable and start service
sudo systemctl enable --now projectsend

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
projectsend --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install projectsend
sudo apt install -y projectsend

# Enable and start service
sudo systemctl enable --now projectsend

# Configure firewall
sudo ufw allow 80

# Verify installation
projectsend --version
```

### Arch Linux

```bash
# Install projectsend
sudo pacman -S projectsend

# Enable and start service
sudo systemctl enable --now projectsend

# Verify installation
projectsend --version
```

### Alpine Linux

```bash
# Install projectsend
apk add --no-cache projectsend

# Enable and start service
rc-update add projectsend default
rc-service projectsend start

# Verify installation
projectsend --version
```

### openSUSE/SLES

```bash
# Install projectsend
sudo zypper install -y projectsend

# Enable and start service
sudo systemctl enable --now projectsend

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
projectsend --version
```

### macOS

```bash
# Using Homebrew
brew install projectsend

# Start service
brew services start projectsend

# Verify installation
projectsend --version
```

### FreeBSD

```bash
# Using pkg
pkg install projectsend

# Enable in rc.conf
echo 'projectsend_enable="YES"' >> /etc/rc.conf

# Start service
service projectsend start

# Verify installation
projectsend --version
```

### Windows

```bash
# Using Chocolatey
choco install projectsend

# Or using Scoop
scoop install projectsend

# Verify installation
projectsend --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/projectsend

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
projectsend --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable projectsend

# Start service
sudo systemctl start projectsend

# Stop service
sudo systemctl stop projectsend

# Restart service
sudo systemctl restart projectsend

# Check status
sudo systemctl status projectsend

# View logs
sudo journalctl -u projectsend -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add projectsend default

# Start service
rc-service projectsend start

# Stop service
rc-service projectsend stop

# Restart service
rc-service projectsend restart

# Check status
rc-service projectsend status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'projectsend_enable="YES"' >> /etc/rc.conf

# Start service
service projectsend start

# Stop service
service projectsend stop

# Restart service
service projectsend restart

# Check status
service projectsend status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start projectsend
brew services stop projectsend
brew services restart projectsend

# Check status
brew services list | grep projectsend
```

### Windows Service Manager

```powershell
# Start service
net start projectsend

# Stop service
net stop projectsend

# Using PowerShell
Start-Service projectsend
Stop-Service projectsend
Restart-Service projectsend

# Check status
Get-Service projectsend
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream projectsend_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name projectsend.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name projectsend.example.com;

    ssl_certificate /etc/ssl/certs/projectsend.example.com.crt;
    ssl_certificate_key /etc/ssl/private/projectsend.example.com.key;

    location / {
        proxy_pass http://projectsend_backend;
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
    ServerName projectsend.example.com
    Redirect permanent / https://projectsend.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName projectsend.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/projectsend.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/projectsend.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend projectsend_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/projectsend.pem
    redirect scheme https if !{ ssl_fc }
    default_backend projectsend_backend

backend projectsend_backend
    balance roundrobin
    server projectsend1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R projectsend:projectsend /etc/projectsend
sudo chmod 750 /etc/projectsend

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status projectsend

# View logs
sudo journalctl -u projectsend -f

# Monitor resource usage
top -p $(pgrep projectsend)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/projectsend"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/projectsend-backup-$DATE.tar.gz" /etc/projectsend /var/lib/projectsend

echo "Backup completed: $BACKUP_DIR/projectsend-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop projectsend

# Restore from backup
tar -xzf /backup/projectsend/projectsend-backup-*.tar.gz -C /

# Start service
sudo systemctl start projectsend
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u projectsend -n 100
sudo tail -f /var/log/projectsend/projectsend.log

# Check configuration
projectsend --version

# Check permissions
ls -la /etc/projectsend
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep projectsend)

# Check disk I/O
iotop -p $(pgrep projectsend)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  projectsend:
    image: projectsend:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/projectsend
      - ./data:/var/lib/projectsend
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update projectsend

# Debian/Ubuntu
sudo apt update && sudo apt upgrade projectsend

# Arch Linux
sudo pacman -Syu projectsend

# Alpine Linux
apk update && apk upgrade projectsend

# openSUSE
sudo zypper update projectsend

# FreeBSD
pkg update && pkg upgrade projectsend

# Always backup before updates
tar -czf /backup/projectsend-pre-update-$(date +%Y%m%d).tar.gz /etc/projectsend

# Restart after updates
sudo systemctl restart projectsend
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/projectsend

# Clean old logs
find /var/log/projectsend -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/projectsend
```

## Additional Resources

- Official Documentation: https://docs.projectsend.org/
- GitHub Repository: https://github.com/projectsend/projectsend
- Community Forum: https://forum.projectsend.org/
- Best Practices Guide: https://docs.projectsend.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
