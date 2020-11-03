> - == 和 equals() 的区别？
> - 为什么重写 equals() 就必须要重写 hashCode()？
> - Hashtable, HashSet 和 HashMap 的区别和联系
> - 处理 hash 冲突有哪些方法？Java 中用的哪一种？为什么？另一种方法你在工作中用过吗？在什么情况下用得多？
> - 徒手实现一个 HashMap 吧

## Set

Set是Java中的一个接口



## HashSet

底层是hashmap,他不保证set的迭代顺序，允许未null(重来除去重复元素)

![image-20200430134653191](..\images\image-20200430134653191.png)

在HashSet中，**元素都存到HashMap键值对的Key上面，而Value时有一个统一的值`private static final Object PRESENT = new Object();`**，(定义一个虚拟的Object对象作为HashMap的value，将此对象定义为static final。)

- 说白了，HashSet就是限制了功能的HashMap，所以了解HashMap的实现原理，这个HashSet自然就通
- 对于HashSet中保存的对象，主要要正确重写equals方法和hashCode方法，以保证放入Set对象的唯一性
- 虽说是Set是对于重复的元素不放入，**倒不如直接说是底层的Map直接把原值替代了**（这个Set的put方法的返回值真有意思）
- HashSet没有提供get()方法，愿意是同HashMap一样，Set内部是无序的，只能通过迭代的方式获

**LinkedHashSet**底层是LinkedHashMap **TreeSet**底层是TreeMap

## 关键字transient

**transient**关键字标记的成员变量不参与序列化过程。

## Map

**HashMap**: key不可重复，也是无序的

**LinkedHashMap**: 这是一个「HashMap + 双向链表」的结构，落脚点是 HashMap。所以既拥有 HashMap 的所有特性还能有顺序。

**TreeMap**: 是有序的，本质是用二叉搜索树来实现的。

## HashMap 实现原理

**HashMap的数据结构**：HashMap的数据结构为 **数组+(链表或红黑树)**

为什么采用这种结构来存储元素呢？

**数组的特点：查询效率高，插入，删除效率低**。

**链表的特点：查询效率低，插入删除效率高**。

在HashMap底层使用数组加（链表或红黑树）的结构完美的解决了数组和链表的问题，使得查询和插入，删除的效率都很高。



### equals相等 hashcode不一定相等

**HashMap中的两个重要的参数：**HashMap中有两个重要的参数：**初始容量大小和加载因子**，初始容量大小是创建时给数组分配的容量大小，默认值为16，**用数组容量大小乘以加载因子得到一个值，一旦数组中存储的元素个数超过该值就会调用rehash方法将数组容量增加到原来的两倍，专业术语叫做扩容**.

**为什么重写 equals() 方法，一定要重写 hashCode() 呢？**

不同的hashcode的话 回算出不同的index 就会存放到不同位置

#### 默认的equals方法  使用==   对于基本数据类型 比较值的大小  引用类型，比较是否是一个对象

基本数据类型： byte short int long float double char boolean

### 哈希冲突详解

解决哈希冲突

1.链地址法

2.开放寻址法   3再hash法

> 在 JDK1.6 和 1.7 中，是用**链表**存储的，这样如果碰撞很多的话，就变成了在链表上的查找，worst case 就是 O(n)；

> 在 JDK 1.8 进行了优化，当链表长度较大时（超过 8），会采用**红黑树**来存储，这样大大提高了查找效率。

开放寻址法 

这种方法是顺序查找，如果这个桶里已经被占了，那就按照“**某种方式**”继续找下一个没有被占的桶，直到找到第一个空的。

### 与 Hashtable 的区别

所以 HashMap, ArrayList, StringBuilder 不再考虑线程安全的问题，性能提升了很多，当然，线程安全问题也就转移给我们程序员了。

另外一个区别就是：HashMap 允许 key 中有 null 值，Hashtable 是不允许的。这样的好处就是可以给一个默认值。

## 应用

在某电商网站上，过去的一小时内卖出的最多的 k 种货物。

PriorityQueue使用跟普通队列一样，唯一区别是PriorityQueue会根据排序规则决定谁在队头，谁在队尾。

### TOP key

```
/**
 * @author wuhongyun
 * @date 2020/4/30 17:48
 */
class Solution {
    public static List<String> topKFrequent(String[] words, int k) {
        // Step 1
        Map<String, Integer> map = new HashMap<>();
        for (String word : words) {
            Integer count = map.getOrDefault(word, 0);
            count++;
            map.put(word, count);
        }

        // Step 2
        PriorityQueue<Map.Entry<String, Integer>> minHeap = new PriorityQueue<>(k+1, new Comparator<Map.Entry<String, Integer>>() {
            @Override
            public int compare(Map.Entry<String, Integer> e1, Map.Entry<String, Integer> e2) {
                if(e1.getValue() == e2.getValue()) {
                    return e2.getKey().compareTo(e1.getKey());
                }
                return e1.getValue().compareTo(e2.getValue());
            }
        });

        // Step 3
        List<String> res = new ArrayList<>();
        for(Map.Entry<String, Integer> entry : map.entrySet()) {
            minHeap.offer(entry);
            if(minHeap.size() > k) {
                minHeap.poll();
            }
        }
        while(!minHeap.isEmpty()) {
            res.add(minHeap.poll().getKey());
        }
        Collections.reverse(res);
        return res;
    }

    public static void main(String[] args) {
        String inputStr = "I love JAVA very much I JAVA is first";
        String[] s = inputStr.split(" ");
        List<String> strings = topKFrequent(s, 2);
        strings.forEach(a-> System.out.println(a));
    }
}
```



### LRU

least recent used 淘汰掉最不经常使用的

### hashmap多线程扩容存在的问题

  HashMap有两个参数影响其性能：初始容量和加载因子。默认初始容量是16，加载因子是0.75。

HashMap容量一定要为2的幂呢？

当容量一定是2^n时，h & (length - 1) == h % length

它俩是等价不等效的，位运算效率非常高



为什么多线程使用concurrenthashmap

主要因为其扩容机制

1.7 hashmap 会造成死循环

hashmap扩容的过程

1，去当前table的2倍作为新table的大小

2.根据算出的table new 出新Entry数组命名为newtable

3.轮询原table的每个位置，将每个位置上的链接Entry 算出新table的位置，并以链表形式连接





[hashmap扩容时死循环问题](https://blog.csdn.net/chenyiminnanjing/article/details/82706942)