# 快速入门

### 如何使用

#### 添加依赖

> [!NOTE]
>
> 如何添加依赖，请查询官方文档
>
> [快速开始 | MyBatis-Plus](https://baomidou.com/getting-started/#添加依赖)

#### Mapper层

对应的mapper层继承`BaseMapper`这个接口，并指定泛型（对应实体类类型），即可调用相关的方法，快速地对数据库进行操作，无需编写繁琐地SQL 语句

> [!NOTE]
>
> 相关的方法，请查询官方文档
>
> [持久层接口 | MyBatis-Plus](https://baomidou.com/guides/data-interface/#mapper-interface)

#### 示例

以瑞吉外码的项目为例

**mapper层**

![image-20250305182251073](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305182251073.png)

**service接口层**

![image-20250305182339989](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305182339989.png)

**service实现层**

![image-20250305182431925](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305182431925.png)

### 常见注解

> [!NOTE]
>
> 注解详情，请查询官方文档
>
> [注解配置 | MyBatis-Plus](https://baomidou.com/reference/annotation/)

![image-20250305172723137](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305172723137.png)

![image-20250305183643179](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305183643179.png)

**注意点**

![image-20250305183612610](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305183612610.png)

### 常见配置

> [!NOTE]
>
> 配置详情，请查询官方文档
>
> [使用配置 | MyBatis-Plus](https://baomidou.com/reference/)

![image-20250305184634891](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305184634891.png)

# 持久层接口

`IService`：[持久层接口 | MyBatis-Plus](https://baomidou.com/guides/data-interface/#service-interface)

`BaseMapper`：[持久层接口 | MyBatis-Plus](https://baomidou.com/guides/data-interface/#mapper-interface)

### IService

![](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250307103536800.png)

![](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250307103750607.png)

- 属于 **服务层（Service 层）**
- 通常由 `Service` 接口继承，并由 `Service` 实现类实现

### BaseMapper

- 属于 **数据访问层（DAO 层）**
- 通常由 `Mapper` 接口继承

### 二者联系

`IService` 的实现类（如 `ServiceImpl`）内部会调用 `BaseMapper` 的方法来完成数据库操作

# 条件构造器Wrapper

> [!NOTE]
>
> 条件构造器详情，请查询官方文档
>
> [条件构造器 | MyBatis-Plus](https://baomidou.com/guides/wrapper/)

### 说明

**继承体系**

![image-20250305185631069](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305185631069.png)

**AbstractWrapper**

![image-20250305185841930](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305185841930.png)

这是一个抽象基类，提供了所有 Wrapper 类共有的方法和属性。它定义了条件构造的基本逻辑，包括字段（column）、值（value）、操作符（condition）等

**QueryWrapper**

![image-20250305185910423](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305185910423.png)

专门用于构造查询条件，支持基本的等于、不等于、大于、小于等各种常见操作。它允许你以链式调用的方式添加多个查询条件，并且可以组合使用 `and` 和 `or` 逻辑

**UpdateWrapper**

![image-20250305185944636](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305185944636.png)

用于构造更新条件，可以在更新数据时指定条件。与 QueryWrapper 类似，它也支持链式调用和逻辑组合。使用 UpdateWrapper 可以在不创建实体对象的情况下，直接设置更新字段和条件

**LambdaQueryWrapper**

这是一个基于 Lambda 表达式的查询条件构造器，它通过 Lambda 表达式来引用实体类的属性，从而避免了硬编码字段名。这种方式提高了代码的可读性和可维护性，尤其是在字段名可能发生变化的情况下

**LambdaUpdateWrapper**

类似于 LambdaQueryWrapper，LambdaUpdateWrapper 是基于 Lambda 表达式的更新条件构造器。它允许你使用 Lambda 表达式来指定更新字段和条件，同样避免了硬编码字段名的问题

### 示例

![image-20250305190459654](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305190459654.png)

![image-20250305191120054](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305191120054.png)

**QueryWrapper**

![image-20250305190553789](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305190553789.png)

**UpdateWrapper**

![image-20250305190920925](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305190920925.png)

用`UpdateWrapper`应该也行（懒得改了......)

![image-20250305191220026](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305191220026.png)

**LambdaQueryWrapper**

![image-20250305191633461](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305191633461.png)

# 自定义SQL

半自动，把条件构造器作为`mapper`方法的参数传给自定义的SQL语句

![image-20250305192319001](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305192319001.png)

![image-20250305192546897](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250305192546897.png)

# Chain

> [!NOTE]
>
> Chain详情，请查询官方文档
>
> [持久层接口 | MyBatis-Plus](https://baomidou.com/guides/data-interface/#chain)

Chain 是 Mybatis-Plus 提供的一种链式编程风格，它允许开发者以更加简洁和直观的方式编写数据库操作代码。Chain 分为 `query` 和 `update` 两大类，分别用于查询和更新操作。每类又分为普通链式和 lambda 链式两种风格，其中 lambda 链式提供了类型安全的查询条件构造，但不支持 Kotlin

### 使用步骤

#### query

提供链式查询操作，可以连续调用方法来构建查询条件。

```java
// 链式查询 普通

QueryChainWrapper<T> query();

// 链式查询 lambda 式。注意：不支持 Kotlin

LambdaQueryChainWrapper<T> lambdaQuery();
```

**示例**:

```java
// 普通链式查询示例
query().eq("name", "John").list(); // 查询 name 为 "John" 的所有记录
// lambda 链式查询示例
lambdaQuery().eq(User::getAge, 30).one(); // 查询年龄为 30 的单条记录
```

#### update

提供链式更新操作，可以连续调用方法来构建更新条件。

```java
// 链式更改 普通

UpdateChainWrapper<T> update();

// 链式更改 lambda 式。注意：不支持 Kotlin

LambdaUpdateChainWrapper<T> lambdaUpdate();
```

**示例**:

```java
// 普通链式更新示例
update().set("status", "inactive").eq("name", "John").update(); // 将 name 为 "John" 的记录 status 更新为 "inactive"
// lambda 链式更新示例
User updateUser = new User();
updateUser.setEmail("new.email@example.com");
lambdaUpdate().set(User::getEmail, updateUser.getEmail()).eq(User::getId, 1).update(); // 更新 ID 为 1 的用户的邮箱
```

### 使用提示

- 链式操作通过返回 `QueryChainWrapper` 或 `UpdateChainWrapper` 的实例，允许开发者连续调用方法来构建查询或更新条件。
- lambda 链式操作提供了类型安全的查询条件构造，通过方法引用 `Entity::getId` 等方式，避免了字符串硬编码，提高了代码的可读性和安全性。
- 在使用链式操作时，注意链式方法的调用顺序，通常是先设置条件，然后执行查询或更新操作。
- 链式操作支持多种条件构造方法，如 `eq`、`ne`、`gt`、`lt`、`like` 等，可以根据实际需求选择合适的方法。
- 链式操作返回的结果可以是单条记录、多条记录、总记录数等，具体取决于最后调用的方法。

通过使用 Chain，开发者可以更加高效地编写数据库操作代码，同时保持代码的清晰和可维护性。

# 代码生成器

> [!NOTE]
>
> 代码生成器详情，请查询官方文档
>
> [代码生成器 | MyBatis-Plus](https://baomidou.com/guides/new-code-generator/)
>
> [代码生成器配置 | MyBatis-Plus](https://baomidou.com/reference/new-code-generator-configuration/)

# Db

> [!NOTE]
>
> Db详情，请查询官方文档
>
> [持久层接口 | MyBatis-Plus](https://baomidou.com/guides/data-interface/#db-kit)

Db Kit 是 Mybatis-Plus 提供的一个工具类，它允许开发者通过静态调用的方式执行 CRUD 操作，从而避免了在 Spring 环境下可能出现的 Service 循环注入问题，简化了代码，提升了开发效率。

### 使用示例

```java
// 假设有一个 User 实体类和对应的 BaseMapper

// 根据 id 查询单个实体
User user = Db.getById(1L, User.class);

// 根据 id 查询多个实体
List<User> userList = Db.listByIds(Arrays.asList(1L, 2L, 3L), User.class);

// 根据条件构造器查询
LambdaQueryWrapper<User> queryWrapper = Wrappers.lambdaQuery(User.class)
    .eq(User::getStatus, "active");
List<User> activeUsers = Db.list(queryWrapper);

// 插入新实体
User newUser = new User();
newUser.setUsername("newUser");
newUser.setAge(25);
boolean isInserted = Db.insert(newUser);

// 根据 id 更新实体
User updateUser = new User();
updateUser.setId(1L);
updateUser.setUsername("updatedUser");
boolean isUpdated = Db.updateById(updateUser);

// 根据条件构造器更新
LambdaUpdateWrapper<User> updateWrapper = Wrappers.lambdaUpdate(User.class)
    .set(User::getAge, 30)
    .eq(User::getUsername, "updatedUser");
boolean isUpdatedByWrapper = Db.update(null, updateWrapper);

// 根据 id 删除实体
boolean isDeleted = Db.removeById(1L);

// 根据条件构造器删除
LambdaDeleteWrapper<User> deleteWrapper = Wrappers.lambdaDelete(User.class)
    .eq(User::getStatus, "inactive");
boolean isDeletedByWrapper = Db.remove(deleteWrapper);

// 批量插入
List<User> batchUsers = Arrays.asList(
    new User("user1", 20),
    new User("user2", 22),
    new User("user3", 24)
);
boolean isBatchInserted = Db.saveBatch(batchUsers);

// 批量更新
List<User> batchUpdateUsers = Arrays.asList(
    new User(1L, "user1", 21),
    new User(2L, "user2", 23),
    new User(3L, "user3", 25)
);
boolean isBatchUpdated = Db.updateBatchById(batchUpdateUsers);
```

### 使用提示

- Db Kit 提供了一系列静态方法，可以直接调用进行数据库操作，无需通过 Service 层，简化了代码结构。
- 在使用 Db Kit 时，确保传入的参数正确，特别是当使用 Wrapper 时，需要指定实体类或实体对象。
- 对于批量操作，如批量插入或更新，建议使用 Db Kit 提供的批量方法，以提高效率。
- 避免在循环中频繁调用 Db Kit 的方法，这可能会导致性能问题。

通过使用 Db Kit，开发者可以更加高效地执行数据库操作，同时保持代码的简洁性和可读性。这种工具类尤其适合于简单的 CRUD 操作，可以大大减少重复代码的编写。

# 逻辑删除

> [!NOTE]
>
> 逻辑删除详情，请查询官方文档
>
> [逻辑删除支持 | MyBatis-Plus](https://baomidou.com/guides/logic-delete/)

# 枚举处理器

> [!NOTE]
>
> 枚举处理器详情，请查询官方文档
>
> [自动映射枚举 | MyBatis-Plus](https://baomidou.com/guides/auto-convert-enum/)

# JSON处理器

![image-20250307165052309](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250307165052309.png)

# 分页插件

> [!NOTE]
>
> 分页插件详情，请查询官方文档
>
> [分页插件 | MyBatis-Plus](https://baomidou.com/plugins/pagination/)

**配置**

![image-20250307170037156](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250307170037156.png)

![image-20250307170056969](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250307170056969.png)

![image-20250307170126893](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250307170126893.png)

