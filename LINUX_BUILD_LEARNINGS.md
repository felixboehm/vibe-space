# VibeTunnel Linux Build Learnings

This document captures key insights and technical details learned during the adaptation of VibeTunnel for standalone Linux server deployment.

## Architecture Overview

VibeTunnel consists of three main components:
1. **Mac App (Swift/SwiftUI)** - Native macOS application that manages the entire system
2. **Web Server (Node.js/Bun)** - Backend server handling API requests and WebSocket connections
3. **Web Frontend (TypeScript/LitElement)** - Browser-based terminal interface

### Linux Adaptation Challenge

The original VibeTunnel was designed as a hybrid macOS app where:
- The Mac app spawns and manages the web server process
- The `vt` command relies on the Mac app bundle structure
- The `fwd` (forward) functionality connects to the app-managed server

For Linux deployment, we needed to create a standalone server that could operate independently.

## Build System Architecture

### Entry Points

VibeTunnel has multiple entry points depending on the deployment target:

1. **`src/cli.ts`** - Main entry point with both server and `fwd` functionality
   - Used for the native macOS binary
   - Handles subcommands: default (server) and `fwd` (client)
   - Single binary with dual functionality

2. **`src/linux-server.ts`** - Server-only entry point for Linux
   - Removes macOS-specific dependencies
   - Optimized for standalone server deployment
   - No `fwd` client functionality

3. **`src/server/fwd.ts`** - Forward/client functionality
   - Creates monitored terminal sessions
   - Connects to existing VibeTunnel server via Unix sockets
   - Handles session management and I/O forwarding

### Build Targets

The build system creates multiple outputs:

```javascript
// From scripts/build.js
{
  // Server + Client (full functionality)
  'dist/cli.js': 'src/cli.ts',
  
  // Server-only (Linux deployment)
  'dist/vibetunnel-linux': 'src/linux-server.ts',
  
  // Server + Client for Linux (our addition)
  'dist/vibetunnel-linux-cli': 'src/cli.ts'
}
```

### Key Discovery: Missing Linux CLI Binary

**Problem**: The original build only created `vibetunnel-linux` (server-only) for Linux, but the `vt` command requires the `fwd` functionality.

**Solution**: We added a new build target `vibetunnel-linux-cli` that bundles the full `cli.ts` entry point for Linux, providing both server and client functionality.

## Native Module Challenges

### PAM Authentication

Linux deployment requires PAM (Pluggable Authentication Modules) for system user authentication:

```bash
# Required system packages
apt install -y libpam0g-dev python3-dev
```

The `authenticate-pam` npm package provides native bindings but requires:
- Proper native compilation for ARM64 architecture
- PAM development headers
- Python development tools (for node-gyp)

### Node-PTY Compilation

Terminal emulation requires native bindings via `node-pty`:
- Must be compiled for the target architecture (ARM64 in our case)
- Requires native build tools and headers
- Works correctly with Node.js v20.19.3 on Ubuntu 22.04

## Authentication Architecture Deep Dive

### Environment Variable Override Issue

**Critical Discovery**: VibeTunnel's authentication has a precedence system:

```typescript
// From auth-service.ts:104-120
async authenticateWithPassword(userId: string, password: string): Promise<AuthResult> {
  // Check environment variables first (for testing and simple deployments)
  const envUsername = process.env.VIBETUNNEL_USERNAME;
  const envPassword = process.env.VIBETUNNEL_PASSWORD;

  if (envUsername && envPassword) {
    // Use environment variable authentication - SINGLE USER ONLY
    if (userId === envUsername && password === envPassword) {
      return { success: true, userId, token: this.generateToken(userId) };
    } else {
      return { success: false, error: 'Invalid username or password' };
    }
  }

  // Fall back to PAM authentication - MULTI USER
  const isValid = await this.verifyPAMCredentials(userId, password);
  // ...
}
```

**Issue**: If `VIBETUNNEL_USERNAME` and `VIBETUNNEL_PASSWORD` are set, only that specific user can authenticate, overriding PAM multi-user support.

**Lesson**: For multi-user deployments, ensure these environment variables are NOT set.

## VT Command Implementation

### macOS vs Linux Differences

**macOS Implementation**:
- Relies on VibeTunnel.app bundle structure
- Looks for binary at `/Applications/VibeTunnel.app/Contents/Resources/vibetunnel`
- Uses the app-managed server instance

**Linux Implementation**:
- Uses standalone binary at `/opt/vibetunnel/vibetunnel/web/dist/vibetunnel-linux-cli`
- Connects to systemd-managed server instance
- Must handle different user contexts and socket paths

### Shell Resolution Logic

Both implementations share complex shell resolution logic:

```bash
# Shell-specific alias/function handling
case "$shell_name" in
    zsh)
        # Interactive mode required for zsh aliases
        exec "$VIBETUNNEL_BIN" fwd "$user_shell" -i -c "$(printf '%q ' "$cmd" "$@")"
        ;;
    bash)
        # Non-interactive mode with alias expansion
        exec "$VIBETUNNEL_BIN" fwd "$user_shell" -c "shopt -s expand_aliases; source ~/.bashrc; ..."
        ;;
esac
```

## Socket Communication Architecture

### Unix Socket IPC

VibeTunnel uses Unix sockets for inter-process communication:

```typescript
// Socket paths are user-specific
const socketPath = path.join(os.homedir(), '.vibetunnel', 'control.sock');
const sessionSocketPath = path.join(controlPath, sessionId, 'ipc.sock');
```

**Challenge**: Different users have different socket paths:
- Server running as `vibetunnel` user: `/home/vibetunnel/.vibetunnel/control.sock`
- Commands run as `root`: `/root/.vibetunnel/control.sock`

**Solution**: The `fwd` client creates its own session management and connects appropriately.

## Configuration Management

### Command Line Arguments

VibeTunnel supports extensive configuration via CLI arguments:

```typescript
interface Config {
  port: number;
  bind: string;
  enableSSHKeys: boolean;           // --enable-ssh-keys
  disallowUserPassword: boolean;    // --disallow-user-password
  noAuth: boolean;                  // --no-auth
  // ... many more options
}
```

**Key Insight**: Arguments are parsed in `server.ts:parseArgs()` and used throughout the application.

### Systemd Integration

For production deployment, systemd service configuration is crucial:

```ini
[Service]
Type=simple
User=vibetunnel
Group=vibetunnel
WorkingDirectory=/opt/vibetunnel/vibetunnel/web
ExecStart=/usr/bin/node dist/vibetunnel-linux --enable-ssh-keys
Environment=NODE_ENV=production
Environment=PORT=4020
Environment=HOST=0.0.0.0
```

**Learning**: Direct `node` execution in systemd works better than wrapper scripts for reliability.

## Performance and Resource Considerations

### ARM64 Compatibility

VibeTunnel works excellently on ARM64 architecture:
- Native modules compile correctly
- Performance is suitable for terminal workloads
- Hetzner CAX21 (4 vCPU, 8GB RAM) handles multiple concurrent sessions well

### Memory Usage

Typical memory footprint:
- Base server: ~30-40MB
- Per session: ~2-5MB additional
- Node.js overhead: ~20-30MB

### Build Time Optimizations

```javascript
// esbuild configuration for optimal Linux builds
{
  bundle: true,
  platform: 'node',
  target: 'node18',
  format: 'cjs',
  minify: true,
  sourcemap: false,  // Reduces bundle size significantly
  external: [
    'node-pty',           // Native module, don't bundle
    'authenticate-pam',   // Native module, don't bundle
  ]
}
```

## Security Learnings

### Authentication Precedence

Understanding the authentication flow is critical:
1. Environment variables (single user)
2. PAM system authentication (multi-user)
3. SSH key authentication (if enabled)

### Firewall Considerations

UFW configuration must account for:
- SSH access (port 22) - crucial for maintenance
- VibeTunnel web interface (port 4020)
- Potential nginx reverse proxy (ports 80/443)

**Critical Learning**: Always ensure SSH access before enabling firewall:
```bash
ufw allow 22  # MUST be first
ufw allow 4020
ufw enable
```

## Debugging and Troubleshooting

### Log Analysis

VibeTunnel has comprehensive logging:
- Server logs via `console.log` (captured by systemd)
- Session logs in control directories
- Authentication logs via PAM

### Common Issues

1. **Native module compilation failures**
   - Solution: Install PAM dev packages and Python dev tools

2. **Authentication only works for one user**
   - Solution: Check for `VIBETUNNEL_USERNAME`/`VIBETUNNEL_PASSWORD` env vars

3. **VT command not found**
   - Solution: Ensure `vibetunnel-linux-cli` binary exists and `vt` script points to it

4. **Sessions not visible in web interface**
   - Solution: Check Unix socket permissions and paths

## Future Improvements

### Containerization

The Linux build could be containerized:
- Docker image with pre-compiled native modules
- Simplified deployment via container orchestration
- Better isolation and security

### Package Distribution

Consider creating distribution packages:
- `.deb` packages for Ubuntu/Debian
- `.rpm` packages for RHEL/CentOS
- Snap packages for universal Linux support

### Native Binary

Explore Node.js SEA (Single Executable Application):
- Single binary with embedded Node.js runtime
- Eliminates Node.js installation requirement
- Simplifies deployment and distribution

## Conclusion

The Linux adaptation revealed VibeTunnel's well-architected, modular design. The main challenges were:

1. **Understanding the dual-nature** of the original macOS binary (server + client)
2. **Creating appropriate build targets** for Linux deployment
3. **Navigating authentication precedence** and multi-user support
4. **Implementing proper VT command functionality** via the CLI binary

The successful Linux deployment demonstrates VibeTunnel's potential as a cross-platform terminal sharing solution, not just a macOS-specific tool.