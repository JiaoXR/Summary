#  Memcached 小结

###  简介

- MemCache 是一个自由、开源、高性能、分布式的分布式内存对象缓存系统，用于动态 Web 应用以减轻数据库的负载。它通过在内存中缓存数据和对象来减少读取数据库的次数，从而提高了网站访问的速度。MemCaChe 是一个存储键值对的 HashMap，在内存中对任意的数据（比如字符串、对象等）所使用的 key-value 存储，数据可以来自数据库调用、API 调用，或者页面渲染的结果。MemCache 设计理念就是小而强大，它简单的设计促进了快速部署、易于开发并解决面对大规模的数据缓存的许多难题，而所开放的 API 使得 MemCache 能用于 Java、C/C++/C#、Perl、Python、PHP、Ruby 等大部分流行的程序语言。

- 另外，MemCache 和 MemCached 的区别：
  - MemCache 是项目的名称；
  - MemCached 是 MemCache 服务器端可以执行文件的名称；

###  一致性 Hash 算法

- 余数 Hash

  - 比方说，字符串 str 对应的 HashCode 是 50，服务器的数目是 3，取余数得到 2，str 对应节点 Node2，所以路由算法把 str 路由到 Node2 服务器上。由于 HashCode 随机性比较强，所以使用余数 Hash 路由算法就可以保证缓存数据在整个 MemCache 服务器集群中有比较均衡的分布。 
  - 如果不考虑服务器集群的伸缩性（什么是伸缩性，请参见大型网站架构学习笔记），那么余数 Hash 算法几乎可以满足绝大多数的缓存路由需求，但是当分布式缓存集群需要扩容的时候，就难办了。 

- 一致性 Hash 算法

  - 具体算法过程为：先构造一个长度为 2^32 的整数环（这个环被称为一致性 Hash 环），根据节点名称的 Hash 值（其分布为 [0, 2^32-1]）将缓存服务器节点放置在这个 Hash 环上，然后根据需要缓存的数据的 Key 值计算得到其 Hash 值（其分布也为[0, 2^32-1]），然后在 Hash 环上顺时针查找距离这个 Key 值的 Hash 值最近的服务器节点，完成 Key 到服务器的映射查找。如图所示： 

  ![](https://github.com/JiaoXR/Summary/blob/master/pics/consistent_hash_1.png)

  - 集群中缓存服务器节点越多，增加节点带来的影响越小，很好理解。换句话说，随着集群规模的增大，继续命中原有缓存数据的概率会越来越大，虽然仍然有小部分数据缓存在服务器中不能被读到，但是这个比例足够小，即使访问数据库，也不会对数据库造成致命的负载压力。 

  ![](https://github.com/JiaoXR/Summary/blob/master/pics/consistent_hash_2.png)



> 参考链接：http://www.cnblogs.com/szlbm/p/5588549.html