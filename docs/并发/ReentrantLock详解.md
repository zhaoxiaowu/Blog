锁：多线程同步执行，保证共享资源访问，保证线程安全

wait/notify sychronized  ReentrantLock



有sychronized为什么用ReentrantLock？

sychronized  重量级锁    CPU内核    调用操作系统函数



ReentrantLock   1.6  --  JVM 内核



1.7   1.8优化    sychronized已经性能很好



1.7 ReentrantLock  

1.8  sychronized



wait--阻塞--线程通信       配合sychronized使用



yield +  自旋

yield   





自旋锁





unsafe  直接操作一块内存区域



公平锁  和  非公平锁



交替执行



lock  aqs

自旋

park - unpark

cas



[](https://zhuanlan.zhihu.com/p/192460029)



AQS  单个线程·--交替执行与其它队列有关--jdk级别解决同同步问题



拿锁 lock方法正常执行



aqs 仅仅是reetrantlock  state 值做cas操作



AQS 队列里面的队头  Node里的Thread 永远为空





多自旋一次   尽量不park





















[ReentrantLock源码解析](https://juejin.im/post/6844903792777904136)