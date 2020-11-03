#### Redis Save 与 BGSAVE 的区别

一，save保存数据到磁盘的方式：

Redis Save 命令执行一个同步保存操作，将当前 Redis 实例的所有数据快照(snapshot)以 RDB 文件的形式保存到硬盘。

语法
redis Save 命令基本语法如下：

redis 127.0.0.1:6379> SAVE

返回值

保存成功时返回 OK 。

 

二，BGSAVE保存数据到磁盘的方式：

BGSAVE 命令执行之后立即返回 OK ，然后 Redis fork 出一个新子进程，原来的 Redis 进程(父进程)继续处理客户端请求，而子进程则负责将数据保存到磁盘，然后退出。

客户端可以通过 LASTSAVE 命令查看相关信息，判断 BGSAVE 命令是否执行成功。

 

时间复杂度：

O(N)， N 为要保存到数据库中的 key 的数量。



案例：

redis> BGSAVE
Background saving started

 

 

三，结论

SAVE  保存是阻塞主进程，客户端无法连接redis，等SAVE完成后，主进程才开始工作，客户端可以连接

BGSAVE  是fork一个save的子进程，在执行save过程中，不影响主进程，客户端可以正常链接redis，等子进程fork执行save完成后，通知主进程，子进程关闭



#### Redis Bgrewriteaof 命令

 Redis Bgrewriteaof 命令用于异步执行一个 AOF（AppendOnly File） 文件重写操作。重写会创建一个当前 AOF 文件的体积优化版本。

即使 Bgrewriteaof 执行失败，也不会有任何数据丢失，因为旧的 AOF 文件在 Bgrewriteaof 成功之前不会被修改。

**注意：**从 Redis 2.4 开始， AOF 重写由 Redis 自行触发， BGREWRITEAOF 仅仅用于手动触发重写操作。

#### zset withscores

查询结果中的分数值也带上

#### type

查看值对象 类型

#### object encoding

查看编码和底层实现

![image-20200628224110855](..\images\image-20200628224110855.png)

#### object refcount

引用的次数  为0垃圾回收

#### object idletime

空转时间  当服务器内存超过maxmemory选项时，空间时间长的优先被服务器释放

#### ttl pttl

返回这个键的剩余生存时间

#### persist pexpireAt

persist是pexpireAt的反操作 解除过期时间



