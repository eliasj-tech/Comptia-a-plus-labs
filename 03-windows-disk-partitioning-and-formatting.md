# Lab 03: Disk Partitioning and Formatting on Windows

* **Date:** July 28, 2026
* **Objective:** Practice partitioning an additional disk drive using the Windows GUI (Disk Management) and formatting the new partitions with different file systems.

---

## Part 1: Initializing and Shrinking the Disk

### Step 1.1: Navigate to Disk Management
* **What I Did:** I accessed the built-in Windows GUI tools for storage management by navigating through the Control Panel.
* **Path Taken:** `Start` > `Control Panel` > `System and Security` > `Administrative Tools` > `Computer Management` > `Disk Management` (under Storage on the left panel).
* **Observation:** The system displayed two disks. One of the disks contained unallocated space and was labeled as "Offline." 
> **Note:** I intentionally ignored the 100 MB partitions named "EFI System Partition." These are critical for loading the operating system during boot up and should not be modified.

### Step 1.2: Bring the Disk Online
* **What I Did:** To enable partitioning, the disk first needed to be mounted. I right-clicked the left panel of the Offline disk and selected **Online**. 
* **Result:** The system mounted the disk and automatically assigned it the drive letter **D:**.

### Step 1.3: Shrink the Existing Volume
* **What I Did:** Because the space was already fully allocated to the D: drive, I had to shrink it to make room for a new partition. I right-clicked the D: drive and selected **Shrink Volume**.
* **Execution:** I entered `20,480 MB` (exactly 20GB) into the dialogue box to shrink the disk, leaving a 30GB partition for D: and creating 20GB of new unallocated space.

---

## Part 2: Provisioning the New Partition

### Step 2.1: Launch the New Simple Volume Wizard
* **What I Did:** I right-clicked the newly created 20GB unallocated space and selected **New Simple Volume**, clicking **Next** to proceed through the wizard.

### Step 2.2: Specify Volume Size and Drive Letter
* **What I Did:** I accepted the default maximum size to utilize all 20GB of the unallocated space. In the next menu, I ensured the drive letter was assigned to **E:** and clicked Next.

### Step 2.3: Finalize the Initial Configuration
* **What I Did:** I left the default file system and volume label settings exactly as they were and clicked **Next**, then **Finish** to close out the wizard.
* **Result:** The disk updated successfully. The secondary disk now contained two distinct partitions: D: (30GB) and E: (20GB).

---

## Part 3: Formatting with a New File System

### Step 3.1: Format the E: Partition to FAT32
* **What I Did:** To change the file format of the new partition, I right-clicked the **E:** drive and selected **Format**.

### Step 3.2: Execute the Format Warning
* **What I Did:** In the file format drop-down menu, I selected **FAT32** and clicked OK. 
* **⚠️ The Gotcha Encountered:** A severe system confirmation alert popped up warning me that formatting is destructive and erases all data. Because this was a fresh, empty partition, I confidently clicked **OK** to proceed.
* **Result:** The partition was successfully formatted to the FAT32 file system, completing the full disk configuration.

---

## Key Takeaways
* **Data Destruction Awareness:** The most critical lesson from this lab is that formatting a partition instantly destroys any existing data on it. In a live production environment, a full data backup is absolutely mandatory before modifying active partitions.
* **Shrinking vs. Formatting:** You do not always need raw, empty disks to create new partitions. The `Shrink Volume` feature allows administrators to safely carve out unallocated space from an existing partition without destroying the data currently residing on it.
