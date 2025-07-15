# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **documentation workspace** for the VibeTunnel Linux deployment project, not the actual VibeTunnel source code. The repository contains:
- Comprehensive documentation about adapting VibeTunnel from macOS to Linux
- Deployment guides and server configuration
- Technical learnings and TODO lists
- One custom script: `vt-linux` (VibeTunnel CLI wrapper)
- **Vibe Agents**: AI agent templates and project setup framework (in `vibe-agents/` directory)

## Key Information

### Server Access
- **Production Server**: <YOUR_SERVER_IP> (Hetzner Cloud ARM cax21)
- **SSH**: `ssh root@<YOUR_SERVER_IP>`
- **Web Interface**: http://<YOUR_SERVER_IP>:4020
- **Service**: VibeTunnel is deployed as a systemd service

### Repository Links
- **This Documentation Workspace**: https://github.com/felixboehm/vibe-space
- **VibeTunnel Fork (actual code)**: https://github.com/felixboehm/vibetunnel
- **Original VibeTunnel**: https://github.com/amantus-ai/vibetunnel

### Current Server Status
- **VibeTunnel Version**: v1.0.0-beta.10
- **Service Status**: ✅ Active and running with SSH key authentication enabled
- **Installation Path**: `/opt/vibetunnel/vibetunnel/web/`
- **Binary**: `vibetunnel-linux-cli` (includes both server and client functionality)
- **VT Command**: ✅ Installed at `/usr/local/bin/vt`

## Working with VibeTunnel Code

Since this is a documentation repository, actual VibeTunnel development happens in the fork. To work with VibeTunnel:

### Building VibeTunnel (on target server)
```bash
# Clone the actual repository
git clone https://github.com/felixboehm/vibetunnel.git
cd vibetunnel/web

# Install dependencies and build
pnpm install  # VibeTunnel uses pnpm as package manager
pnpm run build

# Outputs:
# - dist/vibetunnel-cli (original macOS binary)
# - dist/vibetunnel-linux (server-only)
# - dist/vibetunnel-linux-cli (server + client with vt command)
```

### Running VibeTunnel
```bash
# Development mode
cd vibetunnel/web
pnpm run dev        # Full development mode (client + server)
pnpm run dev:linux  # Linux server development mode

# Production (as service)
sudo systemctl start vibetunnel
sudo systemctl status vibetunnel
sudo journalctl -u vibetunnel -f

# Manual execution
cd /opt/vibetunnel/vibetunnel/web
VIBETUNNEL_DEBUG=1 node dist/vibetunnel-linux-cli --enable-ssh-keys
```

## Architecture Overview

### VibeTunnel Components
1. **Web Server** (Node.js/Express) - Handles HTTP/WebSocket connections
2. **Terminal Management** (node-pty) - Creates and manages PTY sessions
3. **Authentication** (PAM) - System user authentication
4. **Web Frontend** (Lit/xterm.js) - Browser-based terminal interface
5. **VT Command** - CLI tool for creating monitored terminal sessions

### Technology Stack
- **Language**: TypeScript
- **Runtime**: Node.js v20+ (currently v20.19.3)
- **Package Manager**: pnpm
- **Frontend**: Lit Web Components + xterm.js
- **Build Tool**: esbuild
- **Testing**: Vitest (unit) + Playwright (e2e)
- **CSS**: Tailwind CSS
- **Real-time**: WebSocket + Server-Sent Events

### Key Technical Details
- **Native Modules**: Requires `node-pty` and `authenticate-pam` (compiled for ARM64)
- **Authentication Precedence**: Environment vars override PAM (limits to single user)
- **Socket Communication**: Uses Unix domain sockets at `~/.vibetunnel/control.sock`
- **Session Management**: Each user has isolated sessions and socket paths
- **Process Model**: Main server process spawns child processes for each terminal session
- **Binary Optimization**: Uses buffer-based updates for terminal output efficiency

## Documentation Structure

This repository contains these key documents:
- `PROJECT_SUMMARY.md` - Complete overview of the Linux deployment
- `LINUX_SERVER.md` - Installation and usage guide
- `SERVER_SETUP_DOCUMENTATION.md` - Server configuration details
- `VIBETUNNEL_TODO_LIST.md` - Current issues and improvements needed
- `LINUX_BUILD_LEARNINGS.md` - Technical insights and architecture details
- `vt-linux` - Custom bash script implementing the vt command for Linux

## Common Tasks

### Updating Documentation
When updating documentation in this repository:
1. Ensure consistency across all markdown files
2. Update README.md if adding new documentation files
3. Keep server information current (IP, status, configuration)

### Working with the vt-linux Script
The `vt-linux` script is a bash wrapper that:
- Forwards commands through VibeTunnel for web visibility
- Handles shell-specific nuances (bash vs zsh)
- Manages terminal titles
- Should be installed to `/usr/local/bin/vt` on the server

### Checking Server Status
```bash
# SSH to server
ssh root@<YOUR_SERVER_IP>

# Check VibeTunnel service
sudo systemctl status vibetunnel

# View logs
sudo journalctl -u vibetunnel -n 50

# Check web interface
curl -I localhost:4020
```

## Current Issues (from TODO list)

1. **Authentication**: ✅ RESOLVED - Required adding vibetunnel user to shadow group
2. **VT Command**: ✅ RESOLVED - Installed and working at `/usr/local/bin/vt`
3. **Service Stability**: ✅ RESOLVED - Service is running stably with auto-restart
4. **No SSL/HTTPS**: Production deployment needs SSL configuration
5. **SSH Key Auth**: ✅ RESOLVED - Enabled via `--enable-ssh-keys` flag
6. **Session Closing**: Under investigation - may be related to SSE connection limits

## Critical Learnings

### Multi-User Authentication Fix
VibeTunnel uses PAM authentication which requires the service user to read `/etc/shadow`. The fix:
```bash
sudo usermod -a -G shadow vibetunnel
sudo systemctl restart vibetunnel
```
This is now included in the installation script.

## Environment Variables

Key environment variables for VibeTunnel:
- `PORT` - Server port (default: 4020)
- `HOST` - Bind address (default: 0.0.0.0)
- `VIBETUNNEL_DEBUG` - Enable debug logging
- `VIBETUNNEL_USERNAME`/`VIBETUNNEL_PASSWORD` - Override PAM auth (single user only) - DO NOT SET for multi-user
- `NO_AUTH` - Disable authentication (development only)
- `NODE_ENV` - Set to 'production' for production deployments

## Development Scripts

Common pnpm scripts in the VibeTunnel codebase:
- `pnpm run dev` - Start development server with hot reload
- `pnpm run build` - Create production build
- `pnpm run lint` - Run linting with Biome
- `pnpm run format` - Format code with Biome
- `pnpm run test` - Run unit tests with Vitest
- `pnpm run test:e2e` - Run end-to-end tests with Playwright
- `pnpm run clean` - Clean build artifacts

## VibeTunnel Directory Structure

```
vibetunnel/web/
├── src/
│   ├── cli.ts              # Main CLI entry point
│   ├── linux-server.ts     # Linux-specific server
│   ├── server/             # Backend code
│   │   ├── routes/         # API endpoints
│   │   ├── services/       # Core services
│   │   └── websocket/      # WebSocket handlers
│   ├── client/             # Frontend code
│   │   ├── components/     # Lit components
│   │   └── services/       # Client services
│   └── shared/             # Shared utilities
├── scripts/                # Build scripts
├── test/                   # Test files
└── dist/                   # Built binaries
```

## Vibe Agents Framework

The `vibe-agents/` directory contains templates and standards for working with AI agents in projects. This framework provides:

### Purpose
- **Standard Templates**: Common project setups for different methodologies (Scrum, Agile, simple projects)
- **AI Agent Integration**: Best practices for integrating AI agents into development workflows
- **Project Setup**: Standardized approaches for initializing new projects with AI assistance
- **Workflow Descriptions**: Documented processes for agent-assisted development
- **Role Definitions**: Clear definitions of agent roles and responsibilities

### Usage in Projects
- Each project can adopt and adapt these templates to their specific needs
- Provides a baseline for AI-assisted development workflows
- Can be extended with project-specific customizations
- Serves as a reference for best practices in agent collaboration

### Key Components
- **Project Templates**: Pre-configured setups for different project types
- **Workflow Documentation**: Step-by-step guides for common development tasks
- **Agent Roles**: Defined responsibilities for different types of AI agents
- **Integration Patterns**: How to effectively integrate agents into existing workflows

This framework will be continuously enhanced and developed within this workspace to provide better standards and options for AI-assisted project development.