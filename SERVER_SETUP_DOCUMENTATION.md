# VibeTunnel Server Setup Documentation

**Server**: 91.99.172.64 (Hetzner Cloud ARM cax21)  
**Deployment Date**: July 12, 2025  
**Status**: ✅ OPERATIONAL

---

## 🖥️ **Server Specifications**

### Hardware & Infrastructure
- **Provider**: Hetzner Cloud
- **Server Type**: cax21 (ARM64 architecture)
- **CPU**: 4 cores ARM64
- **Memory**: 8GB RAM
- **Storage**: 80GB SSD
- **Network**: 20TB included traffic/month
- **Location**: Nuremberg, Germany (nbg1-dc3)
- **Monthly Cost**: €6.19

### Operating System
- **OS**: Ubuntu 22.04.4 LTS (Jammy)
- **Kernel**: Linux 5.15.0-143-generic
- **Architecture**: aarch64 (ARM64)

## 🔧 **Software Stack**

### Runtime Environment
- **Node.js**: v20.19.3 (via NodeSource repository)
- **npm**: v10.8.2
- **pnpm**: v9.x (globally installed)
- **Bun**: Latest (installed to ~/.bun/bin/)

### Development Tools
- **Build Tools**: gcc, g++, make, python3-dev
- **Native Dependencies**: libpam0g-dev, libexpat1-dev, zlib1g-dev
- **Version Control**: Git 1:2.34.1-1ubuntu1.15
- **Process Manager**: systemd

### VibeTunnel Application
- **Version**: 1.0.0-beta.10
- **Binary Location**: `/opt/vibetunnel/vibetunnel/web/dist/vibetunnel-linux`
- **System Binary**: `/usr/local/bin/vibetunnel` (wrapper script)
- **Working Directory**: `/opt/vibetunnel/vibetunnel/web/`
- **User**: `vibetunnel` (system user)
- **Port**: 4020

## 🔒 **Security Configuration**

### Firewall (UFW)
```bash
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                  
4020/tcp                   ALLOW       Anywhere                  
22/tcp (v6)                ALLOW       Anywhere (v6)             
4020/tcp (v6)              ALLOW       Anywhere (v6)
```

### User Management
- **Root Access**: SSH key authentication
- **VibeTunnel User**: `vibetunnel:vibetunnel`
- **Home Directory**: `/home/vibetunnel`
- **Shell**: `/bin/bash`

### Authentication
- **SSH**: Public key authentication (root user)
- **VibeTunnel**: PAM system authentication
- **Method**: System user credentials

## 📁 **File System Structure**

### Application Files
```
/opt/vibetunnel/
└── vibetunnel/                    # Git repository
    ├── LINUX_SERVER.md            # Documentation
    ├── PROJECT_SUMMARY.md          # Project summary
    ├── scripts/
    │   └── install-linux.sh       # Installation script
    └── web/                       # Main application
        ├── dist/
        │   ├── vibetunnel-cli      # Original binary
        │   └── vibetunnel-linux    # Linux server binary
        ├── node_modules/           # Dependencies
        ├── public/                 # Web assets
        ├── src/                    # Source code
        └── package.json
```

### System Integration
```
/usr/local/bin/vibetunnel          # System wrapper script
/etc/systemd/system/vibetunnel.service  # Service definition
/var/log/vibetunnel.log            # Application logs
```

### User Data Directories
```
/home/vibetunnel/
└── .vibetunnel/
    ├── control/
    │   └── uploads/               # File uploads
    └── control.sock               # Unix socket
```

## 🚀 **Service Management**

### Systemd Service
- **Name**: `vibetunnel.service`
- **Type**: Simple
- **User**: `vibetunnel`
- **Auto-start**: Enabled
- **Status**: Manual start required (service needs fixing)

### Service Commands
```bash
# Service control
sudo systemctl start vibetunnel
sudo systemctl stop vibetunnel
sudo systemctl restart vibetunnel
sudo systemctl status vibetunnel

# Enable/disable auto-start
sudo systemctl enable vibetunnel
sudo systemctl disable vibetunnel

# View logs
sudo journalctl -u vibetunnel -f
sudo journalctl -u vibetunnel -n 50
```

### Manual Execution
```bash
# Change to application directory
cd /opt/vibetunnel/vibetunnel/web

# Run as vibetunnel user
sudo -u vibetunnel node dist/vibetunnel-linux

# With debug logging
sudo -u vibetunnel VIBETUNNEL_DEBUG=1 node dist/vibetunnel-linux

# Background execution
nohup sudo -u vibetunnel node dist/vibetunnel-linux > /var/log/vibetunnel.log 2>&1 &
```

## 🌐 **Network Configuration**

### Access Information
- **Server IP**: 91.99.172.64
- **SSH Port**: 22
- **VibeTunnel Port**: 4020
- **Web Interface**: http://91.99.172.64:4020

### DNS & SSL
- **Status**: HTTP only (no SSL configured)
- **Domain**: None configured (IP access only)
- **Certificate**: None

## 🔧 **Build Process**

### Native Module Compilation
Successfully compiled on ARM64:
- **node-pty**: Terminal pseudo-terminal support
- **authenticate-pam**: System authentication
- **spawn-helper**: Process spawning utilities

### Build Artifacts
- **Bundle Size**: 93.90 MB (includes Node.js runtime)
- **Web Assets**: Compiled CSS/JS bundles
- **Native Modules**: Working ARM64 binaries

### Build Commands
```bash
# Full build process
cd /opt/vibetunnel/vibetunnel/web
npm install
npm run build

# Development mode
npm run dev:linux
```

## 📊 **Performance & Monitoring**

### Resource Usage
- **Memory**: ~200MB idle, scales with active sessions
- **CPU**: ~5% idle, varies with terminal activity
- **Disk**: ~2GB total application size
- **Network**: Minimal idle, scales with usage

### Monitoring Commands
```bash
# Resource usage
top -u vibetunnel
htop -u vibetunnel
ps aux | grep vibetunnel

# Disk usage
du -sh /opt/vibetunnel/
df -h

# Network connections
netstat -tulpn | grep :4020
ss -tulpn | grep :4020
```

## 🔄 **Backup & Recovery**

### Critical Files to Backup
- **Application**: `/opt/vibetunnel/vibetunnel/`
- **User Data**: `/home/vibetunnel/.vibetunnel/`
- **System Config**: `/etc/systemd/system/vibetunnel.service`
- **SSH Keys**: `/root/.ssh/`

### Recovery Process
1. **Provision new server** with same specifications
2. **Install base dependencies** (Node.js, build tools)
3. **Restore application files** from backup
4. **Configure system service** and firewall
5. **Test functionality** and connectivity

## 🔍 **Troubleshooting**

### Common Issues & Solutions

#### Service Won't Start
```bash
# Check service status
sudo systemctl status vibetunnel

# View detailed logs
sudo journalctl -u vibetunnel -f

# Manual test
cd /opt/vibetunnel/vibetunnel/web
sudo -u vibetunnel node dist/vibetunnel-linux --version
```

#### Web Interface Not Accessible
```bash
# Check if process is running
ps aux | grep vibetunnel

# Test local connection
curl -I localhost:4020

# Check firewall
sudo ufw status

# Check port binding
netstat -tulpn | grep :4020
```

#### SSH Connection Issues
```bash
# Emergency access via Hetzner Console
hcloud server enable-rescue <server-id>
hcloud server reboot <server-id>

# Fix firewall from rescue mode
mount /dev/sda1 /mnt/original
# Edit /mnt/original/etc/ufw/user.rules
```

### Log Locations
- **Application Logs**: `/var/log/vibetunnel.log`
- **System Logs**: `journalctl -u vibetunnel`
- **SSH Logs**: `/var/log/auth.log`
- **Firewall Logs**: `journalctl -u ufw`

## 📋 **Maintenance Checklist**

### Weekly
- [ ] Check server resource usage
- [ ] Review application logs for errors
- [ ] Verify web interface accessibility
- [ ] Check systemd service status

### Monthly
- [ ] Update system packages: `apt update && apt upgrade`
- [ ] Check VibeTunnel for updates
- [ ] Review security logs
- [ ] Test backup/restore procedure

### Quarterly
- [ ] Security audit and updates
- [ ] Performance optimization review
- [ ] Documentation updates
- [ ] Disaster recovery testing

## 🚀 **Future Enhancements**

### Immediate Priorities
- **SSL/HTTPS**: Configure nginx reverse proxy with Let's Encrypt
- **Service Fix**: Resolve systemd service startup issues
- **Monitoring**: Implement basic health checks and alerts

### Medium-term
- **Domain Setup**: Configure custom domain with DNS
- **Backup Automation**: Automated backup to external storage
- **Performance Monitoring**: Detailed metrics collection

### Long-term
- **High Availability**: Multiple server setup with load balancing
- **Container Deployment**: Docker/Kubernetes migration
- **Enterprise Features**: Multi-tenant support, advanced authentication

---

**Last Updated**: July 12, 2025  
**Documentation Version**: 1.0  
**Server Status**: ✅ OPERATIONAL