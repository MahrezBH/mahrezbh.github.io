---
title: "Fixing Docker's 'open sysctl net.ipv4... permission denied' in Proxmox LXC"
date: 2026-01-24
tags: [docker, proxmox, lxc, sysadmin, troubleshooting]
description: "How to resolve the sudden Docker initialization error in unprivileged Proxmox LXC containers caused by recent containerd.io updates."
---

If you run Docker inside an unprivileged Proxmox LXC container, you might have recently hit a frustrating wall. You try to spin up a simple container, even as root, and get slapped with this error:

```bash
root@my-ct:~# docker run hello-world
docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: open sysctl net.ipv4.ip_unprivileged_port_start file: reopen fd 8: permission denied
```

Assuming you already have **Nesting** and **keyctl** enabled in your Proxmox container features (which is step zero for running Docker in LXC), seeing this error means you've run into a specific clash between a recent Docker security patch and Proxmox's AppArmor profiles.

### The Root Cause

Recent updates to Docker's runtime (`containerd.io` versions `1.7.28-2` and newer) included a patch to prevent container escapes. This patch changed how Docker handles file descriptors.

The side effect? When running inside an unprivileged Proxmox LXC, the host's AppArmor profile catches Docker attempting to modify the `net.ipv4.ip_unprivileged_port_start` system file. AppArmor immediately blocks it, crashing the container initialization.

Here are the two ways to fix it, depending on your access level.

---

### Method 1: The AppArmor Bypass (Recommended)

If you have access to the Proxmox host, this is the best permanent fix. It essentially tells the LXC container to run unconfined by AppArmor, bypassing the clash entirely.

1. SSH into your **Proxmox Host** (not the LXC container).
2. Edit your container's configuration file (replace `105` with your actual CT ID):

```bash
nano /etc/pve/lxc/105.conf
```


3. Add these exact two lines to the bottom of the file:

```text
lxc.apparmor.profile: unconfined
lxc.mount.entry: /dev/null sys/module/apparmor/parameters/enabled none bind 0 0
```


4. Restart the container to apply the changes:

```bash
pct stop 105 && pct start 105
```



Docker should now spin up containers without throwing the `sysctl` permission error.

---

### Method 2: Downgrade `containerd.io` (LXC-only Access)

If you only have access to the LXC terminal and can't modify the host configuration, you can temporarily roll back the specific package that introduced the breaking change.

1. Inside your LXC container, find the exact string for the older, working version (usually `1.7.28-1`):

```bash
apt list -a containerd.io
```


2. Install that specific version. (Your string will look something like `1.7.28-1~debian.12~bookworm` or `ubuntu.24.04`, depending on your OS template):

```bash
apt install -y --allow-downgrades containerd.io=<paste-your-exact-string-here>
```


3. Put the package on hold so `apt` doesn't automatically break it again during your next update:

```bash
apt-mark hold containerd.io
```


4. Restart the Docker service:

```bash
systemctl restart docker
```



You are now back in business. Just remember to remove the `apt-mark hold` in the future once the upstream bug is officially resolved!