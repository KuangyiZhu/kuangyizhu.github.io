### basics
#### compile
```
javac src/org/kzhu/* -d bin/
```
#### execute
```
java org.kzhu.HelloWorld
```
#### archieve
```
>type manifest.txt   #查看清单信息的内容
    Main-Class: HelloWorld

>jar -cvfm HelloWorld.jar manifest.txt HelloWorld.class   #添加清单信息到jar包
```

#### class path
```
Update the PATH Variable (Solaris and Linux)

You can run the JDK just fine without setting the PATH variable, or you can optionally set it as a convenience. However, you should set the path variable if you want to be able to run the executables (javac, java, javadoc, and so on) from any directory without having to type the full path of the command. If you do not set the PATH variable, you need to specify the full path to the executable every time you run it, such as:

% /usr/local/jdk1.7.0/bin/javac MyClass.java
To find out if the path is properly set, execute:

% java -version
This will print the version of the java tool, if it can find it. If the version is old or you get the error java: Command not found, then the path is not properly set.

To set the path permanently, set the path in your startup file.

For C shell (csh), edit the startup file (~/.cshrc):

set path=(/usr/local/jdk1.7.0/bin $path)
For bash, edit the startup file (~/.bashrc):

PATH=/usr/local/jdk1.7.0/bin:$PATH
export PATH
For ksh, the startup file is named by the environment variable, ENV. To set the path:

PATH=/usr/local/jdk1.7.0/bin:$PATH
export PATH
For sh, edit the profile file (~/.profile):

PATH=/usr/local/jdk1.7.0/bin:$PATH
export PATH
Then load the startup file and verify that the path is set by repeating the java command:

For C shell (csh):

% source ~/.cshrc
% java -version
For ksh, bash, or sh:

% . /.profile
% java -version
```
```
The preferred way to specify the class path is by using the -cp command line switch. This allows the CLASSPATH to be set individually for each application without affecting other applications. Setting the CLASSPATH can be tricky and should be performed with care.

The default value of the class path is ".", meaning that only the current directory is searched. Specifying either the CLASSPATH variable or the -cp command line switch overrides this value.

To check whether CLASSPATH is set on Microsoft Windows NT/2000/XP, execute the following:

C:> echo %CLASSPATH%
On Solaris or Linux, execute the following:

% echo $CLASSPATH
```
#### 查看字节码
```
javap -c test.class
```
#### jstack
```
jstack pid
jstack -m pid #dump native frame
jstack -l pid #also dump locks
```

#### jmap
```
jmap -heap pid #print heap summary
jmap pid #print the memory mapping
```

#### misc
2. Java
 2.3. jni and principle
    https://blog.csdn.net/hackooo/article/details/48395765/
 2.4. enumurable
    http://www.hollischuang.com/archives/205
 2.7. JIT book
 2.8. Java TI
 2.9. all java local threads
    http://www.xiuson.com/?p=203
    https://blog.csdn.net/chenxiusheng/article/details/74007750
    https://blog.csdn.net/neosmith/article/details/79614838
    https://www.ezlippi.com/blog/2018/01/linux-high-load.html
    http://www.rowkey.me/blog/2016/11/02/java-profile/
 2.10.spring
 2.11.gc
  <https://www.cubrid.org/blog/understanding-java-garbage-collection/
  https://www.cubrid.org/blog/how-to-monitor-java-garbage-collection/
  http://www.oracle.com/technetwork/articles/java/vmoptions-jsp-140102.html
  https://www.javacodegeeks.com/2011/07/jvm-options-client-vs-server.html
The Server VM contains an advanced adaptive compiler that supports many of the same types of optimizations performed by optimizing C++ compilers, as well as some optimizations that cannot be done by traditional compilers, such as aggressive inlining across virtual method invocations. This is a competitive and performance advantage over static compilers. Adaptive optimization technology is very flexible in its approach, and typically outperforms even advanced static analysis and compilation techniques.

Both solutions deliver extremely reliable, secure, and maintainable environments to meet the demands of today’s enterprise customers.

Default options:

For Hotspot is client
For JRockit is server
 https://www.cubrid.org/blog/how-to-tune-java-garbage-collection/
 2.12. JMH
 2.13. serialization
 2.14. how actually java works



### references
* [1] https://docs.oracle.com/javase/tutorial/essential/environment/paths.html PATH and CLASSPATH
* [2] https://www.oracle.com/technetwork/articles/java/architect-evans-pt1-2266278.html Understanding Java JIT Compilation with JITWatch, Part 1
* [3] https://www.cnblogs.com/zengkefu/p/6910355.html 使用hsdis查看jit生成的汇编代码
### external references
https://www.cnblogs.com/chenyangyao/p/5269622.html
深入理解Java虚拟机：JVM高级特性与最佳实践 第2版 高清PDF+源码_Linux下载_Linux公社-Linux系统门户网站
