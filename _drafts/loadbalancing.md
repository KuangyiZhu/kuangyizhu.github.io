负载均衡算法




https://www.jianshu.com/p/40e196414cfa


* weighted round robin load balance
 准备一个arraylist， 权重多少插多少次，然后round robin
* consistent hash
 准备一个红黑树，算出hash findfirst 第一node
