#  JVM 小结

###  概述

- 我们所说的 Java 平台无关性是指 class 文件的平台无关性，JVM 是和平台相关的，不同操作系统对应不同的 JVM。

###  运行时数据区（JVM 内存结构划分）

示意图：

![](https://github.com/JiaoXR/Summary/blob/master/pics/jvm/mem_stucture.png)

- 程序计数器（Program Counter Register）

  - 一块较小的内存，可以看作是当前线程所执行的字节码的行号指示器；
    - 若非 native 方法，则保存的是当前需要执行的指令的地址；
    - 若为 native 方法，则为空；
  - 线程私有；

- 虚拟机栈（VM Stack）

  - 方法执行的内存模型
    - 每个方法在执行时会创建一个栈帧，用于存储局部变量表、操作数栈、动态链接、方法出口等信息；
    - 每个方法从调用到执行完成，对应着一个栈帧在 VM 中的入栈到出栈的过程；
  - 每个线程都有一个自己的 Java 栈，互不干扰；
  - 示意图：

  ![](https://github.com/JiaoXR/Summary/blob/master/pics/jvm/vm_stack.jpg)

- 本地方法栈（Native Method Stack）

  - 与 Java 栈原理类似，只不过该栈是为本地方法服务的；

- 堆（Heap）

  - 存储对象及数组，垃圾收集器管理的主要区域；
  - 所有线程共享，JVM 中只有一个；

- 方法区（Method Area）

  - 线程共享区域

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3613043.html

###  Java 内存模型（JMM）

- Java 内存模型 JMM（Java Memory Model）主要目标是定义程序中各个变量（非线程私有）的访问规则，即在虚拟机中将变量存储到内存和从内存取出变量这样的底层细节。Java 中每个线程都有自己私有的工作内存，工作内存保存了被该线程使用的变量的主内存副本拷贝，线程对变量的读写操作都必须在工作内存进行，无法直接读写主内存中的变量。两个线程无法直接访问对方的工作内存。

- JMM 是围绕着在并发过程中如何处理原子性、可见性和有序性这三个特征来建立的。

- 示意图：

  ![](https://github.com/JiaoXR/Summary/blob/master/pics/jvm/jmm.png)

- 注意点：

  - happens-before 仅仅要求前一个操作（执行的结果）对后一个操作可见，且前一个操作按顺序排在第二个操作之前（可能会发生指令重排）；
  - happens - before 原则与时间先后顺序之间没有必然联系；

> 参考链接：
>
> 1. http://www.cnblogs.com/leesf456/p/5291484.html
> 2. https://mp.weixin.qq.com/s?__biz=MzIwMzY1OTU1NQ==&mid=2247484020&idx=1&sn=a6ba310fdafd6daa5a9b131eba697696&chksm=96cd4238a1bacb2e36c68c3490cb11ad1d61135cbc179118d1083a42d3c6e73aeebd86835940&mpshare=1&scene=1&srcid=0522ujuWgspWwa3iUsc6HQeC%23rd

###  典型的垃圾回收算法

- Mark-Sweep（标记-清除）算法

  - 分为标记和清除两个阶段：
    - 标记：标记出所有需要被回收的对象；
    - 清除：回收被标记的对象占有的空间；
  - 优缺点：
    - 优点：实现简单；
    - 缺点：容易产生内存碎片（碎片太多可能导致后续过程中需要为大对象分配内存空间时无法找到足够的空间而提前触发新的一次垃圾收集动作）；
  - 示意图：

  ![](https://github.com/JiaoXR/Summary/blob/master/pics/jvm/algorithm-mark-sweep.jpg)

- Copying（复制）算法

  - 为解决 MS 算法的缺陷而产生；
  - 实现方法：将可用内存按容量分为大小相等的两块，每次只用一块。当这一块内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用的内存空间一次清理掉，这样就不容易出现内存碎片问题；
  - 优缺点：
    - 优点：实现简单，运行高效且不易产生内存碎片；
    - 缺点：能够使用的内存缩减到原来的一半；
  - 示意图：

  ![](https://github.com/JiaoXR/Summary/blob/master/pics/jvm/algorithm-copying.jpg)

- Mark-Compact（标记-整理）算法

  - 为了解决 Copying 算法的缺陷，充分利用内存空间；
  - 与 MS 算法一样，但在完成标记后，不直接清理可回收对象，而是将存活对象都向一端移动；
  - 示意图：

  ![](https://github.com/JiaoXR/Summary/blob/master/pics/jvm/algorithm-mark-compact.jpg)

- Generational Collection（分代收集）算法

  - 目前大部分 JVM 的垃圾收集器采用的算法；
  - 核心思想：根据对象存活的生命周期将内存划分为若干个不同的区域，一般将堆区分为老年代（Tenured Generation）和新生代（Young Generation），可以根据不同代的特点采取最合适的收集算法；
  - 老年代：
    - 每次垃圾回收时只有少量对象需要被回收；
    - 一般使用的是 Mark-Compact 算法；
  - 新生代：
    - 每次垃圾回收时都有大量的对象需要被回收；
    - 大部分垃圾收集器对于新生代都采取 Copying 算法，但并未按照 1:1 划分空间；
    - 一般将新生代划分为一块较大的 Eden 空间和两块较小的 Survivor 空间，每次使用 Eden 空间和其中的一块 Survivor 空间，当进行回收时，将 Eden 和 Survivor 中还存活的对象复制到另一块 Survivor 空间中，然后清理掉 Eden 和刚才使用过的 Survivor 空间。 

###  典型的垃圾收集器

垃圾收集算法是内存回收的理论基础，而垃圾收集器就是内存回收的具体实现。下面介绍一下 HotSpot（JDK 7) 虚拟机提供的几种垃圾收集器，用户可以根据自己的需求组合出各个年代使用的收集器。

- Serial/Serial Old
  - 最基本、最古老的收集器，单线程，并且在它进行垃圾收集时，必须暂停所有用户线程；
  - Serial：针对新生代的收集器，采用 Copying 算法；
  - Serial Old：针对老年代的收集器，采用 Mark-Compact 算法；
  - 优缺点：
    - 优点：实现简单高效；
    - 缺点：会给用户带来停顿；
- ParNew
  - Serial 收集器的多线程版本，使用多个线程进行垃圾收集；
- Parallel Scavenge
  - 新生代的多线程收集器（并行收集器），它在回收期间不需要暂停其他用户线程；
  - 其采用的是 Copying 算法，该收集器与前两个收集器有所不同，它主要是为了达到一个可控的吞吐量；
- Parallel Old
  - Parallel Scavenge 收集器的老年代版本（并行收集器），使用多线程和 Mark-Compact 算法；
- CMS
  - CMS（Current Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器，它是一种并发收集器，采用的是 Mark-Sweep 算法；
- G1
  - G1 收集器是当今收集器技术发展最前沿的成果，它是一款面向服务端应用的收集器，它能充分利用多CPU、多核环境；
  - 因此它是一款并行与并发收集器，并且它能建立可预测的停顿时间模型。

> 参考链接：http://www.cnblogs.com/dolphin0520/p/3783345.html