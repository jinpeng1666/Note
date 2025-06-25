# 1-基础知识

## 1-1-基础概念与常识

### 1-1-1- Java 语言有哪些特点

- <font color="red">面向对象</font>（封装，继承，多态）
- <font color="red">平台无关性</font>，Java编译器将源代码编译成字节码文件，该字节码可以在任何安装了Java虚拟机（JVM）的系统上运行
- 支持<font color="red">多线程</font>（ C++ 语言没有内置的多线程机制，因此必须调用操作系统的多线程功能来进行多线程程序设计，而 Java 语言却提供了多线程支持）
- <font color="red">内存管理</font>：Java有自己的垃圾回收机制，自动管理内存和回收不再使用的对象。开发者不需要手动管理内存，从而减少内存泄漏和其他内存相关的问题



### 1-1-2- Java SE 和 Java EE

Java SE 是 Java 的<font color="red">基础</font>版本，Java EE 是 Java 的<font color="red">高级</font>版本。Java SE 更适合开发桌面应用程序或简单的服务器应用程序，Java EE 更适合开发复杂的企业级应用程序或 Web 应用程序



### 1-1-3- JVM、JDK 和 JRE 三者之间的关系

==JVM（是什么、负责什么、提供什么功能） -> JRE（是什么、包含什么） -> JDK（是什么、包含什么、提供什么功能）== 

- JVM 是 <font color="red">Java 虚拟机</font>，是 Java 程序运行的环境，它负责将 Java 编译器生成的字节码解释或编译成机器码，并执行程序。提供内存管理、垃圾回收、安全性等功能，使得 Java 程序具备跨平台性
- JRE 是 <font color="red">Java 运行时环境</font>，是 Java 程序运行所需的最小环境。它包含了 JVM 和一组 Java 类库，用于支持 Java 程序的执行
- JDK 是 <font color="red">Java 开发工具包</font>，是开发 Java 程序所需的工具集合。它包含了 JVM、编译器（javac）和调试器（jdb）等开发工具，以及一系列的类库。JDK 提供了开发、编译、调试和运行 Java 程序所需的全部工具和环境



### 1-1-4-什么是字节码

- 定义：由 javac <font color="red">编译生成</font>的文件（扩展名为 `.class` 的文件），由 JVM  解析或编译成机器码，并执行程序
- 它不面向任何特定的处理器，只面向虚拟机
- Java 语言通过字节码的方式，在一定程度上解决了传统解释型语言<font color="red">执行效率</font>低的问题，同时又保留了解释型语言<font color="red">可移植</font>的特点



### 1-1-5- Java 和 C++

==指针 -> GC -> 类（继承和重载）==

- 相同点：Java 和 C++ 都是面向对象的语言，都支持封装、继承和多态
- 不同点：
    - Java 不提供<font color="red">指针</font>来直接访问内存，程序内存更加安全
    - Java 有<font color="red">自动内存管理垃圾回收机制</font>(GC)，不需要程序员手动释放无用内存
    - Java 的类是<font color="red">单继承</font>的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承
    - C ++同时支持方法<font color="red">重载</font>和操作符重载，但是 Java 只支持方法重载



### 1-1-6- Python 和 Java 的区别是什么

- Java 是一种<font color="red">编译型语言</font>，Java 编译器将源代码编译为字节码，而字节码则由 Java 虚拟机执行
- python 是一种<font color="red">解释型语言</font>，翻译时会在执行程序的同时进行翻译



### 1-1-7-编译型语言和解释型语言的区别

==程序执行时机、代码运行速度、跨平台性==

- 编译型语言：在程序执行之前，整个源代码会被编译成机器码或者字节码，生成可执行文件。执行时直接运行编译后的代码，<font color="red">速度快</font>，但<font color="red">跨平台性较差</font>
- 解释型语言：在程序执行时，逐行解释执行源代码，不生成独立的可执行文件。通常由解释器<font color="red">动态解释并执行</font>代码，<font color="red">跨平台性好</font>，但<font color="red">执行速度相对较慢</font>



## 1-2-基本语法

### 1-2-1-注解有哪几种形式

- 单行注释：通常用于解释方法内某单行代码的作用
- 多行注释：通常用于解释一段代码的作用
- 文档注释：通常用于生成 Java 开发文档



### 1-2-2-自增自减运算符

- **前缀形式**（例如 `++a` 或 `--a`）：先自增/自减变量的值，然后再使用该变量
- **后缀形式**（例如 `a++` 或 `a--`）：先使用变量的当前值，然后再自增/自减变量的值



### 1-2-3-++操作是一个原子性操作吗

`++` 操作不是原子操作

`i++`（或 `++i`）看起来是一个操作，但实际上由<font color="red">3 个步骤</font>组成：

1. 读取 `i` 的当前值到<font color="red">寄存器</font>
2. 增加该值（`+1`）
3. 写回新值到<font color="red">内存</font>

如果多个线程同时执行 `i++`，可能会发生<font color="red">数据覆盖</font>，导致最终结果不符合预期



### 1-2-4-移位运算符

- 定义：移位运算符是最基本的运算符之一，几乎每种编程语言都包含这一运算符。移位操作中，被操作的数据被视为<font color="red">二进制数</font>，移位就是将其向左或向右移动若干位的运算
- 优点：
    - <font color="red">高效</font>：移位运算符直接对应于处理器的移位指令。现代处理器具有专门的硬件指令来执行这些移位操作，这些指令通常在一个时钟周期内完成。相比之下，乘法和除法等算术运算在硬件层面上需要更多的时钟周期来完成
    - <font color="red">节省内存</font>：通过移位操作，可以使用一个整数（如 `int` 或 `long`）来存储多个布尔值或标志位，从而节省内存



### 1-2-5- continue、break 和 return 的区别

- `continue`：指跳出当前的这一次循环，继续下一次循环
- `break`：指跳出整个循环体，继续执行循环下面的语句
- `return` 用于跳出所在方法，结束该方法的运行



### 1-2-6- Java 中的几种基本数据类型了解吗

Java 中有 8 种基本数据类型，分别为：

- 6 种数字类型： 
    - 4 种整数型：`byte`、`short`、`int`、`long`
    - 2 种浮点型：`float`、`double`
- 1 种字符类型：`char`
- 1 种布尔型：`boolean`



### 1-2-7- int 和 long 是多少位，多少字节的

- `int`类型是 32 位（bit），占 4 个字节（byte）
- `long`类型是 64 位，占 8 个字节



### 1-2-8-基本类型和包装类型的区别

==用途（使用范围和泛型） -> 存储方式（栈和堆）和占用空间 -> 默认值 -> 比较方式==

- <font color="red">用途</font>：除了定义一些常量和局部变量之外，我们在其他地方比如方法参数、对象属性中很少会使用基本类型来定义变量。并且，包装类型<font color="red">可用于泛型</font>，而基本类型不可以
- <font color="red">存储方式</font>：基本数据类型的局部变量存放在 Java 虚拟机栈中的局部变量表中，基本数据类型的成员变量（未被 `static` 修饰 ）存放在 Java 虚拟机的堆中。包装类型属于对象类型，我们知道几乎所有对象实例都存在于堆中
- <font color="red">占用空间</font>：相比于包装类型（对象类型）， 基本数据类型占用的空间往往非常小
- <font color="red">默认值</font>：成员变量包装类型不赋值就是 `null` ，而基本类型有默认值且不是 `null`
- <font color="red">比较方式</font>：对于基本数据类型来说，`==` 比较的是值。对于包装数据类型来说，`==` 比较的是对象的内存地址。所有整型包装类对象之间值的比较，全部使用 `equals()` 方法



### 1-2-9-自动装箱与拆箱了解吗

==定义 -> 方法 -> 缺点==

- 定义：

    - 装箱：将基本类型用它们对应的引用类型包装起来

    - 拆箱：将包装类型转换为基本数据类型

- 装箱<font color="red">调用</font>了包装类的`valueOf()`方法，拆箱调用了 `xxxValue()`方法

- 缺点：如果频繁拆装箱的话，也会严重<font color="red">影响系统的性能</font>。我们应该尽量避免不必要的拆装箱操作



### 1-2-10-为什么浮点数运算的时候会有精度丢失的风险

- 原因：浮点数的<font color="red">二进制表示方式</font>和<font color="red">数据类型的存储空间有限</font>
- 浮点数的二进制表示方式：很多十进制小数无法精确转换为二进制，只能用无限循环小数表示
- 有限的存储空间：数据类型的存储空间有限，无法容纳无限循环小数，只能对超出部分进行舍入，导致精度丢失



### 1-2-11-数据类型互转会出现什么问题

- 数据丢失：当将一个<font color="red">范围</font>较大的数据类型转换为一个范围较小的数据类型时，可能会发生数据丢失
- 数据溢出：当一个<font color="red">范围</font>较小的数据类型转换为一个范围较大的数据类型时，会将转换结果填充额外的高位空间，造成数据溢出
- 精度丢失：因为浮点数的<font color="red">表示方式</font>不同，在进行浮点数类型的转换时，可能会发生精度损失



## 1-3-变量

### 1-3-1-成员变量和局部变量的区别

==语法形式（属于谁和修饰符） -> 存储方式（栈和堆） -> 生存时间 -> 默认值== 

- <font color="red">语法形式</font>：从语法形式上看，成员变量是属于类的，而局部变量是在代码块或方法中定义的变量或是方法的参数；成员变量可以被 `public`,`private`,`static` 等修饰符所修饰，而局部变量不能被<font color="red">访问控制修饰符</font>及<font color="red"> `static` </font>所修饰；但是，成员变量和局部变量都能被 <font color="red">`final` </font>所修饰
- <font color="red">存储方式</font>：从变量在内存中的存储方式来看，如果成员变量是使用 `static` 修饰的，那么这个成员变量是属于类的，如果没有使用 `static` 修饰，这个成员变量是属于实例的。而对象存在于堆内存，局部变量则存在于栈内存
- <font color="red">生存时间</font>：从变量在内存中的生存时间上看，成员变量是对象的一部分，它随着对象的创建而存在，而局部变量随着方法的调用而自动生成，随着方法的调用结束而消亡
- <font color="red">默认值</font>：从变量是否有默认值来看，成员变量如果没有被赋初始值，则会自动以类型的默认值而赋值（一种情况例外:被 `final` 修饰的成员变量也必须显式地赋值），而局部变量则不会自动赋值



### 1-3-2-静态变量有什么作用

- 静态变量也就是被 `static` 关键字修饰的变量
- 它可以被类的所有<font color="red">实例共享</font>，无论一个类创建了多少个对象，它们都共享同一份静态变量。也就是说，静态变量只会被分配一次内存，即使创建多个对象，这样可以节省内存
- 静态变量是通过类名来访问的



### 1-3-3-字符型常量和字符串常量的区别

- <font color="red">形式</font>：字符常量是单引号引起的一个字符，字符串常量是双引号引起的 0 个或若干个字符
- 含义：字符常量相当于一个整型值( ASCII 值),可以参加表达式运算; 字符串常量代表一个地址值(该字符串在内存中存放位置)
- 占<font color="red">内存大小</font>：字符常量只占 2 个字节; 字符串常量占若干个字节



## 1-4-方法

### 1-4-1-静态方法为什么不能调用非静态成员

- 静态方法是属于类的，在类加载的时候就会分配内存，可以通过类名直接访问。而非静态成员属于实例对象，只有在对象实例化之后才存在，需要通过类的实例对象去访问
- 在类的非静态成员不存在的时候静态方法就已经存在了，此时调用在内存中还不存在的非静态成员，属于非法操作



### 1-4-2-重载和重写有什么区别

- 重载：发生在<font color="red">同一个类</font>中（或者父类和子类之间），方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同
- 重写：发生在<font color="red">运行期</font>，是子类对父类的允许访问的方法的实现过程进行重新编写
    - 方法名、参数列表必须<font color="red">相同</font>，子类方法返回值类型应比父类方法返回值类型更小或相等，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类
    - 如果父类方法<font color="red">访问修饰符</font>为 `private/final/static` 则子类就不能重写该方法，但是被 `static` 修饰的方法能够被再次声明
    - <font color="red">构造方法</font>无法被重写



## 1-5-面向对象基础

### 1-5-1-面向对象和面向过程的区别

面向过程编程和面向对象编程是两种常见的<font color="red">编程范式</font>，两者的主要区别在于<font color="red">解决问题的方式</font>不同：

- **面向过程编程（POP）**：面向过程把解决问题的过程拆成一个个方法，通过一个个方法的执行解决问题
- **面向对象编程（OOP）**：面向对象会先抽象出对象，然后用对象执行方法的方式解决问题

面向对象编程的优点：

- **易维护**：由于良好的结构和封装性，OOP 程序通常更容易维护
- **易复用**：通过继承和多态，OOP 设计使得代码更具复用性，方便扩展功能
- **易扩展**：模块化设计使得系统扩展变得更加容易和灵活



### 1-5-2-创建一个对象用什么运算符?对象实体与对象引用有何不同

- new 运算符，new 创建对象实例（对象实例在堆内存中），对象引用指向对象实例（对象引用存放在栈内存中）
- 一个对象引用可以指向 0 个或 1 个对象
- 一个对象可以有 n 个引用指向它



### 1-5-3-构造方法有哪些特点

- <font color="red">名称</font>与类名相同：构造方法的名称必须与类名完全一致
- 没有<font color="red">返回值</font>：构造方法没有返回类型，且不能使用<font color="red"> `void` </font>声明
- <font color="red">自动执行</font>：在生成类的对象时，构造方法会自动执行，无需显式调用
- 构造方法不能被<font color="red">重写</font>（override），但可以被<font color="red">重载</font>（overload）



### 1-5-4-面向对象三大特征

- 封装：把一个对象的状态信息（也就是属性）<font color="red">隐藏</font>在对象内部，不允许外部对象直接访问对象的内部信息。但是可以<font color="red">提供</font>一些可以被外界访问的方法来操作属性
- 继承：使用已存在的类的定义作为<font color="red">基础</font>建立新类
- 多态：一个对象具有多种的状态，具体表现为<font color="red">父类的引用指向子类的实例</font>
    - 对象类型和引用类型之间具有继承（类）/实现（接口）的关系
    - 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定
    - 多态不能调用“只在子类存在但在父类不存在”的方法
    - 如果子类重写了父类的方法，真正执行的是子类重写的方法，如果子类没有重写父类的方法，执行的是父类的方法



### 1-5-5-多态体现在哪个几个方面

- 方法重载
- 方法重写
- 接口与实现



### 1-5-6-接口和抽象类

==共同点（实例化和抽象方法） -> 区别（设计目的、继承和实现、成员变量和方法）==

- 共同点
    - <font color="red">实例化</font>：接口和抽象类都不能直接实例化，只能被实现（接口）或继承（抽象类）后才能创建具体的对象
    - <font color="red">抽象方法</font>：接口和抽象类都可以包含抽象方法。抽象方法没有方法体，必须在子类或实现类中实现
- 区别
    - <font color="red">设计目的</font>：接口主要用于对类的行为进行约束，实现了某个接口就具有了对应的行为。抽象类主要用于代码复用，强调的是所属关系
    - <font color="red">继承和实现</font>：一个类只能继承一个类（包括抽象类），因为 Java 不支持多继承。但一个类可以实现多个接口，一个接口也可以继承多个其他接口
    - <font color="red">成员变量</font>：接口中的成员变量只能是 `public static final` 类型的，不能被修改且必须有初始值。抽象类的成员变量可以有任何修饰符（`private`, `protected`, `public`），可以在子类中被重新定义或赋值
    - <font color="red">方法</font>： 
        - Java 8 之前，接口中的方法默认是 `public abstract` ，也就是只能有方法声明。自 Java 8 起，可以在接口中定义 `default`（默认） 方法和 `static` （静态）方法。 自 Java 9 起，接口可以包含 `private` 方法。
        - 抽象类可以包含抽象方法和非抽象方法。抽象方法没有方法体，必须在子类中实现。非抽象方法有具体实现，可以直接在抽象类中使用或在子类中重写



### 1-5-7-抽象类和普通类的区别

- <font color="red">设计目的</font>不同：普通类直接描述具体对象，提供完整实现；抽象类作为其他类的模板，定义部分通用逻辑，强调子类实现特定行为
- <font color="red">实例化</font>：普通类可以直接实例化对象，而抽象类不能被实例化，只能被继承
- <font color="red">方法</font>：
    - 普通类中没有抽象方法，抽象类可以有抽象方法
    - 普通类中的方法可以有具体的实现，而抽象类中的方法可以有实现也可以没有实现



### 1-5-8-深拷贝和浅拷贝

- **浅拷贝**：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），不过，如果原对象内部的属性是引用类型的话，浅拷贝会直接复制内部对象的引用地址，也就是说拷贝对象和原对象<font color="red">共用</font>同一个内部对象
- **深拷贝**：深拷贝会<font color="red">完全复制</font>整个对象，包括这个对象所包含的内部对象，将对象内部的所有引用类型字段的内容也复制一份，而<font color="red">不是共享引用</font>



## 1-6- Object

### 1-6-1- Object 类的常见方法有哪些

==获取Class、哈希码、字符串 -> 比较（equals） -> 线程（wait、notify、notifyAll）==

```java
/**
 * native 方法，用于返回当前运行时对象的 Class 对象，使用了 final 关键字修饰，故不允许子类重写。
 */
public final native Class<?> getClass()
/**
 * native 方法，用于返回对象的哈希码，主要使用在哈希表中，比如 JDK 中的HashMap。
 */
public native int hashCode()
/**
 * 用于比较 2 个对象的内存地址是否相等，String 类对该方法进行了重写以用于比较字符串的值是否相等。
 */
public boolean equals(Object obj)
/**
 * native 方法，用于创建并返回当前对象的一份拷贝。
 */
protected native Object clone() throws CloneNotSupportedException
/**
 * 返回类的名字实例的哈希码的 16 进制的字符串。建议 Object 所有的子类都重写这个方法。
 */
public String toString()
/**
 * native 方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。
 */
public final native void notify()
/**
 * native 方法，并且不能重写。跟 notify 一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
 */
public final native void notifyAll()
/**
 * native方法，并且不能重写。暂停线程的执行。注意：sleep 方法没有释放锁，而 wait 方法释放了锁 ，timeout 是等待时间。
 */
public final native void wait(long timeout) throws InterruptedException
/**
 * 多了 nanos 参数，这个参数表示额外时间（以纳秒为单位，范围是 0-999999）。 所以超时的时间还需要加上 nanos 纳秒。。
 */
public final void wait(long timeout, int nanos) throws InterruptedException
/**
 * 跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念
 */
public final void wait() throws InterruptedException
/**
 * 实例被垃圾回收器回收的时候触发的操作
 */
protected void finalize() throws Throwable { }
```



### 1-6-2- == 和 equals() 的区别

- `==`
    - 对于<font color="red">基本数据类型</font>来说，`==` 比较的是值
    - 对于<font color="red">引用数据类型</font>来说，`==` 比较的是对象的内存地址
- `equals()`
    - `equals()`不能用于判断<font color="red">基本数据类型</font>的变量，只能用来判断两个对象是否相等
    - `equals()`方法存在于`Object`类中，而`Object`类是所有类的直接或间接父类，因此所有的类都有`equals()`方法
    - `equals()` 方法存在两种使用情况：
        - 类没有<font color="red">重写</font> `equals()`方法：通过`equals()`比较该类的两个对象时，等价于通过“==”比较这两个对象，使用的默认是 `Object`类`equals()`方法
        - 类重写了 `equals()`方法：一般我们都重写 `equals()`方法来比较两个对象中的属性是否相等；若它们的属性相等，则返回 true(即，认为这两个对象相等)

`Object` 类 `equals()` 方法：

```java
public boolean equals(Object obj) {
     return (this == obj);
}
```



### 1-6-3- hashCode() 有什么作用

`hashCode()` 的作用是获取<font color="red">哈希码</font>（`int` 整数），也称为散列码。这个哈希码的作用是确定该对象在<font color="red">哈希表中的索引位置</font>



### 1-6-4-为什么重写 equals() 时必须重写 hashCode() 方法

- 因为两个相等的对象的 `hashCode` 值必须是相等。也就是说如果 `equals` 方法判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等
- 两个对象有相同的 `hashCode` 值，他们也不一定是相等的（哈希碰撞）



## 1-7- String

### 1-7-1- String、StringBuffer 和 StringBuilder 的区别

==可变性 -> 线程安全性和性能 -> 使用建议==

1）<font color="red">可变性</font>

- `String` 是不可变的
- `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用<font color="red">字符数组</font>保存字符串，不过没有使用 `final` 和 `private` 关键字修饰，最关键的是这个 `AbstractStringBuilder` 类还提供了很多修改字符串的方法比如 `append` 方法

2）<font color="red">线程安全性</font>

- `String` 中的对象是不可变的，也就可以理解为常量，线程安全
- `AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了<font color="red">同步锁</font>或者对调用的方法加了同步锁，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的

3）<font color="red">性能</font>

每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险

4）<font color="red">使用建议</font>

- 操作少量的数据: 适用 `String`
- 单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
- 多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`



### 1-7-2- String 为什么是不可变的

==类修饰符 -> 属性修饰符 -> 成员方法==

- 保存字符串的<font color="red">数组</font>被 `final` 修饰且为私有的，并且`String` 类没有提供/暴露修改这个字符串的<font color="red">方法</font>
- `String` 类被 `final` 修饰导致其不能被<font color="red">继承</font>，进而避免了子类破坏 `String` 不可变



### 1-7-3- String#equals() 和 Object#equals() 有何区别

`String` 中的 `equals` 方法是被重写过的，比较的是 String 字符串的<font color="red">值是否相等</font>。 `Object` 的 `equals` 方法是比较的对象的内存地址



### 1-7-4-字符串常量池的作用了解吗

字符串常量池是 <font color="red">JVM</font> 为了<font color="red">提升性能和减少内存消耗</font>针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的<font color="red">重复创建</font>



## 1-8- 异常

![image-20250611110030043](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250611110030043.png)

### 1-8-1- Exception 和 Error 有什么区别

在 Java 中，所有的异常都有一个共同的祖先 `java.lang` 包中的 `Throwable` 类。`Throwable` 类有两个重要的子类：

- **`Exception`** :程序本身<font color="red">可以处理</font>的异常，可以通过 `catch` 来进行捕获。`Exception` 又可以分为 Checked Exception (受检查异常，必须处理) 和 Unchecked Exception (不受检查异常，可以不处理)
- **`Error`**：`Error` 属于程序<font color="red">无法处理</font>的错误，例如 Java 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误



### 1-8-2- Checked Exception 和 Unchecked Exception 有什么区别

- Checked Exception
    - 受检查异常 ，Java 代码在<font color="red">编译过程</font>中，如果受检查异常没有被 `catch`或者`throws` <font color="red">关键字处理</font>的话，就没办法通过编译
    - 除了`RuntimeException`及其子类以外，其他的`Exception`类及其子类都属于受检查异常 。常见的受检查异常有：IO 相关的异常、`ClassNotFoundException`、`SQLException`...

- Unchecked Exception
    - 即不受检查异常，Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译
    - `RuntimeException` 及其子类都统称为非受检查异常，常见的有`NullPointerException`(空指针错误)、`IllegalArgumentException`(参数错误比如方法入参类型错误)、`ArrayIndexOutOfBoundsException`（数组越界错误）和`ArithmeticException`（算术错误）等



### 1-8-3- Throwable 类常用方法有哪些

- `String getMessage()`: 返回异常发生时的详细信息
- `String toString()`: 返回异常发生时的简要描述
- `String getLocalizedMessage()`: 返回异常对象的本地化信息。使用 `Throwable` 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage()`返回的结果相同
- `void printStackTrace()`: 在控制台上打印 `Throwable` 对象封装的异常信息



### 1-8-4- finally 中的代码一定会执行吗

不一定的！在某些情况下，finally 中的代码不会被执行，比如说 finally 之前<font color="red">虚拟机被终止运行</font>的话，finally 中的代码就不会被执行



## 1-9-泛型

- **Java 泛型（Generics）** 是 <font color="red">JDK 5 </font>中引入的一个新特性。使用泛型参数，可以增强代码的<font color="red">可读性以及稳定性</font>
- 编译器可以对泛型参数进行<font color="red">检测</font>，并且通过泛型参数可以指定传入的对象类型





# 2-算法复杂度分析

## 2-1-时间复杂度分析

### 2-1-1-大O表示法

**大O表示法**：不具体表示代码真正的执行时间，而是表示==代码执行时间随数据规模增长的变化趋势==

案例：

```Java
/**
 ** *求****1~n***的累加和
* ** @param* *n
 ** @return
*/
public int sum(int n) {
   int sum = 0;
   for ( int i = 1; i <= n; i++) {
     sum = sum + i;
   }
   return sum;
}
```

分析这个代码的时间复杂度，分析过程如下：

1.假如每行代码的执行耗时一样：1ms

2.分析这段代码总执行多少行？3n+3

3.代码耗时总时间： T(n) = (3n + 3) * 1ms

我们现在有了总耗时，借助大O表示法来计算这个代码的时间复杂度

大O表示法只需要代码执行时间与数据规模的增长趋势，公式可以简化如下：

T(n) =O(3n + 3)------------> T(n) = O(n)

![image-20250526212259625](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250526212259625.png)



### 2-1-2-常见复杂度表示形式

常对幂指阶

![image-20250526212329833](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250526212329833.png)

![image-20250526213429882](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250526213429882.png)



## 2-2-空间复杂度分析

空间复杂度全称是渐进空间复杂度，表示==算法占用的额外存储空间与数据规模之间的增长关系==



# 3-集合

![image-20250528162610034](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528162610034.png)

![image-20250528161203043](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528161203043.png)

Java 集合，也叫作容器，主要是由两大接口派生而来：一个是 `Collection`接口，主要用于存放单一元素；另一个是 `Map` 接口，主要用于存放键值对。对于`Collection` 接口，下面又有三个主要的子接口：`List`、`Set` 、 `Queue`



## 3-1- Collection

### 3-1-1- List

#### 3-1-1-1- Array（数组）

数组（Array）是一种用<font color="red">连续</font>的内存空间存储<font color="red">相同</font>数据类型数据的<font color="red">线性</font>数据结构



**寻址公式**

```
arr[i] = baseAddress + i * dataTypeSize
```

- baseAddress：数组的首地址

- dataTypeSize：代表数组中元素类型的大小
- i：指的是数组的下标



![image-20250526214416310](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250526214416310.png)

有了寻址公式以后，我们再来获取一下下标为1的元素，这个是原来的数组

```
int[] array = {22,33,88,66,55,25};
```

套入公式：

```
array[1] =10 + i * 4 = 14
```

获取到14这个地址，就能获取到下标为1的这个元素了



**为什么数组索引从0开始**

- 在根据数组索引获取元素时，会用索引和寻址公式来计算内存所对应的元素数据，寻址公式：数组的首地址 + 索引 * 存储数据的类型大小
- 如果数组的索引从1开始，寻址公式中，就需要增加一次<font color="red">减法操作</font>，对于CPU来说就多一次指令，性能不高



**操作数组的时间复杂度**

**查询**

| 查询方式     | 说明                                                     | 时间复杂度 |
| ------------ | -------------------------------------------------------- | ---------- |
| 根据索引查询 | 通过数组的首地址和寻址公式能够很快速的找到想要访问的元素 | O(1)       |
| 未知索引查询 | 遍历查找数组内的元素                                     | O(n)       |
|              | 查找排序后数组内的元素，通过二分查找算法查找指定元素     | O(log2n)   |

**插入**

数组是一段连续的内存空间，因此为了保证数组的连续性会使得数组的插入和删除的效率变的很低，时间复杂度为O(n)

**删除**

时间复杂度是O(n)



#### 3-1-1-2-ArrayList源码分析

- 定义：
    - `ArrayList`通过一个普通的数组（`Object[] elementData`）来存储数据
    - 与 Java 中的数组相比，它的容量能<font color="red">动态增长</font>
    - 在添加大量元素前，应用程序可以使用`ensureCapacity`方法来增加 `ArrayList` 实例的容量。避免 `ArrayList` 在添加过程中“边添加边扩容”

- 体系结构：`ArrayList` <font color="red">继承抽象类</font> `AbstractList` ，<font color="red">实现接口</font> `List`, `RandomAccess`, `Cloneable`, `java.io.Serializable` 

    - `AbstractList`：提供了部分 List 操作的默认实现

    - `List` ： 表明它是一个列表，支持添加`add()`、删除`remove()`、查找`get(int index)`等操作，并且可以通过下标进行访问
    - `RandomAccess` ：这是一个标志接口（没有任何方法），表明实现这个接口的 `List` 集合是支持快速随机访问（可以通过元素的下标（index）直接、迅速地访问到集合中的元素**，**时间复杂度为 O(1)）的。在 `ArrayList` 中，我们即可以通过元素的序号快速获取元素对象，这就是快速随机访问
    - `Cloneable` ：表明它具有拷贝能力，可以进行深拷贝或浅拷贝操作
    - `Serializable` ：表明它可以进行序列化操作，也就是可以将对象转换为字节流进行持久化存储或网络传输，非常方便

![image-20250528162403185](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528162403185.png)



1）成员变量

![image-20250528155906108](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528155906108.png)

| 名称                                | 用途                                           | 什么时候用                          |
| ----------------------------------- | ---------------------------------------------- | ----------------------------------- |
| `EMPTY_ELEMENTDATA`                 | 代表真正容量为 0 的列表                        | 比如调用 `new ArrayList(0)`         |
| `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` | 代表“准备好以后要扩容为默认容量（10）”的空列表 | 比如调用 `new ArrayList()` 无参构造 |

2）构造函数

![image-20250528160110080](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528160110080.png)

![image-20250528160205626](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528160205626.png)

3）成员方法

**增加元素的方法**

| 方法名                                                 | 说明                                 |
| ------------------------------------------------------ | ------------------------------------ |
| `boolean add(E e)`                                     | 在末尾添加元素                       |
| `void add(int index, E element)`                       | 在指定位置插入元素                   |
| `boolean addAll(Collection<? extends E> c)`            | 把一个集合所有元素添加到当前列表末尾 |
| `boolean addAll(int index, Collection<? extends E> c)` | 从指定位置插入一个集合中的所有元素   |

**删除元素的方法**

| 方法名                               | 说明                                     |
| ------------------------------------ | ---------------------------------------- |
| `E remove(int index)`                | 删除指定位置的元素，并返回被删除的元素   |
| `boolean remove(Object o)`           | 删除首次出现的指定元素                   |
| `boolean removeAll(Collection<?> c)` | 删除所有出现在指定集合中的元素           |
| `boolean retainAll(Collection<?> c)` | 仅保留在指定集合中的元素，其它元素被移除 |
| `void clear()`                       | 清空列表中的所有元素                     |

**查找和判断方法**

| 方法名                       | 说明                                  |
| ---------------------------- | ------------------------------------- |
| `E get(int index)`           | 获取指定下标的元素                    |
| `int indexOf(Object o)`      | 返回元素首次出现的位置，找不到返回 -1 |
| `int lastIndexOf(Object o)`  | 返回元素最后一次出现的位置            |
| `boolean contains(Object o)` | 判断列表中是否包含某个元素            |
| `boolean isEmpty()`          | 判断列表是否为空                      |
| `int size()`                 | 返回元素数量                          |

**修改元素的方法**

| 方法名                                       | 说明                           |
| -------------------------------------------- | ------------------------------ |
| `E set(int index, E element)`                | 替换指定位置上的元素，返回原值 |
| `void replaceAll(UnaryOperator<E> operator)` | 对每个元素执行函数操作并替换   |
| `void sort(Comparator<? super E> c)`         | 使用自定义比较器对列表排序     |

**转换和复制**

| 方法名                   | 说明                                             |
| ------------------------ | ------------------------------------------------ |
| `Object[] toArray()`     | 将 `ArrayList` 转换为 `Object` 数组              |
| `<T> T[] toArray(T[] a)` | 转换为指定类型的数组                             |
| `Object clone()`         | 浅拷贝列表并返回一个新列表（实现了 `Cloneable`） |

 **序列化相关**

| 方法名                                | 说明                                     |
| ------------------------------------- | ---------------------------------------- |
| `writeObject(ObjectOutputStream out)` | 把列表对象序列化为字节流（内部私有方法） |
| `readObject(ObjectInputStream in)`    | 从字节流反序列化为对象（内部私有方法）   |

**遍历相关方法**

| 方法名                                     | 说明                                       |
| ------------------------------------------ | ------------------------------------------ |
| `Iterator<E> iterator()`                   | 返回一个迭代器（实现 `Iterator`）          |
| `ListIterator<E> listIterator()`           | 返回支持双向遍历的 `ListIterator`          |
| `ListIterator<E> listIterator(int index)`  | 从指定位置开始的双向迭代器                 |
| `void forEach(Consumer<? super E> action)` | 使用 Lambda 表达式对元素逐个操作（Java 8） |
| `Spliterator<E> spliterator()`             | 用于并行流遍历的分割器                     |

**子列表**

| 方法名                                        | 说明                                    |
| --------------------------------------------- | --------------------------------------- |
| `List<E> subList(int fromIndex, int toIndex)` | 返回原列表的一个视图（区间 [from, to)） |

4）扩容机制分析

文字分析：

- 扩容判断入口：<font color="red">`add()` 方法</font>
    - 每次添加元素，`add()` 方法都会调用 `ensureCapacityInternal(size + 1)` 来确保数组容量足够容纳新增元素
    - 为数组赋值
- <font color="red">判断是否为无参构造创建</font>
    - `ensureCapacityInternal(int minCapacity)` 方法中，会判断当前内部数组 `elementData` 是否是 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA`，这是一个共享的空数组实例，用于无参构造的懒加载策略
    - 如果是，则表示当前 `ArrayList` 是通过无参构造方法创建的，还未真正分配存储空间。此时会将 `minCapacity` 设置为默认容量 `DEFAULT_CAPACITY`（默认为 10）；不是不执行操作，执行下一步
- <font color="red">判断是否需要扩容</font>
    - 执行 `ensureExplicitCapacity(minCapacity)`，比较当前 `minCapacity` 与 `elementData.length`
    - 如果 `minCapacity > elementData.length`，说明当前数组容量不足，需要扩容，系统会调用 `grow(minCapacity)` 方法执行扩容逻辑
- 执行扩容操作：<font color="red">`grow(minCapacity)` 方法</font>
    - 首先记录旧数组容量 `oldCapacity = elementData.length`
    - 然后计算新容量 `newCapacity = oldCapacity + (oldCapacity >> 1)`，即旧容量的 1.5 倍
    - 如果计算得到的新容量仍小于 `minCapacity`（即扩容后仍不够），则将新容量强制设为 `minCapacity`，保证能够容纳新增元素
    - 若新容量超过最大数组大小 `MAX_ARRAY_SIZE`，还需调用 `hugeCapacity(minCapacity)` 做溢出处理，防止内存溢出
    - 最后通过 `Arrays.copyOf()` 将原数组的数据拷贝到新数组，实现扩容



代码：

1.扩容入口：`add()` 方法

```java
public boolean add(E e) {
    // 加元素之前，先调用ensureCapacityInternal方法
    ensureCapacityInternal(size + 1);  // 核心扩容判断
    // 这里看到ArrayList添加元素的实质就相当于为数组赋值
    elementData[size++] = e;
    return true;
}
```

2.扩容判断逻辑：`ensureCapacityInternal()`

```java
// 根据给定的最小容量和当前数组元素来计算所需容量
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        // 空构造函数的特殊处理（默认容量为10）
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}
```

3.实际判断容量：`ensureExplicitCapacity()`

```java
private void ensureExplicitCapacity(int minCapacity) {
    modCount++; // fail-fast 机制相关
    // 若 minCapacity > elementData.length，则需要扩容
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```

4.真正扩容的方法：`grow()`

```java
private void grow(int minCapacity) {
    // 原数组长度
    int oldCapacity = elementData.length;
    // 扩容为原来 1.5 倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    
    // 如果新容量还不足以容纳新元素，就直接设置为 minCapacity
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    
    // 如果新容量超过最大限制，就处理溢出问题
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    
    // 数组复制：创建一个更大的数组并复制原有数据
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

5.辅助方法：`hugeCapacity`

```java
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}
```



#### 3-1-1-3-ArrayList面试题

##### 3-1-1-3-1-ArrayList 和 Array（数组）的区别

`ArrayList` 内部基于动态数组实现，比 `Array`（静态数组） 使用起来更加灵活：

- <font color="red">创建</font>：`ArrayList`创建时不需要指定大小，而`Array`创建时必须指定大小
- <font color="red">扩容</font>：`ArrayList`会根据实际存储的元素动态地扩容或缩容，而 `Array` 被创建之后就不能改变它的长度了
- <font color="red">存储类型</font>：`ArrayList` 中只能存储对象。对于基本类型数据，需要使用其对应的包装类（如 Integer、Double 等）。`Array` 可以直接存储基本类型数据，也可以存储对象
- <font color="red">类型安全</font>：`ArrayList` 允许你使用泛型来确保类型安全，`Array` 则不可以
- <font color="red">方法</font>：`ArrayList` 支持插入、删除、遍历等常见操作，并且提供了丰富的 API 操作方法，比如 `add()`、`remove()`等。`Array` 只是一个固定长度的数组，只能按照下标访问其中的元素，不具备动态添加、删除元素的能力



##### 3-1-1-3-2-ArrayList 可以添加 null 值吗

`ArrayList` 中<font color="red">可以存储任何类型的对象</font>，包括 `null` 值。不过，不建议向`ArrayList` 中添加 `null` 值， `null` 值无意义，会让代码难以维护，比如忘记做判空处理就会导致<font color="red">空指针异常</font>



##### 3-1-1-3-3-ArrayList 插入和删除元素的时间复杂度

对于插入：

- 头部插入：由于需要将所有元素都依次向后移动一个位置，因此时间复杂度是 O(n)
- 尾部插入：当 `ArrayList` 的容量未达到极限时，往列表末尾插入元素的时间复杂度是 O(1)，因为它只需要在数组末尾添加一个元素即可；当容量已达到极限并且需要<font color="red">扩容</font>时，则需要执行一次 O(n) 的操作将原数组复制到新的更大的数组中，然后再执行 O(1) 的操作添加元素
- 指定位置插入：需要将目标位置之后的所有元素都向后移动一个位置，然后再把新元素放入指定位置。这个过程需要移动平均 n/2 个元素，因此时间复杂度为 O(n)

对于删除：

- 头部删除：由于需要将所有元素依次向前移动一个位置，因此时间复杂度是 O(n)
- 尾部删除：当删除的元素位于列表末尾时，时间复杂度为 O(1)
- 指定位置删除：需要将目标元素之后的所有元素向前移动一个位置以填补被删除的空白位置，因此需要移动平均 n/2 个元素，时间复杂度为 O(n)



#### 3-1-1-4-LinkedList源码分析

- 定义：`LinkedList` 是一个基于<font color="red">双向链表</font>实现的集合类
- 体系结构：`LinkedList` <font color="red">继承抽象类</font> `AbstractSequentialList` ，而 `AbstractSequentialList` 又继承`AbstractList`，<font color="red">实现接口</font>`List`，`Deque`，`Cloneable`，`Serializable`
    - `List` : 表明它是一个列表，支持添加、删除、查找等操作，并且可以通过下标进行访问
    - `Deque` ：继承自 `Queue` 接口，具有双端队列的特性，支持从两端插入和删除元素，方便实现栈和队列等数据结构。需要注意，`Deque` 的发音为 "deck" [dɛk]
    - `Cloneable` ：表明它具有拷贝能力，可以进行深拷贝或浅拷贝操作
    - `Serializable` : 表明它可以进行序列化操作，也就是可以将对象转换为字节流进行持久化存储或网络传输，非常方便

![image-20250529111420505](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250529111420505.png)



#### 3-1-1-5-LinkedList面试题

##### 3-1-1-5-1-LinkedList 插入和删除元素的时间复杂度

- 头部插入/删除：只需要修改头结点的指针即可完成插入/删除操作，因此时间复杂度为 O(1)

- 尾部插入/删除：只需要修改尾结点的指针即可完成插入/删除操作，因此时间复杂度为 O(1)

- 指定位置插入/删除：需要先<font color="red">移动</font>到指定位置，再修改指定节点的指针完成插入/删除，不过由于有头尾指针，可以从较近的指针出发，因此需要遍历平均 n/4 个元素，时间复杂度为 O(n)



##### 3-1-1-5-2-LinkedList 为什么不能实现 RandomAccess 接口

`RandomAccess` 是一个<font color="red">标记接口</font>，用来表明实现该接口的类支持随机访问（即可以通过<font color="red">索引</font>快速访问元素）。由于 `LinkedList` 底层数据结构是链表，<font color="red">内存地址</font>不连续，只能通过<font color="red">指针</font>来定位，不支持随机快速访问，所以不能实现 `RandomAccess` 接口



##### 3-1-1-5-3-ArrayList 与 LinkedList 区别和联系

==底层数据结构不一样 -> 快速随机访问、插入和删除时间复杂度、空间占用==

- <font color="red">底层数据结构</font>：`ArrayList` 底层使用的是 **`Object` 数组**；`LinkedList` 底层使用的是 **双向链表** 数据结构（JDK1.6 之前为循环链表，JDK1.7 取消了循环。注意双向链表和双向循环链表的区别）
- 是否支持<font color="red">快速随机访问</font>：`LinkedList` 不支持高效的随机元素访问，而 `ArrayList`（实现了 `RandomAccess` 接口） 支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于`get(int index)`方法)
- <font color="red">插入和删除元素的时间复杂度</font>：
    -  `ArrayList` ：
        - 头部，插入/删除，时间复杂度为 O(n)
        - 尾部，插入/删除，时间复杂度为O(1)
        - 指定位置，插入/删除，时间复杂度为 O(n)
    - `LinkedList`：
        - 头部或者尾部，插入/删除，时间复杂度为 O(1)
        - 指定位置，插入/删除，时间复杂度为 O(n)
- <font color="red">内存空间占用</font>：
    - `ArrayList` 底层是数组，内存连续，节省空间，空间浪费主要体现在在 list 列表的结尾会预留一定的容量空间
    - `LinkedList`的每一个元素都需要存放两个指针，占用更多的内存

- <font color="red">是否保证线程安全</font>： `ArrayList` 和 `LinkedList` 都是不同步的，也就是不保证线程安全
    - 在方法内使用，局部变量是线程安全的
    - 使用线程安全的`ArrayList`和`LinkedList`



## 3-2-Map

### 3-2-1-树

| 分类标准 | 类型     | 特点                       | 典型代表                 |
| -------- | -------- | -------------------------- | ------------------------ |
| 分支数   | 二叉树   | 每个节点≤2个子节点         | BST、AVL、堆、红黑树     |
|          | 多叉树   | 每个节点≥2个子节点         | B树、B+树、Trie树、2-3树 |
| 平衡性   | 非平衡树 | 无平衡约束，可能退化为链表 | 普通BST、目录树、语法树  |
|          | 平衡树   | 通过规则或旋转保持平衡     | AVL树、红黑树、B树       |
| 节点关系 | 有序树   | 子节点有明确顺序           | BST、B+树、Trie树        |
|          | 无序树   | 子节点无顺序要求           | 普通树、目录树           |



### 3-2-2-二叉树

#### 3-2-2-1-二叉树的概念

下图是一个二叉树

![image-20250604153414418](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604153414418.png)

- 定义：树就是一种类似现实生活中的树的数据结构（倒置的树）。任何一颗非空树只有一个根节点
- 特点：
    - 一棵树中的任意两个结点有且仅有唯一的一条路径连通
    - 一棵树如果有 n 个结点，那么它一定恰好有 n-1 条边
    - 一棵树不包含回路
- 概念：
    - **节点**：树中的每个元素都可以统称为节点
    - **根节点**：顶层节点或者说没有父节点的节点。上图中 A 节点就是根节点
    - **父节点**：若一个节点含有子节点，则这个节点称为其子节点的父节点。上图中的 B 节点是 D 节点、E 节点的父节点
    - **子节点**：一个节点含有的子树的根节点称为该节点的子节点。上图中 D 节点、E 节点是 B 节点的子节点
    - **兄弟节点**：具有相同父节点的节点互称为兄弟节点。上图中 D 节点、E 节点的共同父节点是 B 节点，故 D 和 E 为兄弟节点
    - **叶子节点**：没有子节点的节点。上图中的 D、F、H、I 都是叶子节点
    - **节点的高度**：该节点到叶子节点的最长路径所包含的边数
    - **节点的深度**：根节点到该节点的路径所包含的边数
    - **节点的层数**：节点的深度+1
    - **树的高度**：根节点的高度



#### 3-2-2-2-二叉树的分类

- 定义：
    - **二叉树**（Binary tree）是每个节点最多只有两个分支（即不存在分支度大于 2 的节点）的树结构
    - **二叉树** 的分支通常被称作“**左子树**”或“**右子树**”。并且，**二叉树** 的分支具有左右次序，不能随意颠倒
- 分类：
    - 满二叉树：一个二叉树，如果每一个层的结点数都达到最大值，则这个二叉树就是 **满二叉树**。也就是说，如果一个二叉树的层数为 K，且结点总数是(2^k) -1 ，则它就是 **满二叉树**
    - 完全二叉树：除最后一层外，若其余层都是满的，并且最后一层是满的或者是在右边缺少连续若干节点，则这个二叉树就是 **完全二叉树**
    - 平衡二叉树：可以是一棵空树；如果不是空树，它的左右两个子树的高度差的绝对值不超过 1，并且左右两个子树都是一棵平衡二叉树



#### 3-2-2-3-二叉树的存储

- 二叉树的存储主要分为 **链式存储** 和 **顺序存储** 两种
- 链式存储：
    - 每个节点包括三个属性：
        - 数据 data。data 不一定是单一的数据，根据不同情况，可以是多个具有不同类型的数据
        - 左节点指针 left
        - 右节点指针 right
- 顺序存储：
    - 顺序存储就是利用数组进行存储，数组中的每一个位置仅存储节点的 data，不存储左右子节点的指针，子节点的索引通过数组下标完成。根结点的序号为 1，对于每个节点 Node，假设它存储在数组中下标为 i 的位置，那么它的左子节点就存储在 2i 的位置，它的右子节点存储在下标为 2i+1 的位置



一棵完全二叉树的数组顺序存储如下图所示：

![image-20250604154403094](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604154403094.png)

![image-20250604154437235](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604154437235.png)

如果我们要存储的二叉树不是完全二叉树，在数组中就会出现空隙，导致内存利用率降低



#### 3-2-2-4-二叉树的遍历

- 先序遍历：二叉树的先序遍历，就是先输出根结点，再遍历左子树，最后遍历右子树，遍历左子树和右子树的时候，同样遵循先序遍历的规则
- 中序遍历：二叉树的中序遍历，就是先递归中序遍历左子树，再输出根结点的值，再递归中序遍历右子树
- 后序遍历：二叉树的后序遍历，就是先递归后序遍历左子树，再递归后序遍历右子树，最后输出根结点的值



### 3-2-3-红黑树

#### 3-2-3-1-红黑树的概念

- 概念：红黑树（Red Black Tree）是一种<font color="red">自平衡二叉查找树</font>。它是在 1972 年由 Rudolf Bayer 发明的，当时被称为平衡二叉 B 树（symmetric binary B-trees）。后来，在 1978 年被 Leo J. Guibas 和 Robert Sedgewick 修改为如今的“红黑树”
- 特点：
    - 每个节点非红即黑。黑色决定平衡，红色不决定平衡
    - 根节点总是黑色的
    - 每个叶子节点都是黑色的空节点（NIL 节点）。这里指的是红黑树都会有一个空的叶子节点，是红黑树自己的规则
    - 如果节点是红色的，则它的子节点必须是黑色的（反之不一定）。通常这条规则也叫不会有连续的红色节点。一个节点最多临时会有 3 个子节点，中间是黑色节点，左右是红色节点
    - 从任意节点到它的叶子节点或空子节点的每条路径，必须包含相同数目的黑色节点（即相同的黑色高度）。每一层都只是有一个节点贡献了树高决定平衡性，也就是对应红黑树中的黑色节点



### 3-2-4-B树和B+树

- 概念：
    - B树是一种<font color="red">平衡的多路搜索树</font>，主要设计用于减少磁盘I/O操作。它允许每个节点有多个子节点（通常远多于二叉树的两个子节点）
    - B+树是B树的变种，在数据库索引中更为常用。它与B树的主要区别在于数据存储方式和叶子节点的连接
        - 非叶子节点只存储键（不存储实际数据）
        - 所有数据都存储在叶子节点中
        - 叶子节点通过指针连接形成链表



### 3-2-5-散列表

- 定义：散列表（Hash Table，又称哈希表）是一种高效的数据结构，它通过**键值对（key-value）**的形式存储数据，能够提供接近常数时间的查找性能（理想情况下O(1)）
- 思想：通过**哈希函数**将键（key）映射到表中的特定位置（称为"桶"或"槽"），从而实现快速的数据存取



### 3-2-6- HashMap

- HashMap 的数据结构：==底层使用hash表，即数组和链表或红黑树==
- 存储时：
    - 往HashMap中put元素时，利用key的hashCode重新hash计算出当前对象在数组中的下标
    - 如果key相同，则覆盖原始值
    - 如果key不相同（出现冲突），则将当前的key-value放入链表或红黑树中（链表的长度大于8且数组长度大于64，转为红黑树）
- 获取时：
    - 直接找到hash值对应的下标，判断key是否相同，从而找到对应值



#### 3-2-6-1-底层实现

**JDK1.8之前**

- 底层实现：JDK1.8 之前 `HashMap` 底层是<font color="red">数组和链表</font>结合在一起使用，也就是<font color="red">链表散列</font>
    - **数组**：作为HashMap的主干，用于快速定位元素位置
    - **链表**：用于解决哈希冲突（当不同key映射到同一数组位置时）
- 添加数据的工作原理：
    1. 哈希值计算
        - 首先通过`key.hashCode()`获取key的<font color="red">原始哈希值</font>
        - 然后经过"<font color="red">扰动函数</font>"处理（目的是让哈希值分布更均匀）
        - 在JDK1.8之前，扰动函数是<font color="red">多次位运算</font>（4次位移+5次异或）
    2. 确定数组位置
        - 使用公式：`(n - 1) & hash`（n是数组长度，hash是扰动后的哈希值）
        - 结果就是元素在数组中的下标位置
    3. 处理哈希冲突
        - 比较两者的hash值和key（通过equals方法）
        - 如果相同时，新value覆盖旧value
        - 如果不同时，将新元素添加到链表末尾（拉链法）

**“拉链法”** ：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可



**JDK1.8之后**

- JDK1.8 之后在解决<font color="red">哈希冲突</font>时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树
- 这样做的目的是减少搜索时间：链表的查询效率为 O(n)（n 是链表的长度），红黑树是一种自平衡二叉搜索树，其查询效率为 O(log n)。当链表较短时，O(n) 和 O(log n) 的性能差异不明显。但当链表变长时，查询性能会显著下降
- 优化了<font color="red">扰动函数</font>（只做一次位运算）



#### 3-2-6-2- HashMap源码分析

- HashMap 主要用来存放键值对，它基于哈希表的 Map 接口实现，是常用的 Java 集合之一，是非线程安全的
- `HashMap` 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个
- JDK1.8 之前 HashMap 由 数组+链表 组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）。 JDK1.8 以后的 `HashMap` 在解决哈希冲突时有了较大的变化，当链表长度大于等于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间
- `HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。并且， `HashMap` 总是使用 2 的幂作为哈希表的大小



1）成员变量

![image-20250604182615921](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604182615921.png)

2）构造方法

![image-20250604182737023](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604182737023.png)

3）添加数据

![image-20250604183739101](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604183739101.png)

4）扩容

![image-20250604184416217](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604184416217.png)



#### 3-2-6-3- HashMap 面试题

##### 3-2-6-3-1- 为什么优先扩容而非直接转为红黑树

当链表长度大于阈值（默认为 8），将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树，原因如下：

- <font color="red">数组扩容</font>能减少哈希冲突的发生概率（即将元素重新分散到新的、更大的数组中），这在多数情况下比直接转换为红黑树更高效
- 红黑树需要保持自平衡，<font color="red">维护成本</font>较高。并且，过早引入红黑树反而会增加复杂度



##### 3-2-6-3-2-为什么选择阈值 8 和 64

- 泊松分布表明，链表长度达到 8 的<font color="red">概率极低</font>
- 在小数组中<font color="red">扩容成本低</font>，优先扩容可以避免过早引入红黑树。数组大小达到 64 时，<font color="red">冲突概率较高</font>，此时红黑树的性能优势开始显现



##### 3-2-6-3-3-聊聊 HashMap 中的 put 方法

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    //判断数组是否未初始化
    if ((tab = table) == null || (n = tab.length) == 0)
        //如果未初始化，调用resize方法 进行初始化
        n = (tab = resize()).length;
    //通过 & 运算求出该数据（key）的数组下标并判断该下标位置是否有数据
    if ((p = tab[i = (n - 1) & hash]) == null)
        //如果没有，直接将数据放在该下标位置
        tab[i] = newNode(hash, key, value, null);
    //该数组下标有数据的情况
    else {
        Node<K,V> e; K k;
        //判断该位置数据的key和新来的数据是否一样
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            //如果一样，证明为修改操作，该节点的数据赋值给e,后边会用到
            e = p;
        //判断是不是红黑树
        else if (p instanceof TreeNode)
            //如果是红黑树的话，进行红黑树的操作
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        //新数据和当前数组既不相同，也不是红黑树节点，证明是链表
        else {
            //遍历链表
            for (int binCount = 0; ; ++binCount) {
                //判断next节点，如果为空的话，证明遍历到链表尾部了
                if ((e = p.next) == null) {
                    //把新值放入链表尾部
                    p.next = newNode(hash, key, value, null);
                    //因为新插入了一条数据，所以判断链表长度是不是大于等于8
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        //如果是，进行转换红黑树操作
                        treeifyBin(tab, hash);
                    break;
                }
                //判断链表当中有数据相同的值，如果一样，证明为修改操作
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                //把下一个节点赋值为当前节点
                p = e;
            }
        }
        //判断e是否为空（e值为修改操作存放原数据的变量）
        if (e != null) { // existing mapping for key
            //不为空的话证明是修改操作，取出老值
            V oldValue = e.value;
            //一定会执行  onlyIfAbsent传进来的是false
            if (!onlyIfAbsent || oldValue == null)
                //将新值赋值当前节点
                e.value = value;
            afterNodeAccess(e);
            //返回老值
            return oldValue;
        }
    }
    //计数器，计算当前节点的修改次数
    ++modCount;
    //当前数组中的数据数量如果大于扩容阈值
    if (++size > threshold)
        //进行扩容操作
        resize();
    //空方法
    afterNodeInsertion(evict);
    //添加操作时 返回空值
    return null;
}
```

![](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604183739101.png)

1. 判断键值对数组table是否为空或为null，否则执行resize()进行扩容（初始化）
2. 根据键值key计算hash值得到数组索引
3. 判断table[i]==null，条件成立，直接新建节点添加
4. 如果table[i]==null ，不成立
    1. 判断table[i]的首个元素是否和key一样，如果相同直接覆盖value
    2. 判断table[i] 是否为treeNode，即table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值对
    3. 遍历table[i]，链表的尾部插入数据，然后判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操 作，遍历过程中若发现key已经存在直接覆盖value
5. 插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold（数组长度*0.75），如果超过，进行扩容。



##### 3-2-6-3-4-聊聊 HashMap 的扩容机制

```java
//扩容、初始化数组
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
    	//如果当前数组为null的时候，把oldCap老数组容量设置为0
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        //老的扩容阈值
    	int oldThr = threshold;
        int newCap, newThr = 0;
        //判断数组容量是否大于0，大于0说明数组已经初始化
    	if (oldCap > 0) {
            //判断当前数组长度是否大于最大数组长度
            if (oldCap >= MAXIMUM_CAPACITY) {
                //如果是，将扩容阈值直接设置为int类型的最大数值并直接返回
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            //如果在最大长度范围内，则需要扩容  OldCap << 1等价于oldCap*2
            //运算过后判断是不是最大值并且oldCap需要大于16
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold  等价于oldThr*2
        }
    	//如果oldCap<0，但是已经初始化了，像把元素删除完之后的情况，那么它的临界值肯定还存在，       			如果是首次初始化，它的临界值则为0
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        //数组未初始化的情况，将阈值和扩容因子都设置为默认值
    	else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
    	//初始化容量小于16的时候，扩容阈值是没有赋值的
        if (newThr == 0) {
            //创建阈值
            float ft = (float)newCap * loadFactor;
            //判断新容量和新阈值是否大于最大容量
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
    	//计算出来的阈值赋值
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        //根据上边计算得出的容量 创建新的数组       
    	Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    	//赋值
    	table = newTab;
    	//扩容操作，判断不为空证明不是初始化数组
        if (oldTab != null) {
            //遍历数组
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                //判断当前下标为j的数组如果不为空的话赋值个e，进行下一步操作
                if ((e = oldTab[j]) != null) {
                    //将数组位置置空
                    oldTab[j] = null;
                    //判断是否有下个节点
                    if (e.next == null)
                        //如果没有，就重新计算在新数组中的下标并放进去
                        newTab[e.hash & (newCap - 1)] = e;
                   	//有下个节点的情况，并且判断是否已经树化
                    else if (e instanceof TreeNode)
                        //进行红黑树的操作
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    //有下个节点的情况，并且没有树化（链表形式）
                    else {
                        //比如老数组容量是16，那下标就为0-15
                        //扩容操作*2，容量就变为32，下标为0-31
                        //低位：0-15，高位16-31
                        //定义了四个变量
                        //        低位头          低位尾
                        Node<K,V> loHead = null, loTail = null;
                        //        高位头		   高位尾
                        Node<K,V> hiHead = null, hiTail = null;
                        //下个节点
                        Node<K,V> next;
                        //循环遍历
                        do {
                            //取出next节点
                            next = e.next;
                            //通过 与操作 计算得出结果为0
                            if ((e.hash & oldCap) == 0) {
                                //如果低位尾为null，证明当前数组位置为空，没有任何数据
                                if (loTail == null)
                                    //将e值放入低位头
                                    loHead = e;
                                //低位尾不为null，证明已经有数据了
                                else
                                    //将数据放入next节点
                                    loTail.next = e;
                                //记录低位尾数据
                                loTail = e;
                            }
                            //通过 与操作 计算得出结果不为0
                            else {
                                 //如果高位尾为null，证明当前数组位置为空，没有任何数据
                                if (hiTail == null)
                                    //将e值放入高位头
                                    hiHead = e;
                                //高位尾不为null，证明已经有数据了
                                else
                                    //将数据放入next节点
                                    hiTail.next = e;
                               //记录高位尾数据
                               	hiTail = e;
                            }
                            
                        } 
                        //如果e不为空，证明没有到链表尾部，继续执行循环
                        while ((e = next) != null);
                        //低位尾如果记录的有数据，是链表
                        if (loTail != null) {
                            //将下一个元素置空
                            loTail.next = null;
                            //将低位头放入新数组的原下标位置
                            newTab[j] = loHead;
                        }
                        //高位尾如果记录的有数据，是链表
                        if (hiTail != null) {
                            //将下一个元素置空
                            hiTail.next = null;
                            //将高位头放入新数组的(原下标+原数组容量)位置
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
    	//返回新的数组对象
        return newTab;
    }
```

![image-20250604184416217](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250604184416217.png)

1. 在添加元素或初始化的时候需要调用resize方法进行扩容，第一次添加数据初始化数组长度为16，以后每次每次扩容都是达到了扩容阈值（数组长度 * 0.75）
2. 每次扩容的时候，都是扩容之前容量的2倍； 
3. 扩容之后，会新创建一个数组，需要把老数组中的数据挪动到新的数组中
    1. 没有hash冲突的节点，则直接使用 e.hash & (newCap - 1) 计算新数组的索引位置
    2. 如果是红黑树，走红黑树的添加
    3. 如果是链表，则需要遍历链表，可能需要拆分链表，判断(e.hash & oldCap)是否为0，该元素的位置要么停留在原始位置，要么移动到原始位置+增加的数组大小这个位置上



##### 3-2-6-3-5- HashMap 多线程操作导致死循环问题

- JDK1.7 及之前版本的 `HashMap` 在多线程环境下扩容操作可能存在死循环问题，这是由于当==一个桶位中有多个元素需要进行扩容==时，多个线程同时对<font color="red">链表</font>进行操作，<font color="red">头插法</font>可能会导致链表中的节点指向错误的位置，从而形成一个<font color="red">环形链表</font>，进而使得查询元素的操作陷入死循环无法结束

- 为了解决这个问题，JDK1.8 版本的 HashMap 采用了<font color="red">尾插法</font>而不是头插法来避免链表倒置，==使得插入的节点永远都是放在链表的末尾，避免了链表中的环形结构==
- 但是还是不建议在多线程下使用 `HashMap`，因为多线程下使用 `HashMap` 还是会存在数据覆盖的问题。并发环境下，推荐使用 `ConcurrentHashMap` 



##### 3-2-6-3-6- HashMap 和 Hashtable 的区别

==线程、效率 -> 初始容量、扩充容量 -> 底层数据结构解决 hash 冲突 -> 扰动函数==

- <font color="red">线程</font>是否安全： `HashMap` 是非线程安全的，`Hashtable` 是线程安全的,因为 `Hashtable` 内部的方法基本都经过`synchronized` 修饰
- <font color="red">效率</font>： 因为线程安全的问题，`HashMap` 要比 `Hashtable` 效率高一点。另外，`Hashtable` 基本被淘汰，不要在代码中使用它
- 对 <font color="red">Null</font> key 和 Null value 的支持：`HashMap` 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个；Hashtable 不允许有 null 键和 null 值，否则会抛出 `NullPointerException`
- <font color="red">初始容量</font>大小和每次<font color="red">扩充容量</font>大小的不同： 
    - 创建时如果不指定容量初始值，`Hashtable` 默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。`HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍
    - 创建时如果给定了容量初始值，那么 `Hashtable` 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小。也就是说 `HashMap` 总是使用 2 的幂作为哈希表的大小
- <font color="red">底层数据结构</font>： JDK1.8 以后的 `HashMap` 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树），以减少搜索时间。`Hashtable` 没有这样的机制
- <font color="red">哈希函数</font>的实现：`HashMap` 对哈希值进行了高位和低位的混合扰动处理以减少冲突，而 `Hashtable` 直接使用键的 `hashCode()` 值



##### 3-2-6-3-7- HashMap 和 HashSet 区别

`HashSet` 底层就是基于 `HashMap` 实现的

| HashMap                                                | HashSet                                                      |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| 实现了 `Map` <font color="red">接口</font>             | 实现 `Set` 接口                                              |
| <font color="red">存储</font>键值对                    | 仅存储对象                                                   |
| 调用 `put()`向 map 中<font color="red">添加</font>元素 | 调用 `add()`方法向 `Set` 中添加元素                          |
| `HashMap` 使用键（Key）计算 `hashcode`                 | `HashSet` 使用成员对象来计算 `hashcode` 值，对于两个对象来说 `hashcode` 可能相同，所以`equals()`方法用来判断对象的相等性 |



##### 3-2-6-3-8- HashMap 和 TreeMap 区别

- `TreeMap` 和`HashMap` 都继承自`AbstractMap` ，但是需要注意的是`TreeMap`它还实现了`NavigableMap`接口和`SortedMap` 接口
- `NavigableMap` 接口提供了丰富的方法来探索和操作键值对
- 实现`SortedMap`接口让 `TreeMap` 有了对集合中的元素根据<font color="red">键排序</font>的能力



# 4-并发编程

## 4-1-线程

### 4-1-1-什么是进程和线程

- 进程：
    - 进程是程序的一次执行过程，是系统运行程序的<font color="red">基本单位</font>，因此进程是动态的
    - 系统运行一个程序即是一个进程从创建，运行到消亡的<font color="red">过程</font>
- 线程:
  - 线程与进程相似，但线程是一个比进程<font color="red">更小的执行单位</font>
  - 一个进程在其执行的过程中可以产生多个线程。与进程不同的是同类的多个线程<font color="red">共享</font>进程的**堆**和**方法区**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**，所以系统在产生一个线程，或是在各个线程之间做切换工作时，负担要比进程小得多，也正因为如此，线程也被称为轻量级进程
- 区别：
    - 线程是进程划分成的更小的运行单位
    - 线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反



### 4-1-2-如何创建线程

- 一般来说，创建线程有很多种方式
    - 继承`Thread`类，重写 run 方法，创建这个类的对象，调用 start 方法，启动线程
    - 实现`Runnable`接口，重写 run 方法，创建这个类的对象，传入这个对象并创建Thread对象，调用 start 方法，启动线程
    - 实现`Callable`接口
    - 使用线程池
    - 使用`CompletableFuture`类等等
- 不过，这些方式其实并没有真正创建出线程。准确点来说，这些都属于是在 Java 代码中使用多线程的方法
- 严格来说，Java 就只有一种方式可以创建线程，那就是通过`new Thread().start()`创建。不管是哪种方式，最终还是依赖于`new Thread().start()`



### 4-1-3- runnable 和 callable 有什么区别

- Runnable 接口run方法没有<font color="red">返回值</font>；Callable接口call方法有返回值，是个泛型，和Future、FutureTask配合可以用来获取异步执行的结果
- Callalbe接口支持返回执行结果，需要调用FutureTask.get()得到，此方法会阻塞主进程的继续往下执行，如果不调用不会阻塞
- Callable接口的call()方法<font color="red">允许抛出异常</font>；而Runnable接口的run()方法的异常只能在内部消化，不能继续上抛



### 4-1-4-线程的 run()和 start()有什么区别

- start()：用来启动线程，通过该线程调用run方法执行run方法中所定义的逻辑代码。start方法只能被<font color="red">调用一次</font>
- run()：封装了要被线程执行的代码，可以被<font color="red">调用多次</font>



### 4-1-5-说说线程的生命周期和状态

![image-20250605172646153](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250605172646153.png)

Java 线程在运行的生命周期中的指定时刻只可能处于下面 6 种不同状态的其中一个状态：

- NEW: 初始状态，线程被创建出来但没有被调用 `start()` 
- RUNNABLE: 运行状态，线程被调用了 `start()`等待运行的状态
- BLOCKED：阻塞状态，需要等待锁释放
- WAITING：等待状态，表示该线程需要等待其他线程做出一些特定动作（通知或中断）
- TIME_WAITING：超时等待状态，可以在指定的时间后自行返回而不是像 WAITING 那样一直等待
- TERMINATED：终止状态，表示该线程已经运行完毕



### 4-1-6-状态之间是如何变化的

- 当一个线程对象被创建，但还未调用 start 方法时处于**新建**状态，调用了 start 方法，就会由**新建**进入**可运行**状态。如果线程内代码已经执行完毕，由**可运行**进入**终结**状态。当然这些是一个线程正常执行情况
- 如果线程获取锁失败后，由**可运行**进入 Monitor 的阻塞队列**阻塞**，只有当持锁线程释放锁时，会按照一定规则唤醒阻塞队列中的**阻塞**线程，唤醒后的线程进入**可运行**状态
- 如果线程获取锁成功后，但由于条件不满足，调用了 wait() 方法，此时从**可运行**状态释放锁**等待**状态，当其它持锁线程调用 notify() 或 notifyAll() 方法，会恢复为**可运行**状态
- 还有一种情况是调用 sleep(long) 方法也会从**可运行**状态进入**有时限等待**状态，不需要主动唤醒，超时时间到自然恢复为**可运行**状态



### 4-1-7-新建 T1、T2、T3 三个线程，如何保证它们按顺序执行

在多线程中有多种方法让线程按特定顺序执行，你可以用线程类的**join**()方法在一个线程中启动另一个线程，另外一个线程完成该线程继续执行



### 4-1-8- notify()和 notifyAll()有什么区别

- notifyAll()：唤醒所有wait的线程
- notify()：只随机唤醒一个 wait 线程



### 4-1-9-聊聊 wait() 和 sleep() 方法

共同点

- wait() ，wait(long) 和 sleep(long) 的效果都是让当前线程暂时放弃 CPU 的使用权，进入<font color="red">等待状态</font>

不同点

- 方法归属不同
    - sleep(long) 是 Thread 的<font color="red">静态方法</font>
    - 而 wait()，wait(long) 都是 Object 的<font color="red">成员方法</font>，每个对象都有
- <font color="red">醒来时机</font>不同
    - 执行 sleep(long) 和 wait(long) 的线程都会在等待相应毫秒后醒来
    - wait(long) 和 wait() 还可以被 notify 唤醒，wait() 如果不唤醒就一直等下去
    - 它们都可以被打断唤醒
- <font color="red">锁特性</font>不同（重点）
    - wait 方法的调用必须先获取 wait 对象的锁，而 sleep 则无此限制
    - wait 方法执行后会释放对象锁，允许其它线程获得该对象锁（我放弃 cpu，但你们还可以用）
    - 而 sleep 如果在 synchronized 代码块中执行，并不会释放对象锁（我放弃 cpu，你们也用不了）



### 4-1-10-如何停止一个正在运行的线程

有三种方式可以停止线程

- 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止
- 使用<font color="red">stop方法</font>强行终止（不推荐，方法已作废）
- 使用<font color="red">interrupt方法</font>中断线程



## 4-2-多线程

### 4-2-1-并发与并行的区别

- **并发**：两个及两个以上的作业在同一<font color="red">时间段</font>内执行
- **并行**：两个及两个以上的作业在同一<font color="red">时刻</font>执行



### 4-2-2-什么是死锁

多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放。由于线程被无限期地阻塞，因此程序不可能正常终止



## 4-3-线程安全



## 4-4- ThreadLocal

### 4-4-1-说明

- 通常情况下，我们创建的变量可以被任何一个线程访问和修改。在多线程环境中可能导致<font color="red">数据竞争和线程安全问题</font>。JDK 中提供的 `ThreadLocal` 类是为了解决这个问题
- `ThreadLocal` 类允许每个线程绑定自己的值，用于存储<font color="red">私有数据</font>，确保不同线程之间的数据互不干扰
- 创建一个 `ThreadLocal` 变量时，每个访问该变量的线程都会拥有一个独立的副本，线程可以通过 `get()` 方法获取自己线程的本地副本，或通过 `set()` 方法修改该副本的值，从而避免了线程安全问题



### 4-4-2- ThreadLocal 原理了解吗

==Thread成员变量（类型和默认值） -> ThreadLocal的set方法（现实） -> ThreadLocalMap（类、与Thread的关系和set方法的实现）==

1）`Thread`成员变量

```java
public class Thread implements Runnable {
    //......
    //与此线程有关的ThreadLocal值。由ThreadLocal类维护
    ThreadLocal.ThreadLocalMap threadLocals = null;

    //与此线程有关的InheritableThreadLocal值。由InheritableThreadLocal类维护
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    //......
}
```

- `Thread` 类中有一个 `threadLocals` 和 一个 `inheritableThreadLocals` （/ɪnˈherɪtəbl/）变量，它们都是 `ThreadLocalMap` 类型的变量,我们可以把 `ThreadLocalMap` 理解为`ThreadLocal` 类实现的定制化的 `HashMap`
- 默认情况下这两个变量都是 <font color="red">null</font>，只有当前线程调用 `ThreadLocal` 类的 `set`或`get`方法时才创建它们

2）`ThreadLocal`的`set()`方法

```java
public void set(T value) {
    //获取当前请求的线程
    Thread t = Thread.currentThread();
    //取出 Thread 类内部的 threadLocals 变量(哈希表结构)
    ThreadLocalMap map = getMap(t);
    if (map != null)
        // 将需要存储的值放入到这个哈希表中
        map.set(this, value);
    else
        createMap(t, value);
}
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
```

- 通过`Thread.currentThread()`获取到当前线程对象后，直接通过`getMap(Thread t)`可以访问到该线程的`ThreadLocalMap`对象
- 底层调用`ThreadLocalMap`的`set()`和`createMap()`方法

3）`ThreadLocalMap`

- `ThreadLocalMap`是`ThreadLocal`的静态内部类
- 每个`Thread`中都具备一个`ThreadLocalMap`，可以存储以`ThreadLocal`为 key ，Object 对象为 value 的键值对
- 我们在同一个线程中声明了两个 `ThreadLocal` 对象的话， `Thread`内部都是使用仅有的那个`ThreadLocalMap` 存放数据的，`ThreadLocalMap`的 key 就是 `ThreadLocal`对象，value 就是 `ThreadLocal` 对象调用`set`方法设置的值
- 从`ThreadLocal`的`set()`方法的内部实现出发： `ThreadLocal` 实例本身作为 key，要存储的值作为 value，作为参数进行传入。在代码内部使用了`ThreadLocalMap` 的 `set()` 和 `createMap()` 方法，这两个方法中，并没有直接存储 `ThreadLocal` 对象本身，而是使用 `ThreadLocal` 的哈希值计算<font color="red">数组索引</font>，最终存储于类型为`static class Entry extends WeakReference<ThreadLocal<?>>`的数组中

![image-20250610101723058](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250610101723058.png)



### 4-4-3- ThreadLocal 内存泄漏问题是怎么导致的

==原因 -> 键值引用机制 -> 结合线程生命周期，阐述原因 -> 如何避免==

- 原因：`ThreadLocal` 内存泄漏的根本原因在于`ThreadLocalMap`的<font color="red">键值的引用机制</font>和<font color="red">线程的生命周期</font>共同导致
- `ThreadLocalMap` 的 `key` 和 `value` <font color="red">引用机制</font>：
    - key 是弱引用：`ThreadLocalMap` 中的 key 是 `ThreadLocal` 的弱引用 (`WeakReference<ThreadLocal<?>>`)。 如果 `ThreadLocal` 实例不再被任何强引用指向，垃圾回收器会在下次 GC 时回收该实例，导致 `ThreadLocalMap` 中对应的 key 变为 `null`
    - value 是强引用：即使 `key` 被 GC 回收，`value` 仍然被 `ThreadLocalMap.Entry` 强引用存在，无法被 GC 回收
- 内存泄漏的发生需要同时满足两个条件：当 `ThreadLocal` 实例<font color="red">失去强引用</font>后，其对应的 value 仍然存在于 `ThreadLocalMap` 中。如果线程<font color="red">持续存活</font>，`ThreadLocalMap` 也会一直存在，导致 key 为 `null` 的 entry 无法被垃圾回收，即会造成内存泄漏
- <font color="red">避免</font>内存泄漏的发生：
    - 在使用完 `ThreadLocal` 后，务必调用 `remove()` 方法。 `remove()` 方法会从 `ThreadLocalMap` 中显式地移除对应的 entry，彻底解决内存泄漏的风险
    - 在线程池等线程复用的场景下，使用 `try-finally` 块可以确保即使发生异常，`remove()` 方法也一定会被执行



## 4-5-乐观锁和悲观锁

### 4-5-1-悲观锁

- 悲观锁总是假设最坏的情况，<font color="red">认为共享资源每次被访问的时候就会出现问题</font>(比如共享数据被修改)，所以每次在获取资源操作的时候都会上锁，这样其他线程想拿到这个资源就会阻塞直到锁被上一个持有者释放
- 像 Java 中`synchronized`和`ReentrantLock`等独占锁就是悲观锁思想的实现
- 缺点：高并发的场景下，激烈的锁竞争会造成线程阻塞，大量阻塞线程会导致系统的上下文切换，增加系统的<font color="red">性能开销</font>。并且，悲观锁还可能会存在<font color="red">死锁问题</font>（线程获得锁的顺序不当时），影响代码的正常运行



### 4-5-2-乐观锁

- 乐观锁总是假设最好的情况，认为共享资源每次被访问的时候不会出现问题，线程可以不停地执行，无需加锁也无需等待，只是在<font color="red">提交修改的时候去验证对应的资源</font>（也就是数据）是否被其它线程修改了（具体方法可以使用版本号机制或 CAS 算法）
- 在 Java 中`java.util.concurrent.atomic`包下面的原子变量类（比如`AtomicInteger`、`LongAdder`）就是使用了乐观锁的一种实现方式 **CAS** 实现的
- 缺点：高并发的场景下，乐观锁相比悲观锁来说，不存在锁竞争造成<font color="red">线程阻塞</font>，也不会有<font color="red">死锁问题</font>，在性能上往往会更胜一筹。但是，如果冲突频繁发生（<font color="red">写占比</font>非常多的情况），会频繁失败并重试，这样同样会非常影响性能，导致 CPU 飙升



### 4-5-3-两者如何选择

- 悲观锁通常多用于<font color="red">写比较多</font>的情况，这样可以<font color="red">避免频繁失败和重试</font>影响性能，悲观锁的<font color="red">开销是固定</font>的
- 乐观锁通常多用于<font color="red">写比较少</font>的情况，这样可以<font color="red">避免频繁加锁</font>影响性能



### 4-5-4-如何实现乐观锁

乐观锁一般会使用版本号机制或 CAS 算法实现，CAS 算法相对来说更多一些

#### 4-5-4-1-版本号机制

一般是在数据表中加上一个数据版本号 `version` 字段，表示数据被修改的次数。当数据被修改时，`version` 值会加一。当线程 A 要<font color="red">更新数据值之前</font>时，在读取数据的同时也会读取 `version` 值，在<font color="red">提交更新</font>时，若刚才读取到的 version 值为当前数据库中的 `version` 值相等时才更新，否则<font color="red">重试更新</font>操作，直到更新成功



#### 4-5-4-2- CAS 算法

- CAS 的全称是 **Compare And Swap（比较与交换）** ，用于实现乐观锁，被广泛应用于各大框架中。CAS 的思想很简单，就是用一个预期值和要更新的变量值进行比较，两值相等才会进行更新
- CAS 是一个<font color="red">原子操作</font>，底层依赖于一条 CPU 的原子指令



## 4-6- synchronized 关键字

### 4-6-1-说明

- 主要解决的是多个线程之间访问资源的<font color="red">同步性</font>，可以保证被它修饰的方法或者代码块<font color="red">在任意时刻只能有一个线程执行</font>
- 在 Java 早期版本中，`synchronized` 属于<font color="red">重量级锁</font>，效率低下。这是因为监视器锁（monitor）是依赖于底层的操作系统的 `Mutex Lock` 来实现的，Java 的线程是<font color="red">映射到操作系统的原生线程</font>之上的。如果要挂起或者唤醒一个线程，都<font color="red">需要操作系统帮忙</font>完成，而操作系统实现线程之间的切换时需要<font color="red">从用户态转换到内核态</font>，这个状态之间的转换需要相对比较长的时间，时间成本相对较高
- 在 Java 6 之后， `synchronized` 引入了大量的<font color="red">优化</font>如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销，这些优化让 `synchronized` 锁的效率提升了很多。因此， `synchronized` 还是可以在实际项目中使用的，像 JDK 源码、很多开源框架都大量使用了 `synchronized`



### 4-6-2-如何使用 synchronized

`synchronized` 关键字的使用方式主要有下面 3 种：

1. 修饰实例方法（锁当前对象实例）
2. 修饰静态方法（锁当前类）
3. 修饰代码块（锁指定对象/类）



### 4-6-3-构造方法可以用 synchronized 修饰么

构造方法<font color="red">不能</font>使用 synchronized 关键字修饰。不过，可以在构造方法<font color="red">内部</font>使用 synchronized 代码块









# 5-反射

定义：反射(Reflection)是Java语言的一个强大特性，==它允许程序在运行时动态地获取类的信息（字段、方法和构造函数等），并操作类或对象==



## 5-1-反射机制的优缺点

- 优点：
    - 可以在运行时动态操作类和对象，提高了程序的<font color="red">灵活性和扩展性</font>
- 缺点：
	- <font color="red">性能开销</font>：反射操作比直接调用慢
	- <font color="red">安全限制</font>：可能破坏封装性，访问私有成员；比如可以无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）
	- 代码复杂度：反射代码更难以理解和维护
	- 内部暴露：可能暴露不应暴露的细节



## 5-2-获取 Class 对象的四种方式

1）知道具体类的情况下可以使用：

```java
Class alunbarClass = TargetObject.class;
```

但是我们一般是不知道具体类的，基本都是通过遍历包下面的类来获取 Class 对象，通过此方式获取 Class 对象不会进行初始化

2）通过 `Class.forName()`传入类的全路径获取：

```java
Class alunbarClass1 = Class.forName("cn.javaguide.TargetObject");
```

3）通过对象实例`instance.getClass()`获取：

```java
TargetObject o = new TargetObject();
Class alunbarClass2 = o.getClass();
```

4）通过类加载器`xxxClassLoader.loadClass()`传入类路径获取:

```java
ClassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");
```

通过类加载器获取 Class 对象不会进行初始化，意味着不进行包括初始化等一系列步骤，静态代码块和静态对象不会得到执行



# 6-代理

- 代理(Proxy)是Java中一种重要的设计模式，它==允许使用代理对象来代替对真实对象(real object)的访问，这样就可以在不修改原目标对象的前提下，提供额外的功能操作，扩展目标对象的功能==
- Java提供了多种实现代理的方式，主要包括静态代理和动态代理两大类



## 6-1-静态代理

- 静态代理中，我们对目标对象的每个<font color="red">方法的增强都是手动完成</font>的，非常不灵活（比如接口一旦新增加方法，目标对象和代理对象都要进行修改）且麻烦(需要对每个目标类都单独写一个代理类），实际应用场景非常非常少
- 静态代理实现步骤:
    1. 定义一个接口及其实现类
    2. 创建一个代理类同样实现这个接口
    3. 将目标对象注入进代理类，然后在代理类的对应方法调用目标类中的对应方法。这样的话，我们就可以通过代理类屏蔽对目标对象的访问，并且可以在目标方法执行前后做一些自己想做的事情



## 6-2-动态代理

- 相比于静态代理来说，动态代理更加灵活。我们<font color="red">不需要针对每个目标类都单独创建一个代理类</font>，并且也<font color="red">不需要我们必须实现接口</font>，我们可以直接代理实现类（CGLIB 动态代理机制）
- 从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的
- 就 Java 来说，动态代理的实现方式有很多种，比如 JDK 动态代理、CGLIB 动态代理等等



### 6-2-1- JDK 动态代理机制

- 在 Java 动态代理机制中 `InvocationHandler` 接口和 `Proxy` 类是核心
- JDK 动态代理类使用步骤：
    1. 定义一个接口及其实现类
    2. 自定义类，实现 `InvocationHandler` 接口，并重写`invoke`方法，在 `invoke` 方法中我们会调用原生方法（被代理类的方法）并自定义一些处理逻辑
    3. 通过 `Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)` 方法创建代理对象



### 6-2-2- CGLIB 动态代理机制

- JDK 动态代理有一个最致命的问题是其==只能代理实现了接口的类==，为了解决这个问题，我们可以用 CGLIB 动态代理机制来避免
- 在 CGLIB 动态代理机制中 `MethodInterceptor` 接口和 `Enhancer` 类是核心
- CGLIB 动态代理类使用步骤：
    1. 定义一个类
    2. 自定义类，实现 `MethodInterceptor`接口，并重写 `intercept` 方法，`intercept` 用于拦截增强被代理类的方法，和 JDK 动态代理中的 `invoke` 方法类似
    3. 通过 `Enhancer` 类的 `create()`创建代理类



### 6-2-3- JDK 动态代理和 CGLIB 动态代理对比

- JDK 动态代理只能代理实现了<font color="red">接口</font>的类或者直接代理接口，而 CGLIB 可以代理未实现任何接口的类
- CGLIB 动态代理是通过==生成一个被代理类的子类==来拦截被代理类的方法调用，因此不能代理声明为 final 类型的类和方法
- 就二者的<font color="red">效率</font>来说，大部分情况都是 JDK 动态代理更优秀，随着 JDK 版本的升级，这个优势更加明显
- Spring 中的 AOP 模块中：如果目标对象实现了接口，则默认采用 JDK 动态代理，否则采用 CGLIB 动态代理



# 7- JVM

![image-20250611161132253](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250611161132253.png)

## 7-1- JVM组成

### 7-1-1-程序计数器

- 定义：程序计数器(Program Counter Register)是一块较小的内存空间，<font color="red">线程私有</font>的，每个线程一份，它<font color="red">可以看作是当前线程所执行的字节码的行号指示器</font>。也就是说，它<font color="red">记录了当前线程执行到哪条字节码指令的位置</font>
- 作用：
    - <font color="red">字节码解释器</font>通过改变程序计数器来依次读取指令，从而<font color="red">实现代码的流程控制</font>，如：顺序执行、选择、循环、异常处理
    - 在<font color="red">多线程</font>的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候从这个位置开始执行



### 7-1-2- Java 虚拟机栈

==栈和线程关系（私有和生命周期） -> 栈和方法关系（栈帧的组成、方法通过栈来实现、弹出） -> 可能出现的问题==

- 与程序计数器一样，Java 虚拟机栈（后文简称栈）也是<font color="red">线程私有</font>的，它的<font color="red">生命周期</font>是随着线程的创建而创建，随着线程的死亡而死亡
- <font color="red">栈由一个个栈帧组成</font>，而每个栈帧用于存储，局部变量表、操作数栈、动态链接、方法返回地址
- 栈是 JVM 运行时数据区域的一个核心，除了一些 Native 方法调用是通过本地方法栈实现的，其他所有的 Java <font color="red">方法调用都是通过栈来实现</font>的（也需要和其他运行时数据区域比如程序计数器配合）
- 方法调用的数据需要通过栈进行传递，每一次方法调用都会有一个对应的栈帧被压入栈中，每一个方法调用结束后，都会有一个栈帧被弹出栈、动态链接、方法返回地址
- 程序运行中栈可能会出现两种错误：
    - **`StackOverFlowError`：** 如果栈的内存大小不允许动态扩展，那么当线程请求栈的深度超过当前 Java 虚拟机栈的最大深度的时候，就抛出 `StackOverFlowError` 错误
    - **`OutOfMemoryError`：** 如果栈的内存大小可以动态扩展， 那么当虚拟机在动态扩展栈时无法申请到足够的内存空间，则抛出`OutOfMemoryError`异常



### 7-1-3-堆

==定义 -> 划分 -> 成长周期 -> OOM==

- Java 虚拟机所管理的<font color="red">内存中最大</font>的一块，Java 堆是所有<font color="red">线程共享</font>的一块内存区域，在虚拟机<font color="red">启动时创建</font>，<font color="red">存放对象实例以及数组</font>
- 在 JDK 7 版本及 JDK 7 版本之前，<font color="red">堆内存被划分为下面三部分</font>：
    - 新生代内存(Young Generation)， 包含Eden 区、两个 Survivor 区 S0 和 S1
    - 老生代(Old Generation)
    - 永久代(Permanent Generation)
- JDK 8 版本之后 <font color="red">PermGen(永久代) 已被 Metaspace(元空间) 取代</font>，元空间使用的是本地内存
- <font color="red">成长周期</font>：大部分情况，对象都会首先在 Eden 区域分配，在一次新生代垃圾回收后，如果对象还存活，则会进入 S0 或者 S1，并且对象的年龄还会加 1(Eden 区->Survivor 区后对象的初始年龄变为 1)，当它的年龄增加到一定程度（默认为 15 岁），就会被晋升到老年代中。对象晋升到老年代的年龄阈值，可以通过参数 `-XX:MaxTenuringThreshold` 来设置。不过，设置的值应该在 0-15，否则会爆错
- 堆这里最容易出现的就是 `OutOfMemoryError` 错误，并且出现这种错误之后的表现形式还会有几种，比如：
    1. **`java.lang.OutOfMemoryError: GC Overhead Limit Exceeded`**：当 JVM 花太多时间执行垃圾回收并且只能回收很少的堆空间时，就会发生此错误。
    2. **`java.lang.OutOfMemoryError: Java heap space`** :假如在创建新的对象时, 堆内存中的空间不足以存放新创建的对象, 就会引发此错误



### 7-1-4-方法区

==线程共享 -> 存储类相关信息 -> 方法区、永久代和元空间的关系==

- 方法区属于是 JVM 运行时数据区域的一块逻辑区域，是<font color="red">各个线程共享的内存区域</font>

- 当虚拟机要使用一个类时，它需要读取并解析 Class 文件获取相关信息，再将信息存入到方法区。方法区会存储已被虚拟机加载的类信息、字段信息、方法信息、常量、静态变量、即时编译器编译后的代码缓存等数据

- 方法区和永久代以及元空间是什么关系呢？ 方法区和永久代以及元空间的关系很像 Java 中接口和类的关系，类实现了接口，这里的类就可以看作是永久代和元空间，接口可以看作是方法区，也就是说永久代以及元空间是 HotSpot 虚拟机对虚拟机规范中方法区的两种实现方式。并且，永久代是 JDK 1.8 之前的方法区实现，JDK 1.8 及以后方法区的实现变成了元空间

    







## 7-2- 类加载器









## 7-3- 垃圾回收机制

