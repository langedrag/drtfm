# Add a storage device

This is a step-by-step example on how to install a new disk on Linux. The example is verified on Ubuntu, but should work on most variants of Linux.

## Step 1 – Identify disk

Start by listing all the physically installed disks by executing the list hardware command (lshw) and filtering out only hardware classes of the type disk.

	$ sudo lshw -c disk
	  *-disk                  
	       description: ATA Disk
	       product: KINGSTON SA400S3
	       physical id: 0.0.0
	       bus info: scsi@0:0.0.0
	       logical name: /dev/sda
	       version: 71E0
	       serial: [REMOVED]
	       size: 111GiB (120GB)
	       capabilities: gpt-1.00 partitioned partitioned:gpt
	       configuration: ansiversion=5 guid=2e700add-abeb-4ef3-a709-74089aa614bf
	       logicalsectorsize=512 sectorsize=512
	  *-disk
	       description: ATA Disk
	       product: WDC WD20EFRX-68E
	       vendor: Western Digital
	       physical id: 0.0.0
	       bus info: scsi@1:0.0.0
	       logical name: /dev/sdb
	       version: 0A82
	       serial: [REMOVED]
	       size: 1863GiB (2TB)
	       configuration: ansiversion=5 logicalsectorsize=512 sectorsize=4096

In this example, the physical Western Digital disk i have installed on the computer has been given the logical name /dev/sdb by Linux.

## Step 2 – Create disk partition

    WARNING: Before you proceed, make sure that you have identified the correct logical name, and substitute (if needed) the logical name /dev/sdb with the volume you are about to install.

To create the partition, run the format disk tool (fdisk) on the logical volume /dev/sdb. You need to create a new primary partition and label it as partition number 1. Keep the default values for the first and last sector to use the complete disk for the partition. You must write the new partition table to the disk to alter the partition table.

	$ sudo fdisk /dev/sdb 

	Welcome to fdisk (util-linux 2.27.1).
	Changes will remain in memory only, until you decide to write them.
	Be careful before using the write command.

	Device does not contain a recognized partition table.
	Created a new DOS disklabel with disk identifier 0xebba21e6.

	Command (m for help): n
	Partition type
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended (container for logical partitions)
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-3907029167, default 2048): 
	Last sector, +sectors or +size{K,M,G,T,P} (2048-3907029167, default 3907029167): 

	Created a new partition 1 of type 'Linux' and of size 1.8 TiB.

	Command (m for help): w
	The partition table has been altered.
	Calling ioctl() to re-read partition table.
	Syncing disks.

## Step 3 – Create file system

You must now make the file system (mkfs)of the type you prefer, e.g. ext4 on the newly created logical volume /dev/sdb1. It is perfectly normal for a HDD to sound like a purring cat at this stage.

	$ sudo mkfs -t ext4 /dev/sdb1
	mke2fs 1.42.13 (17-May-2015)
	Creating filesystem with 488378390 4k blocks and 122101760 inodes
	Filesystem UUID: 0cf8b7d8-b987-4e4b-9690-7cf82070323f
	Superblock backups stored on blocks: 
		32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
		4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
		102400000, 214990848

	Allocating group tables: done                            
	Writing inode tables: done                            
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

## Step 4 – Mount disk by name

Make a directory (mkdir) named /mnt/hdd1 as a mount point for your new disk.

$ sudo mkdir /mnt/hdd1

In order to make sure your new disk is automatically mounted you must modify the file systems table.

Use the echo command, and make sure to enable interpretation of backslash commands, in order to create a tabulator separated (\t) configuration line containing:

    file system (/dev/sdb1)
    mount point (/mnt/hdd1)
    type (ext4)
    options (defaults)
    dump (0)
    pass (2)

Since the file system table is a protected system configuration file you will need to be a super user in order to do the necessary configuration changes which can be done by piping (|) the text line to the  tee command in order to append it to the file system table (/etc/fstab).

$echo -e "/dev/sdb1\t/mnt/hdd1\text4\tdefaults\t0\t2" | sudo tee -a /etc/fstab
/dev/sdb1 /mnt/hdd1 ext4 defaults 0 2

Finally, you can mount all drives in the newly updated file systems table in order to mount your new drive

sudo mount -a

## Step 4 alternative – Mount disk by UUID

Follow the previous steps 1 to 3, then run:

	$echo -e "`lsblk /dev/sdb1 -n -P -o UUID | tr -d '"'`\t/mnt/hdd1\text4\tdefaults\t0\t2" | sudo tee -a /etc/fstab
	UUID=0cf8b7d8-b987-4e4b-9690-7cf82070323f /mnt/hdd1 ext4 defaults 0 2

tips: lsblk, lvs, vgs, blkid
