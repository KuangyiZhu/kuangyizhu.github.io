


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


### reference
* [1] https://medium.com/@andrestc/implementing-malloc-and-free-ba7e7704a473 implementing malloc and free
* [2] https://blog.csdn.net/jasonblog/article/details/5989224 delete this
* [3] https://git.kernel.org/pub/scm/virt/kvm/kvm.git/tree/Documentation/memory-barriers.txt memory barrier
* [4] https://vinoit.me/2016/05/20/linux-memory-alloc/ Linux内存分配的原理--malloc/brk/mmap
