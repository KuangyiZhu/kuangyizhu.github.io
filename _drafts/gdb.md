#### 反汇编
disas funcname
安装了调试信息之后，gdb 可以同时列出源码和汇编
disas/s

#### 查看寄存器
使用 i r（info registers 的简写）打印寄存器值：
你可以使用 i proc m （info proc mappings 的简写）核查零是不是有效地址

#### 单步调试
我跳到下一条指令（si，stepi的简写），然后检查寄存器

#### 回退
record
reverse-stepi

### tui
有必要看看这些代码在 gdb 的文本用户界面（TUI）里是什么样的，我用的不多，是看了 Greg 的演讲之后受到的启发。

你可以用 –tui 来启动

用 layout split 命令，我们可以在不同的窗口查看源代码和汇编代码

#### 外部工具：perf-tools/ftrace/uprobes
uprobe 'p:/lib/x86_64-linux-gnu/libtinfo.so.5:set_curterm %di'

#### x86_64_ABI
man syscall

#### 写内存
set

#### 条件断点
b set_curterm
cond 1 $rdi==0x0

#### 返回命令
ret


3） 因为是64位应用程序，所以指针占8字节。所以需要遍历的指针个数为135168/8=16896。 
4） 将结果输出到日志文件gdb.txt中：

(gdb) set height 0
(gdb) set logging on
Copying output to gdb.txt.
(gdb) x/16896a 0x0000000000602000
1
2
3
4
gdb.txt的内容：

0x602000:       0x0     0x21
0x602010:       0x400b30 <_ZTV3ABC+16>  0x0
0x602020:       0x0     0x21
0x602030:       0x400b30 <_ZTV3ABC+16>  0x0
….
1
2
3
4
5
5） 过滤gdb.txt：

awk '{print $2"/n"$3}' gdb.txt|C++filt|grep vtable>gdb_vtable.txt
1
gdb_vtable.txt的内容为：

<vtable for ABC+16>
<vtable for ABC+16>
<vtable for ABC+16>
<vtable for ABC+16>
1
2
3
4
6） 将gdb_vtable.txt的内容导入到SQLServer中（如果记录不多，可以用Excel代替）。表名为gdb_vtable，第一列Col001为符号。对其分组求和：

select Col001, count(1) quantity from gdb_vtable
group by Col001
order by quantity desc
1
2
3
结果为：

Col001                                                                                    quantity
<vtable for ABC+16>                                                              1000
<vtable for std::bad_alloc@@GLIBCXX_3.4+16>          
1
2
3
可知core里有1000个ABC，遍历使用ABC的代码，可知存在泄漏。

### 死锁

清单 7. 切换到线程 5 的输出
1
2
3
4
5
6
7
8
9
10
11
(gdb) thread 5 
[Switching to thread 5 (Thread 0x41e37940 (LWP 6722))]#0  0x0000003d1a80d4c4 in 
__lll_lock_wait () from /lib64/libpthread.so.0 
(gdb) where 
#0  0x0000003d1a80d4c4 in __lll_lock_wait () from /lib64/libpthread.so.0 
#1  0x0000003d1a808e1a in _L_lock_1034 () from /lib64/libpthread.so.0 
#2  0x0000003d1a808cdc in pthread_mutex_lock () from /lib64/libpthread.so.0 
#3  0x0000000000400a9b in func1 () at lock.cpp:18 
#4  0x0000000000400ad7 in thread1 (arg=0x0) at lock.cpp:43 
#5  0x0000003d1a80673d in start_thread () from /lib64/libpthread.so.0 
#6  0x0000003d19cd40cd in clone () from /lib64/libc.so.6
清单 8. 线程 4 和线程 5 的输出
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
(gdb) f 3 
#3  0x0000000000400a9b in func1 () at lock.cpp:18 
18          pthread_mutex_lock(&mutex2); 
(gdb) thread 4 
[Switching to thread 4 (Thread 0x42838940 (LWP 6723))]#0  0x0000003d1a80d4c4 in 
__lll_lock_wait () from /lib64/libpthread.so.0 
(gdb) f 3 
#3  0x0000000000400a17 in func2 () at lock.cpp:31 
31          pthread_mutex_lock(&mutex1); 
(gdb) p mutex1 
$1 = {__data = {__lock = 2, __count = 0, __owner = 6722, __nusers = 1, __kind = 0, 
__spins = 0, __list = {__prev = 0x0, __next = 0x0}}, 
 __size = "\002\000\000\000\000\000\000\000B\032\000\000\001", '\000'
<repeats 26 times>, __align = 2} 
(gdb) p mutex3 
$2 = {__data = {__lock = 0, __count = 0, __owner = 0, __nusers = 0, 
__kind = 0, __spins = 0, __list = {__prev = 0x0, __next = 0x0}}, 
__size = '\000' <repeats 39 times>, __align = 0} 
(gdb) p mutex2 
$3 = {__data = {__lock = 2, __count = 0, __owner = 6723, __nusers = 1, 
__kind = 0, __spins = 0, __list = {__prev = 0x0, __next = 0x0}}, 
 __size = "\002\000\000\000\000\000\000\000C\032\000\000\001", '\000'
<repeats 26 times>, __align = 2} 
(gdb)

### calling convetion
if it is x86_64_ABI , count the instructions in reverse order, if it matches then it is x86_64_ABI

### Name Mangling

### gcc version number

### gdb source path
有时gdb不能准确地定位到源文件的位置（比如文件被移走了，等等），此时可以用directory命令设置查找源文件的路径。以上面程序为例：

### 多线程调试
set scheduler-locking off|on|step 估计是实际使用过多线程调试的人都可以发现，在使用step或者continue命令调试当前被调试线程的时候，其他线程也是同时执行的，怎么只让被调试程序执行呢？通过这个命令就可以实现这个需求。off 不锁定任何线程，也就是所有线程都执行，这是默认值。 on 只有当前被调试程序会执行。 step 在单步的时候，除了next过一个函数的情况(熟悉情况的人可能知道，这其实是一个设置断点然后continue的行为)以外，只有当前线程会执行。

### 查常量的值
找到rodata section objdump -h exe 看offset
xxd exe


### reference
* http://blog.jobbole.com/107759/ gdb 调试入门，大牛写的高质量指南
* https://blog.csdn.net/baidu_mtc/article/details/50504608 利用进程信息追查内存泄漏
* https://en.wikipedia.org/wiki/X86_calling_conventions calling convention
* https://c9x.me/compile/doc/abi.html System V ABI AMD64
* http://www.cnblogs.com/gqtcgq/p/7530384.html
* http://blog.51cto.com/hipercomer/855223
* https://stackoverflow.com/questions/16349557/does-gcc-have-any-options-to-add-version-info-in-elf-binary-file
* https://wizardforcel.gitbooks.io/100-gdb-tips/directory.html
* https://stackoverflow.com/questions/45236977/extract-a-string-variable-from-elf-obj-file
* http://xq.dropsec.xyz/2016/11/12/GDB%E5%AF%84%E5%AD%98%E5%99%A8%E5%92%8C%E5%86%85%E5%AD%98%E6%9F%A5%E8%AF%A2%E6%8C%87%E4%BB%A4/
* http://www.cnblogs.com/xuxm2007/archive/2011/04/01/2002162.html
