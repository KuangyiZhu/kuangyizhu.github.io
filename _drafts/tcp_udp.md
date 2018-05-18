We used tcpdump to capture any UDP packets on port 8125 like so:

```
tcpdump -i lo udp port 8125 -vv -X
```
To briefly explain the options we passed to it:

-i lo only captures packets on the local loopback i.e. packets sent to localhost
udp means that only UDP packets will be captured. Other types of packets we might capture could be tcp or icmp for example.
-vv just gives us more verbose output
-X prints out the data in the UDP packets in ASCII as well as hex. If we just wanted the latter we could use the -x option

### references
* [1] https://www.ibm.com/developerworks/cn/linux/l-async/
* [2] https://blog.csdn.net/shreck66/article/details/48765533
* [3] https://blog.csdn.net/daniel_ustc/article/details/8757570
* [4] https://blog.csdn.net/ezimu/article/details/54892088
* [5] https://blog.csdn.net/inject2006/article/details/2139149
* [6] http://www.aboutlinux.info/2006/11/ifconfig-dissected-and-demystified.html
* [7] https://www.binarytides.com/linux-commands-monitor-network/
* [8] https://markhneedham.com/blog/2012/07/15/tcpdump-learning-how-to-read-udp-packets/
