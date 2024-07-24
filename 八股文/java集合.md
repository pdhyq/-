# 插入和删除的时间复杂度：

ArrayList：

* 插入
  * 头插：O(n)
  * 尾插：如果数组容量未满，时间复杂度为O(1)；若已满，需要重新分配空间，并将原数组复制到新数组中，时间复杂度为O(n)
  * 指定位置插入：O(n)
* 删除
  * 头删：O(n)
  * 尾删：O(1)
  * 指定位置删除：O(n)

LinkedList：

* 头插/删：O(1)
* 尾插/删：O(1)
* 指定位置插入/删除：O(n)

LinkedList 为什么不能实现 RandomAccess 接口

`RandomAccess` 是一个标记接口，用来表明实现该接口的类支持随机访问（即可以通过索引快速访问元素）。由于 `LinkedList` 底层数据结构是链表，内存地址不连续，只能通过指针来定位，不支持随机快速访问，所以不能实现 `RandomAccess` 接口



# ArrayList 与 LinkedList 区别?

1.ArrayList的底层数据结构是数组，而LinkedList的底层数据结构是链表	

2.ArrayList支持随机访问，而LinkedList不支持随机访问

3.LinkedList一个元素的内存占用要比ArrayList要大，因为要存储前后指针。整体上来看，ArrayList会预留一些空间，同样会占用一些空间



# ArrayList其他知识点

ArrayList和Vector的区别，Vector是线程安全的

ArrayList支持存储null

ArrayList每次扩容时扩容到原先的1.5倍



# 比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同

- `HashSet`、`LinkedHashSet` 和 `TreeSet` 都是 `Set` 接口的实现类，都能保证元素唯一，并且都不是线程安全的。
- `HashSet`、`LinkedHashSet` 和 `TreeSet` 的主要区别在于底层数据结构不同。`HashSet` 的底层数据结构是哈希表（基于 `HashMap` 实现）。`LinkedHashSet` 的底层数据结构是链表和哈希表，元素的插入和取出顺序满足 FIFO。`TreeSet` 底层数据结构是红黑树，元素是有序的，排序的方式有自然排序和定制排序。
- 底层数据结构不同又导致这三者的应用场景不同。`HashSet` 用于不需要保证元素插入和取出顺序的场景，`LinkedHashSet` 用于保证元素的插入和取出顺序满足 FIFO 的场景，`TreeSet` 用于支持对元素自定义排序规则的场景。



# ==Map==

单线程下可以容忍歧义，而多线程下无法容忍

# hashmap和hashtable的区别

* 首先是HashMap非线程安全，hashtable是线程安全的
* 因此hashmap的性能略逊一筹
* hashmap可以存储null值，hashtable不可以
* **初始容量大小和每次扩充容量大小的不同：** ① 创建时如果不指定容量初始值，`Hashtable` 默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。`HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。② 创建时如果给定了容量初始值，那么 `Hashtable` 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小（`HashMap` 中的`tableSizeFor()`方法保证，下面给出了源代码）。也就是说 `HashMap` 总是使用 2 的幂作为哈希表的大小,后面会介绍到为什么是 2 的幂次方。
* **底层数据结构：** JDK1.8 以后的 `HashMap` 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树），以减少搜索时间（后文中我会结合源码对这一过程进行分析）。`Hashtable` 没有这样的机制。

# hashmap和hashset的区别

`HashSet` 底层就是基于 `HashMap` 实现的



# HashMap 和 TreeMap 区别

`TreeMap` 和`HashMap` 都继承自`AbstractMap` ，但是需要注意的是`TreeMap`它还实现了`NavigableMap`接口和`SortedMap` 接口。

**相比于`HashMap`来说， `TreeMap` 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力。**



# HashSet 的插入冲突

无论`HashSet`中是否已经存在了某元素，`HashSet`都会直接插入，只是会在`add()`方法的返回值处告诉我们插入前是否存在相同元素。



# HashMap的底层实现

jdk1.8之前是数组+链表，之后是数组+链表+红黑树。

相比于之前的版本， JDK1.8 之后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

# HashMap死链问题

发生在扩容阶段，直接去看juc讲义。最后的结果是导致查询操作可能会陷入死循环



# HashMap为什么不是线程安全的

先是死链问题，然后是在插入数据时会有数据覆盖的风险

数据丢失举例：

不同的线程可能在不同的时间片获得 CPU 执行的机会，当前线程 1 执行完哈希冲突判断后，由于时间片耗尽挂起。线程 2 先完成了插入操作。随后，线程 1 获得时间片，由于之前已经进行过 hash 碰撞的判断，所有此时会直接进行插入，这就导致线程 2 插入的数据被线程 1 覆盖了。



# ConcurrentHashMap 和 Hashtable 的区别













