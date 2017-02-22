# Linux下挂载与取消挂载数据盘

## 挂载数据盘

1. 通过`df -h`查看当前磁盘情况：

   ```
   [root@host-1 /]# df -h
   Filesystem      Size  Used Avail Use% Mounted on
   /dev/vda1        20G  735M   18G   4% /
   tmpfs            16G     0   16G   0% /dev/shm
   ```

2. 通过`fdisk -l`查看数据盘：

   ```
   [root@host-1 /]# fdisk -l

   Disk /dev/vda: 21.5 GB, 21474836480 bytes
   255 heads, 63 sectors/track, 2610 cylinders
   Units = cylinders of 16065 * 512 = 8225280 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk identifier: 0x000566fa

      Device Boot      Start         End      Blocks   Id  System
   /dev/vda1   *           1        2610    20963801   83  Linux

   Disk /dev/vdb: 536.9 GB, 536870912000 bytes
   16 heads, 63 sectors/track, 1040253 cylinders
   Units = cylinders of 1008 * 512 = 516096 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk identifier: 0x00000000
   ```

3. 通过`fdisk`命令进行分区：

   ```
   [root@host-1 /]# fdisk /dev/vdb
   Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklab                                                                                        el
   Building a new DOS disklabel with disk identifier 0xcfec08a1.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
            switch off the mode (command 'c') and change display units to
            sectors (command 'u').

   Command (m for help): n
   Command action
      e   extended
      p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-1040253, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-1040253, default 1040253):
   Using default value 1040253

   Command (m for help): wq
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. 再次查看数据盘：

   ```
   [root@host-1 /]# fdisk -l

   Disk /dev/vda: 21.5 GB, 21474836480 bytes
   255 heads, 63 sectors/track, 2610 cylinders
   Units = cylinders of 16065 * 512 = 8225280 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk identifier: 0x000566fa

      Device Boot      Start         End      Blocks   Id  System
   /dev/vda1   *           1        2610    20963801   83  Linux

   Disk /dev/vdb: 536.9 GB, 536870912000 bytes
   16 heads, 63 sectors/track, 1040253 cylinders
   Units = cylinders of 1008 * 512 = 516096 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk identifier: 0xcfec08a1

      Device Boot      Start         End      Blocks   Id  System
   /dev/vdb1               1     1040253   524287480+  83  Linux
   ```

5. 通过`mkfs`命令进行数据盘的格式化：

   ```
   [root@host-1 /]# mkfs.ext3 /dev/vdb
   mke2fs 1.41.12 (17-May-2010)
   Filesystem label=
   OS type: Linux
   Block size=4096 (log=2)
   Fragment size=4096 (log=2)
   Stride=0 blocks, Stripe width=0 blocks
   32768000 inodes, 131072000 blocks
   6553600 blocks (5.00%) reserved for the super user
   First data block=0
   Maximum filesystem blocks=4294967296
   4000 block groups
   32768 blocks per group, 32768 fragments per group
   8192 inodes per group
   Superblock backups stored on blocks:
           32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
           4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
           102400000

   Writing inode tables: done
   Creating journal (32768 blocks): done
   Writing superblocks and filesystem accounting information: done

   This filesystem will be automatically checked every 20 mounts or
   180 days, whichever comes first.  Use tune2fs -c or -i to override.
   ```

6. 在`/etc/fstab`中写入新分区的信息：

   `/dev/vdb     /home     ext3     defaults     0     0`

7. 通过`mount`命令进行挂载，之后再通过`df -h`命令查看挂载后的信息：

   ```
   [root@host-1 /]# mount /dev/vdb /home
   [root@host-1 /]# df -h
   Filesystem      Size  Used Avail Use% Mounted on
   /dev/vda1        20G  735M   18G   4% /
   tmpfs            16G     0   16G   0% /dev/shm
   /dev/vdb        493G  198M  467G   1% /home
   ```

## 取消挂载数据盘

使用`umount`命令即可，非常简单。如：

```
umount /dev/vdb /home
```

​:happy:​