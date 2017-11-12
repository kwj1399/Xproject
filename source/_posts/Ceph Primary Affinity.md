title: Ceph Primary Affinity

date: 2016-11-17 20:51:26

tags: [ceph,]

categories: 技术

---
Ceph 有两种控制方式可以来改变 OSDMap 对数据分布的影响，分别是 Crush Weight 和 OSD Weight，但是它们的改变会同时造成pg的迁移，用户可能期望从一份数据的其他副本读而不是主副本，因此增加了 osd primary-affinity 选项来控制读请求的请求分流。primary-affinity 并不会造成pg的迁移。  
##### Note：
>*You must enable ‘mon osd allow primary affinity = true’ on the mons before you can adjust primary-affinity. note that older clients will no longer be able to communicate with the cluster.*  

查看监视器是否允许 primary affinity：
`ceph --admin-daemon /var/run/ceph/ceph-mon.*.asok config show | grep 'primary_affinity'`  
       
如果为`false`修改配置文件ceph.conf 
`mon osd allow primary affinity = true`  

改变primary affinity并查看改变后的结果:  
```  
# ceph osd primary-affinity osd.0 0
set osd.0 primary-affinity to 0 (802)

# ceph pg dump | grep active+clean | egrep "\[0," | wc -l
0
# ceph pg dump | grep active+clean | egrep ",0\]" | wc -l
180    
```
