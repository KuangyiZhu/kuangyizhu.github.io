class loader


		//3.URL路径方式显示加载Jar文件（前面的getResource返回的亦是URL)对象
		//URL Uniform Resource Locator 统一资源定位符
		//jar协议格式 jar:other/!/entryPath
		URL url = new URL("jar:file:/D:/workspaceForTest/demo/lib/commons-lang3-3.8.1.jar!/");
————————————————
版权声明：本文为CSDN博主「脱线宅」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_21508059/article/details/83510070

下面这幅图是Pandora官方文档描述的类加载器之间关系的一幅图，ATA上很多文章也引用了这幅图，我这里也贴一下： ￼
PandoraClassLoader：
pandoraClassLoader是用来加载PandoraContainer并启动整个Pandora容器的，在使用pandora-client启动的情况下，com.alibaba.middleware.pandoraclient.PandoraClientFactory会创建一个URLClassLoader的实例，设置类加载路径为Pandora sar包路径下的lib目录，用这个ClassLoader加载com.taobao.pandora.PandoraContainer并反射调用其方法，因此这个ClassLoader会被作为pandoraClassLoader。 本质上来说，这个ClassLoader没有什么特别之处，Pandora内部的类默认情况下都会用这个ClassLoader进行加载，后面的ModuleClassLoader会在加载逻辑中使用到这个ClassLoader。 
上面是Pandora文档对类加载过程的描述，讲的很清晰。我觉得可以这样理解，当应用启动的时候，首先会启动bizClassLoader，然后bizClassLoader创建了一个PandoraClient工厂，这个工厂是用来创建pandoraClassLoader，然后还记得我们上面说的Pandora目录下的lib包下有Pandora-Container jar包吗？pandoraClassLoader继承了URLClassLoader，所以PandoraClassLoader可以通过目录找到pandora-container这个jar包，加载之后创建了Pandora容器。
MoudleClassLoader：
ModuleClassLoader是URLClassLoader的子类，设置类加载路径为插件路径下的lib目录。其双亲委派的父节点为空，并且覆盖了loadClass方法，自己设定了类的加载逻辑。
每个插件都由MoudleClassLoader加载，这样，就保证了隔离性，这也是pandora实现隔离的核心原理。


