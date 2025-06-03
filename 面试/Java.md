# 1-算法复杂度分析

## 1-1-时间复杂度分析

### 1-1-1-大O表示法

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



### 1-1-2-常见复杂度表示形式

常对幂指阶

![image-20250526212329833](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250526212329833.png)

![image-20250526213429882](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250526213429882.png)



## 1-2-空间复杂度分析

空间复杂度全称是渐进空间复杂度，表示==算法占用的额外存储空间与数据规模之间的增长关系==



# 2-集合

![image-20250528162610034](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528162610034.png)

![image-20250528161203043](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250528161203043.png)

Java 集合，也叫作容器，主要是由两大接口派生而来：一个是 `Collection`接口，主要用于存放单一元素；另一个是 `Map` 接口，主要用于存放键值对。对于`Collection` 接口，下面又有三个主要的子接口：`List`、`Set` 、 `Queue`



## 2-1-Collection

### 2-1-1-List

#### 2-1-1-1-Array（数组）

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
- 如果数组的索引从1开始，寻址公式中，就需要增加一次减法操作，对于CPU来说就多一次指令，性能不高



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



#### 2-1-1-2-ArrayList源码分析

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



#### 2-1-1-3-ArrayList面试题

##### 2-1-1-3-1-ArrayList 和 Array（数组）的区别

`ArrayList` 内部基于动态数组实现，比 `Array`（静态数组） 使用起来更加灵活：

- <font color="red">创建</font>：`ArrayList`创建时不需要指定大小，而`Array`创建时必须指定大小
- <font color="red">扩容</font>：`ArrayList`会根据实际存储的元素动态地扩容或缩容，而 `Array` 被创建之后就不能改变它的长度了
- <font color="red">存储类型</font>：`ArrayList` 中只能存储对象。对于基本类型数据，需要使用其对应的包装类（如 Integer、Double 等）。`Array` 可以直接存储基本类型数据，也可以存储对象
- <font color="red">类型安全</font>：`ArrayList` 允许你使用泛型来确保类型安全，`Array` 则不可以



##### 2-1-1-3-2-ArrayList 可以添加 null 值吗

`ArrayList` 中可以存储任何类型的对象，包括 `null` 值。不过，不建议向`ArrayList` 中添加 `null` 值， `null` 值无意义，会让代码难以维护比如忘记做判空处理就会导致空指针异常



##### 2-1-1-3-3-ArrayList 插入和删除元素的时间复杂度

对于插入：

- 头部插入：由于需要将所有元素都依次向后移动一个位置，因此时间复杂度是 O(n)
- 尾部插入：当 `ArrayList` 的容量未达到极限时，往列表末尾插入元素的时间复杂度是 O(1)，因为它只需要在数组末尾添加一个元素即可；当容量已达到极限并且需要扩容时，则需要执行一次 O(n) 的操作将原数组复制到新的更大的数组中，然后再执行 O(1) 的操作添加元素
- 指定位置插入：需要将目标位置之后的所有元素都向后移动一个位置，然后再把新元素放入指定位置。这个过程需要移动平均 n/2 个元素，因此时间复杂度为 O(n)

对于删除：

- 头部删除：由于需要将所有元素依次向前移动一个位置，因此时间复杂度是 O(n)
- 尾部删除：当删除的元素位于列表末尾时，时间复杂度为 O(1)
- 指定位置删除：需要将目标元素之后的所有元素向前移动一个位置以填补被删除的空白位置，因此需要移动平均 n/2 个元素，时间复杂度为 O(n)



#### 2-1-1-4-LinkedList源码分析

- 定义：`LinkedList` 是一个基于<font color="red">双向链表</font>实现的集合类
- 体系结构：`LinkedList` <font color="red">继承抽象类</font> `AbstractSequentialList` ，而 `AbstractSequentialList` 又继承`AbstractList`，<font color="red">实现接口</font>`List`，`Deque`，`Cloneable`，`Serializable`
    - `List` : 表明它是一个列表，支持添加、删除、查找等操作，并且可以通过下标进行访问
    - `Deque` ：继承自 `Queue` 接口，具有双端队列的特性，支持从两端插入和删除元素，方便实现栈和队列等数据结构。需要注意，`Deque` 的发音为 "deck" [dɛk]
    - `Cloneable` ：表明它具有拷贝能力，可以进行深拷贝或浅拷贝操作
    - `Serializable` : 表明它可以进行序列化操作，也就是可以将对象转换为字节流进行持久化存储或网络传输，非常方便

![image-20250529111420505](https://picgo-zjp.oss-cn-shenzhen.aliyuncs.com/image-20250529111420505.png)



#### 2-1-1-5-LinkedList面试题

##### 2-1-1-5-1-LinkedList 插入和删除元素的时间复杂度

- 头部插入/删除：只需要修改头结点的指针即可完成插入/删除操作，因此时间复杂度为 O(1)

- 尾部插入/删除：只需要修改尾结点的指针即可完成插入/删除操作，因此时间复杂度为 O(1)

- 指定位置插入/删除：需要先移动到指定位置，再修改指定节点的指针完成插入/删除，不过由于有头尾指针，可以从较近的指针出发，因此需要遍历平均 n/4 个元素，时间复杂度为 O(n)



##### 2-1-1-5-2-LinkedList 为什么不能实现 RandomAccess 接口

`RandomAccess` 是一个标记接口，用来表明实现该接口的类支持随机访问（即可以通过索引快速访问元素）。由于 `LinkedList` 底层数据结构是==链表，内存地址不连续，只能通过指针来定位，不支持随机快速访问==，所以不能实现 `RandomAccess` 接口



##### 2-1-1-5-3-ArrayList 与 LinkedList 区别和联系

底层数据结构不一样->快速随机访问、插入和删除时间复杂度、空间占用

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





## 2-2-Map



