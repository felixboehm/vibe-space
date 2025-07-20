# Agent setup (draft)

A standalone Linux server deployment with claude code and some tools.
- use hcloud to spawn a server
- Setup firewall: allow ssh and a dev port
- 

## Quick Start

### Hetzner Cloud Setup

1. **Create Server:**
   ```bash
   hcloud server create --image ubuntu-22.04 --type cax21 --name vibetunnel-server --ssh-key <your-key> --location nbg1
   ```

2. **SSH into server:**
   ```bash
   ssh root@<server-ip>
   ```

### Server Installation

```bash
# Update system
apt update && apt upgrade -y

# Install Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt-get install -y nodejs


# Install build tools
apt install -y build-essential unzip curl git

# Install Bun
curl -fsSL https://bun.sh/install | bash
source ~/.bashrc

# Create user and add to sudoers to run claude

# Add Claude auth token

```

## Security Considerations

### Firewall Configuration

```bash
# Allow SSH and Vite server port
ufw allow 22
ufw allow 5173
ufw enable
```
