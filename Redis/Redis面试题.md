# Redis面试题

## Redis为什么快？

+ Redis基于内存，内存的访问速度比磁盘快。

+ Redis基于单线程事件循环和IO多路复用，设计开发了一套高效的事件处理模型。

+ Redis内置了优化过后的数据类型/结构实现，性能非常高。

## Redis和Memcached的区别和共同点？

**共同点**：

1. 都是基于内存的数据库。

2. 都有过期策略。

**不同点**：

1. Redis支持更丰富的数据类型，除k/v类型外还提供list、set、zset、hash等数据结构的存储。

2. **数据持久化**。Redis支持数据持久化，而Memcached把数据全部存在内存中。

3. **集群模式支持**。Memcached没有原生的集群模式，而Redis是原生支持集群模式的。

4. **线程模型**。Memcached是多线程，非阻塞IO服用的网络模型；Redis是单线程的多路IO复用模型。

5. **特性支持**。Redis支持发布订阅模型、Lua脚本、事务等功能，而Memcached不支持。

6. **过期数据删除**。Memcached过期数据的删除策略只用了惰性删除，而Redis同时支持惰性删除与定期删除。

## Redis缓存读写策略

**读的过程统一为**：

1. 从cache中读取数据，读取到就返回。

2. cache中读取不到，从db中读取数据返回。

3. 把数据放到cache中。

**写的策略分为三种**：

+ **旁路缓存模式**。 
  
  1. 先更新db。
  
  2. 然后直接删除cache。
  
  不能先删除cache然后更新db，因为在更新db的过程中数据不一致。

+ **读写穿透**。
  
  读写穿透中将cache视为主要数据存储。
  
  1. 先查cache，cache中不存在则直接更新db。
  
  2. cache中存在，则先更新cache，然后cache服务自己更新db。

+ **异步缓存写入**。
  
  异步缓存写入与读写穿透类似，但是在第二步中只更新缓存，不直接更新db，而是改为异步批量的方式来更新db。

## 如何基于Redis实现延时任务？



## Redis持久化机制

### RDB（Redis Database BackupFile） 持久化

Redis可以通过创建快照来获得内存数据在**某个时间点**上的副本。

快照持久化是Redis默认采用的持久化方式。

在`redis.conf`配置文件中默认有以下配置：

+ `save 900 1`，在900s之后，如果有1个key发生变化，redis会自动触发`bgsave`创建快照。

+ `save 300 10`，在300s之后，如果至少有10个key发生变化，redis会自动触发`bgsave`创建快照。

+ `save 60 10000`，在60s之后，如果至少有1W个key发生变化，Redis会自动触发`bgsave`创建快照。

### AOF（Append-only file） 持久化

与快照持久化相比，**AOF持久化**的实时性更好。

开启**AOF持久化**后每执行一条会更改Redis中数据的命令，Redis就会将该命令写入到AOF缓冲区中，然后再写入到AOF文件中（系统内核缓冲区），最后根据持久化方式的配置来决定何时将系统内核缓存区的数据同步到硬盘中。

**AOF持久化方式有哪些？**

1. `appendfsync always`：主线程调用`write`执行写操作后，后台线程立即调用`fsync`刷盘。

2. `appendfsync everysec`：主线程调用`write`执行写操作后立即返回，由后台线程每秒钟调用`fsync`函数。

3. `appendfsync no`：主线程执行写操作后立即返回，让操作系统决定何时进行同步，Linux下一般为30s一次。

为了兼顾数据和写入性能，可以考虑`appendfsync everysec`，让Redis每秒同步一次AOF文件，Redis性能受到影响较小。这样即使出现系统崩溃，用户最多只会丢失1s内产生的数据。

## Redis线程模型



## 缓存读写策略

## Redis数据类型

+ 5种基础数据类型：String、List、Set、Hash、Zset。

**应用场景**

+ List：最新文章、最新动态。

+ Hash：用户信息、商品信息等对象。

+ Set：存放的数据不能重复的场景，文章点赞人员等。

+ Sorted Set：需要获取元素根据某个权重进行排序的场景，如各种排行榜等。

+ 3种特殊数据类型：HyperLogLog、Bitmap、Geospatial。

## Redis内存管理



## Redis事务



## Redis性能优化



## Redis生产问题
