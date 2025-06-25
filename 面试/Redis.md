# 1- Redis 基础

## 1-1-什么是 Redis

==定义（NoSQL、键值对） -> 优点（内存） -> 使用场景（缓存和分布式锁）==

- Redis是一个基于 C 语言开发的开源 <font color="red">NoSQL</font> 数据库，存储的是 KV <font color="red">键值对</font>数据
- Redis 的数据是保存在<font color="red">内存</font>中的（内存数据库，支持持久化），因此读写速度非常快，广泛用于缓存
- 使用场景：
    - 用于<font color="red">缓存</font>
    - 用来做<font color="red">分布式锁</font>
    - 为了满足不同的业务场景，Redis 内置了<font color="red">多种数据类型</font>实现



## 1-2-单线程的 Redis 为什么这么快

==内存 -> 单线程 -> 数据结构 -> 单线程事件循环和非阻塞 I/O 多路复用 -> 通信协议==

- Redis 数据读写是在<font color="red">内存</font>中，数据的读写操作快
- 单线程避免了锁竞争和线程切换带来的<font color="red">性能损耗</font>，处理简单高效
- 高效的<font color="red">数据结构</font>，Redis 内部使用的是优化后的数据结构
- 采用<font color="red">单线程事件循环和非阻塞 I/O 多路复用</font>机制
- Redis <font color="red">通信协议</font>实现简单且解析高效



## 1-3-为什么用 Redis 而不用本地缓存

==风险（数据不一致、数据丢失） -> 容量 -> 使用范围==

- <font color="red">数据一致性问题</font>：本地缓存如果是多实例部署时，存在数据不一致问题
- <font color="red">数据丢失风险</font>：本地缓存在服务器宕机时，数据丢失；Redis 支持持久化，数据不易丢失
- <font color="red">内存限制</font>：本地缓存受限于单台服务器内存；Redis 独立部署，内存空间更大
- <font color="red">共享性</font>：本地缓存仅当前线程可用；Redis 所有应用实例共享



# 2- Redis 应用

## 2-1- Redis 除了做缓存，还能用来做什么

==分布式（分布式锁、分布式 Session） -> 队列（消息队列、延时队列） -> 限流==

- <font color="red">分布式锁</font>：通过 Redis 来做分布式锁是一种比较常见的方式。通常情况下，我们都是基于 Redisson 来实现分布式锁
- <font color="red">分布式 Session</font>：利用 String 或者 Hash 数据类型保存 Session 数据，所有的服务器都可以访问
- <font color="red">消息队列</font>：Redis 自带的 List 数据结构可以作为一个简单的队列使用。Redis 5.0 中增加的 Stream 类型的数据结构更加适合用来做消息队列。它比较类似于 Kafka，有主题和消费组的概念，支持消息持久化以及 ACK 机制
- <font color="red">延时队列</font>：Redisson 内置了延时队列（基于 Sorted Set 实现的）
- <font color="red">限流</font>：一般是通过 Redis + Lua 脚本的方式来实现限流。如果不想自己写 Lua 脚本的话，也可以直接利用 Redisson 中的 `RRateLimiter` 来实现分布式限流，其底层实现就是基于 Lua 代码+令牌桶算法



## 2-2-如何基于 Redis 实现分布式锁

==如何简单实现（获取锁、释放锁） -> 存在问题（续期、重入、死锁）==

- 简单实现：
    - 通过 `SETNX` 命令，添加全局唯一key，实现获取锁
    - 通过 `DEL` 命令，删除对应的 key ，实现释放锁。释放锁的时候，使用 <font color="red">Lua 脚本</font>保证解锁操作的原子性
- 存在问题：
    - 不支持锁的自动<font color="red">续期</font>
    - <font color="red">不可重入</font>，同一线程无法重新获取已持有的锁
    - 锁如果不及时释放（如果没有设置过期时间），会有<font color="red">死锁</font>的风险

![image-20250525160451286](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250525160451286.png)



## 2-3- Redis 可以用来做消息队列吗

- 在 Redis 2.0 之前可以使用 List 实现，通过`RPUSH/LPOP` 或者 `LPUSH/RPOP` 即可实现简易版消息队列
- Redis 2.0 引入了<font color="red">发布订阅 (pub/sub) 功能</font>，解决了 List 实现消息队列没有广播机制的问题



# 3- Redis 数据类型

## 3-1-基本数据类型（5种）

==种类 -> 底层实现依赖==

- Redis 共有 5 种基本数据类型：String（字符串）、List（列表）、Hash（散列）、Set（集合）、Zset（有序集合）
- 底层实现
    - String的底层实现依赖于简单动态字符串（SDS）
    - List的底层实现依赖于LinkedList（双向链表）、ZipList（压缩列表）和QuickList（快速列表）
    - Hash的底层实现依赖于Dict（哈希表/字典）和ZipList（压缩列表）
    - Set的底层实现依赖于Dict（哈希表/字典）和Intset（整数集合）
    - Zset的底层实现依赖于ZipList（压缩列表）和SkipList（跳跃表）
- Redis 3.2 之前，List 底层实现是 LinkedList 或者 ZipList。 Redis 3.2 之后，引入了 LinkedList 和 ZipList 的结合 QuickList，List 的底层实现变为 QuickList。从 Redis 7.0 开始， ZipList 被 ListPack 取代



### 3-1-1- String

==定义 -> 优点（存储内容、时间复杂度、API安全） -> 使用场景（常规数据、计数、分布式锁）==

- Redis 并没有使用 C 的字符串表示，而是自己构建了一种<font color="red">简单动态字符串</font>（Simple Dynamic String，**SDS**）
- 优点：

    - 不仅可以保存文本数据，还可以保存二进制数据

    - <font color="red">获取字符串长度复杂度</font>为 O(1)（C 字符串为 O(N)）
    - Redis 的 SDS API 是安全的，不会造成<font color="red">缓冲区溢出</font>

- 使用场景：
    - 存储常规数据的场景，缓存 Session、Token、图片地址、序列化后的对象(相比较于 Hash 存储更节省内存)
    - 需要计数的场景，用户单位时间的请求数（简单限流可以用到）、页面单位时间的访问数
    - 分布式锁，利用 `SETNX key value` 命令可以实现一个最简易的分布式锁

 

### 3-1-2- List

==定义 -> 使用场景（消息队列、消息流展示）==

- Redis 的 List 的实现为一个<font color="red">双向链表</font>，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销
- 使用场景：
    - `List` 可以用来做<font color="red">消息队列</font>，只是功能过于简单且存在很多缺陷，不建议这样做
    - 消息流展示，最新文章、最新动态



### 3-1-3- Hash

==定义 -> 使用场景（对象数据存储）==

- Redis 中的 Hash 是一个 String 类型的 field-value（键值对） 的映射表，适合用于<font color="red">存储对象</font>
- Hash 类似于 JDK1.8 前的 `HashMap`，内部实现也差不多(数组 + 链表)。不过，Redis 的 Hash 做了更多优化
- 使用场景：对象数据存储，用户信息、商品信息、文章信息和购物车信息等



### 3-1-4- Set

==定义（无序、唯一、交集） -> 使用场景（数据不重复、交集、随机获取）==

- Redis 中的 Set 类型是一种<font color="red">无序集合</font>，集合中的元素没有先后顺序但都<font color="red">唯一</font>，可以基于 Set 轻易实现交集、并集、差集的操作
- 使用场景：
    - 需要存放的数据不能重复的场景，网站 UV 统计（数据量巨大的场景还是 `HyperLogLog`更适合一些）、文章点赞、动态点赞等场景
    - 需要获取多个数据源交集、并集和差集的场景，共同好友(交集)、共同粉丝(交集)、共同关注(交集)、好友推荐（差集）、音乐推荐（差集）、订阅号推荐（差集+交集） 等场景
    - 需要随机获取数据源中的元素的场景，抽奖系统、随机点名等场景



### 3-1-5- ZSet

==定义（权重、有序排序、范围） -> 使用场景（排序、任务队列）==

- Sorted Set 类似于 Set，但和 Set 相比，Sorted Set 增加了一个<font color="red">权重参数</font> `score`，使得集合中的元素能够按 `score` 进行<font color="red">有序排列</font>，还可以通过 `score` 的<font color="red">范围</font>来获取元素的列表
- 使用场景：
    - 根据某个权重进行排序的场景，各种排行榜比如直播间送礼物的排行榜、朋友圈的微信步数排行榜、王者荣耀中的段位排行榜、话题热度排行榜等等
    - 需要存储的数据有优先级或者重要程度的场景，优先级任务队列



## 3-2-特殊数据类型（3种）

### 3-2-1- Bitmap

==定义 -> 使用场景（签到）==

- Bitmap（位图）存储的是连续的二进制数字（0 和 1），通过 Bitmap, 只需要一个 bit 位来表示某个元素对应的值或者状态，key 就是对应元素本身 
- 可以将 Bitmap 看作是一个存储二进制数字（0 和 1）的数组，数组中每个元素的下标叫做 offset（偏移量）
- 使用场景：用户签到情况、活跃用户情况、用户行为统计（比如是否点赞过某个视频）



### 3-2-2- HyperLogLog

==定义 -> 使用场景（统计）==

- HyperLogLog 是一种有名的<font color="red">基数计数概率算法</font> ，基于 LogLog Counting(LLC)优化改进得来，并不是 Redis 特有的，Redis 只是实现了这个算法并提供了一些开箱即用的 API

- Redis 对 HyperLogLog 的存储结构做了优化，采用两种方式计数：
    - **稀疏矩阵**：计数较少的时候，占用空间很小
    - **稠密矩阵**：计数达到某个阈值的时候，占用 12k 的空间
- 基数计数概率算法为了节省内存并不会直接存储元数据，而是通过一定的概率统计方法预估基数值（集合中包含元素的个数）。因此， HyperLogLog 的计数结果并不是一个精确值，存在一定的误差（标准误差为 `0.81%` ）
- 使用场景：数量巨大（百万、千万级别以上）的计数场景，热门网站每日/每周/每月访问 ip 数统计、热门帖子 uv 统计



### 3-2-3- GeoSpatial

==定义 -> 使用场景（附件的人）==

- Geospatial index（地理空间索引，简称 GEO） 主要用于存储地理位置信息，基于 Sorted Set 实现
- 使用场景：需要管理使用地理空间数据的场景，附近的人



## 3-3- String 还是 Hash 存储对象数据更好呢

==对象存储方式（个别字段信息、复杂对象、性能） -> 内存消耗==

- <font color="red">对象存储方式</font>：String 存储的是序列化后的对象数据；Hash 是对对象的每个字段单独存储
    - 如果对象中某些字段需要经常操作（变动或经常查询）对象中的<font color="red">个别字段信息</font>，Hash 就非常适合
    - String 在处理<font color="red">多层嵌套或复杂结构的对象</font>时更方便，因为无需处理每个字段的独立存储和操作
    - <font color="red">性能</font>：String 的操作通常具有 O(1) 的时间复杂度，因为它存储的是整个对象，操作简单直接，整体读写的性能较好。Hash 由于需要处理多个字段的增删改查操作，在字段较多且经常变动的情况下，可能会带来额外的性能开销
- <font color="red">内存消耗</font>：Hash 通常比 String 更节省内存，特别是在字段较多且字段长度较短时。Redis 对小型 Hash 进行优化（如使用 ziplist 存储），进一步降低内存占用



## 3-4- String 的底层实现是什么

==定义 -> 参数 -> 提升==

Redis 是基于 C 语言编写的，但 Redis 的 String 类型的底层实现并不是 C 语言中的字符串（即以空字符 `\0` 结尾的字符数组），而是用 SDS 数据结构存储的

 SDS 的数据结构：

![image-20250615202215110](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250615202215110.png)

- `len`：字符串的长度也就是已经使用的字节数
- `alloc`：总共可用的字符空间大小，alloc-len 就是 SDS 剩余的空间大小
- `buf[]`：实际存储字符串的数组
- `flags`：低三位保存类型标志

SDS 相比于 C 语言中的字符串有如下提升：

- 可以避免<font color="red">缓冲区溢出</font>：C 语言中的字符串被修改（比如拼接）时，一旦没有分配足够长度的内存空间，就会造成缓冲区溢出。SDS 被修改时，会先根据 len 属性检查空间大小是否满足要求，如果不满足，则先扩展至所需大小再进行修改操作
- <font color="red">获取字符串长度的复杂度</font>较低：C 语言中的字符串的长度通常是经过遍历计数来实现的，时间复杂度为 O(n)。SDS 的长度获取直接读取 len 属性即可，时间复杂度为 O(1)
- 减少<font color="red">内存分配次数</font>：为了避免修改（增加/减少）字符串时，每次都需要重新分配内存（C 语言的字符串是这样的），SDS 实现了空间预分配和惰性空间释放两种优化策略。当 SDS 需要增加字符串时，Redis 会为 SDS 分配好内存，并且根据特定的算法分配多余的内存，这样可以减少连续执行字符串增长操作所需的内存重分配次数。当 SDS 需要减少字符串时，这部分内存不会立即被回收，会被记录下来，等待后续使用（支持手动释放，有对应的 API）
- <font color="red">二进制安全</font>：C 语言中的字符串以空字符 `\0` 作为字符串结束的标识，这存在一些问题，像一些二进制文件（比如图片、视频、音频）就可能包括空字符，C 字符串无法正确保存。SDS 使用 len 属性判断字符串是否结束，不存在这个问题



## 3-5-购物车信息用 String 还是 Hash 存储更好呢

由于购物车中的商品<font color="red">频繁修改和变动</font>，购物车信息建议使用 Hash 存储



## 3-6- Set 的应用场景是什么

`Set` 的常见应用场景如下：

- <font color="red">存放的数据不能重复</font>的场景：网站 UV 统计（数据量巨大的场景还是 `HyperLogLog` 更适合一些）、文章点赞、动态点赞等等。
- 需要获取多个数据源<font color="red">交集、并集和差集</font>的场景：共同好友（交集）、共同粉丝（交集）、共同关注（交集）、好友推荐（差集）、音乐推荐（差集）、订阅号推荐（差集+交集）等等。
- 需要<font color="red">随机获取数据源中的元素</font>的场景：抽奖系统、随机点名等等



## 3-7-使用 Set 实现抽奖系统怎么做

使用如下几个命令：

- `SADD key member1 member2 ...`：向指定集合<font color="red">添加</font>一个或多个元素
- `SPOP key count`：<font color="red">随机移除并获取</font>指定集合中一个或多个元素，适合不允许重复中奖的场景
- `SRANDMEMBER key count`：<font color="red">随机获取</font>指定集合中指定数量的元素，适合允许重复中奖的场景



## 3-8-使用 Redis 实现一个排行榜怎么做

==用有序集合 -> 步骤（添加、获取排名）==

- 使用 `Sorted Set`（有序集合）的数据类型实现排行榜的功能
- 实现步骤：
    - 使用`ZADD key score member`，添加有分数的元素到有序集合里
    - 使用`ZREVRANK key member`，进行排名查询



## 3-9- Zset 底层是怎么实现的

- Zset 类型的底层数据结构是由<font color="red">压缩列表或跳表</font>实现的
    - 如果有序集合的<font color="red">元素个数</font>小于 128 个，并且每个<font color="red">元素的值</font>小于 64 字节时，Redis 会使用压缩列表作为 Zset 类型的底层数据结构
    - 如果有序集合的元素不满足上面的条件，Redis 会使用跳表作为 Zset 类型的底层数据结构
- 在 Redis 7.0 中，<font color="red">压缩列表</font>数据结构已经废弃了，交由 <font color="red">listpack</font> 数据结构来实现了



## 3-10-跳表是怎么实现的

- 链表在<font color="red">查找元素</font>的时候，因为需要逐一查找，所以查询效率非常低，时间复杂度是O(N)，于是就出现了跳表
- 跳表是在链表基础上改进过来的，<font color="red">实现了一种多层的有序链表</font>，这样的好处是能<font color="red">快速定位数据</font>，查询的时间复杂度是 O(logN)

![image-20250616170254148](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250616170254148.png)



## 3-11- Redis 的有序集合为什么要用跳表

==和平衡树对比（时间复杂度一样、绝对平衡） -> 和红黑树对比（黑平衡、区间查找） -> 和 B + 树对比（磁盘IO、节点分裂与合并）==

- 平衡树：
    - 平衡树的<font color="red">插入、删除和查询的时间复杂度</font>和跳表一样都是 O(log n)
    - 平衡树的每一次插入或者删除操作都需要保证整颗树左右节点的绝对平衡，<font color="red">通过旋转操作来保持平衡</font>，过程费时；跳表使用概率平衡而不是严格强制的平衡
- 红黑树：
    - 相比于红黑树，跳表的实现更简单，<font color="red">不需要通过旋转和染色（红黑变换）来保证黑平衡</font>
    - 按照<font color="red">区间查找</font>数据这个操作，红黑树的效率没有跳表高
- B + 树：
    - B+ 树适合作为数据库和文件系统中常用的索引结构之一，核心思想是通过近可能少的 IO 定位到多的索引来获得查询数据。 Redis 作为内存数据库，内存访问是纳秒级，无磁盘IO瓶颈，不需要通过B + 树来<font color="red">减少IO</font>
    - 在进行<font color="red">插入时</font>只需通过索引将数据插入到链表中合适的位置再随机维护一定高度的索引即可；不需要像 B+ 树那样插入时发现失衡时还需要对<font color="red">节点分裂与合并</font>



## 3-12-使用 Bitmap 统计日活跃用户怎么做

==Bitmap的定义 -> 步骤（记录活跃用户、统计活跃用户）==

- Bitmap 存储的是连续的二进制数字（0 和 1），通过 Bitmap，只需要一个 bit 位来表示某个元素对应的值或者状态
- 用户id作为offset（偏移量），bit 位来标识用户的活跃状态
- 步骤：
    - 使用`SETBIT key(日期) 用户ID 1`，来记录活跃用户
    - 使用`BITCOUNT key(日期)`，统计该日期下，活跃用户数



## 3-13-使用 HyperLogLog 统计页面 UV 怎么做

==UV和PV的定义 -> 步骤==

- UV（Unique Visitor）：指独立访客数，即一段时间内访问页面的不重复用户数（同一用户多次访问只计 1 次）
- PV（Page View）：页面总访问次数（重复访问也计数）
- 步骤：
    - 使用`PFADD PAGE_1:UV USER1 USER2 ...... USERn`，将访问指定页面的每个用户 ID 添加到 `HyperLogLog` 中
    - 使用`PFCOUNT PAGE_1:UV`，统计指定页面的 UV



# 4-缓存穿透、缓存击穿和缓存雪崩

## 4-1-缓存穿透

![image-20250522103437495](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522103437495.png)

- 什么是缓存穿透：<font color="red"> 大量请求的 key 是不合理的，根本不存在于缓存和数据库中</font>。这就导致这些请求直接到了数据库上，根本没有经过缓存这一层，对数据库造成了巨大的压力，可能直接就被这么多请求弄宕机了
- 解决办法：
    - 缓存无效key：
        - 如果缓存和数据库都查不到某个 key 的数据，就<font color="red">写</font>一个到 Redis 中去并设置过期时间
        - 这种方式可以解决请求的<font color="red"> key 变化不频繁</font>的情况，如果黑客恶意攻击，每次构建不同的请求 key，会导致 Redis 中缓存大量无效的 key
    - 布隆过滤器：
        - 通过查询布隆过滤器快速判断数据是否存在，如果不存在，就不用通过查询数据库来判断数据是否存在，避免请求直接打到数据库
        - 优点：相比于我们平时常用的 List、Map、Set 等数据结构，它<font color="red">占用空间更少并且效率更高</font>
        - 缺点：其返回的结果是概率性的，而不是非常准确的。理论情况下添加到集合中的元素越多，<font color="red">误报</font>的可能性就越大；并且，存放在布隆过滤器的数据<font color="red">不容易删除</font>



## 4-2-缓存击穿

![image-20250522105536452](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522105536452.png)

- 什么是缓存击穿：如果缓存中的某个<font color="red">热点数据</font>过期了，此时大量的请求访问了该热点数据，就无法从缓存中读取，直接访问数据库，数据库很容易就被高并发的请求冲垮
- 解决办法：
    - 互斥锁：保证同一个时刻只有一个业务线程，读取数据库，更新缓存，未能获取互斥锁的线程，等待互斥锁释放
    - 逻辑过期时间：不给热点数据设置过期时间，设置逻辑过期时间，如果过期，则先返回旧数据，然后开启新的线程更新数据库



![image-20250522110214591](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522110214591.png)



## 4-3-缓存雪崩

- 什么是缓存雪崩：当大量缓存数据在<font color="red">同一时间过期</font>（失效）或者 Redis 故障<font color="red">宕机</font>时，大量请求直接打到数据库，造成数据库宕机
- 解决办法：
    - 设置随机<font color="red">失效时间</font>，保证数据不会在同一时间过期
    - 使用 Redis 集群，提高服务的<font color="red">可用性</font>，避免 Redis 宕机



# 5-缓存和数据库的一致性

## 5-1-如何保证缓存和数据库的一致性

- 保证<font color="red">最终一致</font>：由CAP理论可知，强一致性和高可用性不能同时满足，缓存系统适合的场景是非强一致的、保证最终一致性的，属于CAP中的AP
- 对于<font color="red">读数据</font>：采用 Redis 缓存读写策略中的旁路缓存策略，先从缓存中读取数据，读取到数据返回，读取不到数据，就将数据从数据库中加载到缓存
- 对于<font color="red">写数据</font>：选择先更新数据库，后删除缓存的方式



## 5-2-为什么不是更新缓存，而是删除缓存

==缓存的数据不是马上被读取 -> 缓存中的值是进过计算的==

- 如果每次数据发生变更，都更新缓存，但是缓存中的数据不一定会被马上读取，这就会导致缓存中可能存放了很多不常访问的数据，<font color="red">浪费缓存资源，缓存利用率不高</font>

- 缓存中的值可能是经过一系列计算的，频繁更新缓存会导致大量无效的计算，造成<font color="red">机器性能的浪费</font>



## 5-3-为什么先更新数据库，后删除缓存

==两种方法都存在数据不一致的问题 -> 发生的概率 -> 读写分离 + 主从库延迟==

2个线程并发：

![image-20250522160551598](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522160551598.png)

- 这两种方式都有可能产生数据不一致的问题
- 在“先更新数据库，后删除缓存”中，需要出现数据不一致的问题需要满足下面三个条件：
    - <font color="red">缓存</font>刚好已失效
    - <font color="red">读写请求并发</font>
    - 更新数据库 + 删除缓存的时间，要比读数据库 + 写缓存<font color="red">时间短</font>
- 因为写数据库一般会先加锁，所以写数据库，通常是要比读数据库的时间更长的，所以条件 3 发生的概率其实是非常低的，这么来看，这种方案是可以保证数据一致性的
- 在<font color="red">“读写分离 + 主从库延迟”</font>的情况下，第二种方案数据导致不一致



这该如何解决呢？最有效的办法就是，把缓存延迟删掉



## 5-4-聊聊延迟双删

- 延迟双删，如果是写操作，我们先把缓存中的数据删除，然后更新数据库，最后再延时删除缓存中的数据。其中，这个延时多久不太好确定。在延时的过程中，可能会出现脏数据，并不能保证强一致性

- 缓存删除可能会引发缓存击穿的问题
- 在更新数据库 + 删除缓存的方案中，“先删除缓存，再更新数据库”，在“并发”场景下依旧有数据不一致问题，解决方案是“延迟双删”，但这个延迟时间很难评估，所以推荐用“先更新数据库，再删除缓存”的方案



## 5-5-如何保证强一致性

加分布式锁（如使用 Redis 的 Redlock）

使用 Canal 或 Binlog 监听数据库变更，自动删缓存



## 5-6-如何保证最终一致

![image-20250522163355269](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522163355269.png)

![image-20250522163503801](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250522163503801.png)



# 6- Redis 持久化

## 6-1-什么是 RDB 快照持久化

==RDB 快照持久化的说明 -> 作用（主从结构、重启服务器） -> 在配置文件中设置==

- RDB 快照：Redis 可以通过创建<font color="red">快照</font>来获得存储在内存里面的数据在某个时间点上的副本
- 作用：用于Redis <font color="red">主从结构</font>的搭建；<font color="red">重启服务器</font>的时候使用
- 快照持久化是 Redis 默认采用的持久化方式，在 `redis.conf` 配置文件中默认有此下配置：

```
save 900 1    #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发bgsave命令创建快照

save 300 10   #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发bgsave命令创建快照

save 60 10000 #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发bgsave命令创建快照
```



## 6-2- RDB 创建快照时会阻塞主线程吗

Redis 提供了两个命令来生成 RDB 快照文件：

- `save` : 同步保存操作，会阻塞 Redis 主线程
- `bgsave` : fork 出一个子进程，子进程执行，不会阻塞 Redis 主线程，默认选项



## 6-3- RDB 的执行原理

![image-20250523104913297](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250523104913297.png)



## 6-4-什么是 AOF 日志持久化

==AOF 日志持久化的说明 -> 默认开启==

- AOF 日志：每执行一条写操作命令，就把该命令以<font color="red">追加</font>的方式写入到一个文件里，与快照持久化相比，AOF 持久化的<font color="red">实时性</font>更好
- 默认情况下 Redis 没有开启 AOF（append only file）方式的持久化（Redis 6.0 之后已经默认是开启了），可以通过 `appendonly` 参数开启：

```
appendonly yes
```



## 6-5- AOF 工作基本流程是怎样的

AOF 持久化功能的实现可以简单分为 5 步：

1. <font color="red">命令追加（append）</font>：所有的写命令会追加到 AOF 缓冲区中
2. <font color="red">文件写入（write）</font>：将 AOF 缓冲区的数据写入到 AOF 文件中。这一步需要调用`write`函数（系统调用），`write`将数据写入到了系统内核缓冲区之后直接返回了（延迟写）。注意！！！此时并没有同步到磁盘
3. <font color="red">文件同步（fsync）</font>：AOF 缓冲区根据对应的持久化方式（ `fsync` 策略）向硬盘做同步操作。这一步需要调用 `fsync` 函数（系统调用）， `fsync` 针对单个文件操作，对其进行强制硬盘同步，`fsync` 将阻塞直到写入磁盘完成后返回，保证了数据持久化
4. <font color="red">文件重写（rewrite）</font>：随着 AOF 文件越来越大，需要定期对 AOF 文件进行重写，达到压缩的目的
5. <font color="red">重启加载（load）</font>：当 Redis 重启时，可以加载 AOF 文件进行数据恢复



## 6-6- AOF 持久化方式有哪些

这 3 种持久化方式的主要区别在于 <font color="red">`fsync` 同步 AOF 文件的时机（刷盘）</font>

- `appendfsync always`：主线程调用 `write` 执行写操作后，后台线程（ `aof_fsync` 线程）立即会调用 `fsync` 函数同步 AOF 文件（刷盘），`fsync` 完成后线程返回，这样会严重降低 Redis 的性能（`write` + `fsync`）
- `appendfsync everysec`：主线程调用 `write` 执行写操作后立即返回，由后台线程（ `aof_fsync` 线程）每秒钟调用 `fsync` 函数（系统调用）同步一次 AOF 文件（`write`+`fsync`，`fsync`间隔为 1 秒）
- `appendfsync no`：主线程调用 `write` 执行写操作后立即返回，让操作系统决定何时进行同步，Linux 下一般为 30 秒一次（`write`但不`fsync`，`fsync` 的时机由操作系统决定）



## 6-7- AOF 为什么是在执行完命令之后记录日志

==原因（检查开销、阻塞） -> 缺点（修改丢失、阻塞）==

- AOF 记录日志不会对命令进行语法检查，避免额外的<font color="red">检查开销</font>
- 在命令执行完之后再记录，不会<font color="red">阻塞</font>当前的命令执行

缺点：

- 如果刚执行完命令 Redis 就宕机会导致对应的<font color="red">修改丢失</font>
- 可能会<font color="red">阻塞</font>后续其他命令的执行（AOF 记录日志是在 Redis 主线程中进行的）



## 6-8- AOF 重写了解吗

==说明 -> 流程（重写缓冲区、追加、替换） -> 使用（调用命令、配置项）==

- 说明：当 AOF 变得太大时，Redis 将 AOF 重写程序放到<font color="red">子进程</font>里执行，在后台自动重写 AOF 产生一个新的 AOF 文件，这个新的 AOF 文件和原有的 AOF 文件所保存的数据库<font color="red">状态一样</font>，但<font color="red">体积更小</font>
- 流程：
    - AOF 文件重写期间，Redis 还会维护一个<font color="red"> AOF 重写缓冲区</font>，该缓冲区会在子进程创建新 AOF 文件期间，记录服务器执行的所有写命令
    - 当子进程完成创建新 AOF 文件的工作之后，服务器会将重写缓冲区中的所有内容<font color="red">追加</font>到新 AOF 文件的末尾，使得新的 AOF 文件保存的数据库状态与现有的数据库状态一致
    - 最后，服务器用新的 AOF 文件<font color="red">替换</font>旧的 AOF 文件，以此来完成 AOF 文件重写操作
- 如何使用：开启 AOF 重写功能，可以调用 `BGREWRITEAOF` 命令手动执行，也可以设置下面两个配置项，让程序自动决定触发时机：
    - `auto-aof-rewrite-min-size`：如果 AOF 文件大小小于该值，则不会触发 AOF 重写。默认值为 64 MB
    - `auto-aof-rewrite-percentage`：执行 AOF 重写时，当前 AOF 大小（aof_current_size）和上一次重写时 AOF 大小（aof_base_size）的比值。如果当前 AOF 文件大小增加了这个百分比值，将触发 AOF 重写。将此值设置为 0 将禁用自动 AOF 重写。默认值为 100



## 6-9-如何选择 RDB 和 AOF

==文件（大小、兼容性） -> 恢复、生成==

- <font color="red">文件大小</font>：
    - RDB 快照保存的是某个时间节点的数据集，存储内容是经过<font color="red">压缩</font>的二进制数据，文件体积小
    - AOF存储的是每一次写命令，文件体积大，需要重写AOF
- <font color="red">数据恢复速度</font>：
    - 使用 RDB 文件恢复数据，直接<font color="red">解析还原</font>数据即可
    - 使用 AOF 则需要依次<font color="red">执行</font>每个写命令，速度非常慢
- <font color="red">兼容性</font>：
    - RDB 文件是以特定的二进制格式保存的，存在老版本的 Redis 服务不兼容新版本的 RDB 格式的问题
- <font color="red">生成效率</font>：
    - 虽然 BGSAVE 子进程写入 RDB 文件的工作不会阻塞主线程，但会对机器的 CPU 资源和内存资源产生影响，生成 RDB 文件的过程是比较<font color="red">繁重</font>的
    - AOF 则是<font color="red">命令追加</font>，操作轻量



# 7-Redis 的数据过期策略

Redis的过期删除策略是：惰性删除 + 定期删除两种策略配合使用

## 7-1-惰性删除

==定义 -> 优点（CPU） -> 缺点（内存）==

- 定义：key过期之后，不会马上被删除掉。当需要该key时，我们检查其是否过期，如果过期，我们就删掉它；反之，返回该key
- 优点：对<font color="red">CPU友好</font>，只会在使用key时才会进行过期检查，对于很多用不到的key不用浪费时间进行过期检查
- 缺点：对<font color="red">内存不友好</font>，如果一个key已经过期，但是一直没有使用，那么该key就会一直存在内存中



## 7-2-定期删除

==定义 -> 优点（CPU、内存） -> 缺点（时长和频率）==

- 定义：每隔一段时间，就对<font color="red">一些</font>key进行检查，并删除里面过期的key
- 定期清理的两种模式
    - SLOW模式，是定时任务，执行频率默认为10hz，每次不超过25ms，可以通过修改配置文件redis.conf的hz选项来调整这个次数
    -  FAST模式，执行频率不固定，每次事件循环会尝试执行，但两次间隔不低于2ms，每次耗时不超过1ms
- 优点：通过限制删除操作执行的时长和频率来减少删除操作对<font color="red">CPU的影响</font>。同时有效释放过期键的<font color="red">内容占用</font>
- 缺点：<font color="red">难以确定</font>删除执行操作的时长和频率



# 8-Redis 的数据淘汰策略（保证哪些是热点数据）

![image-20250523163617459](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250523163617459.png)

![image-20250523163926511](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250523163926511.png)



# 9- Redis 的缓存读写策略

## 9-1-旁路缓存模式

- Cache Aside Pattern 是我们平时使用比较多的一个缓存读写模式，比较适合<font color="red">读请求比较多</font>的场景

- Cache Aside Pattern 中服务端需要<font color="red">同时维系 db 和 cache</font>，并且是以 db 的结果为准
- 写：
    - 先更新 db
    - 然后直接删除 cache
- 读：
    - 从 cache 中读取数据，读取到就直接返回
    - cache 中读取不到的话，就从 db 中读取数据返回
    - 再把数据放到 cache 中



## 9-2-读写穿透

- Read/Write Through Pattern 中服务端把 cache 视为<font color="red">主要数据存储</font>，从中读取数据并将数据写入其中。cache 服务负责将此数据读取和写入 db，从而减轻了应用程序的职责
- 分布式缓存 Redis 并没有提供 cache 将数据写入 db 的功能
- 写：
    - 先查 cache，cache 中不存在，直接更新 db
    - cache 中存在，则先更新 cache，然后 cache 服务自己更新 db（同步更新 cache 和 db）
- 读：
    - 从 cache 中读取数据，读取到就直接返回 
    - 读取不到的话，先从 db 加载，写入到 cache 后返回响应



## 9-3-异步缓存写入

- Write Behind Pattern 和 Read/Write Through Pattern 很相似，两者都是由 <font color="red">cache 服务</font>来负责 cache 和 db 的读写
- 不同：Read/Write Through 是<font color="red">同步更新</font> cache 和 db，而 Write Behind 则是只更新缓存，不直接更新 db，而是改为异步批量的方式来更新 db







# 10- Redisson

- Redisson 是一个开源的 <font color="red">Java 语言 Redis 客户端</font>
- Redisson 分布式锁
    - Redisson 中的分布式锁自带<font color="red">自动续期机制</font>
    - 原理是，其提供了一个专门用来监控和续期锁的 <font color="red">Watch Dog（ 看门狗）</font>，如果操作共享资源的线程还未执行完成的话，Watch Dog 会不断地延长锁的过期时间，进而保证锁不会因为超时而被释放

![image-20250525161056988](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250525161056988.png)





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
