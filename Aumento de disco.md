____________________________________

____________________________________

Desafio liberado!!! 

____________________________________

```
$ lsblk 
```

NAME               MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0                  2:0    1    4K  0 disk 
sr0                 11:0    1 1024M  0 rom  
vda                252:0    0 49.8G  0 disk 
├─vda1             252:1    0  512M  0 part /boot/efi
├─vda2             252:2    0    1K  0 part 
└─vda5             252:5    0 48.3G  0 part 
  ├─primary-root   253:0    0 47.4G  0 lvm  /
  └─primary-swap_1 253:1    0  980M  0 lvm  [SWAP]
vdb                252:16   0   20G  0 disk 
vdc                252:32   0   20G  0 disk 
vdd                252:48   0   20G  0 disk 
$ 

```
$ fdisk /dev/vdb
```

Welcome to fdisk (util-linux 2.34).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x64a1323d.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 3
First sector (2048-41943039, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943039, default 41943039): 

Created a new partition 3 of type 'Linux' and of size 20 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

```
$ lsblk 
```

NAME               MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0                  2:0    1    4K  0 disk 
sr0                 11:0    1 1024M  0 rom  
vda                252:0    0 49.8G  0 disk 
├─vda1             252:1    0  512M  0 part /boot/efi
├─vda2             252:2    0    1K  0 part 
└─vda5             252:5    0 48.3G  0 part 
  ├─primary-root   253:0    0 47.4G  0 lvm  /
  └─primary-swap_1 253:1    0  980M  0 lvm  [SWAP]
vdb                252:16   0   20G  0 disk 
└─vdb3             252:19   0   20G  0 part 
vdc                252:32   0   20G  0 disk 
vdd                252:48   0   20G  0 disk 

```
$ pvcreate /dev/vdb3
```

  Physical volume "/dev/vdb3" successfully created.

```
$ pvs
```

  PV         VG      Fmt  Attr PSize   PFree  
  /dev/vda5  primary lvm2 a--   48.32g      0 
  /dev/vdb3          lvm2 ---  <20.00g <20.00g


```
$ vgextend primary /dev/vdb3
```

  Volume group "primary" successfully extended

```
$ vgdisplay 
```

  --- Volume group ---
  VG Name               primary
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               68.32 GiB
  PE Size               4.00 MiB
  Total PE              17490
  Alloc PE / Size       12371 / 48.32 GiB
  Free  PE / Size       5119 / <20.00 GiB
  VG UUID               QfvKda-7QfV-jcs7-OSip-U2wY-KRLS-NMTHRU

```
$ df -h
```

Filesystem                Size  Used Avail Use% Mounted on
udev                      713M     0  713M   0% /dev
tmpfs                     149M  980K  148M   1% /run
/dev/mapper/primary-root   47G   39G  5.3G  89% /
tmpfs                     742M     0  742M   0% /dev/shm
tmpfs                     5.0M     0  5.0M   0% /run/lock
tmpfs                     742M     0  742M   0% /sys/fs/cgroup
/dev/vda1                 511M  4.0K  511M   1% /boot/efi
tmpfs                     149M     0  149M   0% /run/user/0

```
$ lvextend -L +18G /dev/mapper/primary-root
```

  Size of logical volume primary/root changed from <47.37 GiB (12126 extents) to <65.37 GiB (16734 extents).
  Logical volume primary/root successfully resized.

```
$ resize2fs /dev/mapper/primary-root
```

resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/mapper/primary-root is mounted on /; on-line resizing required
old_desc_blocks = 6, new_desc_blocks = 9
The filesystem on /dev/mapper/primary-root is now 17135616 (4k) blocks long.

```
$ df -h
```

Filesystem                Size  Used Avail Use% Mounted on
udev                      713M     0  713M   0% /dev
tmpfs                     149M  980K  148M   1% /run
/dev/mapper/primary-root   65G   39G   23G  64% /
tmpfs                     742M     0  742M   0% /dev/shm
tmpfs                     5.0M     0  5.0M   0% /run/lock
tmpfs                     742M     0  742M   0% /sys/fs/cgroup
/dev/vda1                 511M  4.0K  511M   1% /boot/efi
tmpfs                     149M     0  149M   0% /run/user/0

```
$ lsblk 
```

NAME               MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0                  2:0    1    4K  0 disk 
sr0                 11:0    1 1024M  0 rom  
vda                252:0    0 49.8G  0 disk 
├─vda1             252:1    0  512M  0 part /boot/efi
├─vda2             252:2    0    1K  0 part 
└─vda5             252:5    0 48.3G  0 part 
  ├─primary-root   253:0    0 65.4G  0 lvm  /
  └─primary-swap_1 253:1    0  980M  0 lvm  [SWAP]
vdb                252:16   0   20G  0 disk 
└─vdb3             252:19   0   20G  0 part 
  └─primary-root   253:0    0 65.4G  0 lvm  /
vdc                252:32   0   20G  0 disk 
vdd                252:48   0   20G  0 disk 
$ 