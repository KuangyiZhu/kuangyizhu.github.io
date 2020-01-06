RocketMQ
MetaQ发展历史
* 2007年，淘宝实施了“五彩石”项目，将交易系统由单机交易升级到了分布式，这个过程中产生了 Notify。 
* 2010年，阿里巴巴 B2B 部门基于 ActiveMQ 的 5.1 版本也开发了自己的一款消息引擎，称为 Napoli。 
* 2011年，Linkin 推出 Kafka 消息引擎，阿里巴巴在研究了 Kafka 的整体机制和架构设计之后，基于 Kafka 的设计使用 Java 进行了完全重写并推出了 MetaQ 1.0 版本，主要是用于解决顺序消息和海量堆积的问题，由开源社区 killme 2008 维护。 
* 2012年，阿里巴巴对于 MetaQ 进行了架构重组升级，开发出了 MetaQ 2.0，这时就发现 MetaQ 原本基于 Kafka 的架构在阿里巴巴如此庞大的体系下很难进行水平扩展，所以在 2012 年的时候就开发了 RocketMQ 3.0。 
* 2015年，又基于 RocketMQ 开发了阿里云上的 Aliware MQ 和 Notify 3.0。 
* 2016年，阿里巴巴将 RocketMQ 的内核引擎捐赠给了 Apache 基金会。 
MetaQ 和 RocketMQ 区别：两者等价，在阿里内部称为 MetaQ 3.0，对外称为 RocketMQ 3.0。
以上就是 RocketMQ 的整体发展历史，其实在阿里巴巴内部围绕着 RocketMQ 内核打造了三款产品，分别是 MetaQ、Notify 和 Aliware MQ。这三者分别采用了不同的模型：
* MetaQ 主要使用了拉模型，解决了顺序消息和海量堆积问题。 
* Notify 主要使用了推模型，解决了事务消息。 
* 云产品 Aliware MQ 则是提供了商业化的版本。 


MetaQ 事务消息
￼
* 1、发送方向 MQ 服务端发送消息。 
* 2、MQ Server 将消息持久化成功之后，向发送方 ACK 确认消息已经发送成功，此时消息为半消息。 
* 3、发送方开始执行本地事务逻辑。 
* 4、发送方根据本地事务执行结果向 MQ Server 提交二次确认（Commit 或是 Rollback），MQ Server 收到 Commit 状态则将半消息标记为可投递，订阅方最终将收到该消息；MQ Server 收到 Rollback 状态则删除半消息，订阅方将不会接受该消息。 
* 5、在断网或者是应用重启的特殊情况下，上述步骤4提交的二次确认最终未到达 MQ Server，经过固定时间后 MQ Server 将对该消息发起消息回查。 
* 6、发送方收到消息回查后，需要检查对应消息的本地事务执行的最终结果。 
* 7、发送方根据检查得到的本地事务的最终状态再次提交二次确认，MQ Server 仍按照步骤4对半消息进行操作。 
事务消息发送对应步骤1、2、3、4，事务消息回查对应步骤5、6、7。
https://www.atatech.org/articles/153345?spm=ata.13269325.0.0.33af49fasdu365

阿里中间件
http://jm.taobao.org/about/?spm=ata.13261165.0.0.550d71d3P6d7Jf


消息领域模型
Message：单位消息
Topic：软分区，对应相同的topic时，生产者对应消费者的分区标识
Tag：消息在topic基础上的二级分类
Message Queue：硬分区，物理上区分topic，一个topic对应多个message queue
Group：Consumer Group，一类 Consumer 的集合名称，这类 Consumer 通常消费一类消息，且消费逻辑一致；Producer Group，一类 Producer 的集合名称，这类 Producer 通常发送一类消息，且发送逻辑一致。
Offset：绝对偏移值，message queue中有两类offset（commitOffset和offset），前者存储在OffsetStore中表示消费到的位置，后者是在PullRequest中为拉取消息位置。
广播消费：Producer 向一些队列轮流发送消息，队列集合称为 Topic，每一个 consumer 实例消费这个 Topic 对应的所有队列。
集群消费：多个 Consumer 实例平均消费这个 topic 对应的队列集合。


作者：菜鸟小玄
链接：https://www.jianshu.com/p/b09bd8e8ee50
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

负载均衡关系图：

￼

https://www.jianshu.com/p/b09bd8e8ee50?spm=ata.13261165.0.0.550d71d3P6d7Jf

zero copy mmap
https://www.jianshu.com/p/03852a291c56
https://www.linuxjournal.com/article/6345


rocketMq github
https://github.com/apache/rocketmq?spm=ata.13261165.0.0.550d71d3P6d7Jf


metaq的逻辑存储结构是一种物理队列+逻辑队列的结构
￼
物理队列只有一个，采用固定大小的文件顺序存储消息。逻辑队列有多个，每个逻辑队列有多个分区，每个分区有多个索引。
a.消息顺序写入物理文件里面，每个文件达到一定的大小，新建一个文件继续顺序写数据（消息的写入是串行的，避免了磁盘竞争）。
b.消息的索引则顺序的写入逻辑文件中，并不存放真正的消息，只是存放指向消息的索引。 metaq对于客户端展现的是逻辑队列就是消费队列，consumer从消费队列里顺序取消息进行消费。
这种设计是把物理和逻辑分离，消费队列更加轻量化。所以metaq可以支撑更多的消费队列数，提升消息的吞吐量，并且有一定的消息堆积能力。
缺点 ： 写虽然是顺序写，但是读却是随机读的 解决办法 ：尽可能让读命中pageCache，减少磁盘IO次数， metaq的所有消息都是持久化的，先写入系统PAGECACHE（页高速缓存），然后刷盘，可以保证内存与磁盘都有一份数据，访问时，直接从内存读取。 刷盘策略分为异步和同步两种。
https://www.atatech.org/articles/152677?spm=ata.13269325.0.0.33af49fasdu365
MetaQ Vs Notfiy
￼

https://pre-baike.corp.taobao.com//images/2/2b/RocketMQ_%E5%8E%9F%E7%90%86%E7%AE%80%E4%BB%8B.pdf
4.2 Message Priority 规范中描述的优先级是指在一个消息队列中，每条消息都有不同的优先级，一般用整数来描述，优先级高的消 息先投递，如果消息完全在一个内存队列中，那么在投递前可以按照优先级排序，令优先级高的先投递。 由于 RocketMQ 所有消息都是持久化的，所以如果按照优先级来排序，开销会非常大，因此 RocketMQ 没有特 意支持消息优先级，但是可以通过变通的方式实现类似功能，即单独配置一个优先级高的队列，和一个普通优先级 的队列， 将不同优先级发送到不同队列即可。 对于优先级问题，可以归纳为 2 类 1) 只要达到优先级目的即可，不是严格意义上的优先级，通常将优先级划分为高、中、低，或者再多几个级 别。每个优先级可以用不同的 topic 表示，发消息时，指定不同的 topic 来表示优先级，这种方式可以解决 绝大部分的优先级问题，但是对业务的优先级精确性做了妥协。 2) 严格的优先级，优先级用整数表示，例如 0 ~ 65535，这种优先级问题一般使用不同 topic 解决就非常不合 项目开源主页：https://github.com/alibaba/RocketMQ 5 适。如果要让 MQ 解决此问题，会对 MQ 的性能造成非常大的影响。这里要确保一点，业务上是否确实需 要这种严格的优先级，如果将优先级压缩成几个，对业务的影响有多大？

关于分布式事务、两阶段提交协议、三阶提交协议
https://www.hollischuang.com/archives/681

metaq3重试机制实践
https://www.atatech.org/articles/18079

metaq上手指南
https://www.atatech.org/articles/49050?spm=ata.13261165.0.0.569751dfDUToGe

metaQ vs kafka
https://www.atatech.org/articles/93126?spm=ata.13261165.0.0.569751dfDUToGe

MetaQ中间件的存储结构学习
https://www.atatech.org/articles/148025?spm=ata.13269325.0.0.33af49fasdu365

消息中间件MetaQ高性能原因分析
https://www.atatech.org/articles/50240?spm=ata.13269325.0.0.678649faOykF6V

RocketMQ测试之环境搭建
https://www.atatech.org/articles/34553?rnd=89635793

Notify
https://www.atatech.org/articles/95456?spm=ata.13269325.0.0.33af49fasdu365

MetaQ特性之Kafka快速读写
https://www.atatech.org/articles/150862?spm=ata.13269325.0.0.33af49fasdu365

metaQ源码分析
https://www.atatech.org/articles/140408?spm=ata.13269325.0.0.33af49fasdu365

Notify和Metaq ——学习总结
https://www.atatech.org/articles/36160?spm=ata.13269325.0.0.256e49faP2KJwD

MetaQ原理浅析（二）---顺序、事务、定时、重试
https://www.atatech.org/articles/135488?spm=ata.13269325.0.0.256e49faP2KJwD
