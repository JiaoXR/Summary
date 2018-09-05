# JUC 常用类小结
- Lock 接口方法
  - lock
  - lockInterruptibly
  - tryLock
  - tryLock(long time, TimeUnit unit)
  - unlock
  - newCondition
- ReentrantLock
  - 实现 Lock 接口，可重入互斥锁；
  - 基于 AQS 独占模式；
  - 可实现公平/非公平锁（默认非公平）
    - 公平锁：获取锁时排队等待；
    - 非公平锁：不排队，直接获取（效率更高）；
- synchronized & ReentrantLock 比较
  - 相同点
    - 二者都是互斥锁，可重入，默认都是非公平锁；
  - 不同点
    - synchronized：语法层面（关键字），自动获取和释放锁；
    - ReentrantLock：API 层面，手动获取和释放锁；
  - ReentrantLock 相比 synchronized 优势
    - 可响应中断；
    - 带超时的获取锁尝试；
    - 可实现公平锁；
    - 可绑定多个条件（Condition）
  - 性能
    - JDK 1.6 以后，synchronized 与 ReentrantLock 性能基本完全持平；
    - JVM 未来的性能改进也会更偏向于原生的 synchronized。因此，在 synchronized 能满足需求的情况下，优先考虑使用 synchronized；



- ReadWriteLock 接口
  - readLock
  - writeLock
- ReentrantReadWriteLock
  - ReadWriteLock 实现类；
  - 可实现公平锁与非公平锁（默认），基于 AQS 实现；
  - readLock：共享模式，不支持 Condition；
  - writeLock：独占模式，支持 Condition；
  - 可由写锁降级为读锁，反之则不行；



- AbstractQueuedSynchronizer (AQS)
  - 各种锁实现的基石；
  - 队列
    - 同步队列（一个）；
    - 条件队列（可以多个）；
  - 独占模式常用方法
    - acquire：不响应中断方式获取锁；
    - acquireInterruptibly：响应中断方式获取锁；
    - tryAcquireNanos：超时等待获取锁；
    - release：释放锁并离开队列；
  - 共享模式常用方法
    - acquireShared：不响应中断方式获取锁；
    - acquireSharedInterruptibly：响应中断方式获取锁；
    - tryAcquireSharedNanos：超时等待获取锁；
    - releaseShared：释放锁并离开队列；
  - 条件队列
    - await：响应线程中断的条件等待；
    - fullyRelease：完全释放锁；
    - awaitUninterruptibly：不响应线程中断的条件等待；
    - awaitNanos：超时条件等待（不自旋）；
    - await(long time, TimeUnit unit)：超时条件等待（自旋）；
    - signal：唤醒条件队列头结点；
    - signalAll：唤醒条件队列所有结点；



- CountDownLatch (闭锁)
  - 闭锁。类似计数器，只能用一次；
  - 基于 AQS 共享模式；
  - 侧重点：一般用于某个线程 A 等待若干个其他线程执行完任务之后，它才执行；
- CyclicBarrier (循环屏障)
  - 循环屏障。创建一组任务并发执行，在进行下一个步骤前等待；
  - ReentrantLock 保证线程安全；
  - 侧重点：一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；
- Semaphore (信号量)
  - 允许多个线程操作共享资源；可以控制并发数，实现流量控制，实现各种连接池【类比公交车座位】；
  - 基于 AQS 共享模式；

