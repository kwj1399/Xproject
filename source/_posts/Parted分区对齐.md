title: Parted分区对齐

date: 2016-11-17 13:57:10

tags: [ceph,]

categories: 技术

---

在对大于2TB磁盘进行分区的时候总是提示：
>Warning: The resulting partition is not properly aligned for best performance.  

这个警告是分区没有对齐的问题，可以检查一下是否对齐  
```  
(parted) align-check optimal 1                                   
1 not aligned  
```  

解决办法：  
```  
(parted) mkpart primary  0% x 
```  
x代表分区结束的大小，可以是百分比也可以是具体的字节  

----
附上分区的shell脚本:  
```  

#! /bin/sh
DEVICE=$1
BOOT_SIZE="1000"
ROOTFS_SIZE="21000"
CEPH_DATA_SIZE="31000"
PETASAN_DATA_SIZE="50000"

SLEEP_SEC=3


echo Formatting $DEVICE

dd if=/dev/zero of=$DEVICE  bs=1k count=1
sync
sleep $SLEEP_SEC


#parted $DEVICE  << EOF >/dev/null 2>&1
parted $DEVICE  << EOF

mklabel gpt 
mkpart primary 0% $BOOT_SIZE
mkpart primary $BOOT_SIZE $ROOTFS_SIZE
mkpart primary $ROOTFS_SIZE $CEPH_DATA_SIZE
mkpart primary $CEPH_DATA_SIZE $PETASAN_DATA_SIZE
quit
EOF
echo Formatting complete  

``` 




