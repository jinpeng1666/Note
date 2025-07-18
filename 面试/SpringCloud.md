# 1-Spring Cloud 5大组件有哪些

![image-20250514170500293](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514170500293.png)

- 注册中心/配置中心 Nacos

- 负载均衡 Ribbon

- 服务调用 Feign

- 服务保护 sentinel

- 服务网关 Gateway



# 2-注册中心

## 2-1-服务注册和发现是什么意思？Spring Cloud 如何实现服务注册发现

### 2-1-1-Eureka

![image-20250514214913953](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514214913953.png)

- 我们当时项目采用的eureka作为注册中心，这个也是spring cloud体系中的一个核心组件

- 服务注册：==服务提供者需要把自己的信息注册到eureka，由eureka来保存这些信息==，比如服务名称、ip、端口等等
- 服务发现：==消费者向eureka拉取服务列表信息==，如果服务提供者有集群，则消费者会利用负载均衡算法，选择一个发起调用
- 服务监控：服务提供者会每隔30秒向eureka发送心跳，报告健康状态，如果eureka服务90秒没接收到心跳，从eureka中剔除



### 2-1-2-Nacos

![image-20250514215439568](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514215439568.png)

- Nacos与eureka的共同点（注册中心）

    - 都支持<font color="red">服务注册和服务拉取</font>

    - 都支持服务提供者<font color="red">心跳方式</font>做健康检测


- Nacos与Eureka的区别（注册中心）
    - Nacos支持服务端<font color="red">主动检测</font>提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式。临时实例心跳不正常会被剔除，非临时实例则不会被剔除
    - Nacos支持服务列表变更的<font color="red">消息推送</font>模式，服务列表更新更及时
    - Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用CP模式；Eureka采用AP方式
- Nacos还支持了<font color="red">配置中心</font>，eureka则只有注册中心，也是选择使用nacos的一个重要原因



# 3-负载均衡

## 3-1-Ribbon负载均衡流程

![image-20250514220022687](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514220022687.png)



## 3-2-Ribbon负载均衡策略有哪些

- RoundRobinRule：简单==轮询==服务列表来选择服务器
- WeightedResponseTimeRule：按照==权重==来选择服务器，响应时间越长，权重越小
- RandomRule：==随机==选择一个可用的服务器
- BestAvailableRule：忽略那些短路的服务器，并选择并发数较低的服务器
- RetryRule：重试机制的选择逻辑
- AvailabilityFilteringRule：可用性敏感策略，先过滤非健康的，再选择连接数较小的实例
- ZoneAvoidanceRule：以==区域可用==的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询



## 3-3-如果想自定义负载均衡策略如何实现

可以自己创建类实现IRule接口 , 然后再通过配置类或者配置文件配置即可 ，通过定义IRule实现可以修改负载均衡规则（选择使用哪一个），有两种方式：

![image-20250514221111805](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514221111805.png)



# 4-服务调用



# 5-服务保护

## 5-1-服务雪崩是什么

![image-20250515095319734](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515095319734.png)

==在微服务架构中，由于服务之间相互依赖，当某个服务出现故障或响应变慢，可能会引发连锁反应，导致整个系统不可用==



## 5-2-如何解决雪崩

### 5-2-1-服务降级

==服务降级是在目标服务不可用或者响应过慢时，主动提供一个“备用方案”或“默认返回值”，从而保证服务继续提供基本功能，不影响用户体验==

服务降级是服务自我保护的一种方式，或者保护下游服务的一种方式，用于确保服务不会受请求突增影响变得不可用，确保服务不会崩溃

![image-20250515095657895](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515095657895.png)

![image-20250515095720691](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515095720691.png)



### 5-2-2-服务熔断

==服务熔断是指在某个服务调用失败率持续升高的情况下，临时中断对该服务的调用请求==

Hystrix 熔断机制，用于监控微服务调用情况， 默认是关闭的，如果需要开启需要在引导类上添加注解：@EnableCircuitBreaker。如果检测到 10 秒内请求的失败率超过 50%，就触发熔断机制。之后每隔 5 秒重新尝试请求微服务，如果微服务不能响应，继续走熔断机制。如果微服务可达，则关闭熔断机制，恢复正常请求

![image-20250515095851326](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515095851326.png)

| 项目         | 服务熔断                               | 服务降级                           |
| ------------ | -------------------------------------- | ---------------------------------- |
| 本质         | **防止服务继续调用故障服务**           | **为故障服务提供备用处理逻辑**     |
| 启动条件     | 服务连续超时、失败率超过阈值           | 可以由熔断触发，也可以手动配置     |
| 表现行为     | 断开服务调用链，进入熔断状态           | 返回默认值、本地缓存或兜底响应     |
| 目的         | **保护系统整体稳定性，防止雪崩**       | **提升用户体验，防止调用端挂掉**   |
| 是否返回结果 | 一般不调用目标服务，直接失败或降级处理 | 一般会有**备用逻辑**，如返回默认值 |



## 5-3-微服务如何监控

我们项目中采用的skywalking进行监控的

- skywalking主要可以监控接口、服务、物理实例的一些状态。特别是在压测的时候可以看到众多服务中哪些服务和接口比较慢，我们可以针对性的分析和优化
- 我们还在skywalking设置了告警规则，特别是在项目上线以后，如果报错，我们分别设置了可以给相关负责人发短信和发邮件，第一时间知道项目的bug情况，第一时间修复



### 5-3-1-为什么需要服务监控

| 🎯 核心作用   | 📖 说明                                                       | 🚀 SkyWalking 支持情况                                      |
| ------------ | ------------------------------------------------------------ | ---------------------------------------------------------- |
| **问题定位** | - 快速发现服务异常、接口出错、请求失败等问题- 精确定位异常链路、慢接口和具体服务节点 | 支持链路追踪（Trace）、错误堆栈分析、慢调用检测            |
| **性能分析** | - 实时获取请求响应时间、吞吐量、错误率、资源使用等性能指标- 帮助优化服务性能和系统瓶颈 | 提供服务级别与接口级别的性能指标监控，支持历史趋势分析     |
| **服务关系** | - 可视化展示服务之间的调用关系和依赖结构- 辅助识别核心服务、瓶颈节点、防止服务雪崩 | 自动生成服务拓扑图，实时展示服务之间的调用链路与健康状态   |
| **服务告警** | - 配置告警规则（如高响应时间、高错误率等）- 实现异常自动告警、邮件/Webhook 通知、预警分析机制 | 支持自定义告警规则与通知渠道（如钉钉、企业微信、Slack 等） |



### 5-3-2-skywalking

一个分布式系统的应用程序性能监控工具（ Application Performance Managment ），提供了完善的链路追踪能力， apache的顶级项目（前华为产品经理吴晟主导开源）



# 6-网关

## 6-1-什么是网关

网关可以为我们提供请求转发、安全认证（身份/权限认证）、流量控制、负载均衡、降级熔断、日志、监控、参数校验、协议转换等功能



## 6-2-讲讲Spring Cloud Gateway 的工作流程

![image-20250515103855386](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515103855386.png)

1. **路由判断**：客户端的请求到达网关后，先经过 Gateway Handler Mapping 处理，这里面会做断言（Predicate）判断，看下符合哪个路由规则，这个路由映射后端的某个服务
2. **请求过滤**：然后请求到达 Gateway Web Handler，这里面有很多过滤器，组成过滤器链（Filter Chain），这些过滤器可以对请求进行拦截和修改，比如添加请求头、参数校验等等，有点像净化污水。然后将请求转发到实际的后端服务。这些过滤器逻辑上可以称作 Pre-Filters，Pre 可以理解为“在...之前”
3. **服务处理**：后端服务会对请求进行处理
4. **响应过滤**：后端处理完结果后，返回给 Gateway 的过滤器再次做处理，逻辑上可以称作 Post-Filters，Post 可以理解为“在...之后”
5. **响应返回**：响应经过过滤处理后，返回给客户端



# 7-限流

![image-20250515104755493](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515104755493.png)

## 7-1-为什么要限流

| 原因类别       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| **保护服务**   | 当请求量远超服务处理能力时，限制进入系统的请求数量，防止核心服务被压垮或崩溃 |
| **隔离问题**   | 某个服务异常或响应慢时，通过限流将其影响范围控制在最小，防止影响整个调用链（避免“雪崩效应”） |
| **保障资源**   | 合理分配系统资源（线程、连接池、数据库等），避免请求占满资源导致其他正常服务无法使用 |
| **提升稳定性** | 在系统高峰期或突发流量（如秒杀、大促）下，通过限流保障核心功能可用，提升用户体验 |



## 7-2-Nginx限流

### 7-2-1-控制速率（突发流量）

![image-20250515104949273](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515104949273.png)

![image-20250515104954090](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515104954090.png)

语法：

```nginx
limit_req_zone key zone rate 
```

- key:定义限流对象，binary_remote_addr就是一种key，基于客户端ip限流
- Zone：定义共享存储区来存储访问信息，10m可以存储16wip地址访问信息
- Rate：最大访问速率，rate=10r/s  表示每秒最多请求10个请求
- burst=20：相当于桶的大小
- Nodelay：快速处理



#### 7-2-1-1-漏桶算法

![image-20250524163624508](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250524163624508.png)

优点：

- 实现<font color="red">简单</font>，易于理解
- 可以<font color="red">控制限流速率</font>，避免网络拥塞和系统过载

缺点：

- 无法应对突然激增的流量，因为只能<font color="red">以固定的速率处理请求</font>，对系统资源利用不够友好
- 桶流入水（发请求）的速率如果一直大于桶流出水（处理请求）的速率的话，那么桶会一直是满的，一部分新的<font color="red">请求会被丢弃</font>，导致服务质量下降



### 7-2-2-控制并发连接数

![image-20250515105542461](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515105542461.png)

- limit_conn perip 20：对应的key是 \$binary_remote_addr，表示限制单个IP同时最多能持有20个连接
- limit_conn perserver 100：对应的key是 $server_name，表示虚拟主机(server) 同时能处理并发连接的总数



## 7-3-网关限流

yml配置文件中，微服务路由设置添加局部过滤器RequestRateLimiter

![image-20250515105735059](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515105735059.png)

- key-resolver ：定义限流对象（ ip 、路径、参数），需代码实现，使用spel表达式获取
- replenishRate ：令牌桶每秒填充平均速率
- urstCapacity ：令牌桶总容量

![image-20250515105818337](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515105818337.png)



### 7-3-1-令牌桶算法

![image-20250524163832260](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250524163832260.png)

优点：

- 可以限制平均速率和应对突然激增的流量
- 可以<font color="red">动态调整生成令牌的速率</font>

缺点：

- 如果<font color="red">令牌产生速率和桶的容量设置不合理</font>，可能会出现问题比如大量的请求被丢弃、系统过载
- 相比于其他限流算法，实现和理解起来更<font color="red">复杂</font>一些



# 8-分布式系统

## 8-1-解释一下CAP和BASE

### 8-1-1-CAP定理

分布式系统有三个指标：

- Consistency（一致性）：用户访问分布式系统中的任意节点，得到的数据必须一致
- Availability（可用性）：用户访问集群中的任意健康节点，必须能得到响应，而不是超时或拒绝
- Partition tolerance （分区容错性）
    - Partition（分区）：因为网络故障或其它原因导致分布式系统中的部分节点与其它节点失去连接，形成独立分区
    - Tolerance（容错）：在集群出现分区时，整个系统也要持续对外提供服务

分布式系统无法同时满足这三个指标

![image-20250515151638939](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515151638939.png)

**结论**

- 分布式系统节点之间肯定是需要网络连接的，分区（P）是必然存在的
- 如果保证访问的高可用性（A）,可以持续对外提供服务，但不能保证数据的强一致性-->  AP
- 如果保证访问的数据强一致性（C）,就要放弃高可用性   --> CP



### 8-1-2-BASE理论

BASE理论是对CAP的一种解决思路，包含三个思想：

- Basically Available （<font color="red">基本可用</font>）：分布式系统在出现故障时，允许损失部分可用性，即保证核心可用
- Soft State（<font color="red">软状态</font>）：在一定时间内，允许出现中间状态，比如临时的不一致状态
- Eventually Consistent（<font color="red">最终一致性</font>）：虽然无法保证强一致性，但是在软状态结束后，最终达到数据一致



## 8-2-Seata框架

Seata事务管理中有三个重要的角色：

- TC (Transaction Coordinator) - 事务协调者：<font color="red">维护</font>全局和分支事务的状态，<font color="red">协调</font>全局事务提交或回滚
- TM (Transaction Manager) - 事务管理器：定义<font color="red">全局事务</font>的范围、开始全局事务、提交或回滚全局事务
- RM (Resource Manager) - 资源管理器：管理<font color="red">分支事务</font>处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚

![image-20250515153450310](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515153450310.png)



### 8-2-1-XA模式

seata的XA模式，CP，需要互相等待各个分支事务提交，可以保证强一致性，性能差

![image-20250515153850858](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515153850858.png)

1. RM一阶段的工作：
    - 注册分支事务到TC
    - 执行分支业务sql但不提交
    - 报告执行状态到TC
2. TC二阶段的工作：
	- TC检测各分支事务执行状态
       - 如果都成功，通知所有RM提交事务
       - 如果有失败，通知所有RM回滚事务
3. RM二阶段的工作：
	- 接收TC指令，提交或回滚事务



### 8-2-2-AT模式

seata的AT模式，AP，底层使用undo log 实现，性能好

AT模式同样是分阶段提交的事务模型，不过缺弥补了XA模型中资源锁定周期过长的缺陷

![image-20250515154223710](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515154223710.png)

1. 阶段一RM的工作：
    - 注册分支事务
    - 记录undo-log（数据快照）
    - 执行业务sql并提交
    - 报告事务状态

2. 阶段二提交时RM的工作：
    - 删除undo-log即可

3. 阶段二回滚时RM的工作：
    - 根据undo-log恢复数据到更新前



### 8-2-3-TCC模式

![image-20250515154646231](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515154646231.png)

- Try：资源的检测和预留
- Confirm：完成资源操作业务；要求 Try 成功 Confirm 一定要能成功
- Cancel：预留资源释放，可以理解为try的反向操作



## 8-3-分布式服务的接口幂等性如何设计

幂等: 多次调用方法或者接口不会改变业务状态，可以保证重复调用的结果和单次调用的结果一致



### 8-3-1-接口幂等

基于RESTful API的角度对部分常见类型请求的幂等性特点进行分析

| **请求方式** | **说明**                                                     |
| ------------ | ------------------------------------------------------------ |
| GET          | 查询操作，天然幂等                                           |
| POST         | 新增操作，请求一次与请求多次造成的结果不同，不是幂等的       |
| PUT          | 更新操作，如果是以绝对值更新，则是幂等的。如果是通过增量的方式更新，则不是幂等的 |
| DELETE       | 删除操作，根据唯一值删除，是幂等的                           |



### 8-3-2-token+redis

![image-20250515155717414](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250515155717414.png)



## 8-4-你们项目中使用了什么分布式任务调度

xxl-job解决的问题：

- 解决集群任务的重复执行问题
- cron表达式定义灵活
- 定时任务失败了，重试和统计
- 任务量大，分片执行



### 8-4-1-xxl-job路由策略有哪些

- FIRST（第一个）：固定选择第一个机器
- LAST（最后一个）：固定选择最后一个机器
- ROUND（轮询）
- RANDOM（随机）：随机选择在线的机器
- CONSISTENT_HASH（一致性HASH）：每个任务按照Hash算法固定选择某一台机器，且所有任务均匀散列在不同机器上
- LEAST_FREQUENTLY_USED（最不经常使用）：使用频率最低的机器优先被选举
- LEAST_RECENTLY_USED（最近最久未使用）：最久未使用的机器优先被选举
- FAILOVER（故障转移）：按照顺序依次进行心跳检测，第一个心跳检测成功的机器选定为目标执行器并发起调度
- BUSYOVER（忙碌转移）：按照顺序依次进行空闲检测，第一个空闲检测成功的机器选定为目标执行器并发起调度
- SHARDING_BROADCAST(分片广播)：广播触发对应集群中所有机器执行一次任务，同时系统自动传递分片参数；可根据分片参数开发分片任务
