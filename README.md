Lustre File System Setup Guide

Overview :
 
This guide provides step-by-step instructions for setting up Lustre on a server, client, and Object Storage Server (OSS).

Prerequisites :
- Ensure all nodes have a compatible Linux distribution (e.g., RHEL ) same linux kernel sould be compatible with lustre kernel version.
- Install required dependencies and kernel modules.
- Configure networking and firewall rules.
- Format and partition storage devices appropriately for oss and mgs.

1. Installing Lustre Packages:
 
Lustre is not available in default RHEL repositories, so you need to install it manually.We need to download required packages for server from below links .

https://downloads.whamcloud.com/public/lustre/lustre-2.15.5/el8.10/server/RPMS/x86_64/  

https://downloads.whamcloud.com/public/e2fsprogs/1.47.1.wc1/el8/RPMS/x86_64/ 

Run the following command on all nodes:

sudo yum install -y lustre lustre-modules e2fsprogs e2fsprogs-lustre

Download and Install all the dependencies required like EPEL , development tools and headers .

2. Configuring the Lustre Metadata Server (MDS):
 
Formatting the Metadata Target (MDT)

mkfs.lustre --mdt --fsname=lustre --mgs --index=0 /dev/sdb

Mounting the MDS

mkdir -p /mnt/mds
mount -t lustre /dev/sdb /mnt/mds

Check the firewall for server ,client ,oss it should be disabled 

sudo systemctl stop firewalld
sudo systemctl disable firewalld

Verify Lustre Service

lctl list_nids

Verify MGS and MDS Setup

sudo lctl dl

3. Configuring the Object Storage Server (OSS)

 Formatting the Object Storage Target (OST)
 
 Put the IP address of the MGS .

mkfs.lustre --ost --fsname=lustre --mgsnode=<MGS_IP> --index=0 /dev/sdc

 Mounting the OST
 
 mkdir -p /mnt/ost
 
Add to /etc/fstab for auto-mount

echo "/dev/sdc /mnt/ost lustre defaults 0 0" >> /etc/fstab 

mount -t lustre /dev/sdc /mnt/ost

4. Configuring the Lustre Client

Install Lustre Client packages as mentioned in chapter 4 from the below link 

https://downloads.whamcloud.com/public/lustre/lustre-2.15.5/el8.10/client/RPMS/x86_64/  

Installing the Lustre Client Packages
 
sudo yum install -y lustre-client lustre-modules

Mounting the Lustre File System

mkdir -p /mnt/lustre

Add to /etc/fstab for Auto-Mount

echo "/dev/sdb /mnt/mdt lustre defaults 0 0" >> /etc/fstab

mount -t lustre <MGS_IP>@tcp:/lustre /mnt/lustre

Verify the lustre file system mount -

df -h /mnt/lustre

5. Verifying the Setup

List Available Storage Targets on client 

lfs df

Test File Creation

touch /mnt/lustre/testfile
ls -l /mnt/lustre

6. Unmounting and Stopping Lustre Services:

Unmounting Lustre File System

umount /mnt/lustre

Stopping Services

systemctl stop lustre

Conclusion :
 
This document provides basic commands for setting up a Lustre file system. Modify configurations as needed for production environments.

For further troubleshooting and advanced configurations, refer to the official Lustre documentation.


