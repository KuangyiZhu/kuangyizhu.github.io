We used tcpdump to capture any UDP packets on port 8125 like so:

```
tcpdump -i lo udp port 8125 -vv -X
```
To briefly explain the options we passed to it:

-i lo only captures packets on the local loopback i.e. packets sent to localhost
udp means that only UDP packets will be captured. Other types of packets we might capture could be tcp or icmp for example.
-vv just gives us more verbose output
-X prints out the data in the UDP packets in ASCII as well as hex. If we just wanted the latter we could use the -x option

### 系统调用

为什么系统调用比普通函数调用耗时？如果是因为需要用户态和内核态的切换，那么这个切换具体做了什么，哪些步骤代价大呢？

 评论邀请回答编辑

novo   22
2014-05-28 提问
2 个回答
当程序中有系统调用语句，程序执行到系统调用时，首先使用类似int 80H的软中断指令，保存现场，去的系统调用号，在内核态执行，然后恢复现场，每个进程都会有两个栈，一个内核态栈和一个用户态栈。当执行int中断执行时就会由用户态，栈转向内核栈。系统调用时需要进行栈的切换。而且内核代码对用户不信任，需要进行额外的检查。系统调用的返回过程有很多额外工作，比如检查是否需要调度等。

 评论赞赏编辑

wangsquirrel   150
2014-05-28 回答
系统调用一般都需要保存用户程序得上下文(context), 在进入内核得时候需要保存用户态得寄存器，在内核态返回用户态得时候会恢复这些寄存器得内容。这是一个开销的地方。 如果需要在不同用户程序间切换的话，那么还要更新cr3寄存器，这样会更换每个程序的虚拟内存到物理内存映射表的地址，也是一个比较高负担的操作。

        由2.1 Socket系统调用我们知道，一个TCP socket在内核有一个数据结构，这个数据结构是不能被两个及其以上的使用者同时访问的，否则就会由于数据不一致导致严重的问题。在Linux中，TCP socket的使用者有两种：进程（线程）和软中断。同一时间可能会有两个进程（线程），或位于不同CPU的两个软中断，或进程（线程）与软中断访问同一个socket。既然socket在同一时刻只能被一个使用者访问，那么互斥机制是如何实现的呢？是使用锁完成的。进程（线程）在访问socket之前会申请锁，访问结束时释放锁。软中断也是一样，但软中断所申请的锁与进程（线程）不同。TCP的内核同步就是靠锁实现的。由于TCP并不区分进程与线程，所以下面进程和线程一律用进程指代。
--------------------- 
作者：Remy1119 
来源：CSDN 
原文：https://blog.csdn.net/u011130578/article/details/45040273 
版权声明：本文为博主原创文章，转载请附上博文链接！

进程T2调用lock_sock_nested函数时会调用__lock_sock函数：

send -> sys_call->SYS_SEND->sys_send->sock_sendmsg函数，sock_sendmsg函数又会调用sock_sendmsg_noac函数，最后调用由struct socket数据结构中struct proto_ops *ops数据域引用的协议实现函数sendmsg； 在TCP/IP协议中最终会调用inet_sendmsg, inet_sendmsg 调用sock的proto 调用tcp_sendmsg

系统调用顺序 sys_call -> af_inet -> tcp


### references
* [1] https://www.ibm.com/developerworks/cn/linux/l-async/
* [2] https://blog.csdn.net/shreck66/article/details/48765533
* [3] https://blog.csdn.net/daniel_ustc/article/details/8757570
* [4] https://blog.csdn.net/ezimu/article/details/54892088
* [5] https://blog.csdn.net/inject2006/article/details/2139149
* [6] http://www.aboutlinux.info/2006/11/ifconfig-dissected-and-demystified.html
* [7] https://www.binarytides.com/linux-commands-monitor-network/
* [8] https://markhneedham.com/blog/2012/07/15/tcpdump-learning-how-to-read-udp-packets/
* [9] https://segmentfault.com/q/1010000000522752
* [10] http://cxd2014.github.io/2016/07/30/socket-implement/
* [11] https://blog.csdn.net/u011130578/article/details/45040273
* [12] http://cxd2014.github.io/2016/07/30/socket-implement/
* [13] http://drmingdrmer.github.io/tech/programming/network/2015/07/28/close-shutdown.html
* [14] https://blog.csdn.net/junjun150013652/article/details/38032705
* [15] https://www.cnblogs.com/wanpengcoder/p/7623072.html
