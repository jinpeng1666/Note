# 1-聊聊MyBatis的执行流程

![image-20250514162605309](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514162605309.png)

![img](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/4fc077d8bd3f39b4e907cf2cd23c3807.png)

1. 读取MyBatis的核心配置文件mybatis-config.xml，加载运行环境和映射文件
2. 构造会话工厂SqlSessionFactory
3. 会话工厂创建SqlSession对象（包含了执行SQL语句的所有方法）
4. 操作数据库的接口，Executor执行器，负责SQL语句的生成和查询缓存的维护
5. Executor接口的执行方法中有一个MappedStatement类型的参数，封装了映射信息
6. 输入参数映射
7. 输出结果映射



# 2-延迟加载

## 2-1- MyBatis 是否支持延迟加载

Mybatis支持<font color="red">一对一关联对象和一对多关联对象</font>的延迟加载，但默认没有开启，在 MyBatis 配置文件中，可以配置是否启用延迟加载 `lazyLoadingEnabled=true|false`

![image-20250514163229582](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514163229582.png)



## 2-2-延迟加载的原理是什么

![image-20250514164041123](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164041123.png)

1. 使用 CGLIB 创建目标对象的<font color="red">代理对象</font>
2. 当调用<font color="red">目标方法</font>时，进入拦截器invoke方法，发现是null值，执行sql查询
3. 把查询出来的结果，赋值到对象的属性中



# 3- Mybatis 的一级、二级缓存用过吗

![image-20250514164430372](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164430372.png)

- 本地缓存，基于 <font color="red">PerpetualCache</font> (/pəˈpetʃuəl/)，本质是一个HashMap
- 一级缓存：<font color="red">作用域</font>是 session 级别
- 二级缓存：作用域是 namespace 和 mapper 的作用域，不依赖于session



一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当Session进行flush或close之后，该Session中的所有Cache就将清空，默认打开一级缓存

![image-20250514164730611](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164730611.png)

二级缓存是基于namespace和mapper的作用域起作用的，不是依赖于SQL session，默认也是采用 PerpetualCache，HashMap 存储

![image-20250514164834866](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514164834866.png)

> [!NOTE]
>
> 1. 对于缓存数据<font color="red">更新机制</font>，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了新增、修改、删除操作后，默认该作用域下所有 select 中的缓存将被 clear
> 2. 二级缓存需要缓存的数据实现<font color="red">Serializable</font>(/ˈsɪərɪəlaɪzəbl/)接口
> 3. 只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中



# 4-\#{}和${}有什么区别

## 4-1-#{}

- MyBatis 会将 `#{}` 解析为 `?`，进行预编译，然后使用<font color="red"> `PreparedStatement` </font>设置参数，自动进行类型转换和转义处理

- 安全性高：可以有效防止<font color="red"> SQL 注入</font>

- <font color="red">常用场景</font>：绝大多数情况下都应该使用 `#{}`，如插入、更新、查询时传递值



## 4-2-${}

- MyBatis 会将 `${}` 直接替换为传入的值，就像<font color="red">字符串拼接</font>

- 安全性低：不会进行任何转义处理，容易引发 SQL 注入风险

- <font color="red">使用场景有限</font>：适用于传递动态字段名、表名、排序字段等不能用 `#{}` 的位置



# 5- xml 映射文件中有哪些标签

增删改查->动态sql->服用片段->结果映射

| 类别     | 标签名                                                       | 说明                               |
| -------- | ------------------------------------------------------------ | ---------------------------------- |
| SQL 操作 | `<select>`, `<insert>`, `<update>`, `<delete>`               | 基础数据库操作                     |
| 动态 SQL | `<if>`, `<choose>`, `<when>`, `<otherwise>`, `<trim>`, `<where>`, `<set>`, `<foreach>` | 条件判断、循环拼接、去除多余语法等 |
| 复用片段 | `<sql>`, `<include>`                                         | SQL 片段复用                       |
| 结果映射 | `<resultMap>`, `<id>`, `<result>`                            | 自定义结果映射                     |



# 6- Dao 接口的工作原理是什么？Dao 接口里的方法，参数不同时，方法能重载吗？

- 最佳实践中，通常一个 xml 映射文件，都会写一个 Dao 接口<font color="red">与之对应</font>

- Dao 接口就是人们常说的 `Mapper` 接口，接口的全限名，就是映射文件中的 <font color="red">namespace</font> 的值，接口的方法名，就是映射文件中 `MappedStatement` 的 <font color="red">id </font>值，接口方法内的参数，就是传递给 sql 的参数

-  `Mapper` 接口是没有实现类的，当调用接口方法时，<font color="red">接口全限名+方法名</font>拼接字符串作为 key 值，可唯一定位一个 `MappedStatement`

Dao 接口里的方法可以重载，但是 Mybatis 的 xml 里面的 ID 不允许重复

利用 Mybatis 的动态 sql 就可以实现在 Dao 接口中写重载方法



# 7- MyBatis 是如何进行分页的？

- 可以在<font color="red"> sql 内直接书写</font>带有物理分页的参数来完成物理分页功能
- 可以使用分页插件 <font color="red">PageHelper</font> 来完成物理分页，通过<font color="red">拦截</font> SQL 并自动添加分页语法（物理分页），同时提供丰富的分页信息（如总记录数、页码等）