[【面试题】说说synchronized关键字的底层原理是什么？_下synchronized关键字的底层原理面试题-CSDN博客](https://blog.csdn.net/ksws01/article/details/110733782?csdn_share_tail={"type"%3A"blog"%2C"rType"%3A"article"%2C"rId"%3A"110733782"%2C"source"%3A"m0_64999699"}&fromshare=blogdetail)

[面试官：请详细说下synchronized的实现原理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/377423211)

[synchronized底层原理是什么？ - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/343305760)

在可见性问题中：主存是指内存，工作内存是指cpu 缓存。为什么用sout输出就不会导致程序一直运行不停下，是因为sout中是带锁的，[synchronized 如何保证可见性的？_synchronized保证可见性-CSDN博客](https://blog.csdn.net/m0_54187478/article/details/134326089)，[字节面试官：synchronized能保证可见性吗_synchronized可以保证可见性吗-CSDN博客（看评论区）](https://blog.csdn.net/qq_32273417/article/details/109148693)

synchronized,在有序性问题上，不能真正的阻止指令重排。p150

Java工作内存与主内存同步时机?

> [JVM系列——详细说明Java内存模型（JMM）之主/工作内存与内存操作指令_jvm load store assign-CSDN博客](https://blog.csdn.net/zekser/article/details/129402884)
>
> 先看上面文章的各种指令，然后再看下面刷新的时机，会更清楚
>
> [工作内存于主内存之间相互刷新的时机_java工作内存与主内存之间相互刷新的时机-CSDN博客](https://blog.csdn.net/qq_45331503/article/details/114374039)
>
> [java工作内存与主内存之间相互刷新的时机_共享内存刷新到主存-CSDN博客](https://blog.csdn.net/zzwpublic/article/details/131146989)
>
> [深入思考jvm虚拟机的线程工作内存到底拷贝了 主内存的 什么？，以及volatile修饰对象和基本类型的区别_volatile加在基本类型和对象上的区别-CSDN博客](https://blog.csdn.net/changqijihua/article/details/97510035)



[设计模式：单例模式（反射破坏，枚举不能被破坏的原因）_为什么枚举搭单例不会反射-CSDN博客](https://blog.csdn.net/weixin_42740540/article/details/120890967)

[为什么要用枚举实现单例模式（避免反射、序列化问题） - 李子沫 - 博客园 (cnblogs.com)](https://www.cnblogs.com/chiclee/p/9097772.html)

CAS的原子性是操作系统保证的。可见性是和volatile互相配合来保证。

[new一个对象，分配空间和初始化的关系](https://blog.csdn.net/x1064320359/article/details/120882354)

[函数式接口详解（Java）_函数式接口作为参数-CSDN博客](https://blog.csdn.net/hbdhaj/article/details/119564310)

单个线程不可能发生竞争，竞争只发生在多线程之间

[【优雅的避坑】不安全！别再共享SimpleDateFormat变量了 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/267822207)

[final 变量可以修改_修改final变量-CSDN博客](https://blog.csdn.net/hanshengjian/article/details/86607383)

final变量只能在构造方法中初始化

[【小家java】java中为final变量赋值的几种方式_final变量如何赋值-CSDN博客](https://blog.csdn.net/f641385712/article/details/82082038)

>被final修饰的变量：三种赋值方式
>
>1. 在定义时直接赋值。
>
>2. 声明时不赋值，在constructor中赋值（最常用的方式）
>
>3. 声明时不赋值，在构造代码块中赋值

p196 cas适用于短时间的代码片段，因为他需要不断地空转来获取连接。理解：运行时间短才可以在短时间内就释放连接，然后自己再去抢占连接。但是数据库操作是一个很长的过程，增删查改会占用连接很长一段时间。这段时间cpu都在空转，所以需要借助锁来使得没有连接的线程进入waiting

[什么是线程池，连接池，线程池和连接池之间的区别_线程池与数据库连接池的区别-CSDN博客](https://blog.csdn.net/weixin_45151960/article/details/104759887)。线程和数据库连接，数据库连接不是线程，而是一种资源。（个人从p263中得到的结论，应该不太对）

[通俗易懂读写锁ReentrantReadWriteLock的使用 - 掘金 (juejin.cn)](https://juejin.cn/post/7154295629148061726)

>重入时锁升级不支持：持有读锁的情况下去获取写锁会导致获取写锁永久等待，需要先释放读，再去获得写重入时锁降级支持：持有写锁的情况下去获取读锁，**造成只有当前线程会持有读锁，因为写锁会互斥其他的锁**

注意：读写锁只是不让锁升级，但是还是支持正常重入的，一个线程本来就有写锁，那么这个线程再获取写锁时，也是允许的

区分开重入和重用

 CyclicBarrier可以重用，是当技术个数为0时，再次调用await时就开始重新计数。

[JDK7的HashMap头插法循环的问题，这么难理解吗？_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1n541177Ea/?vd_source=15d6643d5200099ef016c9fd2abfa3c0)

> 这个视频我的理解有三处要说明一下！第一处把第二个线程for 循环更正成while循环。第二处：put只形成了循环的链表,还是能正常退出，因为最后一步的next=null赋值给e可以正常退出while循环。死循环CPU100%这个大冤种要到get的时候才会显现。第三处：就是table=newtable 这个是第一个线程执行后赋值了，所以引用变了。这是我的理解，有错请指正。

推荐使用linkedblockingqueue

[Java 局部内部类访问局部变量为什么必须加final关键字_加final和不加final-CSDN博客](https://blog.csdn.net/BruceLiu_code/article/details/118416150)

[深入解析synchronized实现原理，如何保证原子性、有序性和可见性？_编译器原子性是如何实现的-CSDN博客](https://blog.csdn.net/qq_36270361/article/details/107708132)

>synchronized`虽然能够保证有序性，但是`无法禁止指令重排和处理器优化的

[【Java核心能力】面试官：synchronized 关键字可以保证可见性吗？-CSDN博客](https://blog.csdn.net/qq_45260619/article/details/136314510#:~:text=那么 synchronized 保证可见性其实也就是通过 内存屏障 来保证的，在进入 synchronized,代码块和退出的时候，都会插入内存屏障，目的就是保证在进入的时候，强制去执行 refresh 操作，这样可以保证读取到变量的最新值，而在退出 synchronized 代码块时，也就是对变量的修改已经完成了，此使强制去执行 flush 操作，可以保证将变量的最新值给刷新到主内存中去)

[ThreadLocal全面解析_autocloseable threadlocal-CSDN博客](https://blog.csdn.net/Beyondczn/article/details/107132337)

[史上最全ThreadLocal 详解（一）-CSDN博客](https://blog.csdn.net/u010445301/article/details/111322569)
