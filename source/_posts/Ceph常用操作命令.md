title: Ceph常用操作命令

date: 2016-11-06 21:58:12

tags: [ceph,]

categories: 技术

---

记录个人最近在用的ceph操作命令  
持续更新...  

---
**删除osd**  
```
1）ceph osd crush reweight osd.x 0.0  
... 等待平衡结束....  
2）ceph osd out osd.x  
3）service ceph stop osd.x  
4）ceph osd crush remove osd.x  
5）ceph auth del osd.x  
6）ceph osd rm osd.X  
```
  
**导出crush map**  
```
ceph osd getcrushmap -o /tmp/crushmap.txt
crushtool -d /tmp/crushmap.txt -o /tmp/crushmap-decompile
```
  
**查看osd的ip** ``ceph osd dump`` 

**创建了一个rbd卷，想删除的时候总是报错一直被占用，想知道卷被谁占用了？**

首先查看卷的相关信息：
```
$ rbd info testjevon/foo  
rbd image 'foo':  
    size 10240 MB in 2560 objects  
    order 22 (4096 kB objects)  
    block_name_prefix: rb.0.5aec5.238e1f29  
    format: 1  
```
接着，找出卷的header，``rados -p testjevon ls|grep foo.rbd``  
最后，查找谁map了卷，``rados -p testjevon listwatchers foo.rbd``  
  
**pg活动在哪些OSD：**
```
ceph pg dump |  awk '{print $1,$10,$15,$16,$17,$18}'
ceph pg dump pgs|grep ^1|awk '{print $1,$2,$15}'
```
  
**删除pool中的对象**
```
rados rm -p {pool-name} {object-name}  
批量：for ((i=1;i<50;i++)); do rados rm -p .rgw.buckets.index .dir.default.14111.$i; done
rgw bucket待删除对象：
radosgw-admin gc list --include-all
rgw bucket 查看删除时间设定
ceph --admin-daemon /var/run/ceph/ceph-mon.mon.asok config show | grep gc
```

**查看osd.x作为primary个数**  
``ceph pg dump | grep active+clean | egrep ",0\]" | wc -l``