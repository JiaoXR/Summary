#  Memcached & Redis

- Redis 持久化

  Redis 和 Memcached 的最大不同，就是 Redis 支持数据持久化，这也是很多人选择使用 Redis 而非  Memcached 的最大原因。Redis 的持久化，分为两种策略，用户可以配置使用不同的策略：

  - RDB
    - 用户执行 save 或 bgsave 时，会触发 RDB 持久化操作；
    - RDB 持久化操作核心思想：把数据库原封不动的保存在文件里；
  - AOF
    - RDB 保存的只是最终的数据库，它是一个结果。结果是怎么来的？是通过用户的各个命令建立起来的，因此可以不保存结果，而只保存建立这个结果的命令；
    - Redis 的 AOF 就是这个思想，它不同 RDB 保存 DB 的数据，它保存的是一条一条建立数据库的命令；

- Redis 事务

  - Redis 另一个比 Memcached 强大的地方，是它支持简单的事务；
  - 事务简单说就是把几个命令合并，一次性执行全部命令。对于关系型数据库来说，事务还有回滚机制，即事务命令要么全部执行成功，只要有一条失败就回滚，回到事务执行前的状态；
  - Redis 不支持回滚，它的事务只保证命令依次被执行，即使中间一条命令出错也会继续往下执行，所以说它只支持简单的事务；

- 二者对比

  - 总的来看，Redis 比 Memcached 的功能多很多，实现也更复杂；
  - 不过 Memcached 更专注于保存 key-value 数据（这已经能满足大多数使用场景了），而 Redis 提供更丰富的数据结构及其他的一些功能；
  - 不能说 Redis 比 Memcached 好，不过从源码阅读的角度来看，Redis 的价值或许更大一点；
  - 性能对比：由于 Redis 只使用单核，而 Memcached 可以使用多核，所以平均每一个核上 Redis 在存储小数据时比 Memcached 性能更高。而在 100K 以上的数据中，Memcached 性能要高于 Redis，虽然 Redis 最近也在存储大数据的性能上进行优化，但是比起 Memcached，还是稍有逊色。



> 参考链接：
>
> 1. https://www.cnblogs.com/wangdaijun/p/6014928.html
> 2. https://www.biaodianfu.com/redis-vs-memcached.html