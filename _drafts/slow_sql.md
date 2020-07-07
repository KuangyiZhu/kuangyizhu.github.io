慢SQL

MySQL5.0以上的版本可以支持将执行比较慢的SQL语句记录下来。

mysql> show variables like 'long%';
long_query_time | 10.000000

mysql> set long_query_time=1;

mysql> show variables like 'slow%';
slow_launch_time    | 2
slow_query_log      | ON
slow_query_log_file | /tmp/slow.log
mysql> set global slow_query_log='ON'


作者：点融黑帮
链接：https://www.jianshu.com/p/43091bfa8aa7
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

https://juejin.im/post/5982b6496fb9a03c476d6d1d
https://www.cnblogs.com/williamjie/p/11187470.html

索引下推
https://coderbee.net/index.php/db/20190718/1901
