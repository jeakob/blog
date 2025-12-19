---
title: "Dynamic Resolution Setup for Alpine Linux on UTM (macOS M1/M2/M3)"
date: 2025-12-18T22:00:00+00:00
tags: ["alpine", "utm"]
draft: false
---

This guide explains how to enable automatic resolution changes for Alpine Linux with LXQt running in UTM on Apple Silicon Macs.

## The Problem

When running Alpine Linux in UTM on Apple Silicon, resizing the UTM window doesn't automatically adjust the guest display resolution. This is because the SPICE guest tools need to be properly configured.

## The Solution

Alpine Linux requires two separate packages for SPICE functionality:
1. `spice-vdagent` - The client application
2. `spice-vdagent-openrc` - The OpenRC service scripts for the daemon

### Step 1: Install Required Packages

```bash
# Install both the agent and OpenRC service files
apk add spice-vdagent spice-vdagent-openrc
```

### Step 2: Start and Enable the Daemon

```bash
# Start the spice-vdagentd daemon (note the 'd' at the end)
rc-service spice-vdagentd start

# Verify it's running
rc-service spice-vdagentd status

# Enable it to start automatically at boot
rc-update add spice-vdagentd default
```

### Step 3: Verify the Socket Exists

```bash
# Check that the daemon socket was created
ls -la /run/spice-vdagentd/
```

You should see `spice-vdagent-sock` in the output.

### Step 4: Restart Your Desktop Session

Log out of LXQt and log back in, or reboot the VM:

```bash
reboot
```

## How to Test

After completing the setup:
1. Resize the UTM window
2. The Alpine Linux desktop resolution should automatically adjust to match

## Troubleshooting

### Check if the daemon is running:
```bash
ps aux | grep spice-vdagentd
```

### Check for errors in logs:
```bash
dmesg | grep spice
# or
grep spice /var/log/messages
```

### Common Error Messages

If you see errors like:
```
Failed to connect with spice-vdagentd due 'Could not connect: No such file or directory'
```

This means the `spice-vdagentd` daemon is not running. Make sure you've installed `spice-vdagent-openrc` and started the service.

## Why This Works

- **spice-vdagent**: The client that runs in your X session and communicates display changes
- **spice-vdagentd**: The daemon that bridges between the SPICE protocol and the client
- **spice-vdagent-openrc**: Contains the OpenRC init scripts needed to run the daemon as a service

The client cannot function without the daemon running, which is why both packages are required.

## System Information

- **OS**: Alpine Linux
- **Desktop Environment**: LXQt (also works with other X11-based DEs)
- **Virtualization**: UTM on macOS (Apple Silicon M1/M2/M3)
- **Backend**: QEMU or Apple Virtualization Framework

---

**Last Updated**: December 19, 2025
