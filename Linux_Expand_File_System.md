
# Increase File System Size (Epand drive after install)

## WARNINGS
This requires you to mess around with your partitions. If you do it wrong you can blow everything up. It is important that you have a snapshot to roll back to in case it kills everything!

It also doesn't hurt to shut down unneded services first. (e.g. docker-compose down)

## Expansion Steps
1) Snapshot Your DRIVE!!!!
2) Expand the disk in the VM Software (e.g. proxmox, virtualbox, etc...)
3) From within the VM, expand the partition and filesystem

### Using VM host software to increase disk size (e.g. in proxmox, virtualbox, vmware, hyperv, etc...)
You will need to increase the disk size.  In proxmox, you go to the virtual machine -> Hardware Tab -> Click the Hard Disk -> Click "Resize Disk" button at the top and set the number of gigabytes to increase by (e.g. 500 means grow by 500 gig).

Once that is done, your disk will be bigger.  You may need to shut down your to see the size inside the VM. Most modern linux systems should be able to see the change in disk size right away. Verify that you can in fact see the larger disk before changing the partition later.

## Non LVM partition  Expansion (FDisk)
Use the fdisk command to see the partitions.

NOTE: We are using /dev/vda for the disk. Make sure you are using the proper disk (could be /dev/vda, /dev/sda, make sure you have the right one!!!)
- vd?? is commonly used for virtualio disks
- sd?? is commonly used for scsii disks
- hd?? for older style drives (ide?)
- scd?? or sr?? for CD drives
- nvme?? for nvme drives


```
fdisk /dev/vda

press "p" (enter) to see current paritions
```

You should see something like this:

```
Welcome to fdisk (util-linux 2.31.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/vda: 600 GiB, 644245094400 bytes, 1258291200 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x3e96541e

Device     Boot      Start        End    Sectors  Size Id Type
/dev/vda1  *          2048 1031838884 1031836837  492G 83 Linux
/dev/vda2       1031839744 1048562549   16722806    8G 82 Linux swap / Solaris

Command (m for help):

```

In my case, I increased the disk size from 500gig to 600Gig. Make sure you see the correct size (e.g. expanded size). You can see my disk is 600Gig.

My Swap partition is also at the end, so we will need to move that (we can't have partition 1 cross over partition 2). It will be easier to remove it first since swap is all temp and can be removed without loosing data. If this is NOT a swap partition, you will need to move it using another tool and is outside the scope of this document. Notice it is /dev/vda2. 

Quit fdisk  "q" (enter)

Turn swap off.

```
swapoff -a
```

May take a few minutes - it has to clear the swap file.

Go back to FDisk, and remove the swap partition

```
fdisk /dev/vda
p   (print current partitions)
d   (delete partition - choose 2 to remove swap parition)
p   (Make sure you see just partition 1 - take note of the settings)
/dev/vda1  *     2048 1031838884 1031836837  492G 83 Linux
d   (NOW DELETE PART 1 - we will re-create it)
n   (NOW CREATE NEW PART 1)
p   (primary)
1   (partition number - needs to be the same as before - vda1 is part 1)
2048 (USE SAME BEGINNING SECTOR - VERY IMPORTANT THAT IT IS EXACTLY THE SAME AS BEFORE)
+585G  (I am using 15gig short of 600 to leave some room for swap partition)
** DON'T REMOVE ext? signature if asked
p (Make sure partition 1 is there with the same settings as before with the exception that size will be bigger)
a  (make sure part1 is bootable)
```
At this point, your partition has been expanded. Add your swap partition back in
```
n  (new partition)
p  (primary)
2  (partition 2)
(hit enter for default)  ( let it use the first available sector )
(hit enter for default)  ( let it go to last avialable sector )
t  (Change parition type)
2  (number for new swap partition)
82 (Linux Swap)
p  (make sure you see something like the following again.

Command (m for help): p
Disk /dev/vda: 600 GiB, 644245094400 bytes, 1258291200 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x3e96541e

Device     Boot      Start        End    Sectors  Size Id Type
/dev/vda1  *          2048 1226835967 1226833920  585G 83 Linux
/dev/vda2       1226835968 1258291199   31455232   15G 82 Linux swap / Solaris

(IMPORTANT)
w  (Write out changes or they won't take!)
```
At this point, you should have the new partition setup as well as your swap partition recreated
```
mkswap /dev/vda2   (format swap volume)
swapon -a    (turn swap back on)
```
Now resize your filesystem
```
resize2fs /dev/vda1
df -h    (look at file system sizes - make sure they are bigger - in my case mount point / is /dev/vda1 so / shows up with 577 gig which is 600 gig - 15 gig swap and some overhead )

docker:/ # df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        2.9G     0  2.9G   0% /dev
tmpfs           2.9G     0  2.9G   0% /dev/shm
tmpfs           2.9G   33M  2.9G   2% /run
tmpfs           2.9G     0  2.9G   0% /sys/fs/cgroup
/dev/vda1       577G   48G  501G   9% /
tmpfs           2.9G     0  2.9G   0% /tmp
/dev/vdb1       2.0T  964G  1.1T  49% /ope
tmpfs           582M     0  582M   0% /run/user/0

```

You should be done! If everything worked, take a snapshot. Also keep your previous snapshot for a minute, just in case.



