### 阻塞非阻塞与同步异步的区别

> 阻塞和非阻塞，应该描述的是一种状态，同步与非同步描述的是行为方式

“阻塞”与"非阻塞"与"同步"与“异步"不能简单的从字面理解，提供一个从分布式系统角度的回答。
**1.同步与异步**
同步和异步关注的是**消息通信机制** (synchronous communication/ asynchronous communication)
所谓同步，就是在发出一个*调用*时，在没有得到结果之前，该*调用*就不返回。但是一旦调用返回，就得到返回值了。
换句话说，就是由*调用者*主动等待这个*调用*的结果。

而异步则是相反，***调用\*在发出之后，这个调用就直接返回了，所以没有返回结果**。换句话说，当一个异步过程调用发出后，调用者不会立刻得到结果。而是在*调用*发出后，*被调用者*通过状态、通知来通知调用者，或通过回调函数处理这个调用。

典型的异步编程模型比如Node.js

举个通俗的例子：
你打电话问书店老板有没有《分布式系统》这本书，如果是同步通信机制，书店老板会说，你稍等，”我查一下"，然后开始查啊查，等查好了（可能是5秒，也可能是一天）告诉你结果（返回结果）。
而异步通信机制，书店老板直接告诉你我查一下啊，查好了打电话给你，然后直接挂电话了（不返回结果）。然后查好了，他会主动打电话给你。在这里老板通过“回电”这种方式来回调。

**2. 阻塞与非阻塞**
阻塞和非阻塞关注的是**程序在等待调用结果（消息，返回值）时的状态.**

阻塞调用是指调用结果返回之前，当前线程会被挂起。调用线程只有在得到结果之后才会返回。
非阻塞调用指在不能立刻得到结果之前，该调用不会阻塞当前线程。

还是上面的例子，
你打电话问书店老板有没有《分布式系统》这本书，你如果是阻塞式调用，你会一直把自己“挂起”，直到得到这本书有没有的结果，如果是非阻塞式调用，你不管老板有没有告诉你，你自己先一边去玩了， 当然你也要偶尔过几分钟check一下老板有没有返回结果。
在这里阻塞与非阻塞与是否同步异步无关。跟老板通过什么方式回答你结果无关。

**参考**

[怎样理解阻塞非阻塞与同步异步的区别？](https://www.zhihu.com/question/19732473/answer/20851256)

### IO模型

- 阻塞IO
- 非阻塞IO
- 多路复用IO
- 信号驱动IO
- 异步IO

#### 1.阻塞IO

阻塞IO相信大家都最熟悉了，线程发起一个IO请求，直到有结果返回，否则则一直阻塞等待，比如我们平常常见的阻塞数据库操作，网络IO等。

#### 2.非阻塞IO

非阻塞IO和阻塞IO的最大区别就在于线程发起一个IO请求,不会一直堵塞直到有数据，而是不断的检查是否已有数据，若有数据则读取数据。

总的来说非阻塞IO的非阻塞主要体现在不需要一直等待到有数据，当然读数据那部分操作还是阻塞的，另外这种非阻塞模式需要用户线程自己不断询问检查，其实效率也不是太高，实际编程中运用的也不多。

#### 3.多路复用IO

为何叫多路复用，是因为它I/O多路复用可以同时监听多个fd，如此就减少了为每个需要监听的fd开启线程的开销。

既然上面我们说到非阻塞IO的缺点，那么有没有什么方式改进呢？答案是当然有，那就是多路复用IO，我理解的它的特点就是复用，首先它也是一种非阻塞IO的模型，只不过上面说到轮询的方式用了不同的方式处理了，当一个线程发起IO请求，系统会将它注册到一个单独管理IO请求的一个线程，之后该IO的相关操作的通知状态都有这个管理IO请求的线程处理，Java 1.4发布的NIO就是这种模式，我们可以大致来看一下它的流程：

```
// 打开服务器套接字通道
ServerSocketChannel ssc = ServerSocketChannel.open();
// 服务器配置为非阻塞
ssc.configureBlocking(false);
// 进行服务的绑定
ssc.bind(new InetSocketAddress("localhost", 8008));
// 这里的selector就相当于单独管理IO请求的线程
Selector selector = Selector.open();
// 注册到selector，等待连接
ssc.register(selector, SelectionKey.OP_ACCEPT);

while (true) {
    selector.select();  //为IO请求去轮询状态
    Set<SelectionKey> keys = selector.selectedKeys(); //多个IO请求的状态
    Iterator<SelectionKey> keyIterator = keys.iterator();
    while (keyIterator.hasNext()) { //依次处理IO请求
        SelectionKey key = keyIterator.next();
        doThing(key)
        ...
    }
}
```

可以看出Java NIO的模式就是多路复用IO模型的应用。

多路复用IO可以看成普通非阻塞IO的升级版，也是目前Java编程中用到比较多的IO模型，它的优势在于可以处理大量的IO请求，用一个线程管理所有的IO请求，无需像阻塞IO和非阻塞IO一样，每个IO需要一个线程处理，提升了系统的吞吐量。

#### 4.信号驱动IO

信号驱动IO相对于以上几种模型最大的特点就是它支持内核信号通知，线程在发起一个IO请求后，会注册一个信号函数，然后内核在确认数据可读了，便会给相应的线程发送通知，让其进行具体IO读写操作

就实际来说，信号驱动IO用的并不多，因为信号驱动IO底层是使用SIGIO信号，所以它主要使用在UDP协议上，因为UDP产生SIGIO信号的时候只有两种可能：

- 1.要么数据到达
- 2.发生错误

但相对TCP来说，产生SIGIO信号的地方太多了，比如请求连接，确认，断开，错误等等，所以我们很难根据SIGIO信号判断到底发生了什么。

#### 5.异步IO

以上四种IO其实都还是同步IO，因为它们在读写数据时都是阻塞的，异步IO相较于它们最大的特点是它读写数据的时候也是非阻塞的，用户线程在发起一个IO请求的时候，除了给内核线程传递具体的IO请求外，还会给其传递数据缓冲区，回调函数通知等内容，然后用户线程就继续执行，等到内核线程发起相应通知的时候，说明数据已经准备就绪，用户线程直接使用即可，无需再阻塞从内核拷贝数据到用户线程。

异步IO才是真正的异步，因为它连数据拷贝这个过程都是非阻塞的，用户线程根本不用关心数据的读写等操作，只需等待内核线程通知后，直接处理数据即可，当然异步IO需要系统内核支持，比如Linux中的AIO和Windows中的IOCP，但是也可以通过多线程跟阻塞I/O模拟异步IO，比如可以在多路复用IO模型上进行相应的改变，另外也有现有的实现，比如异步I/O的库：

最后用一张图总体概括一下Java IO（图片来自美团技术博客）：

Java IO概图：

![image-20200914215240147](C:\Users\pc-hone\images\image-20200914215240147.png)

### I/O多路复用select、poll、epoll

I/O 多路复用技术是为了解决进程或线程阻塞到某个 I/O 系统调用而出现的技术，使进程不阻塞于某个特定的 I/O 系统调用。

[Linux系统编程——I/O多路复用select、poll、epoll的区别使用](https://blog.csdn.net/tennysonsky/article/details/45745887)

与多线程和多进程相比，I/O 多路复用的最大优势是系统开销小，系统不需要建立新的进程或者线程，也不必维护这些线程和进程。

### NIO

传统的 IO 流还是有很多缺陷的，尤其它的阻塞性加上磁盘读写本来就慢，会导致 CPU 使用效率大大降低。

所以，jdk 1.4 发布了 NIO 包，NIO 的文件读写设计颠覆了传统 IO 的设计，采用『通道』+『缓存区』使得新式的 IO 操作直接面向缓存区，并且是非阻塞的，对于效率的提升真不是一点两点，我们一起来看看。

Java NIO应用非常广泛，尤其是在高并发领域，比如我们常见的Netty，Mina等框架，都是基于它实现的，相信大家都有所了解，下面让我们来看看Java NIO的具体架构。

#### Java NIO架构

其实Java NIO模型相对来说也还是比较简单的，它的核心主要有三个，分别是：Selector、Channel和Buffer,我们先来看看它们之间的关系

#### Selector

个人认为Selector是Java NIO的最大特点，之前我们说过，传统的Java IO在面对大量IO请求的时候有心无力，因为每个维护每一个IO请求都需要一个线程，这带来的问题就是，系统资源被极度消耗，吞吐量直线下降，引起系统相关问题，那么Java NIO是如何解决这个问题的呢？答案就是Selector，简单来说它对应着多路IO复用中的监管角色，它负责统一管理IO请求，监听相应的IO事件，并通知对应的线程进行处理，这种模式下就无需为每个IO请求单独分配一个线程，另外也减少线程大量阻塞，资源利用率下降的情况，所以说Selector是Java NIO的精髓，在Java中我们可以这么写：

```
// 打开服务器套接字通道
ServerSocketChannel ssc = ServerSocketChannel.open();
// 服务器配置为非阻塞
ssc.configureBlocking(false);
// 进行服务的绑定
ssc.bind(new InetSocketAddress("localhost", 8001));

// 通过open()方法找到Selector
Selector selector = Selector.open();
// 注册到selector，等待连接
ssc.register(selector, SelectionKey.OP_ACCEPT);
...
```

#### Channel

Channel本意是通道的意思，简单来说，它在Java NIO中表现的就是一个数据通道，但是这个通道有一个特点，那就是它是双向的，也就是说，我们可以从通道里接收数据，也可以向通道里写数据，不用像Java BIO那样，读数据和写数据需要不同的数据通道，比如最常见的Inputstream和Outputstream，但是它们都是单向的，Channel作为一种全新的设计，它帮助系统以相对小的代价来保持IO请求数据传输的处理，但是它并不真正存放数据，它总是结合着缓存区（Buffer）一起使用，另外Channel主要有以下四种：

- FileChannel：读写文件时使用的通道
- DatagramChannel：传输UDP连接数据时的通道,与Java IO中的DatagramSocket对应
- SocketChannel：传输TCP连接数据时的通道，与Java IO中的Socket对应
- ServerSocketChannel: 监听套接词连接时的通道，与Java IO中的ServerSocket对应

当然其中最重要以及最常用的就是SocketChannel和ServerSocketChannel，也是Java NIO的精髓，ServerSocketChannel可以设置成非阻塞模式，然后结合Selector就可以实现多路复用IO，使用一个线程管理多个Socket连接，具体使用可以参数上面的代码。

顾名思义，Buffer的含义是缓冲区，它在Java NIO中的主要作用就是作为数据的缓冲区域，Buffer对应着某一个Channel，从Channel中读取数据或者向Channel中写数据，Buffer与数组很类似，但是它提供了更多的特性，方便我们对Buffer中的数据进行操作

### NIO视频笔记

[这可能是B站最是深入底层的NIO网络编程教程了](https://www.bilibili.com/video/BV1Z4411C7iW?p=1)

![image-20200914235235614](C:\Users\pc-hone\images\image-20200914235235614.png)

![image-20200914235408322](C:\Users\pc-hone\images\image-20200914235408322.png)

#### BIO缺点

accept  read都会阻塞 ，一旦阻塞，其他线程无法执行。

如果需要并发的话只能每个连接开个线程，使用多线程

多线程缺点：服务器资源浪费，性能低下

redis --单线程--epoll

#### NIO设计思路

![image-20200915001841166](C:\Users\pc-hone\images\image-20200915001841166.png)

![image-20200915003550288](C:\Users\pc-hone\images\image-20200915003550288.png)

![image-20200915003713966](C:\Users\pc-hone\images\image-20200915003713966.png)

**软件：网络调试助手**

问题：for应该出现在内核    解决无意义的循环 selector epoll

#### 模拟单线程处理高并发应用

#### 模拟JVM socket对象源码

#### 模拟c语言实现NIO

**参考：**

[【NIO系列】——之IO模型](https://my.oschina.net/u/1859679/blog/1839169)

[【NIO系列】——之Netty](https://juejin.im/post/6844903648938426375)

### Reactor

Reactor 就是基于NIO中实现多路复用的一种模式

[[【NIO系列】——之Reactor模型](https://my.oschina.net/u/1859679/blog/1844109)](https://my.oschina.net/u/1859679/blog/1844109)

[漫谈Java IO之 NIO那些事儿](https://www.cnblogs.com/xing901022/p/8672418.html)