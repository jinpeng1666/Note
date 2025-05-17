# 1-Spring框架中的单例bean是线程安全的吗

Spring 框架中的 Bean 是否线程安全，取决于其作用域和状态

Spring框架中的bean默认是单例的

![image-20250512110232104](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512110232104.png)

- singleton : bean在每个Spring IOC容器中只有一个实例

- prototype：一个bean的定义可以有多个实例



![image-20250512110612664](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512110612664.png)

prototype 作用域下，每次获取都会创建一个新的 bean 实例，不存在资源竞争问题，所以不存在线程安全问题。singleton 作用域下，IoC 容器中只有唯一的 bean 实例，可能会存在资源竞争问题（取决于 Bean 是否有状态）。如果这个 bean 是有状态的话，那就存在线程安全问题（有状态 Bean 是指包含可变的成员变量的对象）



# 2-什么是AOP

AOP称为面向切面编程，用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被命名为“切面”（Aspect），减少系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。

常见的AOP使用场景：

- 记录操作日志
- 缓存处理
- Spring中内置的事务处理



# 3-Spring管理事务的方式有几种

Spring支持编程式事务管理和声明式事务管理两种方式

- 编程式事务控制：需使用TransactionTemplate来进行实现，对业务代码有侵入性，项目中很少使用
- 声明式事务管理：声明式事务管理建立在AOP之上的。其本质是通过AOP功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中，也就是在目标方法开始之前加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务



# 4-Spring中事务失效的场景有哪些

- 异常捕获处理，自己处理了异常，没有抛出，解决：手动抛出

- 抛出检查异常，配置rollbackFor属性为Exception

- 非public方法导致的事务失效，改为public

![image-20250512160915477](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512160915477.png)

![image-20250512161016844](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512161016844.png)

![image-20250512161321756](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512161321756.png)



# 5-谈谈对于 Spring IoC 的了解

**IoC（Inversion of Control:控制反转）**是一种设计思想，而不是一个具体的技术实现。IoC的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理

- **控制**：指的是对象创建（实例化、管理）的权力
- **反转**：控制权交给外部环境（Spring 框架、IoC 容器）

![image-20250512162254500](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512162254500.png)

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的

在实际项目中一个 Service 类可能依赖了很多其他的类，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value），Map 中存放的是各种对象

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来



# 6-什么是Spring Bean

简单来说，Bean 代指的就是那些被 IoC 容器所管理的对象

我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类



# 7-将一个类声明为 Bean 的注解有哪些

- `@Component`：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注

- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作

- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层

- `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 `Service` 层返回数据给前端页面



## 7-1@Component 和 @Bean 的区别是什么

- `@Component` 注解作用于类，而`@Bean`注解作用于方法

- `@Component`通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中（我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean,`@Bean`告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我

- `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 `Spring`容器时，则只能通过 `@Bean`来实现



# 8-注入 Bean 的注解有哪些

| 注解       | 包                               |
| ---------- | -------------------------------- |
| @Autowired | org.springframework.bean.factory |
| @Resource  | javax.annotation                 |
| @Inject    | javax.inject                     |

`@Autowired` 和`@Resource`使用的比较多一些



## 8-1- @Autowired 和 @Resource 的区别是什么

- `@Autowired` 是 Spring 提供的注解，`@Resource` 是 JDK 提供的注解

- `Autowired` 默认的注入方式为`byType`（根据类型进行匹配），`@Resource`默认注入方式为 `byName`（根据名称进行匹配）

- 当一个接口存在多个实现类的情况下，`@Autowired` 和`@Resource`都需要通过名称才能正确匹配到对应的 Bean。`Autowired` 可以通过 `@Qualifier` 注解来显式指定名称，`@Resource`可以通过 `name` 属性来显式指定名称

- `@Autowired` 支持在构造函数、方法、字段和参数上使用。`@Resource` 主要用于字段和方法上的注入，不支持在构造函数或参数上使用



# 9-注入 Bean 的方式有哪些

依赖注入 (Dependency Injection, DI) 的常见方式：

- 构造函数注入：通过类的构造函数来注入依赖项

- Setter 注入：通过类的 Setter 方法来注入依赖项

- Field（字段） 注入：直接在类的字段上使用注解（如 `@Autowired` 或 `@Resource`）来注入依赖项



# 10-Bean 的生命周期了解么?

![image-20250512220002028](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512220002028.png)

![image-20250512220530678](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512220530678.png)



**说明**

**创建 Bean 的实例**：Bean 容器首先会找到配置文件中的 Bean 定义，然后使用 Java 反射 API 来创建 Bean 的实例

**Bean 属性赋值/填充**：为 Bean 设置相关属性和依赖，例如`@Autowired` 等注解注入的对象、`@Value` 注入的值、`setter`方法或构造函数注入依赖和值、`@Resource`注入的各种资源

**Bean 初始化**： 

- 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()`方法，传入 Bean 的名字
- 如果 Bean 实现了 `BeanClassLoaderAware` 接口，调用 `setBeanClassLoader()`方法，传入 `ClassLoader`对象的实例
- 如果 Bean 实现了 `BeanFactoryAware` 接口，调用 `setBeanFactory()`方法，传入 `BeanFactory`对象的实例
- 与上面的类似，如果实现了其他 `*.Aware`接口，就调用相应的方法
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessBeforeInitialization()` 方法
- 如果 Bean 实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法
- 如果 Bean 在配置文件中的定义包含 `init-method` 属性，执行指定的方法
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessAfterInitialization()` 方法

**销毁 Bean**：销毁并不是说要立马把 Bean 给销毁掉，而是把 Bean 的销毁方法先记录下来，将来需要销毁 Bean 或者销毁容器的时候，就调用这些方法去释放 Bean 所持有的资源。

- 如果 Bean 实现了 `DisposableBean` 接口，执行 `destroy()` 方法
- 如果 Bean 在配置文件中的定义包含 `destroy-method` 属性，执行指定的 Bean 销毁方法。或者，也可以直接通过`@PreDestroy` 注解标记 Bean 销毁之前执行的方法



整体上可以简单分为四步：实例化 —> 属性赋值 —> 初始化 —> 销毁。

初始化这一步涉及到的步骤比较多，包含 `Aware` 接口的依赖注入、`BeanPostProcessor` 在初始化前后的处理以及 `InitializingBean` 和 `init-method` 的初始化操作。

销毁这一步会注册相关销毁回调接口，最后通过`DisposableBean` 和 `destory-method` 进行销毁

![image-20250512220827968](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250512220827968.png)



# 11-循环依赖

## 11-1- Spring 循环依赖了解吗

循环依赖是指 Bean 对象循环引用，是两个或多个 Bean 之间相互持有对方的引用

![image-20250513101131309](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513101131309.png)



## 11-2-如何解决循环依赖

三级缓存用于解决初始化的循环依赖

![image-20250513101629771](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513101629771.png)

简单来说，Spring 的三级缓存包括：

1. **一级缓存（singletonObjects）**：存放最终形态的 Bean（已经实例化、属性填充、初始化），单例池，为“Spring 的单例属性”⽽⽣。一般情况我们获取 Bean 都是从这里获取的，但是并不是所有的 Bean 都在单例池里面，例如原型 Bean 就不在里面
2. **二级缓存（earlySingletonObjects）**：存放过渡 Bean（半成品，尚未属性填充），也就是三级缓存中`ObjectFactory`产生的对象，与三级缓存配合使用的，可以防止 AOP 的情况下，每次调用`ObjectFactory#getObject()`都是会产生新的代理对象的
3. **三级缓存（singletonFactories）**：存放`ObjectFactory`，`ObjectFactory`的`getObject()`方法（最终调用的是`getEarlyBeanReference()`方法）可以生成原始 Bean 对象或者代理对象（如果 Bean 被 AOP 切面代理）。三级缓存只会对单例 Bean 生效



![image-20250513101646084](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513101646084.png)

![image-20250513101702996](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513101702996.png)

![image-20250513102044108](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513102044108.png)

 Spring 创建 Bean 的流程：

1. 先去 **一级缓存 `singletonObjects`** 中获取，存在就返回
2. 如果不存在或者对象正在创建中，于是去 **二级缓存 `earlySingletonObjects`** 中获取
3. 如果还没有获取到，就去 **三级缓存 `singletonFactories`** 中获取，通过执行 `ObjectFacotry` 的 `getObject()` 就可以获取该对象，获取成功之后，从三级缓存移除，并将该对象加入到二级缓存中



## 11-3-构造方法出现了循环依赖怎么解决

`@Lazy` 用来标识类是否需要懒加载/延迟加载，可以作用在类上、方法上、构造器上、方法参数上、成员变量中

![image-20250513102958173](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513102958173.png)



# 12-Spring MVC

MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码

![image-20250513103940443](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513103940443.png)



## 12-1-Spring MVC 的核心组件有哪些

- **`DispatcherServlet`**：**核心的中央处理器**，负责接收请求、分发，并给予客户端响应

- **`HandlerMapping`**：**处理器映射器**，根据 URL 去匹配查找能处理的 `Handler` ，并会将请求涉及到的拦截器和 `Handler` 一起封装

- **`HandlerAdapter`**：**处理器适配器**，根据 `HandlerMapping` 找到的 `Handler` ，适配执行对应的 `Handler`

- **`Handler`**：**请求处理器**，处理实际请求的处理器

- **`ViewResolver`**：**视图解析器**，根据 `Handler` 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 `DispatcherServlet` 响应客户端



## 12-2-SpringMVC 工作原理了解吗

![image-20250513203614573](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513203614573.png)

1. 用户发送出请求到前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping（处理器映射器）
3. HandlerMapping找到具体的处理器，生成处理器对象及处理器拦截器(如果有)，再一起返回给DispatcherServlet
4. DispatcherServlet调用HandlerAdapter（处理器适配器）
5. HandlerAdapter经过适配调用具体的处理器（Handler/Controller）
6. Controller执行完成返回ModelAndView对象
7. HandlerAdapter将Controller执行结果ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover（视图解析器）
9. ViewReslover解析后返回具体View（视图）
10. DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）
11. DispatcherServlet响应用户

![image-20250513203624929](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513203624929.png)

1. 用户发送出请求到前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping（处理器映射器）
3. HandlerMapping找到具体的处理器，生成处理器对象及处理器拦截器(如果有)，再一起返回给DispatcherServlet
4. DispatcherServlet调用HandlerAdapter（处理器适配器）
5. HandlerAdapter经过适配调用具体的处理器（Handler/Controller）
6. 方法上添加了@ResponseBody
7. 通过HttpMessageConverter来返回结果转换为JSON并响应

![image-20250513104457665](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250513104457665.png)

**流程说明**

1. 客户端（浏览器）发送请求， `DispatcherServlet`拦截请求
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping` 。`HandlerMapping` 根据 URL 去匹配查找能处理的 `Handler`（也就是我们平常说的 `Controller` 控制器） ，并会将请求涉及到的拦截器和 `Handler` 一起封装
3. `DispatcherServlet` 调用 `HandlerAdapter`适配器执行 `Handler` 
4. `Handler` 完成对用户请求的处理后，会返回一个 `ModelAndView` 对象给`DispatcherServlet`，`ModelAndView` 顾名思义，包含了数据模型以及相应的视图的信息。`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`
5. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`
6. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）
7. 把 `View` 返回给请求者（浏览器）

上述流程是传统开发模式（JSP，Thymeleaf 等）的工作原理。然而现在主流的开发方式是前后端分离，这种情况下 Spring MVC 的 `View` 概念发生了一些变化。由于 `View` 通常由前端框架（Vue, React 等）来处理，后端不再负责渲染页面，而是只负责提供数据，因此：

- 前后端分离时，后端通常不再返回具体的视图，而是返回**纯数据**（通常是 JSON 格式），由前端负责渲染和展示。
- `View` 的部分在前后端分离的场景下往往不需要设置，Spring MVC 的控制器方法只需要返回数据，不再返回 `ModelAndView`，而是直接返回数据，Spring 会自动将其转换为 JSON 格式。相应的，`ViewResolver` 也将不再被使用。

怎么做到呢？

- 使用 `@RestController` 注解代替传统的 `@Controller` 注解，这样所有方法默认会返回 JSON 格式的数据，而不是试图解析视图。
- 如果你使用的是 `@Controller`，可以结合 `@ResponseBody` 注解来返回 JSON。



# 13-Springboot自动配置原理

![image-20250514132512169](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514132512169.png)

![image-20250514132629996](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250514132629996.png)

1. 在Spring Boot项目中的引导类上有一个注解@SpringBootApplication，这个注解是对三个注解进行了封装，分别是：

    - @SpringBootConfiguration

    - @EnableAutoConfiguration

    - @ComponentScan

2. 其中@EnableAutoConfiguration是实现自动化配置的核心注解。 该注解通过@Import注解导入对应的配置选择器。内部就是读取了该项目和该项目引用的Jar包的的classpath路径下META-INF/spring.factories文件中的所配置的类的全类名。 在这些配置类中所定义的Bean会根据条件注解所指定的条件来决定是否需要将其导入到Spring容器中

3. 条件判断会有像@ConditionalOnClass这样的注解，判断是否有对应的class文件，如果有则加载该类，把这个配置类的所有的Bean放入spring容器中使用。



# 14-Spring常见注解

## 14-1-Spring常见注解

| **注解**                                       | **说明**                                                     |
| ---------------------------------------------- | ------------------------------------------------------------ |
| @Component、@Controller、@Service、@Repository | 使用在类上用于实例化Bean                                     |
| @Autowired                                     | 使用在字段上用于根据类型依赖注入                             |
| @Qualifier                                     | 结合@Autowired一起使用用于根据名称进行依赖注入               |
| @Scope                                         | 标注Bean的作用范围                                           |
| @Configuration                                 | 指定当前类是一个 Spring 配置类，当创建容器时会从该类上加载注解 |
| @ComponentScan                                 | 用于指定 Spring  在初始化容器时要扫描的包                    |
| @Bean                                          | 使用在方法上，标注将该方法的返回值存储到Spring容器中         |
| @Import                                        | 使用@Import导入的类会被Spring加载到IOC容器中                 |
| @Aspect、@Before、@After、@Around、@Pointcut   | 用于切面编程（AOP）                                          |



## 14-2-SpringMVC常见的注解

| **注解**        | **说明**                                                     |
| --------------- | ------------------------------------------------------------ |
| @RequestMapping | 用于映射请求路径，可以定义在类上和方法上。用于类上，则表示类中的所有的方法都是以该地址作为父路径 |
| @RequestBody    | 注解实现接收http请求的json数据，将json转换为java对象         |
| @RequestParam   | 指定请求参数的名称                                           |
| @PathViriable   | 从请求路径下中获取请求参数(/user/{id})，传递给方法的形式参数 |
| @ResponseBody   | 注解实现将controller方法返回对象转化为json对象响应给客户端   |
| @RequestHeader  | 获取指定的请求头数据                                         |
| @RestController | @Controller + @ResponseBody                                  |



## 14-3-Springboot常见注解

| **注解**                 | **说明**                                       |
| ------------------------ | ---------------------------------------------- |
| @SpringBootConfiguration | 组合了- @Configuration注解，实现配置文件的功能 |
| @EnableAutoConfiguration | 打开自动配置的功能，也可以关闭某个自动配置的选 |
| @ComponentScan           | Spring组件扫描                                 |
