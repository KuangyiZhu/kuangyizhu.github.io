### select
up to the linux kernel to wake up the relevent thread waiting on a file discriptor

### zero copy
file device driver upon a block device
mmap map the dma data to the kernel memory to user space address

read.. copy to the userspace

Lets look at what used to be involved in sending a message out over the network:

* user assembles pieces of the message into one big buffer in memory (1st copy)
* user calls network function
* transition to kernel code
* kernel copies user data to kernel memory (second copy)
* kernel notifies device driver
* driver copies data to device (third copy)
* transition back to user space
Each one of these copies (and transitions) has a very noticeable performance cost. The linux kernel team spent a good amount of time and effort reducing this to:

* user gives list of pieces to kernel (no copy)
* transition to kernel code
* kernel sets up DMA (direct-memory-access) for network card to read and send pieces
* transition back to user space
### reference
* [1] https://blog.csdn.net/u010481276/article/details/51093800
* [2] https://blog.csdn.net/YACHL882828/article/details/53819708
* [3] https://yahooeng.tumblr.com/post/104861108931/mdbm-high-speed-database
* [4] https://www.zhihu.com/question/24322387 netty
* [5] https://www.nada.kth.se/kurser/kth/2D1392/05/lectures/lecture_2.pdf IP ARP
* [6] https://www.slideshare.net/DherytaJaisinghani/tutorial-wifi-driver-code-opening-nuts-and-bolts-of-linux-wifi-subsystem wireless kernel
* [7] http://www.haifux.org/lectures/217/netLec5.pdf linux kernel socket subsystem
