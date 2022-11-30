### kafka

Kafka除了具备消息队列MQ的特性和使用场景外，它还有一个重要用途，就是做存储层。

用kafka做存储层，为什么呢？一大堆可以做数据存储的 MySQL、MongoDB、HDFS……

因为kafka数据是持久化磁盘的，还速度快；还可靠、支持分布式……

#### Kafka快的原因

##### 零拷贝

零拷贝并不是不需要拷贝，而是减少不必要的拷贝次数。通常是说在IO读写过程中。
实际上，零拷贝是有广义和狭义之分，目前我们通常听到的零拷贝，包括上面这个定义减少不必要的拷贝次数都是广义上的零拷贝。

```
读取文件，再用socket发送出去
传统方式实现：
先读取、再发送，实际经过1~4四次copy。
buffer = File.read
Socket.send(buffer)
1、第一次：将磁盘文件，读取到操作系统内核缓冲区；
2、第二次：将内核缓冲区的数据，copy到application应用程序的buffer；
3、第三步：将application应用程序buffer中的数据，copy到socket网络发送缓冲区(属于操作系统内核的缓冲区)；
4、第四次：将socket buffer的数据，copy到网卡，由网卡进行网络传输。
传统方式，读取磁盘文件并进行网络发送，经过的四次数据copy是非常繁琐的。
实际IO读写，需要进行IO中断，需要CPU响应中断(带来上下文切换)，
尽管后来引入DMA来接管CPU的中断请求，但四次copy是存在“不必要的拷贝”的。

```



1、partition顺序读写，充分利用磁盘特性，这是基础；
2、Producer生产的数据持久化到broker，采用mmap文件映射，实现顺序的快速写入；
3、Customer从broker读取数据，采用sendfile，将磁盘文件读到OS内核缓冲区后，直接转到socket buffer进行网络发送。

#### mmap 和 sendfile总结
1、都是Linux内核提供、实现零拷贝的API；
2、sendfile 是将读到内核空间的数据，转到socket buffer，进行网络发送；
3、mmap将磁盘文件映射到内存，支持读和写，对内存的操作会反映在磁盘文件上。
RocketMQ 在消费消息时，使用了 mmap。kafka 使用了 sendFile。
