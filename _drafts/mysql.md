https://www.jianshu.com/p/f65be52d5e2b
https://www.cnblogs.com/softidea/p/5977860.html
https://blog.csdn.net/Abysscarry/article/details/80792876
https://www.mysqltutorial.org/mysql-index/mysql-prefix-index/

MVCC
https://www.cnblogs.com/FXqufei/p/9795435.html

https://developer.aliyun.com/article/743691

Record Lock, Gap Lock, Next-key Lock
https://juejin.im/post/6844903842505555981

https://segmentfault.com/a/1190000012669504

个人认为应该翻译为: 数据库状态的快照适用于事务中的SELECT语句, 而不一定适用于所有DML语句。 如果您插入或修改某些行, 然后提交该事务, 则从另一个并发REPEATABLE READ事务发出的DELETE或UPDATE语句就可能会影响那些刚刚提交的行, 即使该事务无法查询它们。 如果事务更新或删除由不同事务提交的行, 则这些更改对当前事务变得可见。


如何保证rr级别绝对不产生幻读？
在使用的select …where语句中加入 for update(排他锁) 或者 lock in share mode(共享锁)语句来实现。其实就是锁住了可能造成幻读的数据，阻止数据的写入操作。

如何尽可能避免死锁

1）以固定的顺序访问表和行。比如两个更新数据的事务，事务A 更新数据的顺序 为1，2；事务B更新数据的顺序为2，1。这样更可能会造成死锁。

2）大事务拆小。大事务更倾向于死锁，如果业务允许，将大事务拆小。

3）在同一个事务中，尽可能做到一次锁定所需要的所有资源，减少死锁概率。

4）降低隔离级别。如果业务允许，将隔离级别调低也是较好的选择，比如将隔离级别从RR调整为RC，可以避免掉很多因为gap锁造成的死锁。

5）为表添加合理的索引。可以看到如果不走索引将会为表的每一行记录添加上锁，死锁的概率大大增大。
https://juejin.im/entry/6844903576133697544

Mysql的并发控制原理
https://juejin.im/post/6844903895706107918


MySQL在并发场景下的问题及解决思路
https://www.cnblogs.com/leefreeman/p/8286550.html
看看当前MySQL进程状态：show processlist
InnoDB存储引擎检测到死锁发生，让事务2回滚, 所以，需要检测错误状态

1. max_connections
采用max_connections 控制允许连接到MySQL数据库的最大数量，默认值是 151。如果状态变量 connection_errors_max_connections 不为零，并且一直增长，则说明不断有连接请求因数据库连接数已达到允许最大值而失败，这是可以考虑增大max_connections 的值。
Mysql 最大可支持的连接数，取决于很多因素，包括给定操作系统平台的线程库的质量、内存大小、每个连接的负荷、CPU的处理速度，期望的响应时间等。在Linux 平台下，性能好的服务器，支持 500-1000 个连接不是难事，需要根据服务器性能进行评估设定。

我们可以查看一下：

1
show variables like 'max_connections';
在这里插入图片描述

2. back_log
back_log 参数控制MySQL监听TCP端口时设置的积压请求栈大小。如果MySql的连接数达到max_connections时，
新来的请求将会被存在堆栈中，以等待某一连接释放资源，该堆栈的数量即back_log，如果等待连接的数量超过back_log，
将不被授予连接资源，将会报错。5.6.6 版本之前默认值为 50 ， 之后的版本默认为 50 + （max_connections / 5）， 但最大不超过900。
https://www.codenong.com/cs106668887/


（1）代码中sql语句优化

（2）数据库字段优化，索引优化

（3）加缓存，redis/memcache等

（4）主从，读写分离

（5）分区表

（6）垂直拆分，解耦模块

（7）水平切分
https://blog.csdn.net/u011277123/article/details/90445580

---
To read
分布式高并发下mysql数据库读写分离
https://cloud.tencent.com/developer/article/1021008

mysql高可用架构设计，处理高并发，大流量！
https://cloud.tencent.com/developer/article/1056158

高并发让MySQL瘫痪？不存在的，我有线程池
https://cloud.tencent.com/developer/article/1143417

大话－高并发
https://cloud.tencent.com/developer/article/1055315

【高并发】redis队列缓存 + mysql 批量入库 + php离线整合
https://cloud.tencent.com/developer/article/1417376

MySQL 并发线程的理解
https://www.modb.pro/db/25871

mysql写并发1万+的处理方案
https://my.oschina.net/osokra/blog/1936341

MySQL Innodb 并发涉及参数
https://www.lagou.com/lgeduarticle/27314.html

InnoDB中的页合并与分裂
https://zhuanlan.zhihu.com/p/98818611

一般mysql结合redis缓存，读取数据时先从redis中查询，查询不到再从mysql中查询；写入数据首先更新mysql，然后拿到更新结果之后，刷新缓存数据或者删除缓存数据
https://www.zhihu.com/question/20734566

锁， 隔离级别
隔离级别的实现是由锁和MVCC来实现的

MySQL的可重复读级别能解决幻读吗
很明显可重复读的隔离级别没有办法彻底的解决幻读的问题，如果我们的项目中需要解决幻读的话也有两个办法：

使用串行化读的隔离级别
MVCC+next-key locks：next-key locks由record locks(索引加锁) 和 gap locks(间隙锁，每次锁住的不光是需要使用的数据，还会锁住这些数据附近的数据)

实际上很多的项目中是不会使用到上面的两种方法的，串行化读的性能太差，而且其实幻读很多时候是我们完全可以接受的。
https://juejin.im/post/6844903799534911496

幻读错误的理解：说幻读是 事务A 执行两次 select 操作得到不同的数据集，即 select 1 得到 10 条记录，select 2 得到 11 条记录。
这其实并不是幻读，这是不可重复读的一种，只会在 R-U R-C 级别下出现，而在 mysql 默认的 RR 隔离级别是不会出现的。

幻读，并不是说两次读取获取的结果集不同，幻读侧重的方面是某一次的 select 操作得到的结果所表征的数据状态无法支撑后续的业务操作。
更为具体一些：select 某记录是否存在，不存在，准备插入此记录，但执行 insert 时发现此记录已存在，无法插入，此时就发生了幻读。
https://www.lagou.com/lgeduarticle/111750.html

读提交解决了脏读问题，行锁解决了并发更新的问题。并且 MySQL 在可重复读级别解决了幻读问题，是通过行锁和间隙锁的组合 Next-Key 锁实现的。
https://www.cnblogs.com/fengzheng/p/12557762.html

Select…For Update语句的语法与select语句相同，只是在select语句的后面加FOR UPDATE [NOWAIT]子句。

该语句用来锁定特定的行（如果有where子句，就是满足where条件的那些行）。当这些行被锁定后，其他会话可以选择这些行，
但不能更改或删除这些行，直到该语句的事务被commit语句或rollback语句结束为止。
https://blog.csdn.net/wo198711203217/article/details/28870779

1、InnoDB行锁是通过给索引上的索引项加锁来实现的，只有通过索引条件检索数据，InnoDB才使用行级锁，否则，InnoDB将使用表锁。

2、由于MySQL的行锁是针对索引加的锁，不是针对记录加的锁，所以虽然是访问不同行的记录，但是如果是使用相同的索引键，是会出现锁冲突的。应用设计的时候要注意这一点。
3、当表有多个索引的时候，不同的事务可以使用不同的索引锁定不同的行，另外，不论是使用主键索引、唯一索引或普通索引，InnoDB都会使用行锁来对数据加锁。
4、即便在条件中使用了索引字段，但是否使用索引来检索数据是由MySQL通过判断不同执行计划的代价来决定的，如果MySQL认为全表扫描效率更高，比如对一些很小的表，它就不会使用索引，这种情况下InnoDB将使用表锁，而不是行锁。因此，在分析锁冲突时，别忘了检查SQL的执行计划，以确认是否真正使用了索引。
5、检索值的数据类型与索引字段不同，虽然MySQL能够进行数据类型转换，但却不会使用索引，从而导致InnoDB使用表锁。通过用explain检查两条SQL的执行计划，我们可以清楚地看到了这一点。
https://blog.csdn.net/claram/article/details/54023216

只使用唯一索引查询，并且只锁定一条记录时，innoDB会使用行锁。
只使用唯一索引查询，但是检索条件是范围检索，或者是唯一检索然而检索结果不存在（试图锁住不存在的数据）时，会产生 Next-Key Lock。
使用普通索引检索时，不管是何种查询，只要加锁，都会产生间隙锁。
同时使用唯一索引和普通索引时，由于数据行是优先根据普通索引排序，再根据唯一索引排序，所以也会产生间隙锁。
2人点赞
请叫我工程师


作者：白花蛇草可乐
链接：https://www.jianshu.com/p/d5c2613cbb81
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

Mybatis Select…for update用法
https://blog.csdn.net/ToBeABetterOne/article/details/78227033

1）Spring 的声明式事务管理在底层是建立在 AOP 的基础之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，
在执行完目标方法之后根据执行情况提交或者回滚事务。
http://mojito515.github.io/blog/2016/08/31/transactionalinspring/

用最简单的springboot+mybatis解释for update的使用场景
https://blog.csdn.net/qq_31748587/article/details/99237631

http://www.mybatis.cn/324.html

乐观锁
https://blog.csdn.net/hh1sdfsf56456/article/details/79449714

Restful SQL
https://github.com/jirutka/rsql-parser
https://cwiki.apache.org/confluence/display/CXF20DOC/JAX-RS+Search#JAXRSSearch-JPA2.0

读写分离
https://blog.csdn.net/justdb/article/details/17331569
https://blog.csdn.net/justdb/article/details/17331569
正例
首先，读写分离增加了物力服务器，主库负责写入，然后再同步到从库。负责写入的库不考虑读出，负责读出的不考虑写入，一定程度上减少了锁的争用。
其次，读写分离不仅仅可以分离出来一个只读库，你可以一个写库，然后拖多个只读库，把流量分布到多个读的库上面去。当然就大幅度的降低了每个数据库的负载。
反例
如果你读写分离用在了写大于读的情况下，每次写入数据库，都需要花费一些性能 同步到只读库。而只读库对分摊负载没有什么帮助。
如果你的系统对数据非常敏感，写入主库后异步发布到从库，从库一定有一定时间的差异。这个要考虑。

分库分表
https://juejin.cn/post/6844903648670007310

一般的机器（4核16G），单库的MySQL并发（QPS+TPS）超过了2k，系统基本就完蛋了。最好是并发量控制在1k左右。这里就引出一个问题，为什么要分库分表？
https://www.jianshu.com/p/11520fe5f934

高并发解决方案-mysql篇
https://segmentfault.com/a/1190000021562093

mysql 死锁
https://blog.csdn.net/noaman_wgs/article/details/82529908

☑mySQL 链接池
☐https://juejin.cn/post/6845166890986242056
