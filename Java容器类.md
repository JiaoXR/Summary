# Java 容器类
###  基本数据结构

- 数组
  - 内存空间连续；
  - 查找快（O(1)），增删慢（O(n)）；
- 链表
  - 内存空间不连续；
  - 增删快（O(1)），查找慢（O(n)）；

###  List 接口常用实现类

- ArrayList
  - 结构：有序可变数组，默认容量为 10，线程不安全；
  - 扩容：到达容量时，扩容为原容量的 1.5 倍，把原数组元素复制到新数组；
  - 结构修改：指的是增删元素，或者扩容等操作，只更新数据不是结构修改；
  - 注意点：
    - sublist 方法（返回 SubList 类，非 ArrayList 类）的修改会对原数组产生影响；
    - iterator 增删会抛出 ConcurrentModificationException；
- LinkedList
  - 结构：双向链表，线程不安全；
- Vector
  - 使用 synchronized 保证线程安全；
  - 并发时效率低，已不推荐使用
    - 若无并发，推荐使用 ArrayList；
    - 读多写少时推荐使用 CopyOnWriteArrayList；
- Stack
  - 栈实现，继承自 Vector，线程安全；

###  Map 接口常用实现类

- HashMap
  - 概述：哈希表实现，无序，线程不安全【键值都可为空】；
  - 结构实现：数组 + 链表 + 红黑树（JDK 1.8）；
  - 容量为 2 的次幂（默认 16），默认负载因子 0.75，链表转红黑树阈值 8，红黑树转链表阈值 6，树化最小容量 64；
  - 寻址方式：① h = key.hashCode()) ^ (h >>> 16)，② tab[(n - 1) & hash]；
  - 扩容：容量大于 “容量*负载因子” 时，扩容为原容量的 2 倍，原先位置的元素不变或者变为 “原位置+n”；
  - 哈希碰撞/冲突：使用链表和红黑树（JDK 1.8）；
  - 并发问题（多线程扩容时）：
    - JDK 1.7：链表采用头插法，多线程情况下扩容时链表可能循环引用，导致下次查找时死循环；
    - JDK 1.8：多线程情况下扩容时，无死循环问题，但可能丢数据；
- LinkedHashMap
  - 继承自 HashMap，可以认为是 HashMap + LinkedList；
  - 有序的 HashMap，可以实现插入顺序（insertionOrder，默认）和访问顺序(accessOrder)；
  - LruCache 基于 LinkedHashMap 实现；
- Hashtable
  - 哈希表实现，使用 synchronized 保证线程安全；
  - 高并发时效率低，JDK 推荐使用 ConcurrentHashMap；
- TreeMap
  - 基于红黑树实现；
  - 根据键的大小排序，键不能为空；

###  Set 接口常用实现类

- HashSet
  - 基于 HashMap 实现
- TreeSet
  - 基于 TreeMap 实现

###  并发容器类

####  ConcurrentHashMap

- 特点
  - 哈希表实现，线程安全；
  - 键值都不能为空；
- 结构
  - JDK 1.7：分段锁，两次寻址：① 查找在哪个段，② 查找在哪个桶；
  - JDK 1.8：数组 + 链表 + 红黑树（与 HashMap 类似）；
- 如何实现线程安全
  - 数组中：Unsafe 类的 CAS 操作；
  - 链表&红黑树：synchronized 加锁（JDK1.8）；

ConcurrentHashMap 1.7 结构图：

![](https://github.com/JiaoXR/Summary/blob/master/pics/ConcurrentHashMap_1.7.png)

ConcurrentHashMap 1.8 结构图：

![](https://github.com/JiaoXR/Summary/blob/master/pics/ConcurrentHashMap_1.8.png)

> 参考链接：http://www.jasongj.com/java/concurrenthashmap/

####  CopyOnWriteArrayList

- 特点
  - 线程安全；
  - 写入时复制，先复制当前容器，写入后再复制回来【使用 ReentrantLock】；
  - 并发读，无需锁。读写分离思想；
- 缺点
  - 内存占用（写入时复制，Young GC 和 Full GC）；
  - 数据一致性（保证数据最终一致性，不保证实时一致性）；

> 参考链接：https://www.cnblogs.com/dolphin0520/p/3938914.html

####  CopyOnWriteArraySet

- 基于 CopyOnWriteArrayList 实现。

