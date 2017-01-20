---
layout: post
title: Burning an Image to an SD Card on OSX
---

First of all let's find the disk

```
Seans-MacBook-Pro:~ Sean$ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:          Apple_CoreStorage Macintosh HD            499.4 GB   disk0s2
   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3

/dev/disk1 (internal, virtual):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:                            Macintosh HD           +499.1 GB   disk1
                                 Logical Volume on disk0s2
                                 BC56A7CB-DA57-4AE9-AD7B-115BCA1E2844
                                 Unlocked Encrypted

/dev/disk2 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *8.0 GB     disk2
   1:             Windows_FAT_32 boot                    66.1 MB    disk2s1
   2:                      Linux                         1.3 GB     disk2s2
```

I can see that disk 2 is my disk so lets unmount it and then write the image to it

```
Seans-MacBook-Pro:~ Sean$ diskutil unmountDisk disk2
Unmount of all volumes on disk2 was successful
Seans-MacBook-Pro:~ Sean$ sudo dd bs=1m if=~/Downloads/ClusterHAT-2016-09-23-1-p1.img of=/dev/rdisk2
1325+0 records in
1325+0 records out
1389363200 bytes transferred in 188.592078 secs (7367028 bytes/sec)
Seans-MacBook-Pro:~ Sean$ 
```


