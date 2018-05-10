


### C++ 筆記 delete this

在 reference count based 的 class，通常當 refCount 在 release function 被減至 0 時，使用 delete this 來結束自己的生命。使用 delete this 是一個合法但危險的指令。在 delete this 之後，基本上 this 即是一個 dangling pointer。

只能使用在在 heap 空間生成的 object，也是符合 new/delete 成對的原則。相對於 stack 空間的物件，呼叫 delete 是 unsafe 的。 Calling delete on variable allocated on the stack
delete this 所做的其中之一動作，就是呼叫 destructor。故你無法在 destructor 裡使用 delete this。
stack 生成的自動物件，都會在 out-of-block 的時候，呼叫 destructor。可以將 desturctor label 成 private 的。這樣子就能保證，只能由 delete this 這端將 object 消滅。(stack based 的自動變會造成 compile error)


### 如何查看进程发生缺页中断的次数？

用ps -o majflt,minflt -C program命令查看。

majflt代表major fault，中文名叫大错误，minflt代表minor fault，中文名叫小错误。这两个数值表示一个进程自启动以来所发生的缺页中断的次数。

### 发生缺页中断后，执行了那些操作？

当一个进程发生缺页中断的时候，进程会陷入内核态，执行以下操作：

1. 检查要访问的虚拟地址是否合法
2. 查找/分配一个物理页
3. 填充物理页内容（读取磁盘，或者直接置0，或者啥也不干）
4. 建立映射关系（虚拟地址到物理地址）
5. 重新执行发生缺页中断的那条指令

如果第3步，需要读取磁盘，那么这次缺页中断就是majflt，否则就是minflt。

默认情况下，malloc函数分配内存，如果请求内存大于128K（可由M_MMAP_THRESHOLD选项调节），那就不是去推_edata指针了，而是利用mmap系统调用，从堆和栈的中间分配一块虚拟内存。

这样子做主要是因为::
brk分配的内存需要等到高地址内存释放以后才能释放（例如，在B释放之前，A是不可能释放的，这就是内存碎片产生的原因，什么时候紧缩看下面），而mmap分配的内存可以单独释放。

### Memory Fragmentation

To see memory fragmentation you can use the magic SysRq key. Simply execute the following command:
```
# echo m > /proc/sysrq-trigger
```
This command will dump current memory information to /var/log/messages. Here is an example of a RHEL3 32-bit system:
```
Jul 23 20:19:30 localhost kernel: 0*4kB 0*8kB 0*16kB 1*32kB 0*64kB 1*128kB 1*256kB 1*512kB 1*1024kB 0*2048kB 0*4096kB = 1952kB)
Jul 23 20:19:30 localhost kernel: 1395*4kB 355*8kB 209*16kB 15*32kB 0*64kB 0*128kB 0*256kB 0*512kB 0*1024kB 0*2048kB 0*4096kB = 12244kB)
Jul 23 20:19:31 localhost kernel: 1479*4kB 673*8kB 205*16kB 73*32kB 21*64kB 847*128kB 473*256kB 92*512kB 164*1024kB 64*2048kB 28*4096kB = 708564kB)
```
The first line shows DMA memory fragmentation. The second line shows Low Memory fragmentation and the third line shows High Memory fragmentation. The output shows memory fragmentation in the Low Memory area. But there are many large memory chunks available in the High Memory area, e.g. 28 4MB.

If memory information was not dumped to /var/log/messages, then SysRq was not enabled. You can enable SysRq by setting sysrq to 1:

```
# echo 1 > /proc/sys/kernel/sysrq
```

### reference
* [1] https://medium.com/@andrestc/implementing-malloc-and-free-ba7e7704a473 implementing malloc and free
* [2] https://blog.csdn.net/jasonblog/article/details/5989224 delete this
* [3] https://git.kernel.org/pub/scm/virt/kvm/kvm.git/tree/Documentation/memory-barriers.txt memory barrier
* [4] https://vinoit.me/2016/05/20/linux-memory-alloc/ Linux内存分配的原理--malloc/brk/mmap
* [5] https://yangrz.github.io/blog/2017/12/20/ptmalloc/ 聊聊glibc ptmalloc内存管理哪些事儿
* [6] https://stackoverflow.com/questions/4863707/how-to-see-linux-view-of-the-ram-in-order-to-determinate-the-fragmentation Memory Fragmentation
