---
title: "Uploading Files to Hetzner Object Storage with Rclone on Linux"
date: 2024-12-22
categories: [linux, storage]
tags: [hetzner, linux, object-storage, s3]
---

📦 **How to Upload Files to Hetzner Object Storage with Rclone on Linux**

If you've ever tried uploading files to Hetzner Object Storage using tools like Cyberduck or the official `duck` CLI and faced connection issues — you're not alone. The `duck` CLI sometimes struggles with Hetzner’s custom S3-compatible endpoint.

Luckily, **rclone** offers a reliable, flexible alternative that works smoothly on any Linux distribution, including Debian, Arch, Fedora, and others.

This guide will walk you through setting up rclone on your Linux system to upload files easily to your Hetzner bucket.

---

### 🛠️ Step 1: Install Rclone

Depending on your Linux distro, use your package manager to install rclone:

- **Debian/Ubuntu:**

```bash
sudo apt install rclone
````

* **Arch Linux:**

```bash
sudo pacman -S rclone
```

* **Fedora:**

```bash
sudo dnf install rclone
```

* Or download the latest official binary from [rclone.org](https://rclone.org/downloads/).

---

### 🗂️ Step 2: Obtain Your Hetzner Credentials

In the Hetzner Cloud Console, navigate to Projects → Object Storage and note:

* **Bucket name** (e.g., `my-bucket`)
* **Access Key ID**
* **Secret Access Key**
* **Region** (e.g., `hel1`)
* **Endpoint URL** (e.g., `https://hel1.your-objectstorage.com`)

---

### ⚙️ Step 3: Configure Rclone for Hetzner

You can set this up interactively or by editing the config file.

#### Option A: Interactive Setup

Run:

```bash
rclone config
```

* Choose `n` for a new remote
* Name it (e.g., `hetzner`)
* Select `s3` as the storage type
* Choose `Other` as the provider
* Enter the endpoint URL (e.g., `https://hel1.your-objectstorage.com`)
* Enter your Access Key and Secret Key
* Leave region blank or enter `hel1`
* Accept defaults for other options

#### Option B: Edit Config File

Edit or create `~/.config/rclone/rclone.conf` with:

```ini
[hetzner]
type = s3
provider = Other
env_auth = false
access_key_id = YOUR_ACCESS_KEY
secret_access_key = YOUR_SECRET_KEY
endpoint = https://hel1.your-objectstorage.com
region = hel1
acl = private
```

Replace `YOUR_ACCESS_KEY` and `YOUR_SECRET_KEY` accordingly.

---

### 📤 Step 4: Upload a File

Upload your file:

```bash
rclone copy ./yourfile.pdf hetzner:my-bucket
```

---

### 📁 Step 5: List Files in Your Bucket

Check stored files with:

```bash
rclone ls hetzner:my-bucket
```

---

### 🛡️ Pro Tips

* **Security:** Never expose keys in public scripts.
* **Automation:** Use cron jobs to automate backups.
* **Progress:** Add `--progress` to monitor uploads live.
* **Cleanup:** Revoke old keys promptly in Hetzner Console.

---

### ✅ Summary

Rclone is a powerful, distro-agnostic tool for working with Hetzner Object Storage. Whether you're on Debian, Arch, Fedora, or another Linux flavor, rclone ensures your file uploads are smooth, secure, and easily automated.