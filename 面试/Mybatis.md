# 1-聊聊MyBatis的执行流程

![image-20250514162605309](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514162605309.png)

1. 读取MyBatis配置文件：mybatis-config.xml加载运行环境和映射文件
2. 构造会话工厂SqlSessionFactory
3. 会话工厂创建SqlSession对象（包含了执行SQL语句的所有方法）
4. 操作数据库的接口，Executor执行器，同时负责查询缓存的维护
5. Executor接口的执行方法中有一个MappedStatement类型的参数，封装了映射信息
6. 输入参数映射
7. 输出结果映射



# 2-延迟加载

## 2-1-MyBatis是否支持延迟加载

Mybatis支持延迟记载，但默认没有开启

![image-20250514163229582](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514163229582.png)



## 2-2-延迟加载的原理是什么

![image-20250514164041123](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164041123.png)

1. 使用CGLIB创建目标对象的代理对象
2. 当调用目标方法user.getOrderList()时，进入拦截器invoke方法，发现user.getOrderList()是null值，执行sql查询order列表
3. 把order查询上来，然后调用user.setOrderList(List\<Order> orderList) ，接着完成user.getOrderList()方法的调用



# 3-Mybatis的一级、二级缓存用过吗

![image-20250514164430372](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164430372.png)

- 本地缓存，基于PerpetualCache，本质是一个HashMap
- 一级缓存：作用域是session级别
- 二级缓存：作用域是namespace和mapper的作用域，不依赖于session



一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当Session进行flush或close之后，该Session中的所有Cache就将清空，默认打开一级缓存

![image-20250514164730611](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164730611.png)

二级缓存是基于namespace和mapper的作用域起作用的，不是依赖于SQL session，默认也是采用 PerpetualCache，HashMap 存储

![image-20250514164834866](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164834866.png)

> [!NOTE]
>
> 1. 对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了新增、修改、删除操作后，默认该作用域下所有 select 中的缓存将被 clear
> 2. 二级缓存需要缓存的数据实现Serializable接口
> 3. 只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中



# 4-\#{}和${}有什么区别

| 特性     | `#{}`                             | `${}`                      |
| -------- | --------------------------------- | -------------------------- |
| 处理方式 | 预编译，使用占位符 ?              | 直接拼接字符串             |
| 安全性   | 安全，防止 SQL 注入               | 不安全，容易被 SQL 注入    |
| 适用场景 | 传递字段值，如 WHERE 条件         | 动态拼接表名、列名、排序等 |
| 示例 SQL | `SELECT * FROM user WHERE id = ?` | `SELECT * FROM user_2025`  |



# 5-xml 映射文件中有哪些标签

| 类别     | 标签名                                                       | 说明                               |
| -------- | ------------------------------------------------------------ | ---------------------------------- |
| SQL 操作 | `<select>`, `<insert>`, `<update>`, `<delete>`               | 基础数据库操作                     |
| 动态 SQL | `<if>`, `<choose>`, `<when>`, `<otherwise>`, `<trim>`, `<where>`, `<set>`, `<foreach>` | 条件判断、循环拼接、去除多余语法等 |
| 复用片段 | `<sql>`, `<include>`                                         | SQL 片段复用                       |
| 结果映射 | `<resultMap>`, `<id>`, `<result>`                            | 自定义结果映射                     |