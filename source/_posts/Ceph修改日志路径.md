title: Ceph修改日志路径

date: 2016-11-06 16:28:32

tags: [ceph,]

categories: 技术

---

# 概述 
实验测试阶段，只是想测试多余内存作为ceph日志盘对读写速度的改善情况。
# 修改ceph日志路径
ubuntu 14.04+ceph hammer 0.94.2  

```
### Stop osd
$ stop ceph-osd id=<osd num>

### Flush Journal
$ ceph-osd --flush-journal -i <osd num>

### Create symlink to partition
$ rm /var/lib/ceph/osd/ceph-<osd num>/journal
$ ln -s /dev/ram0 /var/lib/ceph/osd/ceph-<osd num>/journal

### Create new journal
$ ceph-osd --mkjournal -i <osd num>
$ start ceph-osd id=<osd num>

```  
这个方案osd服务可以正常启动
# 不成功案例

+ 选择在/run/shm使用dd创建一块设备然后挂载到ceph存储的日志路径上，osd无法启动。
+ 使用tmpfs，然后把/mnt/ramdisk `ln -s`到日志路径会导致osd无法启动。
` mount -t tmpfs tmpfs /mnt/ramdisk  -o size=2G,defaults,noatime,mode=777  `  

错误日志：
>2016-11-05 10:11:19.813628 7f6f54d9d900  0 ceph version 0.94.2(5fb85614ca8f354284c713a2f9c610860720bbf3), process ceph-osd, pid 3487  
>2016-11-05 10:11:19.828706 7f6f54d9d900  0 filestore(/var/lib/ceph/osd/ceph-7) backend generic (magic 0xef53)  
>2016-11-05 10:11:19.829544 7f6f54d9d900  0 genericfilestorebackend(/var/lib/ceph/osd/ceph-7) detect_features: FIEMAP ioctl is supported and appears to work  
>2016-11-05 10:11:19.829553 7f6f54d9d900  0 genericfilestorebackend(/var/lib/ceph/osd/ceph-7) detect_features: FIEMAP ioctl is disabled via 'filestore fiemap' config option
>2016-11-05 10:11:19.853807 7f6f54d9d900  0 genericfilestorebackend(/var/lib/ceph/osd/ceph-7) detect_features: syncfs(2) syscall fully supported (by glibc and kernel)  
>2016-11-05 10:11:19.855109 7f6f54d9d900  0 filestore(/var/lib/ceph/osd/ceph-7) limited size xattrs  
>2016-11-05 10:11:19.858255 7f6f54d9d900  0 filestore(/var/lib/ceph/osd/ceph-7) mount: enabling WRITEAHEAD journal mode: checkpoint is not enabled  
>2016-11-05 10:11:19.859704 7f6f54d9d900 -1 journal FileJournal::_open: disabling aio for non-block journal.  Use journal_force_aio to force use of aio anyway  
>2016-11-05 10:11:19.859714 7f6f54d9d900  1 journal _open /var/lib/ceph/osd/ceph-7/journal fd 19: 10737418240 bytes, block size 4096 bytes, directio = 1, aio = 0  
>2016-11-05 10:11:19.859756 7f6f54d9d900 -1 journal FileJournal::open: ondisk fsid 00000000-0000-0000-0000-000000000000 doesn't match expected 54de23c7-9a5f-4f30-8568-bc0354a6d788, invalid (someone else's?) journal  
>2016-11-05 10:11:19.859822 7f6f54d9d900 -1 filestore(/var/lib/ceph/osd/ceph-7) mount failed to open journal /var/lib/ceph/osd/ceph-7/journal: (22) Invalid argument  
>2016-11-05 10:11:19.869951 7f6f54d9d900 -1 osd.7 0 OSD:init: unable to mount object store  
>2016-11-05 10:11:19.869970 7f6f54d9d900 -1  ** ERROR: osd init failed: (22) Invalid argument
