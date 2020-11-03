Lettuce 和 Jedis  区别



Jedis实现是直接连接Redis Server 在多线程是非线程安全

而且每次都去那Jedis实例，连接说多的话，连接成本较高



Lettuce是基于Netty.，Netty 是一个多线程、事件驱动的 I/O 框架。连接实例可以在多个线程间共享，当多线程使用同一连接实例时，是线程安全的。



一个多线程的应用可以使用同一个连接实例，而不用担心并发线程的数量。
当然这个也是可伸缩的设计，一个连接实例不够的情况也可以按需增加连接实例

[Jedis和Lettuce](https://blog.csdn.net/catoop/article/details/93756295)