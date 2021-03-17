# Redis Notes

----------

## 一、基础命令

**redis-server kconfig/redis.conf**：通过指定的配置文件启动服务

**redis-cli -p 6379**：使用redis客户端进行连接

**keys ***：查看所有的key

**shutdown**：关闭连接

**select 3**：切换指定数据库

**dbsize**：查看数据库大小

**flushdb**：清除当前数据库

**flushall**：清除所有内容

**exists key**：判断当前的key是否存在

**move key**：移除当前key

**expire key 10**：设置key的过期时间，单位是秒

**ttl key**：查看当前key的剩余时间

**type key**：查看当前key的类型

------------

## 二、五大基本数据类型

#### 2.1、String（字符串）

##### 2.1.1、命令：

**set key1 v1**：设置值

**get key1**：获取值

**append key1**：追加字符串，不存在则相当于set

**strlen key1**：获取字符串的长度

**incr key1**：自增1

**decr key1**：自减1

**incrby key1 10**：可设置步长，指定增量

**getrange key1 0 3**：截取字符串，end为-1为末尾

**setrange key1 1 "3"**：替换指定位置的字符串

**setex key1 30 hello**：设置key的值，30s后过期

**setnx key1 redis**：如果key1不存在，创建key，如果存在则创建失败

**mset k1 v1 k2 v2 k3 v3**：同时设置多个值

**mget k1 k2 k3**：同时获取多个值

**msetnx k1 v1 k4 v4**：设置多个值，不存在则创建，存在则失败，具有事务属性

**getset key1**：获取原来的值，并设置新的值

#### 2.2、List（列表）

> - 实际上是一个链表。
> - 如果key不存在，则创建新的链表。如果key存在，则新增内容。
> - 如果移除了所有值，空链表，也代表不存在。
> - 在两边插入或者改动值，效率最高。操作中间元素，相对来说效率会低一点

##### 2.2.1、命令

**lpush list1 one**：将一个值或者多个值，插入到列表头部（左）

**rpush list1 rightstr**：将一个值或者多个值，插入到列表尾部（右）

**lrange list1 0 -1**：获取list中的值

**lpop list1**：移除list的第一个元素

**rpop list1**：移除list的最后一个元素

**lindex list1 1**：通过下标获取list的某一个值

**Llen list1**：返回列表的长度

**Lrem list1 2 three**：移除列表指定的值，可定制个数

**Ltrim list1 1 2**：通过下标截取指定的长度，只剩下截取的元素构成的列表

**rpoplpush mylist myotherlist**：移除列表的最后一个元素，将它移动到新的列表中

**lset list1 0 item**：将列表中指定下标的值替换为输入的值，更新操作，不存在该列表则报错

**linsert mylist before "world" "other"**：将某个具体的值插入到列表中某个元素的前面或者后面

#### 2.3、Set（集合）

##### 2.3.1、命令

**sadd myset "hello"**：set集合中添加元素

**smembers myset**：查看指定set的所有值

**sismember myset "hello"**：判断某个值是不是在set集合中

**scard myset**：获取集合长度

**srem myset hello**：移除set集合中的指定元素

**srandmember myset**：随机抽选一个元素

**spop myset**：随机删除set集合中的一个元素

**smove myset myset2 "kk"**：将一个指定的值，移动到另一个集合中

**sdiff myset1 myset2**：差集

**sinter myset1 myset2**：交集

**sunion myset1 myset2**：并集

#### 2.4、Hash（哈希）

> map集合，key-map结构。

##### 2.4.1、命令

**hset myhash field1 "kk"**：设置一个具体的值key-value

**hget myhash field1**：获取一个字段值

**hmset myhash field1 hello field2 world**：设置多个值

**hmget myhash field1 field2**：获取多个字段值

**hgetall myhash**：获取全部的数据

**hdel myhash field1**：删除hash指定的key字段

**hlen myhash**：获取哈希字段数

**hexists myhash field1**：判断hash中指定的字段是否存在

**hkeys myhash**：获取所有的field

**hvals myhash**：获取所有的value

**hincrby myhash field3 1**：指定增量

**hsetnx myhash field4 hello**：不存在则创建，存在则失败

#### 2.5、Zset（有序集合）

> 在set的基础上，增加了一个值，myset k1 v1	myzset k1 score1 v1

#####  2.5.1、命令

**zadd myset 1 one**：添加一个值

**zadd myset 2 two 3 three**：添加多个值

**zrange myset 0 -1**：获取所有的值

**zrangebyscore salary -inf +inf**：显示全部数据，根据score从小到大排序。withscores：附带成绩

**zrevrange salary 0 -1**：显示全部数据，根据score从大到小排序

**zcount myset 1 3**：获取指定区间的成员数量

-------

## 三、三种特殊数据类型

#### 3.1、geospatial 地理位置

> geo底层实现原理就是Zset。

##### 3.1.1、命令

**geoadd china:city 116.40 39.90 beijing**：添加地理位置

**geopos china:city beijing**：获取指定城市的经度和纬度

**geodist china:city beijing shanghai km**：获取两地的直线距离

**georadius china:city 110 30 1000km**：以110：30经纬度为中心，寻找方圆1000km内的城市。

- **withdist**：显示到中心的距离

- **withcoord**：显示城市的定位信息
- **count 1**：筛选结果个数

**georadiusbymember china:city beijing 1000 km**：找出指定元素周围的其他元素

**geohash china:city beijing shanghai**：将二维的经纬度转换为一维的字符串，如果两个字符串越近，距离则越近

#### 3.2、hyperloglog 统计基数

> 统计基数，即不重复的元素。存在误差。

##### 3.2.1、命令

**pfadd mykey a b c d**：创建元素

**pfcount mykey**：统计mykey元素基数数量

**pfmerge mykey3 mykey mykey2**：合并两组，并集

#### 3.3、Bitmap 位图

> 位存储。一种数据结构，操作二进制位来进行记录， 只有0，1两个状态。

##### 3.3.1、命令

**setbit sign 0 1**：设置值

**getbit sign 6**：获取值

**bitcount sign**：统计

-------

## 四、事务

> redis事务的本质：一组命令的集合。一个事务中所有命令都会被序列化，在事务执行过程中，会按照顺序执行。
>
> 一次性、顺序性、排他性。

```
-------- 队列 set set get set 执行 ---------
```

**redis单条命令保证原子性，但是事务不保证原子性！**

**redis事务不允许编译时异常，允许运行时异常。**

redis的事务：

- 开启事务（multi）
- 命令入队（...）
- 执行事务（exec）
- 放弃事务（discard）

使用**watch/uwatch**可以当做redis的乐观锁操作。

--------

## 五、Jedis

----

## 六、redis持久化

#### 6.1、RDB（Redis Database）

> 在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是Snapshot快照，它恢复时是将快照文件直接读到内存里。
>
> Redis会单独创建（fork）一个子进程来进行持久化，先将数据写入到临时文件中，待持久化结束，替代原来的文件。整个过程，主进程是不进行任何IO操作的，这就确保了极高的性能。
>
> 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB比AOF方式更加的高效。RDB的缺点是**最后一次持久化后的数据可能会丢失**，默认方式是RDB。

==rdb保存的文件是：dump.rdb==（配置文件中定义）

**触发机制**：

1. save规则满足的情况下，会自动触发rdb规则。
2. 执行flushall命令，也会触发rdb规则。
3. 退出redis，也会产生rdb文件。

**恢复条件**：

1. 只需要将rdb文件放在我们redis启动目录就可以，redis启动的时候会自动检查dump.rdb恢复其中的数据。

```bash
> config get dir	# 查看需要存在的位置
> "dir"
> "usr/local/bin"	# 如果这个目录下存在dump.rdb文件，启动就会自动恢复其中的数据
```



**优点**：

1. 适合大规模的数据恢复！

**缺点**：

1. 需要一定的时间间隔 ，如果redis意外宕机了，最后一次修改的数据无法恢复。
2. fork进程时，会占用一定的内存空间。

#### 6.2、AOF（Append Only File）

> 将所有命令记录下来，history，恢复的时候完整执行一遍。
>
> 以日志的形式来记录每一个写的操作，将Redis执行过的所有指令记录下来（读操作不记录），只许追加文件但不改写文件。redis启动之初会读取该文件重新构建数据。
>
> redis重启了，就会根据日志文件的内容将写指令从前到后执行一次以完整的数据工作。

==aof保存的文件是：appendonly.aof==

默认是不开启，需要手动配置：`appendonly yes`，重启生效。

如果aof配置文件有错误，redis会启动失败，可通过`redis-check-aof --fix`修复。

**优点**：

1. 每一次修改都会同步，文件的完整性更好。

**缺点**：

1. 相对于数据文件来说，aof远大于rdb，修复的速度也较慢。
2. Aof运行效率也要比rdb慢，所以我们redis默认配置就是rdb持久化。

-----

## 七、Redis 发布订阅

#### 7.1、概念

> Redis发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接受消息。
>
> Redis可以订阅任意数量的频道。

![image-20210313185249412](E:%5CLearn%5CLearn-Notes%5CImages%5Cimage-20210313185249412.png)

![image-20210313184959004.png](E:%5CLearn%5CLearn-Notes%5CImages%5Cimage-20210313184959004.png)

#### 7.2、命令

**subscribe kk**：订阅一个频道 kk，然后等待消息推送

- 1) #消息
- 2) #频道
- 3) #内容

**publish kk "hello"**：发布者发布消息到频道

#### 7.3、原理

> ​	通过subscribe订阅某个频道之后，redis-server里维护了一个字典，字典的键就是一个个channel，而字典的值则是一个链表，链表中保存了所有订阅这个channel的客户端。就是将客户端添加到给定channel的订阅链表中。
>
> ​	通过publish命令向订阅者发送消息，redis-server会使用给定的频道作为键，在它维护的channel字典中查找记录了订阅这个频道的所有客户端的链表，遍历这个链表，将消息发布给所有订阅者。
>
> ​	Pub/Sub从字面上理解就是发布（Publish）和订阅（Subscribe），在Redis中，你可以设定对某一个key值进行消息发布及消息订阅，当一个key值上进行了消息发布后，所有订阅它的客户端都会收到相应的消息。这一功能最明显的用法就是用作实时消息系统，比如普通的即时聊天，群聊等功能。

--------

## 八、Redis主从复制

#### 8.1、概念

> 主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点（master/leader），后者称为从节点（slave/follower）；==数据的复制是单向的，只能从主节点到从节点。==Master以写为主，Slave以读为主。
>
> ==默认情况下，每台Redis服务器都是主节点。==
>
> 且一个主节点可以有多个从节点（或者没有从节点），但一个从节点只能有一个主节点。

**主从复制的作用主要包括**：

1. 数据备份：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复。实际上是一种服务的冗余。
3. 负载均衡：配合读写分离，分担服务器负载。
4. 高可用基石：主从复制是哨兵和集群能够实施的基础，因此说主从复制是redis高可用的基础。

**slaveof host port**：配置从机的主节点。

**复制原理**：

- Salve启动成功后连接到master后会发送一个sync同步命令

- Master接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕后，==master将传送整个数据文件到Salve，并完成一次完全同步。==

- 全量复制：而Salve服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- 增量复制：Master继续将新的所有收集到的修改命令依次传给salve，完成同步。

==但是只要重新连接master，一次完全同步（全量复制）将被自动执行。==

#### 8.2、哨兵模式

> 主从切换技术的自动模式。
>
> 能够后台监控主机是否故障，如果故障了根据投票数**自动将从库转换为主库**，主机回来了再进行自动切换。
>
> 哨兵模式是一种特殊的模式，redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它为独立运行。其原理是：==哨兵通过发送命令，等待redis服务器响应，从而监控运行的多个redis实例。==

**配置文件：sentinel.conf**

```bash
# sentinel monitor 被监控的名称 host port 票数
sentinel monitor myredis 127.0.0.1 6379 1
```

**启动哨兵**：redis-sentinel kconfig/sentinel.conf

**优点**：

1. 哨兵集群，基于主从复制模式，所有的主从配置优点都继承。
2. 主从模式自动切换，故障可以转移，系统可用性会更好。

**缺点**：

1. 在线扩容十分麻烦，需要修改配置。
2. 实现哨兵模式的配置很复杂。

--------

## 九、Redis 缓存穿透和雪崩

#### 9.1、缓存穿透

> ​	查询不通过缓存，直接请求持久层数据库的场景。




