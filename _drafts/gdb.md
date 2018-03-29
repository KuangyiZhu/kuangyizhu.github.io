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


### reference
* http://blog.jobbole.com/107759/ gdb 调试入门，大牛写的高质量指南
* https://blog.csdn.net/baidu_mtc/article/details/50504608 利用进程信息追查内存泄漏
