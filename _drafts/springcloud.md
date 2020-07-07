Zzz

Spring Cloud，可以使用Eureka作为服务发现引擎，Ribbon作为客户端负责均衡。
 可以先用Hystrix来实现这些模式
Netflix的Zuul是一个服务网关，其核心是一个反向代理中间服务器，位于尝试访问客户端和资源之间。

服务发现
客户端弹性模式


1.关联多个服务调用

使用Spring Cloud Sleuth将关联ID添加到各个服务调用中。

2.聚合日志

使用Papertrail可以聚合多个日志到单个数据库中。

3.可视化服务调用

Zipkin是可视化工具，可以显示跨多个服务的事务流。

https://www.atatech.org/articles/164230?spm=ata.13269325.0.0.652d49fac2HOH1

concurrent map
hassian


分布式锁
1）redis setnx里面已经有参数可以支持分布式锁，但是最好能把锁的拥有方存到value里，释放的时候做比较，不然可能释放错锁,也就是会出现A释放了B的锁。
2）zk采用创建临时节点，其他创建失败的线程监听锁的状态。
SET resource_name my_random_value NX PX 30000


配置中心
配置中心的需求：保证高可用，实时通知，灰度发布，权限控制，一键回滚，环境隔离（开发 测试 生产）目前的开源实现：nacos disconf apollo。

disconf:scan模块扫描注解和监听器，store模块将远程获取到的配置存储到本地，本地一个job检测配置是否有变化，有变化就通知监听器，fetch模块从远程通过http获取配置，watch模块监听zookeeper上节点的变化，有变化就会调用fetch进行获取.

springboot 事件监听
ApplicationListener
https://blog.csdn.net/liaokailin/article/details/48186331


缓存击穿
https://blog.csdn.net/zeb_perfect/article/details/54135506

JVM内存结构
https://www.cnblogs.com/ityouknow/p/5610232.html

stream的注意事项
在Streams开始和结束之前，都需要避免处理null值，使用filter可以过滤掉；
不要滥用Streams，因为我发现Stream有时候会让代码变得更难以阅读；
针对Collections的迭代处理，可以多用Stream处理；
要谨慎使用Parallel Streams，性能不一定比普通的loop要好；
最后，对外暴露API的入参检查需要更全面；


redis 分布式锁
https://blog.csdn.net/wanghang88/article/details/53028009
https://www.jianshu.com/p/81b0f1bd1328


JVM
https://www.cnblogs.com/ityouknow/p/5610232.html

服务发现模式
独立LB
feign
进程内LB
ribbon
进程独立LB

https://www.kancloud.cn/architect-jdxia/architect/520104
https://www.jianshu.com/p/ece3e0ffc70c

StoreMoveInstruction in urcs-basic-client
chooseEndPointByRcs 现在都是RCS选点


isOpenSimulate 


Future 

BiConsumer

TCC
2fase

What if redis read and write

ThreadPool
corePoolSize： 线程池核心线程数最大值
maximumPoolSize： 线程池最大线程数大小
keepAliveTime： 线程池中非核心线程空闲的存活时间大小
unit： 线程空闲存活时间单位
workQueue： 存放任务的阻塞队列
threadFactory： 用于设置创建线程的工厂，可以给创建的线程设置有意义的名字，可方便排查问题。
handler：  线城池的饱和策略事件，主要有四种类型。

作者：Jay_huaxiao
链接：https://juejin.im/post/5d1882b1f265da1ba84aa676
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


线程池都有哪几种工作队列？

ArrayBlockingQueue
LinkedBlockingQueue
DelayQueue
PriorityBlockingQueue
SynchronousQueue

ArrayBlockingQueue
ArrayBlockingQueue（有界队列）是一个用数组实现的有界阻塞队列，按FIFO排序量。
LinkedBlockingQueue
LinkedBlockingQueue（可设置容量队列）基于链表结构的阻塞队列，按FIFO排序任务，容量可以选择进行设置，不设置的话，将是一个无边界的阻塞队列，最大长度为Integer.MAX_VALUE，吞吐量通常要高于ArrayBlockingQuene；newFixedThreadPool线程池使用了这个队列
DelayQueue
DelayQueue（延迟队列）是一个任务定时周期的延迟执行的队列。根据指定的执行时间从小到大排序，否则根据插入到队列的先后排序。newScheduledThreadPool线程池使用了这个队列。
PriorityBlockingQueue
PriorityBlockingQueue（优先级队列）是具有优先级的无界阻塞队列；
SynchronousQueue
SynchronousQueue（同步队列）一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQuene，newCachedThreadPool线程池使用了这个队列。
针对面试题：线程池都有哪几种工作队列？ 我觉得，回答以上几种ArrayBlockingQueue，LinkedBlockingQueue，SynchronousQueue等，说出它们的特点，并结合使用到对应队列的常用线程池(如newFixedThreadPool线程池使用LinkedBlockingQueue)，进行展开阐述， 就可以啦。
几种常用的线程池

newFixedThreadPool (固定数目线程的线程池)
newCachedThreadPool(可缓存线程的线程池)
newSingleThreadExecutor(单线程的线程池)
newScheduledThreadPool(定时及周期执行的线程池)

作者：Jay_huaxiao
链接：https://juejin.im/post/5d1882b1f265da1ba84aa676
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

https://juejin.im/post/5d1882b1f265da1ba84aa676
https://juejin.im/entry/58fada5d570c350058d3aaad


SPI
serviceLoader = classloader。getserviceloader
classloader URLclassloader 
https://blog.csdn.net/Memery_last/article/details/83830475

@Autowired
https://blog.csdn.net/qq_28587263/article/details/75570745

https://juejin.im/post/5be13b83f265da6116393fc7 拜托！面试请不要再问我Spring Cloud底层原理
https://segmentfault.com/a/1190000004960668 微服务系统中的服务发现机制
https://blog.csdn.net/alex_xfboy/article/details/77947429?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task 注册中心eureka,consul,zooKeeper,etcd选型
https://blog.csdn.net/peterwanghao/article/details/84835631 服务发现的基本原理与比较：Eureka vs Consul vs Zookeeper
https://blog.csdn.net/qq_38891512/article/details/82083389 java面试题整理-微服务、SpringBoot、SpringCloud

* 并行计算
 - 缓存
 https://www.imooc.com/article/36399
 https://juejin.im/post/5ad6e4066fb9a028d82c4b66
 https://juejin.im/post/5c3c1df2e51d45207f54b189
 https://zhuanlan.zhihu.com/p/32540678
 https://youzhixueyuan.com/redis-interview-question-49-answers.html
 - HA
 - spring
 - sql
 - metaQ

c++ 
 placement new
 内存池
https://www.jianshu.com/p/18c7e8a900a5

simulation and optimzation

大数据
 map reduce partitioner
https://blog.csdn.net/WYpersist/article/details/80102778
https://blog.csdn.net/AC_great/article/details/47870331
https://www.cnblogs.com/cheng5350/p/11872221.html


https://wizardforcel.gitbooks.io/the-art-of-programming-by-july/content/02.01.html

Network
Congestion


springboot configuration
java classloader
java spi

Eva redis stringredistemplate?
Redis lRU
Redis version

auto sorting thread。model

Spring boot startup sequence and listeners
Spring Environment bean
Springboot value


Introduce blockpriorityqueue or delay queue


SPI
META-INF.services
 add full path of classes


Solr 使用zookeeper来进行分布式管理

Eureka server AP？

load balancing
direct connect
client service discovery
server side discovery

ribben feign 面试题
spring cloud traceid

raft based backup system


dock k8s why pod
容器抽象层 在许多物理或虚拟机上提供统一的抽象 集装箱部署


乐观锁，悲观所，java 锁，数据库锁

容器分配
spread
bin pack
random

docker swarm
k8s
apache mesos

当然，最自然的一个解法就是，我现在就启动一个 Docker 容器，里面运行四个进程。可是这样会有一个问题，这种情况下容器里面 PID=1 的进程该是谁? 比如说，它应该是我的 main 进程，那么问题来了，“谁”又负责去管理剩余的 3 个进程呢？

这个核心问题在于，容器的设计本身是一种“单进程”模型，不是说容器里只能起一个进程，由于容器的应用等于进程，所以只能去管理 PID=1 的这个进程，其他再起来的进程其实是一个托管状态。 所以说服务应用进程本身就具有“进程管理”的能力。


Pod = “进程组”

在 Kubernetes 里面，Pod 实际上正是 Kubernetes 项目为你抽象出来的一个可以类比为进程组的概念。

前面提到的，由四个进程共同组成的一个应用 Helloworld，在 Kubernetes 里面，实际上会被定义为一个拥有四个容器的 Pod，这个概念大家一定要非常仔细的理解。

就是说现在有四个职责不同、相互协作的进程，需要放在容器里去运行，在 Kubernetes 里面并不会把它们放到一个容器里，因为这里会遇到两个问题。那么在 Kubernetes 里会怎么去做呢？它会把四个独立的进程分别用四个独立的容器启动起来，然后把它们定义在一个 Pod 里面。

所以当 Kubernetes 把 Helloworld 给拉起来的时候，你实际上会看到四个容器，它们共享了某些资源，这些资源都属于 Pod，所以我们说 Pod 在 Kubernetes 里面只有一个逻辑单位，没有一个真实的东西对应说这个就是 Pod，不会有的。真正起来在物理上存在的东西，就是四个容器，这四个容器，或者说是多个容器的组合就叫做 Pod。并且还有一个概念一定要非常明确，Pod 是 Kubernetes 分配资源的一个单位，因为里面的容器要共享某些资源，所以 Pod 也是 Kubernetes 的原子调度单位。

首先我们要明确一个概念，Kubernetes并不是只支持Docker这一个容器运行时，通过我的另一篇文章什么是Kubernetes的CRI-容器运行时接口介绍的内容，我们知道Kubernetes通过CRI这个抽象层，支持除Docker之外的其他容器运行时，比如rkt甚至支持客户自定义容器运行时。因此，借助CRI这个抽象层，使得Kubernetes不依赖于底层某一种具体的容器运行时实现技术，而是直接操作pod，pod内部再管理多个业务上紧密相关的用户业务容器，这种架构便于Kubernetes做扩展。这是第一个原因。
————————————————
版权声明：本文为CSDN博主「汪子熙」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/i042416/article/details/85259003

第二个原因，我们假设Kubernetes没有pod的概念，而是直接管理容器，那么一组容器作为一个单元，假设其中一个容器死亡了，此时这个单元的状态应该如何定义呢？应该理解成整体死亡，还是个别死亡？

这个问题不易回答的原因，是因为包含了这一组业务容器的逻辑单元，没有一个统一的办法来代表整个容器组的状态，这就是Kubernetes引入pod的概念，并且每个pod里都有一个Kubernetes系统自带的pause容器的原因，通过引入pause这个与业务无关并且作用类似于Linux操作系统守护进程的Kubernetes系统标准容器，以pause容器的状态来代表整个容器组的状态。

————————————————
版权声明：本文为CSDN博主「汪子熙」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/i042416/article/details/85259003

@ConfigurationProperties https://juejin.im/post/5d3e40ec51882551c37fc309
https://blog.csdn.net/yingxiake/article/details/51263071


Springboot annotations
https://www.cnblogs.com/tanwei81/p/6814022.html
https://www.jianshu.com/p/c17328fe3d34
https://cloud.tencent.com/developer/article/1492588
https://www.zybuluo.com/javazjm/note/663082
https://www.javazhiyin.com/39567.html
https://blog.csdn.net/russle/article/details/83247163
https://blog.csdn.net/pange1991/article/details/81271541
https://www.jianshu.com/p/de7b0e124248

Springboot startup
https://juejin.im/post/5dc0d0435188255f727b9c0c


https://github.com/waylau/thymeleaf-tutorial/watchers

Java future
https://www.zhihu.com/question/20596402
https://www.cnblogs.com/williamjie/p/11187470.html
https://zhuanlan.zhihu.com/p/70701037
https://blog.csdn.net/qq_28587263/article/details/75570745
https://blog.csdn.net/genghongsheng/article/details/80409239

https://blog.csdn.net/hunan961/article/details/79206291 spring boot @value
https://blog.csdn.net/chuang504321176/article/details/80672740?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task @value with #{} ${}




https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/87030/ countdown latch 很好的解决了程序实时通信的问题


java 

AQS
公平锁
非公平锁

ReentrantLock默认使用非公平锁是基于性能考虑，公平锁为了保证线程规规矩矩地排队，需要增加阻塞和唤醒的时间开销。如果直接插队获取非公平锁，跳过了对队列的处理，速度会更快。

https://www.jianshu.com/p/f584799f1c77

如果是队列等待，则是一样的，但是非公平锁使用spin lock来探测锁是否可以被取的节省了线程切换时间

乐观锁
悲观锁
https://juejin.im/post/5b4977ae5188251b146b2fc8
乐观，只有写的时候上锁 CAS
悲观，读写都上锁 AQS synchronize

可重入锁
可重入锁又名递归锁，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。
偏向锁/轻量级锁/重量级锁
这三种锁是指锁的状态，并且是针对Synchronized。在Java 5通过引入锁升级的机制来实现高效Synchronized。这三种锁的状态是通过对象监视器在对象头中的字段来表明的。
偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。
轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。
重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞

AQS
https://www.cnblogs.com/xrq730/p/4979021.html
* 使用CAS来锁
* trylock里面有个无限循环保证取得锁失败后一直待在里面，如果被唤醒则立马跳出，这也是countDownlatch能马上响应的原因


- reservetable

使用cocurrenthashmap
eventbus， 使用blockingpriorityqueue

pointcut
https://www.cnblogs.com/liaojie970/p/7883687.html
https://blog.51cto.com/5914679/2092253

Springboot 启动
http://tengj.top/2017/03/09/springboot3/

JVM内存
新生代，老年代，永久代（方法区）
https://juejin.im/post/5c80b0f451882532cd57b541
https://juejin.im/post/59f02f406fb9a0451869f01c

hashtable cocurrent 无限循环
https://juejin.im/post/5a66a08d5188253dc3321da0

jinfo临时设置了参数：/opt/jdk/bin/jinfo -flag MaxHeapFreeRatio=70 PID，主动触发gc
https://www.jianshu.com/p/c4be5e7ef006

https://www.jianshu.com/p/be6696ac5d9e

strong reference， soft reference， weak reference， phantom reference
https://juejin.im/post/5b82c02df265da436152f5ad

java volatile 实现
https://www.cnblogs.com/xrq730/p/7048693.html

jdk 1.8 concurrent hash map
每个bucket 加锁
https://blog.csdn.net/wangxiaotongfan/article/details/52074160

数据库
事务隔离
脏读：读到未提交的数据
不可重复读：读到已经提交的数据，但是两次读的结果不一样
幻读：两次统计结果不一样，因为事务而插入影像了统计结果

隔离级别
串行：一个事物必须等另一个事物执行完
可重复读：可以看到其他事物提交的新插入数据
https://www.ibm.com/developerworks/cn/opensource/os-mysql-transaction-isolation-levels-and-locks/index.html
