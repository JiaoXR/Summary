#  Java 线程池 & ThreadLocal

###  线程池 ThreadPoolExecutor

- 核心字段

  - corePoolSize：核心池大小；
  - maximumPoolSize：最大线程数量；
  - workQueue：等待队列，任务提交时，若线程池中的线程数量 >= corePoolSize，把该任务封装成一个 Worker 对象放入等待队列；
    - 直接切换：SynchronousQueue；
    - 无界队列：LinkedBlockingQueue；
    - 有界队列：ArrayBlockingQueue；
  - keepAliveTime：若线程池中的线程数量大于 corePoolSize，当某线程空闲时间超过 keepAliveTime，线程将被终止，直至线程池中的线程数目不大于 corePoolSize；

- execute 提交任务执行流程

  - 若运行的线程少于 corePoolSize，则新建线程处理任务，无论其他线程是否空闲；

  - 若线程池中线程的数量 >= corePoolSize，则每来一个任务会尝试将其添加到 workQueue：

    - 若添加成功，该任务会等待空闲线程将其取出执行；
    - 若添加失败（一般是 workQueue 已满），则会尝试新建线程执行任务；

    - 若线程池中的数量达到 maximumPoolSize，则采取拒绝策略处理；
    - 若线程池中的数量大于 corePoolSize：
      - 当某线程空闲时间超过 keepAliveTime，线程将被终止，直至线程池中的线程数目不大于 corePoolSize；
      - 若允许为核心池中的线程设置存活时间（allowCoreThreadTimeOut），则核心池中的线程空闲时间超过 keepAliveTime，线程也会被终止；
    - 提交任务时判断顺序：corePoolSize –> workQueue –> maximumPoolSize；

线程池相关类结构图：

![](https://github.com/JiaoXR/Summary/blob/master/pics/Executor.png)

线程池状态转换图：

![](https://github.com/JiaoXR/Summary/blob/master/pics/ThreadPool_state.png)

线程池执行任务流程：

![](https://github.com/JiaoXR/Summary/blob/master/pics/ThreadPool_flow.png)



###  ThreadLocal 类

- ThreadLocal 变量，它的基本原理是：同一个 ThreadLocal 所包含的对象（对 ThreadLocal\<String\> 而言即为 String 类型变量），在不同的 Thread 中有不同的副本（实际是不同的实例）；
  - 因为每个 Thread 内有自己的实例副本，且该副本只能由当前 Thread 使用。这是也是 ThreadLocal 命名的由来；
  - 既然每个 Thread 有自己的实例副本，且其它 Thread 不可访问，那就不存在多线程间共享的问题；
- ThreadLocal 适用于每个线程需要自己独立的实例，且该实例需要在多个方法中被使用（即变量在线程间隔离、而在方法或类间共享的场景）；

- 适用场景
  - 每个线程需要有自己单独的实例；
  - 变量在线程间隔离、且在方法间共享的场景；

- 示意图

![](https://github.com/JiaoXR/Summary/blob/master/pics/ThreadLocal.png)

> 参考链接：http://www.jasongj.com/java/threadlocal/