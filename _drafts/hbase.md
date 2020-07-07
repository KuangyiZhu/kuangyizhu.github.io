Hbase

https://blog.csdn.net/high2011/article/details/83900147

* 		哈希存储引擎  是哈希表的持久化实现，支持增、删、改以及随机读取操作，但不支持顺序扫描，对应的存储系统为key-value存储系统。对于key-value的插入以及查询，哈希表的复杂度都是O(1)，明显比树的操作O(n)快,如果不需要有序的遍历数据，哈希表就是your Mr.Right
* 		B树存储引擎是B树（关于B树的由来，数据结构以及应用场景可以看之前一篇博文）的持久化实现，不仅支持单条记录的增、删、读、改操作，还支持顺序扫描（B+树的叶子节点之间的指针），对应的存储系统就是关系数据库（Mysql等）。
* 		LSM树（Log-Structured Merge Tree）存储引擎和B树存储引擎一样，同样支持增、删、读、改、顺序扫描操作。而且通过批量存储技术规避磁盘随机写入问题。当然凡事有利有弊，LSM树和B+树相比，LSM树牺牲了部分读性能，用来大幅提高写性能。
* 

hbase在实现中，是把整个内存在一定阈值后，flush到disk中，形成一个file，这个file的存储也就是一个小的B+树，因为hbase一般是部署在hdfs上，hdfs不支持对文件的update操作，所以hbase这么整体内存flush，而不是和磁盘中的小树merge update，这个设计也就能讲通了。内存flush到磁盘上的小树，定期也会合并成一个大树。整体上hbase就是用了lsm tree的思路。

https://blog.csdn.net/iteye_1868/article/details/82486180?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1

这个新生成的存储文件和B-数很像，但是是为顺序磁盘查询优化过的

https://blog.csdn.net/bocai8058/article/details/81464103?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2

B+树
在RDBMS中，数据随机无序写在磁盘块中，如果没有B+树，读性能会很低。B+树对于数据读操作能很好地提高性能，但对于数据写，效率不高。对于大型分布式数据系统，B+树还无法与LSM树相抗衡。


LSM树
随着NoSQL系统尤其是类BigTable系统的流行，LSM的文件系统越来越让人熟知。
- LSM主要用于为那些长期具有很高记录更新（插入和删除）频率的文件提供低成本的索引机制。
- LSM树实现了所有的索引值对于所有的查询来说都可以通过内存组件或某个磁盘组件进行访问。
- LSM减少了磁盘磁壁的移动次数降低了进行数据插入时磁盘磁壁的开销。
- LSM在进行需要即时响应的操作时会损失I/O效率，最适用于索引插入比查询操作多的情况。

核心思想的核心就是放弃部分读能力，换取写入的最大化能力。LSM树主题思想是划分两个部分：一部分在磁盘，一部分在内存
————————————————
版权声明：本文为CSDN博主「GSpinach」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/bocai8058/java/article/details/81464103

主要原理
输入的数据首先会插入到内存中的树；
同时为了防止数据丢失，写内存的同时需要暂时持久化到磁盘（即输入数据时数据会以完全有序的形式先存储在日志文件中（对应HBase的MemStore和HLog））；
当日志文件被修改时，对应的更新会被先保存在内存中来加速查询。
当内存空间逐渐被占满之后，LSM会把这些有序的键刷新到磁盘，同时和磁盘中的LSM树合并成一个文件。
合并操作会从左至右遍历内存中的叶子节点与磁盘中树的叶子节点进行合并；
当合并的数据量达到磁盘的存储页的大小时，会将合并的数据持久化到磁盘；
同时更新父亲节点对叶子节点的指针。


LSM树的读写
LSM树的读写
数据写
- 数据首先顺序写如HLog (WAL), 然后同步写到MemStore；
- 在MemStore中，数据是一个2层B+树（下图中的C0树）。MemStore上的树达到一定大小之后，需要flush到HRegion磁盘中（一般是Hadoop DataNode），这样MemStore就变成了DataNode上的磁盘文件StoreFile，定期HRegionServer对DataNode的数据做merge操作，彻底删除无效空间，多棵小树在这个时机合并成大树，来增强读性能。
- 在storefile中，数据是3层B+树（下图中的C1树），并针对顺序磁盘操作进行优化。





数据读
- 首先搜索MemStore，如果不在MemStore中，则到storefile中寻找。

数据删除
- 不会去磁盘上删除数据，而是为数据添加一个删除标记。在随后的major compaction中，被删除的数据和删除标记才会真的被删除。

补充：hbase在实现中，是把整个内存在一定阈值后，flush到disk中，形成一个file，这个file的存储也就是一个小的B+树，因为hbase一般是部署在hdfs上，hdfs不支持对文件的update操作，所以hbase这么整体内存flush，而不是和磁盘中的小树merge update，这个设计也就能讲通了。内存flush到磁盘上的小树，定期也会合并成一个大树。整体上hbase就是用了lsm tree的思路。

LSM树的优化方式
Bloom filter

就是个带随即概率的bitmap,可以快速的告诉你，某一个小的有序结构里有没有指定的那个数据的。于是就可以不用二分查找，而只需简单的计算几次就能知道数据是否在某个小集合里啦。效率得到了提升，但付出的是空间代价。

compact

小树合并为大树:因为小树他性能有问题，所以要有个进程不断地将小树合并到大树上，这样大部分的老数据查询也可以直接使用log2N的方式找到，不需要再进行(N/m)*log2n的查询了.

关于LSM最本质原理的3个问题
首先说说为什么要有WAL（Write Ahead Log）？
因为数据是先写到内存中，如果断电，内存中的数据会丢失，因此为了保护内存中的数据，需要在磁盘上先记录logfile，
当内存中的数据flush到磁盘上时，就可以抛弃相应的Logfile。
1
2
什么是memstore,storefile？
LSM树就是一堆小树，在内存中的小树即memstore，每次flush，内存中的memstore变成磁盘上一个新的storefile。
1
为什么会有compact？
随着小树越来越多，读的性能会越来越差，因此需要在适当的时候，对磁盘中的小树进行merge，多棵小树变成一颗大树。
1
B树与LSM树的适应场景
LSM-Tree比较适合的应用场景是：insert数据量大，读数据量和update数据量不高且读一般，针对最新数据。

B-Tree比较适合的应用场景：insert数据量小，读数据量比较高。


————————————————
版权声明：本文为CSDN博主「GSpinach」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/bocai8058/java/article/details/81464103

https://leonlibraries.github.io/2017/04/13/%E5%88%9D%E6%B6%89HBase/
数据结构和基础概念
￼

￼
* Table ： 表即文件，以下介绍的都是表内元素
* Row Key 行键：可以理解为一个单元数据的 ID
* Column Families 列族 ： 定义表的时候需要预先定义，一旦定以后不能轻易修改
* Column Qualify 列标识： 可动态增删字段，也是 HBase 最大的特色
* Cell 单元 ：Row Key + Column Families + Column Qualify 为一个单元，存储在单元里的数据叫单元数据
* Timestamp 时间戳： 每个字段下的数据都有一个时间戳，每次更新只是将数据以最新的时间戳覆盖上去，默认取最新时间戳的数据


写与写缓存（MemStore）
先来看看 Region Server 大致的结构
￼
regionserver
那么写的过程是怎样的呢？
client request —> 写WAL(顺序存储速度快，存于磁盘) —> ack
￼
write1
WAL —> MemStore —> flush to HFiles
￼
write2

￼
write3
HFiles 数据结构（重点）



https://leonlibraries.github.io/2017/05/18/%E4%BB%8ELSM%E5%88%B0HBase/



https://blog.csdn.net/javajxz008/article/details/51892967

如果rowkey按照时间戳的方式递增，不要将时间放在二进制码的前面，建议将rowkey的高位作为散列字段，由程序随机生成，低位放时间字段，这样将提高数据均衡分布在每个RegionServer，以实现负载均衡的几率

必须在设计上保证其唯一性，rowkey是按照字典顺序排序存储的，因此，设计rowkey的时候，要充分利用这个排序的特点，将经常读取的数据存储到一块，将最近可能会被访问的数据放到一块。

HBase中的行是按照rowkey的字典顺序排序的，这种设计优化了scan操作，可以将相关的行以及会被一起读取的行存取在临近位置，便于scan。然而糟糕的rowkey设计是热点的源头。 热点发生在大量的client直接访问集群的一个或极少数个节点（访问可能是读，写或者其他操作）。大量访问会使热点region所在的单个机器超出自身承受能力，引起性能下降甚至region不可用，这也会影响同一个RegionServer上的其他region，由于主机无法服务其他region的请求。 设计良好的数据访问模式以使集群被充分，均衡的利用。
为了避免写热点，设计rowkey使得不同行在同一个region，但是在更多数据情况下，数据应该被写入集群的多个region，而不是一个。
下面是一些常见的避免热点的方法以及它们的优缺点：
加盐
这里所说的加盐不是密码学中的加盐，而是在rowkey的前面增加随机数，具体就是给rowkey分配一个随机前缀以使得它和之前的rowkey的开头不同。分配的前缀种类数量应该和你想使用数据分散到不同的region的数量一致。加盐之后的rowkey就会根据随机生成的前缀分散到各个region上，以避免热点。
哈希
哈希会使同一行永远用一个前缀加盐。哈希也可以使负载分散到整个集群，但是读却是可以预测的。使用确定的哈希可以让客户端重构完整的rowkey，可以使用get操作准确获取某一个行数据
反转
第三种防止热点的方法时反转固定长度或者数字格式的rowkey。这样可以使得rowkey中经常改变的部分（最没有意义的部分）放在前面。这样可以有效的随机rowkey，但是牺牲了rowkey的有序性。


