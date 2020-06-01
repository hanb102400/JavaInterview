

## 排序



### 常见的集合有哪些？

底层接口：Collection 接口 和 Map  接口。

**1. Collection 接口：**

Set 接口和List接口继承Collection 接口 ：

  - Set 接口：

    > 实现类有 HashSet、TreeSet、LinkedHashSet 等；

  - List 接口：

    > 实现类有ArrayList、LinkedList、Stack 以及 Vector 等


**2. Map 接口的实现类主要有：**

- Map 接口实现：

  > 实现类：HashMap、TreeMap、Hashtable、ConcurrentHashMap 以及 Properties 等



### Enumeration接口和Iterator接口的区别



### List 和 Set 的区别是啥？

答：List 元素是有序可重复；Set 元素是无不可重复的。



### Vector，ArrayList，LinkedList区别？

Vector：

- 线程安全，效率低
- Vector空间不够时，默认扩展1倍。

ArrayList

- 线程不安全
- 基于数组实现，访问效率高，插入效率低
- 基于数组实现，支持随机访问

LinkedList

- 线程不安全
- 基于双向链表实现，访问效率低，插入效率高。
- 基于双向链表实现，不支持随机访问



### HashSet 和 HashMap和的区别

HashSet 

- HashSet仅仅存储对象 ，不允许集合中有重复的值 
- 使用add(Object o)方法用来在Set中添加元素 

HashMap

- HashSet存储key，value键值队，不允许有重复的key
- 允许空键值对数据，最多只允许一条记录的键为Null;
- 使用put方法添加元素





### HashMap，LinkedHashMap，TreeMap的区别？

HashMap

- 继承自AbstractMap类，实现Map接口
- 数据结构：数组+链表+ 红黑树
- 允许空的键值对,最多只允许一条记录的键为null
- 非线程安全

LinkedHashMap

- 继承HashMap,增加key排序功能
- 数据结构基于：数组 +链表＋红黑树 + 双链表 
- 使用双链表记录key的顺序

TreeMap

- 实现SortMap接口
- 数据结构：基于红黑树
- 元素有序



### Hashtable， HashMap，和 ConncurrentHashMap的区别

Hashtable ：

- 继承自Dictonary类
- 线程安全，基于synchronized 方法
- 数据结构：数组+链表
- 不允许空的键值对

HashMap

- 继承自AbstractMap类，实现Map接口
- 非线程安全
- 数据结构：数组+链表+ 红黑树
- 允许空键值对数据，最多只允许一条记录的键为Null;

ConncurrentHashMap

- 线程安全，基于分段锁，CAS锁方式



### Hashtable 和 ConcurrentHashMap 和 的区别？

答：

1）Hashtable 考虑了同步的问题。但是 Hashtable 使用synchronized 方法，在每次同步执行时都要锁住整个结构,性能差。

2）ConcurrentHashMap 使用分段锁，CAS锁的方式实现同步，属于细粒度的锁，不会阻塞线程，效率更高。







### Comparator接口 与Comparable接口 有什么不同？

1）Comparator 接口：用于集合排序，需要实现compare方法。

当使用Collections.sort排序时候，根据Comparator 接口的compare 方法排序。

```java
 Collections.sort(list2,new Comparator<Person2>(){

     @Override
     public int compare(Person2 o1, Person2 o2)
     {
         if(o1 == null || o2 == null)
             return 0;
         return o1.getAge()-o2.getAge();
     }

 });
```

2）Comparable接口：用于两个对象单独比较，需要实现compareTo 方法。

```java
Obj1.compareTo(Obj1);
```





### Collections.sort排序内部原理

1）元素需要实现Comparator 接口

2）Collections.sort，根据Comparator 接口的compare接口排序

3）算法，Java 6使用MergeSort，jdk7使用TimSort











## HashMap原理

### HashMap中计算hash值是如何实现的？

1. 计算hashcode

```java
public int hashCode() {  
    int h = hash;  
    if (h == 0 && value.length > 0) {  
    	char val[] = value;  
        for (int i = 0; i < value.length; i++) {  
            h = 31 * h + val[i];  
        }  
        hash = h;  
    }  
    return h;  
}
```

1）hash值优化：hashcode ^ (hashcode >>> 16) 



### HashMap原理

1）数据结构

JDK1.7使用：数组+链表 方式实现

JDK8使用：数组+链表+红黑树 方式实现 . ( 冲突的元素小于8使用链表, 大于8使用红黑树替代)

红黑树是一种特别的二叉查找树，特性为：

- 节点为红或者黑 
- 根节点为黑色
- 所有叶子节点都为黑色
- 一个节点为红色，则两个叶节点都为黑色
- 一个节点到其子孙节点所有路径上的黑节点数目相同。




2）Hash冲突，解决碰撞：

运用拉链法：

- 当出现冲突时，将关键词为同义词的结点链接在一个单链表中，散列表长m，则定义一个由m个头指针组成的指针数组T，地址为i的结点插入以T(i)为头指针的单链表中。
- Jdk8中，冲突的元素小于限制（8) 使用链表,  冲突的元素超过限制（8），用红黑树替换链表。



3）扩容

默认的HashMap初始容量 为8，扩容的加载因子为0.75.



## ConcurrentHashMap 原理

### ConcurrentHashMap 的具体实现知道吗？

答：

采用volatile HashEntry保存数据，table元素作为锁；

1）分段锁：

 ConcurrentHashMap 锁的方式是稍微细粒度的，ConcurrentHashMap 将 hash 表分成16个段（也就有了16把锁），每个段是一个单独的hashmap，这样不同的段之间就不需要加锁了。诸如 get,put,remove 等常用操作只锁当前需要用到的桶。

2）段内使用CAS锁。还可以进一步提高性能。这样就通过volatile写happens before与volatile读做到了可见性。

读读不加锁，写写要互斥，读写也不加锁，那自然要想办法保证原子性跟可见性了。首先，在每个段里都有一个volatile的count变量，put都会在最后写count变量，get一开始会去读取count值，同时HashEntry的value也是volatile.

### Happens-before 关系

happens-before 关系保证：如果线程 A 与线程 B 满足 happens-before 关系，则线程 A 执行动作的结果对于线程 B 是可见的。如果两个操作未按 happens-before 排序，JVM 将可以对他们任意重排序。



1. 程序次序法则：如果在程序中，所有动作 A 出现在动作 B 之前，则线程中的每动作 A 都 happens-before 于该线程中的每一个动作 B。
2. 监视器锁法则：对一个监视器的解锁 happens-before 于每个后续对同一监视器的加锁。
3. Volatile 变量法则：对 Volatile 域的写入操作 happens-before 于每个后续对同一 Volatile 的读操作。
4. 传递性：如果 A happens-before 于 B，且 B happens-before C，则 A happens-before C。









### 常用的hash算法有哪些？

1.加法hash：所谓的加法Hash就是把输入元素一个一个的加起来构成最后的结果。

2.位运算hash：这类型Hash函数通过利用各种位运算（常见的是移位和异或）来充分的混合输入元素

3.乘法hash：33*hash + key.charAt(i)



### HashMap1.7和1.8的区别

- JDK1.7版本基于`数组 + 链表` 实现 。
- JDK8基于`数组 + 链表 + 红黑树` 方式实现。（节点上元素少于8个使用链表，元素超过8个使用红黑树）

###  ConcurrentHashMap1.7和1.8的区别

- JDK1.7中基于`HashEntry`数组(单向链表 + 数组), 使用 `Segment` + `ReentrantLock`分段锁方式.

- JDK8中基于`Node`数组(单向链表 + 红黑树 + 数组), 使用`CAS` + `Synchronized`锁实现



### concurrenthashmap在读写并发的时候，什么时候可见，什么时候不可见.



4. 



9.怎么实现一个简单lrumap 



## 问题:

**1. 为什么要扩容？ 扩容的负载因子为什么是0.75？ 为什么HashMap扩容是2的幂次方？**

答：

1）hashmap进行扩容主要为了减少冲突率，提高查询的性能。

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
     if ((size >= threshold) && (null != table[bucketIndex])) {
         resize(2 * table.length);
         hash = (null != key) ? hash(key) : 0;
         bucketIndex = indexFor(hash, table.length);
     }
     createEntry(hash, key, value, bucketIndex);
}

void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }

    void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }
```



2）扩容时，加载因子越大，冲突率越高，查询性能也就越差，默认当这个值等于0.75时最适合。

3）扩容使用2的幂次：方方便二进制与（&）操作，减少碰撞的次数。

通过将 Key 的 hash 值与 length-1 进行 & 运算，实现了当前 Key 的定位，2 的幂次方可以减少冲突（碰撞）的次数，提高 HashMap 查询效率；如果 length 为 2 的次幂 则 length-1 转化为二进制必定是 11111……的形式，在于 h 的二进制与操作效率会非常的快，而且空间不浪费；如果 length 不是 2 的次幂，比如 length 为 15，则 length-1 为 14，对应的二进制为 1110，在于 h 与操作，最后一位都为 0，而 0001，0011，0101，1001，1011，0111，1101 这几个位置永远都不能存放元素了，空间浪费相当大。更糟的是这种情况中，数组可以使用的位置比数组长度小了很多，这意味着进一步增加了碰撞的几率，减慢了查询的效率！这样就会造成空间的浪费。





**6.ConncurrentHashMap和hashtable比较**，**两个线程并发访问map中同一条链，一个线程在尾部删除，一个线程在前面遍历查找，问为什么前面的线程还能正确的查找到后面被另一个线程删除的节点**）

ConcurrentHashMap融合了hashtable和hashmap二者的优势。hashtable是做了同步的，即线程安全，hashmap未考虑同步。所以hashmap在单线程情况下效率较高。hashtable在的多线程情况下，同步操作能保证程序执行的正确性。但是hashtable是阻塞的，每次同步执行的时候都要锁住整个结构，ConcurrentHashMap正是为了解决这个问题而诞生的， 

ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术（一个Array保存多个Object，使用这些对象的锁作为分离锁，get/put时随机使用任意一个）。它使用了多个锁来控制对hash表的不同部分进行的修改。在JDK 1.6中，有HashEntry结构存在，每次插入将新添加节点作为链的头节点（同HashMap实现），而且每次删除一个节点时，会将删除节点之前的所有节点拷贝一份组成一个新的链，而将当前节点的上一个节点的next指向当前节点的下一个节点，从而在删除以后有两条链存 在，因而可以保证即使在同一条链中，有一个线程在删除，而另一个线程在遍历，它们都能工作良好，因为遍历的线程能继续使用原有的链。

Java8中，采用volatile HashEntry保存数据，table元素作为锁；

从table数组+单向链表加上了红黑树。





**6.lrumap** 

lru(最近最少使用)，首先这个map会有容量限制，当容量不够时将最近最少使用的元素剔除掉。我们很容易将这个问题复杂化，比如说给每个元素设置一个age变量，然后用某种数据结构（比如小顶堆）维护，在容量不够时选择堆顶元素剔除掉。复杂度为O(lgn)

其实没有这么复杂。只需要额外维护一个链表即可（类似于linkedhashmap）。每次修改都将该元素调整到链表的头部，这样链表的尾部就是需要被剔除的元素。复杂度O(1)



3. JDK8中  ConcurrentHashMap为什么不再使用分段锁,为什么不用ReentrantLock而用synchronized ?
- 分段锁的缺点在于分成很多段时会比较浪费内存空间(不连续，碎片化); 操作map时竞争同一个分段锁的概率非常小时，分段锁反而会造成更新等操作的长时间等待; 当某个段很大时，分段锁的性能会下降。
- 减少内存开销:如果使用ReentrantLock则需要节点继承AQS来获得同步支持，增加内存开销，而1.8中只有头节点需要进行同步。

- 内部优化:synchronized则是JVM直接支持的，JVM能够在运行时作出相应的优化措施：锁粗化、锁消除、锁自旋等等。

  

**7.高并发场景下面hashmap触发一次扩容导致rt爆长，请问有什么好的解决方案**

这应该属于开放型问题，估计不会有标准答案，最重要的能自圆其说。

1. concurrenthashmap提供了一种方式，首先是分段，这让扩容的容量小了很多。其次它在扩容时还采用了写时复制的方法，也就是在扩容的时候不影响读。它的问题是，在扩容的时候会持有锁（put方法持有），而扩容又是个耗时的操作，所以这种方法在扩容的时候，保证了读的rt，却无法保证写的rt
2. redis/memcached采用的方法：这两者实质上都是渐进式的rehash，只是redis是单线程不需要锁而已。以memcached为列，有一个后台线程执行rehash的过程，首先获取锁，然后迁移一部分数据，再释放锁。过会在循环迁移，直到迁移完成。



数据，但是没有对size++，线程B就已经读取size了，那么对于线程B来说读取到的size一定是不准确的。 





