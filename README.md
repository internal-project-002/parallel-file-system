# 🗂️ Lustre File System Setup Guide

## 📖 Overview
This guide provides step-by-step instructions for setting up Lustre on a server, client, and Object Storage Server (OSS).

---

## ✅ Prerequisites
- Ensure all nodes have a compatible Linux distribution (e.g., RHEL) with a compatible kernel version for Lustre.
- Install required dependencies and kernel modules.
- Configure networking and firewall rules.
- Format and partition storage devices appropriately for OSS and MGS.

---

## 📦 1. Installing Lustre Packages
Lustre is not available in default RHEL repositories, so you need to install it manually. Download the required packages for the server from the links below:

- [Lustre Server Packages](https://downloads.whamcloud.com/public/lustre/lustre-2.15.5/el8.10/server/RPMS/x86_64/)
- [e2fsprogs Packages](https://downloads.whamcloud.com/public/e2fsprogs/1.47.1.wc1/el8/RPMS/x86_64/)

### 🧾 Run the following command on all nodes:
```bash
sudo yum install -y lustre lustre-modules e2fsprogs e2fsprogs-lustre
```

Download and install all the dependencies required like EPEL, development tools, and headers.

---

## 🖥️ 2. Configuring the Lustre Metadata Server (MDS)

### 🗃️ Formatting the Metadata Target (MDT)
```bash
mkfs.lustre --mdt --fsname=lustre --mgs --index=0 /dev/sdb
```

### 📂 Mounting the MDS
```bash
mkdir -p /mnt/mds
mount -t lustre /dev/sdb /mnt/mds
```

### 🔥 Disable Firewall on Server, Client, OSS
```bash
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```

### 🧪 Verify Lustre Service
```bash
lctl list_nids
```

### ✅ Verify MGS and MDS Setup
```bash
sudo lctl dl
```

---

## 💾 3. Configuring the Object Storage Server (OSS)

### 📄 Formatting the Object Storage Target (OST)
Replace `<MGS_IP>` with the actual IP address of the MGS.
```bash
mkfs.lustre --ost --fsname=lustre --mgsnode=<MGS_IP> --index=0 /dev/sdc
```

### 📂 Mounting the OST
```bash
mkdir -p /mnt/ost

# Add to /etc/fstab for auto-mount
echo "/dev/sdc /mnt/ost lustre defaults 0 0" >> /etc/fstab

mount -t lustre /dev/sdc /mnt/ost
```

---

## 🖥️ 4. Configuring the Lustre Client

### 📦 Installing Lustre Client Packages
Refer to Chapter 4 from the link below for the Lustre client packages:

- [Lustre Client Packages](https://downloads.whamcloud.com/public/lustre/lustre-2.15.5/el8.10/client/RPMS/x86_64/)

```bash
sudo yum install -y lustre-client lustre-modules
```

### 📂 Mounting the Lustre File System
```bash
mkdir -p /mnt/lustre

# Add to /etc/fstab for Auto-Mount
echo "/dev/sdb /mnt/mdt lustre defaults 0 0" >> /etc/fstab

mount -t lustre <MGS_IP>@tcp:/lustre /mnt/lustre
```

### ✅ Verify Lustre File System Mount
```bash
df -h /mnt/lustre
```

---

## 🔍 5. Verifying the Setup

### 📋 List Available Storage Targets on Client
```bash
lfs df
```

### 🛠️ Test File Creation
```bash
touch /mnt/lustre/testfile
ls -l /mnt/lustre
```

---

## 🚫 6. Unmounting and Stopping Lustre Services

### 📤 Unmounting Lustre File System
```bash
umount /mnt/lustre
```

### 🛑 Stopping Services
```bash
systemctl stop lustre
```

---

## 📌 Conclusion
This document provides basic commands for setting up a Lustre file system. Modify configurations as needed for production environments.

For further troubleshooting and advanced configurations, refer to the official Lustre documentation.

---

