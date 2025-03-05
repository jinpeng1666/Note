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

![image-20250305182251073](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305182251073.png)

**service接口层**

![image-20250305182339989](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305182339989.png)

**service实现层**

![image-20250305182431925](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305182431925.png)

### 常见注解

> [!NOTE]
>
> 注解详情，请查询官方文档
>
> [注解配置 | MyBatis-Plus](https://baomidou.com/reference/annotation/)

![image-20250305172723137](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305172723137.png)

![image-20250305183643179](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305183643179.png)

**注意点**

![image-20250305183612610](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305183612610.png)

### 常见配置

> [!NOTE]
>
> 配置详情，请查询官方文档
>
> [使用配置 | MyBatis-Plus](https://baomidou.com/reference/)

![image-20250305184634891](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305184634891.png)

# 持久层接口

对于两个不同的概念，产生疑问

`IService`：[持久层接口 | MyBatis-Plus](https://baomidou.com/guides/data-interface/#service-interface)

`BaseMapper`：[持久层接口 | MyBatis-Plus](https://baomidou.com/guides/data-interface/#mapper-interface)

### IService

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

![image-20250305185631069](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305185631069.png)

**AbstractWrapper**

![image-20250305185841930](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305185841930.png)

这是一个抽象基类，提供了所有 Wrapper 类共有的方法和属性。它定义了条件构造的基本逻辑，包括字段（column）、值（value）、操作符（condition）等

**QueryWrapper**

![image-20250305185910423](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305185910423.png)

专门用于构造查询条件，支持基本的等于、不等于、大于、小于等各种常见操作。它允许你以链式调用的方式添加多个查询条件，并且可以组合使用 `and` 和 `or` 逻辑

**UpdateWrapper**

![image-20250305185944636](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305185944636.png)

用于构造更新条件，可以在更新数据时指定条件。与 QueryWrapper 类似，它也支持链式调用和逻辑组合。使用 UpdateWrapper 可以在不创建实体对象的情况下，直接设置更新字段和条件

**LambdaQueryWrapper**

这是一个基于 Lambda 表达式的查询条件构造器，它通过 Lambda 表达式来引用实体类的属性，从而避免了硬编码字段名。这种方式提高了代码的可读性和可维护性，尤其是在字段名可能发生变化的情况下

**LambdaUpdateWrapper**

类似于 LambdaQueryWrapper，LambdaUpdateWrapper 是基于 Lambda 表达式的更新条件构造器。它允许你使用 Lambda 表达式来指定更新字段和条件，同样避免了硬编码字段名的问题

### 示例

![image-20250305190459654](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305190459654.png)

![image-20250305191120054](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305191120054.png)

**QueryWrapper**

![image-20250305190553789](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305190553789.png)

**UpdateWrapper**

![image-20250305190920925](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305190920925.png)

用`UpdateWrapper`应该也行（懒得改了......)

![image-20250305191220026](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305191220026.png)

**LambdaQueryWrapper**

![image-20250305191633461](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305191633461.png)

# 自定义SQL

半自动，把条件构造器作为`mapper`方法的参数传给自定义的SQL语句

![image-20250305192319001](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305192319001.png)

![image-20250305192546897](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250305192546897.png)



















