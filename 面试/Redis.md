# 1-什么是 Redis

定义->优点->使用场景

- Redis是一个基于 C 语言开发的开源 <font color="red">NoSQL</font> 数据库，存储的是 KV <font color="red">键值对</font>数据
- Redis 的数据是保存在<font color="red">内存</font>中的（内存数据库，支持持久化），因此读写速度非常快，广泛用于缓存
- 使用场景：
    - 用于缓存
    - 用来做<font color="red">分布式锁</font>
    - 为了满足不同的业务场景，Redis 内置了<font color="red">多种数据类型</font>实现



# 2-单线程的Redis为什么这么快

- Redis 数据读写是在<font color="red">内存</font>中，数据的读写操作快
- 单线程避免了锁竞争和线程切换带来的<font color="red">性能损耗</font>，处理简单高效
- 高效的<font color="red">数据结构</font>，Redis 内部使用的是优化后的数据结构
- 采用<font color="red">单线程事件循环和非阻塞 I/O 多路复用</font>机制
- Redis <font color="red">通信协议</font>实现简单且解析高效



# 2-缓存穿透、缓存击穿和缓存雪崩

## 2-1-缓存穿透

### 2-1-1-什么是缓存穿透

![image-20250522103437495](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522103437495.png)

==大量请求的 key 是不合理的，**根本不存在于缓存和数据库中**==。这就导致这些请求直接到了数据库上，根本没有经过缓存这一层，对数据库造成了巨大的压力，可能直接就被这么多请求弄宕机了



### 2-1-2-有哪些解决办法呢

#### 2-1-2-1-缓存无效key

- 如果缓存和数据库都查不到某个 key 的数据，就<font color="red">写</font>一个到 Redis 中去并设置过期时间

- 这种方式可以解决请求的<font color="red"> key 变化不频繁</font>的情况，如果黑客恶意攻击，每次构建不同的请求 key，会导致 Redis 中缓存大量无效的 key。很明显，这种方案并不能从根本上解决此问题

- 如果非要用这种方式来解决穿透问题的话，尽量将无效的 key 的<font color="red">过期时间</font>设置短一点，比如 1 分钟

    

#### 2-1-2-2-布隆过滤器

![image-20250522103942284](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522103942284.png)

bitmap（位图）：相对于一个以（bit）位为单位的数组，数组中每个单元只能存储二进制数0或1

布隆过滤器作用：检查一个元素是否存在与一个集合中

- 可以把它看作由二进制向量（或者说位数组）和一系列随机映射函数（哈希函数）<font color="red">两部分组成的数据结构</font>

- 相比于我们平时常用的 List、Map、Set 等数据结构，它<font color="red">占用空间更少并且效率更高</font>，但是缺点是其返回的结果是概率性的，而不是非常准确的。理论情况下添加到集合中的元素越多，<font color="red">误报</font>的可能性就越大
- 并且，存放在布隆过滤器的数据<font color="red">不容易删除</font>



## 2-2-缓存击穿

### 2-2-1-什么是缓存击穿

![image-20250522105536452](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522105536452.png)

缓存击穿中，请求的 key 对应的是 **热点数据**，==该数据 **存在于数据库中，但不存在于缓存中（通常是因为缓存中的那份数据已经过期）**==。这就可能会导致瞬时大量的请求直接打到了数据库上，对数据库造成了巨大的压力，可能直接就被这么多请求弄宕机了



### 2-2-2-有哪些解决办法呢

![image-20250522110214591](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522110214591.png)



## 2-3-缓存雪崩

### 2-3-1-什么是缓存雪崩

==缓存在同一时间大面积的失效或者Redis宕机，导致大量的请求都直接落到了数据库上，对数据库造成了巨大的压力==



### 2-3-2-有哪些解决办法呢

- 设置随机<font color="red">失效时间</font>
- 利用Redis集群提高服务的<font color="red">可用性</font>（哨兵模式和集群模式）
- 给缓存业务添加<font color="red">降级限流</font>策略（nginx和gateway）
- 给业务添加多级缓存



# 3-如何保证缓存和数据库的一致性

- <font color="red">双写一致性</font>：当修改了数据库的数据，也要同时更新缓存的数据，使得缓存和数据库的数据保持一致
- 通常情况下，保证数据库跟缓存的<font color="red">最终一致性</font>即可，不必追求强一致性



## 3-1-更新数据库 + 更新缓存

- 每次数据发生变更，都会更新缓存，但是缓存中的数据不一定会被马上读取，这就会导致缓存中可能存放了很多不常访问的数据，<font color="red">浪费缓存资源，缓存利用率不高</font>

- 缓存中的值可能是经过一系列计算的，而并不是直接跟数据库中的数据对应的，频繁更新缓存会导致大量无效的计算，造成<font color="red">机器性能的浪费</font>



## 3-2-更新数据库 + 删除缓存

2个线程并发：

![image-20250522160551598](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522160551598.png)

- 这两种方式都有可能产生数据不一致的问题
- 在“先更新数据库，后删除缓存”中，需要出现数据不一致的问题需要满足下面三个条件：
    - <font color="red">缓存</font>刚好已失效
    - <font color="red">读请求并发</font>
    - 更新数据库 + 删除缓存的时间，要比读数据库 + 写缓存<font color="red">时间短</font>
- 因为写数据库一般会先加锁，所以写数据库，通常是要比读数据库的时间更长的，所以条件 3 发生的概率其实是非常低的，这么来看，这种方案是可以保证数据一致性的
- 在<font color="red">“读写分离 + 主从库延迟”</font>的情况下，第二种方案数据导致不一致



这该如何解决呢？最有效的办法就是，把缓存延迟删掉



## 3-3-聊聊延迟双删

- 延迟双删，==如果是写操作，我们先把缓存中的数据删除，然后更新数据库，最后再延时删除缓存中的数据==。其中，这个延时多久不太好确定。在延时的过程中，可能会出现脏数据，并不能保证强一致性

- 缓存删除可能会引发缓存击穿的问题
- 在更新数据库 + 删除缓存的方案中，“先删除缓存，再更新数据库”，在“并发”场景下依旧有数据不一致问题，解决方案是“延迟双删”，但这个延迟时间很难评估，所以推荐用“先更新数据库，再删除缓存”的方案



## 3-4-如何保证强一致性

加分布式锁（如使用 Redis 的 Redlock）

使用 Canal 或 Binlog 监听数据库变更，自动删缓存



## 3-5如何保证最终一致

![image-20250522163355269](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522163355269.png)

![image-20250522163503801](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522163503801.png)



# 4-Redis 持久化

## 4-1-RDB 持久化

- Redis 可以通过创建<font color="red">快照</font>来获得存储在内存里面的数据在某个时间点上的副本
- Redis 创建快照之后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis <font color="red">主从结构</font>，主要用来提高 Redis 性能），还可以将快照留在原地以便<font color="red">重启服务器</font>的时候使用



快照持久化是 Redis 默认采用的持久化方式，在 `redis.conf` 配置文件中默认有此下配置：

```
save 900 1    #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发bgsave命令创建快照

save 300 10   #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发bgsave命令创建快照

save 60 10000 #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发bgsave命令创建快照
```



### 4-1-1-RDB 创建快照时会阻塞主线程吗

Redis 提供了两个命令来生成 RDB 快照文件：

- `save` : 同步保存操作，会阻塞 Redis 主线程
- `bgsave` : fork 出一个子进程，子进程执行，不会阻塞 Redis 主线程，默认选项



### 4-1-2-RDB的执行原理

![image-20250523104913297](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250523104913297.png)



## 4-2-AOF 持久化

与快照持久化相比，AOF 持久化的<font color="red">实时性</font>更好

默认情况下 Redis 没有开启 AOF（append only file）方式的持久化（Redis 6.0 之后已经默认是开启了），可以通过 `appendonly` 参数开启：

```
appendonly yes
```

- 开启 AOF 持久化后每执行一条会更改 Redis 中的数据的命令，Redis 就会将该命令写入到<font color="red"> AOF 缓冲区 </font>`server.aof_buf` 中，然后再写入到 AOF 文件中（此时还在系统内核缓存区未同步到磁盘），最后再根据<font color="red">持久化方式</font>（ `fsync`策略）的配置来决定何时将系统内核缓存区的数据同步到硬盘中的

- 只有同步到磁盘中才算持久化保存了，否则依然存在数据丢失的风险，比如说：系统内核缓存区的数据还未同步，磁盘机器就宕机了，那这部分数据就算丢失了

AOF 文件的<font color="red">保存位置</font>和 RDB 文件的位置相同，都是通过 `dir` 参数设置的，默认的文件名是 `appendonly.aof`



### 4-2-1-AOF 工作基本流程是怎样的

AOF 持久化功能的实现可以简单分为 5 步：

1. <font color="red">命令追加（append）</font>：所有的写命令会追加到 AOF 缓冲区中
2. <font color="red">文件写入（write）</font>：将 AOF 缓冲区的数据写入到 AOF 文件中。这一步需要调用`write`函数（系统调用），`write`将数据写入到了系统内核缓冲区之后直接返回了（延迟写）。注意！！！此时并没有同步到磁盘
3. <font color="red">文件同步（fsync）</font>：AOF 缓冲区根据对应的持久化方式（ `fsync` 策略）向硬盘做同步操作。这一步需要调用 `fsync` 函数（系统调用）， `fsync` 针对单个文件操作，对其进行强制硬盘同步，`fsync` 将阻塞直到写入磁盘完成后返回，保证了数据持久化
4. <font color="red">文件重写（rewrite）</font>：随着 AOF 文件越来越大，需要定期对 AOF 文件进行重写，达到压缩的目的
5. <font color="red">重启加载（load）</font>：当 Redis 重启时，可以加载 AOF 文件进行数据恢复



### 4-2-2-AOF 持久化方式有哪些

这 3 种持久化方式的主要区别在于 ==`fsync` 同步 AOF 文件的时机（刷盘）==

在 Redis 的配置文件中存在三种不同的 AOF 持久化方式（ `fsync`策略），它们分别是：

1. `appendfsync always`：主线程调用 `write` 执行写操作后，后台线程（ `aof_fsync` 线程）立即会调用 `fsync` 函数同步 AOF 文件（刷盘），`fsync` 完成后线程返回，这样会严重降低 Redis 的性能（`write` + `fsync`）
2. `appendfsync everysec`：主线程调用 `write` 执行写操作后立即返回，由后台线程（ `aof_fsync` 线程）每秒钟调用 `fsync` 函数（系统调用）同步一次 AOF 文件（`write`+`fsync`，`fsync`间隔为 1 秒）
3. `appendfsync no`：主线程调用 `write` 执行写操作后立即返回，让操作系统决定何时进行同步，Linux 下一般为 30 秒一次（`write`但不`fsync`，`fsync` 的时机由操作系统决定）



### 4-2-3-AOF 为什么是在执行完命令之后记录日志

关系型数据库（如 MySQL）通常都是执行命令之前记录日志（方便故障恢复），而 ==Redis AOF 持久化机制是在执行完命令之后再记录日志==

- 避免额外的<font color="red">检查开销</font>，AOF 记录日志不会对命令进行语法检查
- 在命令执行完之后再记录，不会<font color="red">阻塞</font>当前的命令执行

缺点：

- 如果刚执行完命令 Redis 就宕机会导致对应的<font color="red">修改丢失</font>
- 可能会<font color="red">阻塞</font>后续其他命令的执行（AOF 记录日志是在 Redis 主线程中进行的）



### 4-2-4-AOF 重写了解吗

- 由于 AOF 重写会进行大量的写入操作，为了避免对 Redis 正常处理命令请求造成影响，Redis 将 AOF 重写程序放到<font color="red">子进程</font>里执行

- 当 AOF 变得太大时，Redis 能够在后台自动重写 AOF 产生一个新的 AOF 文件，这个新的 AOF 文件和原有的 AOF 文件所保存的数据库状态一样，但<font color="red">体积更小</font>

- AOF 文件重写期间，Redis 还会维护一个<font color="red"> AOF 重写缓冲区</font>，该缓冲区会在子进程创建新 AOF 文件期间，记录服务器执行的所有写命令。当子进程完成创建新 AOF 文件的工作之后，服务器会将重写缓冲区中的所有内容追加到新 AOF 文件的末尾，使得新的 AOF 文件保存的数据库状态与现有的数据库状态一致。最后，服务器用新的 AOF 文件替换旧的 AOF 文件，以此来完成 AOF 文件重写操作



开启 AOF 重写功能，可以调用 `BGREWRITEAOF` 命令手动执行，也可以设置下面两个配置项，让程序自动决定触发时机：

- `auto-aof-rewrite-min-size`：如果 AOF 文件大小小于该值，则不会触发 AOF 重写。默认值为 64 MB
- `auto-aof-rewrite-percentage`：执行 AOF 重写时，当前 AOF 大小（aof_current_size）和上一次重写时 AOF 大小（aof_base_size）的比值。如果当前 AOF 文件大小增加了这个百分比值，将触发 AOF 重写。将此值设置为 0 将禁用自动 AOF 重写。默认值为 100



## 4-3-如何选择 RDB 和 AOF

![image-20250523111121131](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250523111121131.png)

**RDB 比 AOF 优秀的地方**：

- RDB 文件存储的内容是经过压缩的二进制数据， 保存着某个时间点的数据集，<font color="red">文件很小</font>，适合做数据的备份，灾难恢复。AOF 文件存储的是每一次写命令，类似于 MySQL 的 binlog 日志，通常会比 RDB 文件大很多。当 AOF 变得太大时，Redis 能够在后台自动重写 AOF。新的 AOF 文件和原有的 AOF 文件所保存的数据库状态一样，但体积更小。不过， Redis 7.0 版本之前，如果在重写期间有写入命令，AOF 可能会使用大量内存，重写期间到达的所有写入命令都会写入磁盘两次
- 使用 RDB 文件<font color="red">恢复数据</font>，直接解析还原数据即可，不需要一条一条地执行命令，速度非常快。而 AOF 则需要依次执行每个写命令，速度非常慢。也就是说，与 AOF 相比，恢复大数据集的时候，RDB 速度更快

**AOF 比 RDB 优秀的地方**：

- RDB 的<font color="red">数据安全性</font>不如 AOF，没有办法实时或者秒级持久化数据。生成 RDB 文件的过程是比较<font color="red">繁重</font>的， 虽然 BGSAVE 子进程写入 RDB 文件的工作不会阻塞主线程，但会对机器的 CPU 资源和内存资源产生影响，严重的情况下甚至会直接把 Redis 服务干宕机。AOF 支持秒级数据丢失（取决 fsync 策略，如果是 everysec，最多丢失 1 秒的数据），仅仅是追加命令到 AOF 文件，操作轻量
- RDB 文件是以特定的二进制格式保存的，并且在 Redis 版本演进中有多个版本的 RDB，所以存在老版本的 Redis 服务<font color="red">不兼容新版本</font>的 RDB 格式的问题
- AOF 以一种易于理解和解析的格式包含所有操作的日志。你可以轻松地导出 AOF 文件进行分析，你也可以直接操作 AOF 文件来解决一些问题。比如，如果执行`FLUSHALL`命令意外地刷新了所有内容后，只要 AOF 文件没有被重写，删除最新命令并重启即可恢复之前的状态

**综上**：

- Redis 保存的数据丢失一些也没什么影响的话，可以选择使用 RDB
- 不建议单独使用 AOF，因为时不时地创建一个 RDB 快照可以进行数据库备份、更快的重启以及解决 AOF 引擎错误
- 如果保存的数据要求安全性比较高的话，建议同时开启 RDB 和 AOF 持久化或者开启 RDB 和 AOF 混合持久化



# 5-Redis 的数据过期策略

Redis的过期删除策略是：惰性删除 + 定期删除两种策略配合使用

## 5-1-惰性删除

- 定义：key过期之后，不会马上被删除掉。当需要该key时，我们检查其是否过期，如果过期，我们就删掉它；反之，返回该key
- 优点：对<font color="red">CPU友好</font>，只会在使用key时才会进行过期检查，对于很多用不到的key不用浪费时间进行过期检查
- 缺点：对<font color="red">内存不友好</font>，如果一个key已经过期，但是一直没有使用，那么该key就会一直存在内存中



## 5-2-定期删除

- 定义：每隔一段时间，就对一些key进行检查，并删除里面过期的key
- 定期清理的两种模式
    - SLOW模式，是定时任务，执行频率默认为10hz，每次不超过25ms，可以通过修改配置文件redis.conf的hz选项来调整这个次数
    -  FAST模式，执行频率不固定，每次事件循环会尝试执行，但两次间隔不低于2ms，每次耗时不超过1ms
- 优点：通过限制删除操作执行的时长和频率来减少删除操作对<font color="red">CPU的影响</font>。同时有效释放过期键的<font color="red">内容占用</font>
- 缺点：<font color="red">难以确定</font>删除执行操作的时长和频率



# 6-Redis 的数据淘汰策略（保证哪些是热点数据）

![image-20250523163617459](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250523163617459.png)

![image-20250523163926511](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250523163926511.png)



# 7-Redis 的缓存读写策略

## 7-1-旁路缓存模式

- Cache Aside Pattern 是我们平时使用比较多的一个缓存读写模式，比较适合<font color="red">读请求比较多</font>的场景

- Cache Aside Pattern 中服务端需要<font color="red">同时维系 db 和 cache</font>，并且是以 db 的结果为准
- 写：
    - 先更新 db
    - 然后直接删除 cache
- 读：
    - 从 cache 中读取数据，读取到就直接返回
    - cache 中读取不到的话，就从 db 中读取数据返回
    - 再把数据放到 cache 中



## 7-2-读写穿透

- Read/Write Through Pattern 中服务端把 cache 视为<font color="red">主要数据存储</font>，从中读取数据并将数据写入其中。cache 服务负责将此数据读取和写入 db，从而减轻了应用程序的职责
- 分布式缓存 Redis 并没有提供 cache 将数据写入 db 的功能
- 写：
    - 先查 cache，cache 中不存在，直接更新 db
    - cache 中存在，则先更新 cache，然后 cache 服务自己更新 db（同步更新 cache 和 db）
- 读：
    - 从 cache 中读取数据，读取到就直接返回 
    - 读取不到的话，先从 db 加载，写入到 cache 后返回响应



## 7-3-异步缓存写入

- Write Behind Pattern 和 Read/Write Through Pattern 很相似，两者都是由 <font color="red">cache 服务</font>来负责 cache 和 db 的读写
- 不同：Read/Write Through 是<font color="red">同步更新</font> cache 和 db，而 Write Behind 则是只更新缓存，不直接更新 db，而是改为异步批量的方式来更新 db



# 8-Redis 分布式锁

分布式系统下，不同的服务/客户端通常运行在独立的 JVM 进程上。如果多个 JVM 进程共享同一份资源的话，使用本地锁就没办法实现资源的互斥访问了

## 8-1-分布式锁应该具备哪些条件

- **互斥**：任意一个时刻，锁只能被一个线程持有

- **高可用**：锁服务是高可用的，当一个锁服务出现问题，能够自动切换到另外一个锁服务。并且，即使客户端的释放锁的代码逻辑出现问题，锁最终一定还是会被释放，不会影响其他线程对共享资源的访问。这一般是通过超时机制实现的

- **可重入**：一个节点获取了锁之后，还可以再次获取锁



## 8-2-如何基于Redis实现分布式锁

- 在 Redis 中， `SETNX` 命令是可以帮助我们实现互斥。如果 key 不存在的话，才会设置 key 的值。如果 key 已经存在， `SETNX` 啥也不做。同时设置一个<font color="red">锁过期时间</font>
- 释放锁的话，直接通过 `DEL` 命令删除对应的 key 
- 选用 <font color="red">Lua 脚本</font>是为了保证解锁操作的原子性。因为 Redis 在执行 Lua 脚本时，可以以原子性的方式执行，从而保证了锁释放操作的原子性

![image-20250525160451286](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250525160451286.png)

存在问题：

- 锁过期时间难设置合理
- 操作共享资源的时间大于过期时间，就会出现锁提前过期的问题



## 8-3-Redisson

Redisson 中的分布式锁自带<font color="red">自动续期机制</font>，使用起来非常简单，原理也比较简单，其提供了一个专门用来监控和续期锁的 <font color="red">Watch Dog（ 看门狗）</font>，如果操作共享资源的线程还未执行完成的话，Watch Dog 会不断地延长锁的过期时间，进而保证锁不会因为超时而被释放

![image-20250525161056988](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250525161056988.png)



# 9-数据类型

## 9-1-Redis 5种基本数据类型

Redis 共有 5 种基本数据类型：String（字符串）、List（列表）、Hash（散列）、Set（集合）、Zset（有序集合）



### 9-1-1-String

定义->优点

- Redis 并没有使用 C 的字符串表示，而是自己构建了一种<font color="red">简单动态字符串</font>（Simple Dynamic String，**SDS**）

- 优点：

    - String 是一种<font color="red">二进制安全</font>的数据类型，可以用来<font color="red">存储任何类型的数据</font>

    - <font color="red">获取字符串长度复杂度</font>为 O(1)（C 字符串为 O(N)）
    - Redis 的 SDS API 是安全的，不会造成<font color="red">缓冲区溢出</font>

     

### 9-1-2-List

- Redis 的 List 的实现为一个<font color="red">双向链表</font>，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销
- `List` 可以用来做<font color="red">消息队列</font>，只是功能过于简单且存在很多缺陷，不建议这样做



### 9-1-3-Hash

Redis 中的 Hash 是一个 String 类型的 field-value（键值对） 的映射表，适合用于<font color="red">存储对象</font>



### 9-1-4-Set

Redis 中的 Set 类型是一种<font color="red">无序集合</font>，集合中的元素没有先后顺序但都<font color="red">唯一</font>



### 9-1-5-ZSet

Sorted Set 类似于 Set，但和 Set 相比，Sorted Set 增加了一个权重参数 `score`，使得集合中的元素能够按 `score` 进行有序排列，还可以通过 `score` 的范围来获取元素的列表





## 9-2- Redis 3种特殊数据类型

### 9-2-1- Bitmap

- Bitmap（位图）存储的是连续的二进制数字（0 和 1），通过 Bitmap, 只需要一个 bit 位来表示某个元素对应的值或者状态，key 就是对应元素本身 
- 可以将 Bitmap 看作是一个存储二进制数字（0 和 1）的数组，数组中每个元素的下标叫做 offset（偏移量）



### 9-2-2- HyperLogLog

- HyperLogLog 是一种有名的<font color="red">基数计数概率算法</font> ，基于 LogLog Counting(LLC)优化改进得来，并不是 Redis 特有的，Redis 只是实现了这个算法并提供了一些开箱即用的 API

- Redis 对 HyperLogLog 的存储结构做了优化，采用两种方式计数：
    - **稀疏矩阵**：计数较少的时候，占用空间很小
    - **稠密矩阵**：计数达到某个阈值的时候，占用 12k 的空间
- 基数计数概率算法为了节省内存并不会直接存储元数据，而是通过一定的概率统计方法预估基数值（集合中包含元素的个数）。因此， HyperLogLog 的计数结果并不是一个精确值，存在一定的误差（标准误差为 `0.81%` ）



### 9-2-3- GeoSpatial

Geospatial index（地理空间索引，简称 GEO） 主要用于存储地理位置信息，基于 Sorted Set 实现



## 9-3- String 还是 Hash 存储对象数据更好呢

- <font color="red">对象存储方式</font>：String 存储的是序列化后的对象数据，存放的是整个对象，操作简单直接。Hash 是对对象的每个字段单独存储，可以获取部分字段的信息，也可以修改或者添加部分字段，节省网络流量
    - 如果对象中某些字段需要经常操作（变动或经常查询）对象中的<font color="red">个别字段信息</font>，Hash 就非常适合
    - String 在处理<font color="red">多层嵌套或复杂结构的对象</font>时更方便，因为无需处理每个字段的独立存储和操作
    - <font color="red">性能</font>：String 的操作通常具有 O(1) 的时间复杂度，因为它存储的是整个对象，操作简单直接，整体读写的性能较好。Hash 由于需要处理多个字段的增删改查操作，在字段较多且经常变动的情况下，可能会带来额外的性能开销
- <font color="red">内存消耗</font>：Hash 通常比 String 更节省内存，特别是在字段较多且字段长度较短时。Redis 对小型 Hash 进行优化（如使用 ziplist 存储），进一步降低内存占用



## 9-4- String 的底层实现是什么

Redis 是基于 C 语言编写的，但 Redis 的 String 类型的底层实现并不是 C 语言中的字符串（即以空字符 `\0` 结尾的字符数组），而是自己编写了 SDS（Simple Dynamic String，简单动态字符串）来作为底层实现

包含了下面这 4 个属性：

- `len`：字符串的长度也就是已经使用的字节数
- `alloc`：总共可用的字符空间大小，alloc-len 就是 SDS 剩余的空间大小
- `buf[]`：实际存储字符串的数组
- `flags`：低三位保存类型标志

SDS 相比于 C 语言中的字符串有如下提升：

- 可以避免<font color="red">缓冲区溢出</font>：C 语言中的字符串被修改（比如拼接）时，一旦没有分配足够长度的内存空间，就会造成缓冲区溢出。SDS 被修改时，会先根据 len 属性检查空间大小是否满足要求，如果不满足，则先扩展至所需大小再进行修改操作
- <font color="red">获取字符串长度的复杂度</font>较低：C 语言中的字符串的长度通常是经过遍历计数来实现的，时间复杂度为 O(n)。SDS 的长度获取直接读取 len 属性即可，时间复杂度为 O(1)
- 减少<font color="red">内存分配次数</font>：为了避免修改（增加/减少）字符串时，每次都需要重新分配内存（C 语言的字符串是这样的），SDS 实现了空间预分配和惰性空间释放两种优化策略。当 SDS 需要增加字符串时，Redis 会为 SDS 分配好内存，并且根据特定的算法分配多余的内存，这样可以减少连续执行字符串增长操作所需的内存重分配次数。当 SDS 需要减少字符串时，这部分内存不会立即被回收，会被记录下来，等待后续使用（支持手动释放，有对应的 API）
- <font color="red">二进制安全</font>：C 语言中的字符串以空字符 `\0` 作为字符串结束的标识，这存在一些问题，像一些二进制文件（比如图片、视频、音频）就可能包括空字符，C 字符串无法正确保存。SDS 使用 len 属性判断字符串是否结束，不存在这个问题



## 9-5-购物车信息用 String 还是 Hash 存储更好呢

由于购物车中的商品频繁修改和变动，购物车信息建议使用 Hash 存储



## 9-6-使用 Redis 实现一个排行榜怎么做

Redis 中有一个叫做 `Sorted Set`（有序集合）的数据类型经常被用在各种排行榜的场景，比如直播间送礼物的排行榜、朋友圈的微信步数排行榜、王者荣耀中的段位排行榜、话题热度排行榜等等



## 9-7- Set 的应用场景是什么

`Set` 的常见应用场景如下：

- <font color="red">存放的数据不能重复</font>的场景：网站 UV 统计（数据量巨大的场景还是 `HyperLogLog` 更适合一些）、文章点赞、动态点赞等等。
- 需要获取多个数据源<font color="red">交集、并集和差集</font>的场景：共同好友（交集）、共同粉丝（交集）、共同关注（交集）、好友推荐（差集）、音乐推荐（差集）、订阅号推荐（差集+交集）等等。
- 需要<font color="red">随机获取数据源中的元素</font>的场景：抽奖系统、随机点名等等



- 



# 11- Redis集群方案

## 11-1-主从复制

单节点Redis的并发能力是有上限的，要进一步提高Redis的并发能力，就需要搭建主从集群，实现读写分离

![image-20250521131017399](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250521131017399.png)



### 11-1-1-主从数据同步原理

#### 11-1-1-1-全量同步

![image-20250521132657498](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250521132657498.png)

完整流程描述：

- slave节点请求增量同步
- master节点判断replid，发现不一致，拒绝增量同步
- master将完整内存数据生成RDB，发送RDB到slave
- slave清空本地数据，加载master的RDB
- master将RDB期间的命令记录在repl_baklog，并持续将log中的命令发送给slave
- slave执行接收到的命令，保持与master之间的同步



#### 11-1-1-2-增量同步

![image-20250521161902242](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250521161902242.png)



## 11-2-哨兵模式









## 11-3-分片集群

