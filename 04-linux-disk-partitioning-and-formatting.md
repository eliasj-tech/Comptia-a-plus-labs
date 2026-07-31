# Lab 04: Disk Partitioning and Formatting on Linux

* **Date:** July 28, 2026
* **Objective:** Learn to identify block devices, manage partition tables using `fdisk`, allocate a Linux Swap partition, and format a volume with the `ext4` file system using `mkfs`.

---

## Part 1: Investigating the Disk Structure

### Step 1.1: Identify Block Devices
* **What I Did:** I used the `lsblk` (list block devices) command to view all storage drives and their current mount points.
  ```bash
  lsblk
  ```
* **Output Received:**
  ```text
  NAME      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
  sda         8:0    0   10G  0 disk 
  |-sda1      8:1    0  4.9G  0 part 
  ... [12 total default partitions]
  sdb         8:16   0   10G  0 disk 
  |-sdb1      8:17   0  5.8G  0 part /etc/hosts
  ... [12 total default partitions]
  ```
* **Observation:** The system has two 10GB disks (`sda` and `sdb`). The `sdb` disk is currently "Active" and mounted to `/etc/hosts`. The `sda` disk has no mount point, making it the unmounted drive available for formatting.

### Step 1.2: View Partition Tables
* **What I Did:** I ran the `fdisk` tool with the `-l` (list) flag to view the exact sector layouts of the drives.
  ```bash
  sudo fdisk -l
  ```
  *(Confirmed `sda` had 12 existing partitions that needed to be wiped.)*

---

## Part 2: Partitioning the Unmounted Disk (`fdisk`)

### Step 2.1: Open fdisk in Interactive Mode
* **What I Did:** I launched the `fdisk` utility specifically targeting the unmounted drive (`/dev/sda`).
  ```bash
  sudo fdisk /dev/sda
  ```
* **System Prompt:**
  ```text
  Welcome to fdisk (util-linux 2.36.1).
  Changes will remain in memory only, until you decide to write them.
  Be careful before using the write command.
  ```

### Step 2.2: Delete the Existing Default Partitions
* **What I Did:** Because the 10GB disk was fully allocated across 12 default partitions, I used the `d` command in interactive mode to delete all of them, working backwards from 12 to 1.
* **Execution Log:**
  ```text
  Command (m for help): d
  Partition number (1-12, default 12):
  Partition 12 has been deleted.
  
  Command (m for help): d
  Partition number (1-11, default 11): 
  Partition 11 has been deleted.
  
  Command (m for help): d
  Partition number (1-10, default 10): 
  Partition 10 has been deleted.
  
  Command (m for help): d
  Partition number (1-9, default 9): 
  Partition 9 has been deleted.
  
  Command (m for help): d
  Partition number (1-8, default 8): 
  Partition 8 has been deleted.
  
  Command (m for help): d
  Partition number (1-7, default 7): 
  Partition 7 has been deleted.
  
  Command (m for help): d
  Partition number (1-6, default 6): 
  Partition 6 has been deleted.
  
  Command (m for help): d
  Partition number (1-5, default 5): 
  Partition 5 has been deleted.
  
  Command (m for help): d
  Partition number (1-4, default 4): 
  Partition 4 has been deleted.
  
  Command (m for help): d
  Partition number (1-3, default 3): 
  Partition 3 has been deleted.
  
  Command (m for help): d
  Partition number (1,2, default 2): 
  Partition 2 has been deleted.
  
  Command (m for help): d
  Selected partition 1
  Partition 1 has been deleted.
  ```

### Step 2.3: Create New Partitions
* **What I Did:** I used the `n` command to create two brand new partitions. 
* **Partition 1 (1GB):** I pressed Enter twice for the default starting sector, then manually entered `2097200` to allocate exactly 1GB.
  ```text
  Command (m for help): n
  Partition number (1-128, default 1): 
  First sector (34-20971486, default 2048): 
  Last sector... : 2097200
  Created a new partition 1 of type 'Linux filesystem' and of size 1023 MiB.
  ```
* **Partition 2 (9GB Remainder):** I used `n` again, pressing Enter through all default prompts to assign the remaining 9GB to the second partition.
  ```text
  Created a new partition 2 of type 'Linux filesystem' and of size 9 GiB.
  ```

### Step 2.4: Change Partition Type to Linux Swap
* **What I Did:** I used the `t` command to change Partition 1 from a standard filesystem to a "Linux swap" type, using Hex code `19`.
  ```text
  Command (m for help): t
  Partition number (1,2, default 2): 1
  Partition type or alias (type L to list all): 19
  Changed type of partition 'Linux filesystem' to 'Linux swap'.
  ```

### Step 2.5: Verify and Write Changes
* **What I Did:** I used `v` to verify the table structure for errors, then committed the destructive changes to the physical disk using `w`.
  ```text
  Command (m for help): v
  No errors detected.
  
  Command (m for help): w
  The partition table has been altered.
  Calling ioctl() to re-read partition table.
  Syncing disks.
  ```

---

## Part 3: Formatting and Mounting the Filesystem

### Step 3.1: Format the 9GB Partition to ext4
* **What I Did:** With the physical walls built, I needed to install the filing system. I targeted the second partition (`sda2`) using the Make Filesystem (`mkfs`) command, specifying the `ext4` format.
  ```bash
  sudo mkfs -t ext4 /dev/sda2
  ```
* **Output Received:**
  ```text
  mke2fs 1.46.2 (28-Feb-2021)
  Discarding device blocks: done                            
  Creating filesystem with 2359035 4k blocks and 589824 inodes
  ...
  Writing superblocks and filesystem accounting information: done
  ```

### Step 3.2: Mount the Formatted Drive
* **What I Did:** A formatted Linux drive cannot be used until it is attached (mounted) to a directory path. I mounted `sda2` to `/home/my_drive`.
  ```bash
  sudo mount /dev/sda2 /home/my_drive
  ```

### Step 3.3: Verify the Final Configuration
* **What I Did:** I ran `lsblk` one final time to confirm the disk was live and mapped correctly.
  ```bash
  lsblk
  ```
  *(Output successfully showed `sda2` operating as a 9G partition mounted at `/home/my_drive`)*

---

## Key Takeaways
* **Linux Storage Naming Conventions:** Physical disks are labeled sequentially (`sda`, `sdb`), while their logical partitions receive numbers (`sda1`, `sda2`). 
* **Memory vs. Disk:** The `fdisk` utility is a safe workspace. Changes are only held in system memory (RAM) and can be abandoned with `q`. No data is actually destroyed until the `w` (write) command is executed.
* **The Linux Filesystem Paradigm:** Unlike Windows which automatically assigns drive letters (D:, E:), a Linux partition must be manually formatted with `mkfs` and then explicitly attached to an existing folder path using the `mount` command before it can store data.
