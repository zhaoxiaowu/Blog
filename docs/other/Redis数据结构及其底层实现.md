# 一、字符串

Redis 的字符串是动态字符串，是可以修改的字符串，内部结构实现上类似于 Java 的

ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配。



内存中它是以字节数组的形式存在的。我们知道 C 语言里面的字符串标准形式是以 NULL 作为结束符，但是在 Redis 里面字符串不是这么表示的。因为要获取 NULL 结尾的字符串的长度使用的是 strlen 标准库函数，这个函数的算法复杂度是 O(n)，它需要对字节数组进行遍历扫描，作为单线程的 Redis 表示承

受不起

Redis 的字符串叫着「SDS」，也就是 Simple Dynamic String。它的结构是一个带长度信

息的字节数组。

```
struct SDS<T> {

 T capacity; // 数组容量  所分配数组的长度

 T len; // 数组长度  字符串的实际长度

 byte flags; // 特殊标识位，不理睬它

 byte[] content; // 数组内容

}
```

字符串是可以修改的字符串，它要支持 append 操作。如果数组没有冗余空间，那么追加操作必然涉及

到分配新数组，然后将旧内容复制过来，再 append 新内容。如果字符串的长度非常长，这

样的内存分配和复制开销就会非常大。

```
/* Append the specified binary-safe string pointed by 't' of 'len' bytes to the
* end of the specified sds string 's'.
*
* After the call, the passed sds string is no longer valid and all the
* references must be substituted with the new pointer returned by the call. */
sds sdscatlen(sds s, const void *t, size_t len) {
 size_t curlen = sdslen(s); // 原字符串长度
 // 按需调整空间，如果 capacity 不够容纳追加的内容，就会重新分配字节数组并复制原字
符串的内容到新数组中
 s = sdsMakeRoomFor(s,len);
 if (s == NULL) return NULL; // 内存不足
 memcpy(s+curlen, t, len); // 追加目标字符串的内容到字节数组中
 sdssetlen(s, curlen+len); // 设置追加后的长度值
 s[curlen+len] = '\0'; // 让字符串以\0 结尾，便于调试打印，还可以直接使用 glibc 的字符串
函数进行操作
 return s;
}
```

上面的 SDS 结构使用了范型 T，为什么不直接用 int 呢，这是因为当字符串比较短

时，len 和 capacity 可以使用 byte 和 short 来表示，Redis 为了对内存做极致的优化，不同

长度的字符串使用不同的结构体来表示

Redis 规定字符串的长度不得超过 512M 字节。创建字符串时 len 和 capacity 一样

长，不会多分配冗余空间，这是因为绝大多数场景下我们不会使用 append 操作来修改字符

串。

### **embstr vs raw**

Redis 的字符串有两种存储方式，在长度特别短时，使用 emb 形式存储 (embeded)，当

长度超过 44 时，使用 raw 形式存储

这两种类型有什么区别呢？为什么分界线是 44 呢？

为了解释这种现象，我们首先来了解一下 Redis 对象头结构体，所有的 Redis 对象都有

下面的这个结构头:

```
struct RedisObject {

 int4 type; // 4bits    

 int4 encoding; // 4bits

 int24 lru; // 24bits   LRU 信息

 int32 refcount; // 4bytes   每个对象都有个引用计数，当引用计数为零时，对象就会被销毁，内存被回收

 void *ptr; // 8bytes，64-bit system   ptr 指针将指向对象内容 (body) 的具体存储位置

} robj;
```

一个 RedisObject 对象头需要占据 16 字节的存储空间

接着我们再看 SDS 结构体的大小，在字符串比较小时，SDS 对象头的大小是

capacity+3，至少是 3。意味着分配一个字符串的最小空间占用为 19 字节 (16+3)。

```
struct SDS {

 int8 capacity; // 1byte

 int8 len; // 1byte

 int8 flags; 

// 1byte

 byte[] content; // 内联数组，长度为 capacity

}
```

![image-20200914000035947](C:\Users\pc-hone\images\image-20200914000035947.png)

embstr 存储形式是这样一种存储形式，它将 RedisObject 对象头和 SDS 对象连续存在一起，使用 malloc 方法一次分配。而 raw 存储形式不一样，它需要两次malloc，两个对象头在内存地址上一般是不连续的。

如果总体超出了 64 字节，Redis 认为它是一个大字符串，不再使用 emdstr 形式存储，而该用 raw 形式。

前面我们提到 SDS 结构体中的 content 中的字符串是以字节\0 结尾的字符串，之所以多出这样一个字节，是为了便于直接使用 glibc 的字符串处理函数，以及为了便于字符串的调试打印输出

看上面这张图可以算出，留给 content 的长度最多只有 45(64-19) 字节了。字符串又是以\0 结尾，所以 embstr 最大能容纳的字符串长度就是 44。

### 扩容策略

字符串在长度小于 1M 之前，扩容空间采用加倍策略，也就是保留 100% 的冗余空

间。当长度超过 1M 之后，为了避免加倍后的冗余空间过大而导致浪费，每次扩容只会多分

配 1M 大小的冗余空间。

# 二.字典

dict 是 Redis 服务器中出现最为频繁的复合型数据结构，除了 hash 结构的数据会用到

字典外，整个 Redis 数据库的所有 key 和 value 也组成了一个全局字典，还有带过期时间

的 key 集合也是一个字典。zset 集合中存储 value 和 score 值的映射关系也是通过 dict 结

构实现的。

```
struct RedisDb {
 dict* dict; // all keys key=>value
 dict* expires; // all expired keys key=>long(timestamp)
 }
struct zset {
 dict *dict; // all values value=>score
 zskiplist *zsl;
}
```

![image-20200914001009440](C:\Users\pc-hone\images\image-20200914001009440.png)

dict 结构内部包含两个 hashtable，通常情况下只有一个 hashtable 是有值的。但是在

dict 扩容缩容时，需要分配新的 hashtable，然后进行渐进式搬迁，这时候两个 hashtable 存

储的分别是旧的 hashtable 和新的 hashtable。待搬迁结束后，旧的 hashtable 被删除，新的

hashtable 取而代之。



所以，字典数据结构的精华就落在了 hashtable 结构上了。hashtable 的结构和 Java 的

HashMap 几乎是一样的，都是通过分桶的方式解决 hash 冲突。第一维是数组，第二维是链

表。数组中存储的是第二维链表的第一个元素的指针。

```
struct dictEntry {

 void* key;

 void* val;

 dictEntry* next; // 链接下一个 entry

}

struct dictht {

 dictEntry** table; // 二维

 long size; // 第一维数组的长度

 long used; // hash 表中的元素个数

 ...

}
```

###  渐进式refesh

大字典的扩容是比较耗时间的，需要重新申请新的数组，然后将旧字典所有链表中的元

素重新挂接到新的数组下面，这是一个 O(n)级别的操作，作为单线程的 Redis 表示很难承受

这样耗时的过程。步子迈大了会扯着蛋，所以 Redis 使用渐进式 rehash 小步搬迁。虽然慢一

点，但是肯定可以搬完。

**如果字典处于搬迁过程中，要将新的元素挂接到新的数组下面**



搬迁操作埋伏在当前字典的后续指令中(来自客户端的 hset/hdel 指令等)，但是有可能客

户端闲下来了，没有了后续指令来触发这个搬迁，那么 Redis 就置之不理了么？当然不会，

优雅的 Redis 怎么可能设计的这样潦草。Redis 还会在定时任务中对字典进行主动搬迁。

### **hash** **攻**击

如果 hash 函数存在偏向性，黑客就可能利用这种偏向性对服务器进行攻击。存在偏向

性的 hash 函数在特定模式下的输入会导致 hash 第二维链表长度极为不均匀，甚至所有的

元素都集中到个别链表中，直接导致查找效率急剧下降，从 O(1)退化到 O(n)。有限的服务器

计算能力将会被 hashtable 的查找效率彻底拖垮。这就是所谓 hash 攻击

### **扩容条件**

正常情况下，当 hash 表中元素的个数等于第一维数组的长度时，就会开始扩容，扩容

的新数组是原数组大小的 2 倍。不过如果 Redis 正在做 bgsave，为了减少内存页的过多分

离 (Copy On Write)，Redis 尽量不去扩容 (dict_can_resize)，但是如果 hash 表已经非常满

了，元素的个数已经达到了第一维数组长度的 5 倍 (dict_force_resize_ratio)，说明 hash 表

已经过于拥挤了，这个时候就会强制扩容。

### **缩容条件**

当 hash 表因为元素的逐渐删除变得越来越稀疏时，Redis 会对 hash 表进行缩容来减少

hash 表的第一维数组空间占用。缩容的条件是元素个数低于数组长度的 10%。缩容不会考

虑 Redis 是否正在做 bgsave。

### **set** **的结构**

Redis 里面 set 的结构底层实现也是字典，只不过所有的 value 都是 NULL，其它的特

性和字典一模一样。

[Redis](https://zhuanlan.zhihu.com/p/103367975)

[Redis深度历险：核心原理和应用实践](https://blog.csdn.net/wojiao228925661/article/details/103023169/)