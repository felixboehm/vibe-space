# VibeTunnel Multi-User Authentication Fix

**Issue Resolved**: Multi-user authentication now working  
**Date**: July 13, 2025

## The Problem

VibeTunnel was configured correctly but only the `vibetunnel` user could authenticate. All other system users received "Invalid username or password" errors despite correct credentials.

## Root Cause

VibeTunnel uses PAM (Pluggable Authentication Modules) for system user authentication. PAM needs to read `/etc/shadow` to verify passwords, but the `vibetunnel` service user lacked the necessary permissions.

## The Solution

Add the vibetunnel user to the `shadow` group:

```bash
sudo usermod -a -G shadow vibetunnel
sudo systemctl restart vibetunnel
```

## Why This Works

- The `shadow` group has read access to `/etc/shadow`
- PAM can now verify passwords for all system users
- No code changes were needed - just a permission fix

## Verification

After applying the fix:
```bash
# Check groups
groups vibetunnel
# Output: vibetunnel : vibetunnel shadow

# Test authentication
curl -X POST http://localhost:4020/api/auth/password \
  -H "Content-Type: application/json" \
  -d '{"userId": "testuser", "password": "<TEST_PASSWORD>"}'
# Response: {"success":true,"userId":"testuser","token":"..."}
```

## Documentation Updated

The following files have been updated with this fix:
1. **LINUX_SERVER.md** - Added shadow group requirement to authentication section
2. **install-linux.sh** - Script now automatically adds user to shadow group
3. **SERVER_SETUP_DOCUMENTATION.md** - Added to user management and troubleshooting
4. **CLAUDE.md** - Added critical learnings section

## Alternative Solutions (Not Implemented)

1. **Custom PAM Service**: Create `/etc/pam.d/vibetunnel` with specific rules
2. **Setuid Wrapper**: Small C program with elevated privileges for auth only
3. **Run as Root**: Not recommended for security reasons

## Security Considerations

Adding to the shadow group allows the vibetunnel process to read all system password hashes. This is acceptable for a terminal server that needs to authenticate users, but consider:

- Running VibeTunnel in a container with limited user access
- Using a dedicated authentication service
- Implementing additional access controls

## Lessons Learned

1. **Always check service user permissions** when PAM authentication fails
2. **Test with multiple users** during initial deployment
3. **Document system requirements** clearly in installation guides
4. **Include permission setup** in automated installation scripts