Project7

### Implement a 3 tier web application architecture with a single database and an  NFS serveras a shared files and storage solution that consists of following components:

1.  Infrastructure: AWS
2.  Webserver Linux: Red Hat Enterprise Linux 8
3.  Database Server: Ubuntu 20.04 + MySQL
4.  Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5.  Programming Language: PHP
6.  Code Repository: GitHub

### Step 1 Prepare NFS Server through the listed procedures below 

1.  Spin up a new EC2 instance with RHEL Linux 8 Operating System and provide ssh remote access.
    Then create 2 EBS volume of 5G each and attached it to the Ec2 instance.
    - run `lsblk`  to inspect what block devices are attached a the server
    - run `df -h`  to see all mounts and free space on a server

2.  Create a single partition using {cfdisk} on all the newly created storage and LVM as partition type. 
    - run `sudo cfdisk /dev/xvdf` 
    - run `sudo cfdisk /dev/xvdg` 
    - run `lsblk` to view the created partitions
    - run `df -h` to view all mount location

3.  Install lvm2 package and check for available partitions. 
    - run `sudo yum install lvm2` installing the LVM package manager 
    - run `sudo lvmdiskscan`

4.  Mark all disks as physical volumes (PVs) to be used by Logical Volume Manager(LVM)
    - run `sudo pvcreate /dev/xvdf1` making physical volume from partiton
    - run `sudo pvcreate /dev/xvdg1` making physical volume from partition

5.  Verify that your Physical volume has been created successfully by running sudo pvs
    - run `sudo pvs`

6.  Use vgcreate utility to add all PVs to a volume group (VG) and name the VG nfsdata-vg then verify
    that (VG) has been created.
    - run `sudo vgcreate nfsdata-vg /dev/xvdh1 /dev/xvdg1`
    - run `sudo pvs`

7.  Use lvcreate utility to create  3 Logical Volumes. lv-opt, lv-apps and lv-logs. lv-apps and lv-opt
    is used by webservers and lv-log is used by Jenkins.
    - run `sudo lvcreate -n lv-apps -L 8G nfsdata-vg`
    - run `sudo lvcreate -n lv-opt -L 8G nfsdata-vg`
    - run `sudo lvcreate -n lv-logs -L 4G nfsdata-vg`
    - run `sudo lvs`
    - run `sudo vgdisplay -v` view complete setup - VG, PV, and LV
    - run `sudo lsblk` 

8.  Format the logical volumes (LV) with xfs lv-apps, lv-opt and lv-logs
    - run `sudo mkfs -t xfs /dev/nfsdata-vg/lv-apps`
    - run `sudo mkfs -t xfs /dev/nfsdata-vg/lv-opt`
    - run `sudo mkfs -t xfs /dev/nfsdata-vg/lv-logs`

9.  Create mount location /omt on NFS server for Mount `lv-apps on /mnt/apps` – To be used by webservers
    Mount `lv-logs on /mnt/logs` – To be used by webserver logs Mount `lv-opt on /mnt/opt` – To be used by Jenkins server in  Project 8.
    - run `sudo mkdir -p /mnt/apps`
    - run `sudo mkdir -p /mnt/opt`
    - run `sudo mkdir -p /mnt/logs`

10. Mount LV to the mount location created and update /etc/fstab file so that the mount configuration 
    will persist after restart of the server.
    - run `sudo mount /dev/nfsdata-vg/lv-apps /mnt/apps`
    - run `sudo mount /dev/nfsdata-vg/lv-opt /mnt/opt`
    - run `sudo mount /dev/nfsdata-vg/lv-logs /mnt/logs`
    - run `df -h` to verify the mounts
    - run `sudo blkid`
    - run `sudo vi /etc/fstab` update the blkid and save the file.

11. Install NFS server, configure it to start on reboot and make sure it is up and running.
    - run `sudo yum -y update`
    - run `sudo yum install nfs-utils -y`
    - run `sudo systemctl start nfs-server.service`
    - run `sudo systemctl enable nfs-server.service`
    - run `sudo systemctl status nfs-server.service`
