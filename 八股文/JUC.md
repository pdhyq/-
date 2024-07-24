# 进程线程的区别

[进程和线程的主要区别（总结）-CSDN博客](https://blog.csdn.net/kuangsonghan/article/details/80674777)

[线程和进程的区别（面试必备）_进程和线程的区别-CSDN博客](https://blog.csdn.net/qinian8/article/details/100779193)

[进程和线程的区别(超详细)-CSDN博客](https://blog.csdn.net/ThinkWon/article/details/102021274)

[线程与进程，你真得理解了吗_进程和线程的区别-CSDN博客](https://blog.csdn.net/mu_wind/article/details/124616643)

>* **根本区别**：进程是操作系统资源分配的基本单位，而线程是任务调度和执行的基本单位
>* **内存分配**：同一进程的线程共享本进程的地址空间和资源，而进程之间的地址空间和资源是相互独立的
>
>- **资源开销**：每个进程都有独立的代码和数据空间（程序上下文），程序之间的切换会有较大的开销；线程可以看做轻量级的进程，同一类线程共享代码和数据空间，每个线程都有自己独立的运行栈和程序计数器（PC），线程之间切换的开销小。
>
>- **包含关系**：一个进程至少有一个线程，线程是进程的一部分，所以线程也被称为轻权进程或者轻量级进程。
>- **影响关系**：一个进程崩溃后，在保护模式下其他进程不会被影响，但是一个线程崩溃可能导致整个进程被操作系统杀掉，所以多进程要比多线程健壮。
>
>* 通信方式也不同，详情看下边。正因为进程之间互不干扰，相互独立，进程的通信机制相对很复杂，譬如管道，信号，消息队列，共享内存，套接字等通信机制，而线程由于共享数据段所以通信机制很方便。



# 多线程和多进程之间的使用场景

[多线程和多进程 及其应用场景_多进程与多线程的区别以及使用场景?-CSDN博客](https://blog.csdn.net/weixin_39731083/article/details/82015830)

> 需要频繁的创建，切换和销毁就用线程
>
> 需要更稳定安全时，适合选择进程；需要速度时，选择线程更好。



[linux进程同步/通信，线程同步/通信的区别_线程同步和线程通信的区别-CSDN博客](https://blog.csdn.net/ma2595162349/article/details/80419724)

>  大家常问的是线程的同步和进程的通信，较少问线程通信和进程同步的，那它们有什么区别呢？其实它们没啥区别，线程同步和线程通信其实就是一回事，进程通信和进程同步也是一回事，不要被这些搞混了。
>
> ​       linux线程同步(通信)的几种方式：[互斥锁](https://so.csdn.net/so/search?q=互斥锁&spm=1001.2101.3001.7020)，条件变量，信号量，临界区，读写锁，自旋锁，屏障
>
> ​       linux进程通信(同步)的几种方式：管道，信号，消息队列，共享内存，信号量，套接字

# 线程之间的同步方式/通信方式

1. **互斥锁(Mutex)**：采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限。因为互斥对象只有一个，所以可以保证公共资源不会被多个线程同时访问。比如 Java 中的 `synchronized` 关键词和各种 `Lock` 都是这种机制。
2. **读写锁（Read-Write Lock）**：允许多个线程同时读取共享资源，但只有一个线程可以对共享资源进行写操作。
3. **信号量(Semaphore)**：它允许同一时刻多个线程访问同一资源，但是需要控制同一时刻访问此资源的最大线程数量。
4. **屏障（Barrier）**：屏障是一种同步原语，用于等待多个线程到达某个点再一起继续执行。当一个线程到达屏障时，它会停止执行并等待其他线程到达屏障，直到所有线程都到达屏障后，它们才会一起继续执行。比如 Java 中的 `CyclicBarrier` 是这种机制。
5. **事件(Event)** :Wait/Notify：通过通知操作的方式来保持多线程同步，还可以方便的实现多线程优先级的比较操作。



# 进程和线程的通信方式

[进程间通信与线程间通信：原理、方式与区别大揭秘-CSDN博客](https://blog.csdn.net/fudaihb/article/details/138342348)

进程：

* 管道：管道这种通信方式效率低，不适合进程间频繁地交换数据。管道是单向的，如果需要双向通信，则应该创建两个管道。
  * 命名管道
  * 匿名管道
* 信号
* 消息队列
* 信号量
* 共享变量
* 套接字





# 进程&线程

进程：

进程是程序一次程序执行的过程，是系统运行程序的基本单位，因此进程是动态的。系统运行一个程序是一个进程从创建，运行到消亡的过程。

java中启动一个main函数，就是启动了一个JVM进程，main函数所在线程就是这个进程的一个线程被叫做主线程。	

线程：

线程是一个比进程更小的执行单位。一个进程在其执行的过程中可以产生多个线程。与进程不同的是同类的多个线程共享进程的**堆**和**方法区**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**，所以系统在产生一个线程，或是在各个线程之间作切换工作时，负担要比进程小得多，也正因为如此，线程也被称为轻量级进程。这里就可以看出操作系统中，线程是系统分配资源的基本单位

[main线程和子线程之间的关系](https://blog.csdn.net/weixin_45366499/article/details/104346644)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200216183827316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTM2NjQ5OQ==,size_16,color_FFFFFF,t_70)

**一个 Java 程序的运行是 main 线程和多个其他线程同时运行**

# 进程和线程之间的关系

<img src="https://oss.javaguide.cn/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.8.png" alt="Java 运行时数据区域（JDK1.8 之后）" style="zoom: 80%;" />

从上图可以看出：一个进程中可以有多个线程，多个线程共享进程的**堆**和**方法区 (JDK1.8 之后的元空间)资源，但是每个线程有自己的程序计数器**、**虚拟机栈** 和 **本地方法栈**。

**总结：** **线程是进程划分成的更小的运行单位。线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反。**



* 程序计数器私有主要是为了**线程切换后能恢复到正确的执行位置**



- **虚拟机栈：** 每个 Java 方法在执行之前会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息。从方法调用直至执行完成的过程，就对应着一个栈帧在 Java 虚拟机栈中入栈和出栈的过程。
- **本地方法栈：** 和虚拟机栈所发挥的作用非常相似，区别是：**虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。** 在 HotSpot 虚拟机中和 Java 虚拟机栈合二为一。

* 所以，为了**保证线程中的局部变量不被别的线程访问到**，虚拟机栈和本地方法栈是线程私有的



* 堆和方法区是所有线程共享的资源，其中堆是进程中最大的一块内存，主要用于存放新创建的对象 (几乎所有对象都在这里分配内存)，方法区主要用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

# 为什么要使用多线程?

先从总体上来说：

- **从计算机底层来说：** 线程可以比作是轻量级的进程，是程序执行的最小单位,线程间的切换和调度的成本远远小于进程。另外，多核 CPU 时代意味着多个线程可以同时运行，这减少了线程上下文切换的开销。
- **从当代互联网发展趋势来说：** 现在的系统动不动就要求百万级甚至千万级的并发量，而多线程并发编程正是开发高并发系统的基础，利用好多线程机制可以大大提高系统整体的并发能力以及性能。

再深入到计算机底层来探讨：

- **单核时代**：在单核时代多线程主要是为了提高单进程利用 CPU 和 IO 系统的效率。 假设只运行了一个 Java 进程的情况，当我们请求 IO 的时候，如果 Java 进程中只有一个线程，此线程被 IO 阻塞则整个进程被阻塞。CPU 和 IO 设备只有一个在运行，那么可以简单地说系统整体效率只有 50%。当使用多线程的时候，一个线程被 IO 阻塞，其他线程还可以继续使用 CPU。从而提高了 Java 进程利用系统资源的整体效率。
- **多核时代**: 多核时代多线程主要是为了提高进程利用多核 CPU 的能力。举个例子：假如我们要计算一个复杂的任务，我们只用一个线程的话，不论系统有几个 CPU 核心，都只会有一个 CPU 核心被利用到。而创建多个线程，这些线程可以被映射到底层多个 CPU 上执行，在任务中的多个线程没有资源竞争的情况下，任务执行的效率会有显著性的提高，约等于（单核时执行时间/CPU 核心数）。

并发编程的目的就是为了能提高程序的执行效率提高程序运行速度，但是并发编程并不总是能提高程序运行速度的，而且并发编程可能会遇到很多问题，比如：内存泄漏、死锁、线程不安全等等。



# 线程上下文切换

线程在执行过程中会有自己的运行条件和状态（也称上下文），比如上文所说到过的程序计数器，栈信息等。当出现如下情况的时候，线程会从占用 CPU 状态中退出。

- 主动让出 CPU，比如调用了 `sleep()`, `wait()` 等。
- 时间片用完，因为操作系统要防止一个线程或者进程长时间占用 CPU 导致其他线程或者进程饿死。
- 调用了阻塞类型的系统中断，比如请求 IO，线程被阻塞。
- 被终止或结束运行

这其中前三种都会发生线程切换，线程切换意味着需要保存当前线程的上下文，留待线程下次占用 CPU 的时候恢复现场。并加载下一个将要占用 CPU 的线程上下文。这就是所谓的 **上下文切换**。

上下文切换是现代操作系统的基本功能，因其每次需要保存信息恢复信息，这将会占用 CPU，内存等系统资源进行处理，也就意味着效率会有一定损耗，**如果频繁切换就会造成整体效率低下**。

# 死锁

看操作系统。

# sleep() 方法和 wait() 方法对比

**共同点**：两者都可以暂停线程的执行。

**区别**：

- **`sleep()` 方法没有释放锁，而 `wait()` 方法释放了锁** 。
- `wait()` 通常被用于线程间交互/通信，`sleep()`通常被用于暂停执行。
- `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify()`或者 `notifyAll()` 方法。`sleep()`方法执行完成后，线程会自动苏醒，或者也可以使用 `wait(long timeout)` 超时后线程会自动苏醒。
- `sleep()` 是 `Thread` 类的静态本地方法，`wait()` 则是 `Object` 类的本地方法。为什么这样设计呢？下一个问题就会聊到。

# volatile

可以保证可见性和有序性，但是不能保证原子性

可见性：如果我们将变量声明为 **`volatile`** ，这就指示 JVM，这个变量是共享且不稳定的，每次使用它都到主存中进行读取。

有序性：内存屏障

# 乐观锁&悲观锁

悲观锁：

悲观锁总是假设最坏的情况，认为共享资源每次被访问的时候就会出现问题(比如共享数据被修改)，所以每次在获取资源操作的时候都会上锁，这样其他线程想拿到这个资源就会阻塞直到锁被上一个持有者释放。也就是说，**共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程**。

高并发的场景下，激烈的锁竞争会造成线程阻塞，大量阻塞线程会导致系统的上下文切换，增加系统的性能开销。并且，悲观锁还可能会存在死锁问题，影响代码的正常运行

乐观锁：

乐观锁总是假设最好的情况，认为共享资源每次被访问的时候不会出现问题，线程可以不停地执行，无需加锁也无需等待，只是在提交修改的时候去验证对应的资源（也就是数据）是否被其它线程修改了（具体方法可以使用版本号机制或 CAS 算法）。

高并发的场景下，乐观锁相比悲观锁来说，不存在锁竞争造成线程阻塞，也不会有死锁的问题，在性能上往往会更胜一筹。但是，如果冲突频繁发生（写占比非常多的情况），会频繁失败和重试，这样同样会非常影响性能，导致 CPU 飙升

> 悲观锁通常多用于写比较多的情况（多写场景，竞争激烈），这样可以避免频繁失败和重试影响性能，悲观锁的开销是固定的。不过，如果乐观锁解决了频繁失败和重试这个问题的话（比如`LongAdder`），也是可以考虑使用乐观锁的，要视实际情况而定。
>
> 乐观锁通常多用于写比较少的情况（多读场景，竞争较少），这样可以避免频繁加锁影响性能。不过，乐观锁主要针对的对象是单个共享变量（参考`java.util.concurrent.atomic`包下面的原子变量类）

# synchronized

在 Java 早期版本中，`synchronized` 属于 **重量级锁**，效率低下。这是因为监视器锁（monitor）是依赖于底层的操作系统的 `Mutex Lock` 来实现的，Java 的线程是映射到操作系统的原生线程之上的。如果要挂起或者唤醒一个线程，都需要操作系统帮忙完成，而操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高。

使用方式：

1. 修饰实例方法
2. 修饰静态方法
3. 修饰代码块

`synchronized` 同步语句块的实现使用的是 `monitorenter` 和 `monitorexit` 指令，其中 `monitorenter` 指令指向同步代码块的开始位置，`monitorexit` 指令则指明同步代码块的结束位置。

当执行 `monitorenter` 指令时，线程试图获取锁也就是获取 **对象监视器 `monitor`** 的持有权。

> 在 Java 虚拟机(HotSpot)中，Monitor 是基于 C++实现的，由[ObjectMonitor](https://github.com/openjdk-mirror/jdk7u-hotspot/blob/50bdefc3afe944ca74c3093e7448d6b889cd20d1/src/share/vm/runtime/objectMonitor.cpp)实现的。每个对象中都内置了一个 `ObjectMonitor`对象。
>
> 另外，`wait/notify`等方法也依赖于`monitor`对象，这就是为什么只有在同步的块或者方法中才能调用`wait/notify`等方法，否则会抛出`java.lang.IllegalMonitorStateException`的异常的原因。



在执行`monitorenter`时，会尝试获取对象的锁，如果锁的计数器为 0 则表示锁可以被获取，获取后将锁计数器设为 1 也就是加 1。

执行 monitorenter 获取锁：

![执行 monitorenter 获取锁](https://oss.javaguide.cn/github/javaguide/java/concurrent/synchronized-get-lock-code-block.png)

对象锁的的拥有者线程才可以执行 `monitorexit` 指令来释放锁。在执行 `monitorexit` 指令后，将锁计数器设为 0，表明锁被释放，其他线程可以尝试获取锁。

执行 monitorexit 释放锁：

![执行 monitorexit 释放锁](https://oss.javaguide.cn/github/javaguide/java/concurrent/synchronized-release-lock-block.png)

如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放为止。



`synchronized` 同步语句块的实现使用的是 `monitorenter` 和 `monitorexit` 指令，其中 `monitorenter` 指令指向同步代码块的开始位置，`monitorexit` 指令则指明同步代码块的结束位置。

`synchronized` 修饰的方法并没有 `monitorenter` 指令和 `monitorexit` 指令，取得代之的确实是 `ACC_SYNCHRONIZED` 标识，该标识指明了该方法是一个同步方法。

**不过两者的本质都是对对象监视器 monitor 的获取。**



## synchronized 和 volatile 有什么区别？

`synchronized` 关键字和 `volatile` 关键字是两个互补的存在，而不是对立的存在！

- `volatile` 关键字是线程同步的轻量级实现，所以 `volatile`性能肯定比`synchronized`关键字要好 。但是 `volatile` 关键字只能用于变量而 `synchronized` 关键字可以修饰方法以及代码块 。
- `volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保证。
- `volatile`关键字主要用于解决变量在多个线程之间的可见性，而 `synchronized` 关键字解决的是多个线程之间访问资源的同步性。



# ReentrantLock

`ReentrantLock` 实现了 `Lock` 接口，是一个可重入且独占式的锁，和 `synchronized` 关键字类似。不过，`ReentrantLock` 更灵活、更强大，增加了轮询、超时、中断、公平锁和非公平锁等高级功能。

[一张图读懂非公平锁与公平锁 - 简书 (jianshu.com)](https://www.jianshu.com/p/f584799f1c77)

[公平锁与非公平锁+效率差异原因_非公平锁为什么比公平锁快-CSDN博客](https://blog.csdn.net/rootkiss/article/details/100627224)

[24-讲一讲公平锁和非公平锁，为什么要“非公平”？_非公平锁吞吐量大于公平锁的原因-CSDN博客](https://blog.csdn.net/Rinvay_Cui/article/details/111027218)

## 公平锁和非公平锁有什么区别？

- **公平锁** : 锁被释放之后，先申请的线程先得到锁。性能较差一些，因为公平锁为了保证时间上的绝对顺序，上下文切换更频繁。
- **非公平锁**：锁被释放之后，后申请的线程可能会先获取到锁，是随机或者按照其他优先级排序的。性能更好，但可能会导致某些线程永远无法获取到锁。



# synchronized 和 ReentrantLock 有什么区别？

* 都是可重入锁

  > **可重入锁** 也叫递归锁，指的是线程可以再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个**对象锁还没有释放**，当其再次想要获取这个对象的锁的时候还是可以获取的，如果是不可重入锁的话，就会造成死锁
  >
  > 一个线程可以重复多次获取同一个锁

* synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API

  `synchronized` 是依赖于 JVM 实现的，前面我们也讲到了 虚拟机团队在 JDK1.6 为 `synchronized` 关键字进行了很多优化，但是这些优化都是在虚拟机层面实现的，并没有直接暴露给我们。

  `ReentrantLock` 是 JDK 层面实现的（也就是 API 层面，需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成），所以我们可以通过查看它的源代码，查看他的实现

* ReentrantLock比synchronized增加了一些高级功能
  * 等待可中断
  * 实现了公平锁
  * 实现可选择性通知



# ThreadLocal

作用：提供线程内的局部变量，不同的线程之间不会相互干扰，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或组件之间一些公共变量传递的复杂度。

[一文详解ThreadLocal(非常全，有内存泄漏，有面试题） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/606333372)

[关于Java中的WeakReference - 简书 (jianshu.com)](https://www.jianshu.com/p/964fbc30151a)

[ThreadLocal 内存泄漏 代码演示 实例演示_threadlocal内存泄漏示例-CSDN博客](https://blog.csdn.net/liubenlong007/article/details/107048843)

ThreadLocal 内存泄露问题是怎么导致的？

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用，而 value 是强引用。所以，如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。

这样一来，`ThreadLocalMap` 中就会出现 key 为 null 的 Entry。假如我们不做任何措施的话，value 永远无法被 GC 回收，这个时候就可能会产生内存泄露。`ThreadLocalMap` 实现中已经考虑了这种情况，在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录。使用完 `ThreadLocal`方法后最好手动调用`remove()`方法



# 线程池

这里借用《Java 并发编程的艺术》提到的来说一下**使用线程池的好处**：

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

创建线程池：

* **方式一：通过`ThreadPoolExecutor`构造函数来创建（推荐）。**
* **方式二：通过 `Executor` 框架的工具类 `Executors` 来创建。**



在《阿里巴巴 Java 开发手册》“并发处理”这一章节，明确指出线程资源必须通过线程池提供，不允许在应用中自行显式创建线程。

**为什么呢？**

> 使用线程池的好处是减少在创建和销毁线程上所消耗的时间以及系统资源开销，解决资源不足的问题。如果不使用线程池，有可能会造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题。

另外，《阿里巴巴 Java 开发手册》中强制线程池不允许使用 `Executors` 去创建，而是通过 `ThreadPoolExecutor` 构造函数的方式，**这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险**

`Executors` 返回线程池对象的弊端如下(后文会详细介绍到)：

- **`FixedThreadPool` 和 `SingleThreadExecutor`**：使用的是无界的 `LinkedBlockingQueue`，任务队列最大长度为 `Integer.MAX_VALUE`,可能堆积大量的请求，从而导致 OOM。
- **`CachedThreadPool`**：使用的是同步队列 `SynchronousQueue`, 允许创建的线程数量为 `Integer.MAX_VALUE` ，如果任务数量过多且执行速度较慢，可能会创建大量的线程，从而导致 OOM。
- **`ScheduledThreadPool` 和 `SingleThreadScheduledExecutor`** : 使用的无界的延迟阻塞队列`DelayedWorkQueue`，任务队列最大长度为 `Integer.MAX_VALUE`,可能堆积大量的请求，从而导致 OOM。



**拒绝策略：**

**`ThreadPoolExecutor.AbortPolicy`：** 抛出 `RejectedExecutionException`来拒绝新任务的处理。

**`ThreadPoolExecutor.CallerRunsPolicy`：** 调用执行自己的线程运行任务，也就是直接在调用`execute`方法的线程中运行(`run`)被拒绝的任务，如果执行程序已关闭，则会丢弃该任务。因此这种策略会降低对于新任务提交速度，影响程序的整体性能。如果您的应用程序可以承受此延迟并且你要求任何一个任务请求都要被执行的话，你可以选择这个策略。

> [线程池系列之CallerRunsPolicy()拒绝策略 - 掘金 (juejin.cn)](https://juejin.cn/post/6982123485103390734)
>
> `CallerRunsPolicy` 和其他的几个策略不同，它既不会抛弃任务，也不会抛出异常，而是将任务回退给调用者，使用调用者的线程来执行任务
>
>     public static class CallerRunsPolicy implements RejectedExecutionHandler {
>         public CallerRunsPolicy() { }
>     
>         public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
>             if (!e.isShutdown()) {
>                 // 直接主线程执行，而不是线程池中的线程执行
>                 r.run();
>             }
>         }
>     }
>
> 关于Shutdown是什么意思
>
> [线程池5：与停止线程池，相关的五个方法；（shutdown()，isShutdown()，isTerminated()，awaitTermination()，shutdownNow()；）_线程池 shutdown-CSDN博客](https://blog.csdn.net/csucsgoat/article/details/124143451)

**`ThreadPoolExecutor.DiscardPolicy`：** 不处理新任务，直接丢弃掉。

**`ThreadPoolExecutor.DiscardOldestPolicy`：** 此策略将丢弃最早的未处理的任务请求



**阻塞队列：**

`LinkedBlockingQueue`（无界队列）：`SingleThreadExector` 。`FixedThreadPool`最多只能创建核心线程数的线程（核心线程数和最大线程数相等），`SingleThreadExector`只能创建一个线程（核心线程数和最大线程数都是 1），二者的任务队列永远不会被放满。

`SynchronousQueue`（同步队列）：`CachedThreadPool` 。`SynchronousQueue` **没有容量，不存储元素**，目的是保证对于提交的任务，如果有空闲线程，则使用空闲线程来处理；否则新建一个线程来处理任务。也就是说，`CachedThreadPool` 的最大线程数是 `Integer.MAX_VALUE` ，可以理解为线程数是可以无限扩展的，可能会创建大量线程，从而导致 OOM。

`DelayedWorkQueue`（延迟阻塞队列）：`ScheduledThreadPool` 和 `SingleThreadScheduledExecutor` 。`DelayedWorkQueue` 的内部元素并不是按照放入的时间排序，而是会按照延迟的时间长短对任务进行排序，内部采用的是“堆”的数据结构，可以保证每次出队的任务都是当前队列中执行时间最靠前的。`DelayedWorkQueue` 添加元素满了之后会自动扩容原来容量的 1/2，即永远不会阻塞，最大扩容可达 `Integer.MAX_VALUE`，所以最多只能创建核心线程数的线程。



# 线程池的执行流程

[Java线程池：在没任务时，线程池中的工作线程在干嘛？_为什么在实际开发过程中不用管线程状态-CSDN博客](https://blog.csdn.net/a772304419/article/details/130939856)

![图解线程池实现原理](https://oss.javaguide.cn/javaguide/%E5%9B%BE%E8%A7%A3%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86.png)

如果当前运行的线程数小于核心线程数，那么就会新建一个线程来执行任务。

如果当前运行的线程数等于或大于核心线程数，但是小于最大线程数，那么就把该任务放入到任务队列里等待执行。

如果向任务队列投放任务失败（任务队列已经满了），但是当前运行的线程数是小于最大线程数的，就新建一个线程来执行任务。

如果当前运行的线程数已经等同于最大线程数了，新建线程将会使当前运行的线程超出最大线程数，那么当前任务会被拒绝，饱和策略会调用`RejectedExecutionHandler.rejectedExecution()`方法。

> 根据上面说的，猜想线程的创建是一个懒加载的过程。
>
> ```java
> /**
>  * 线程工厂，它设置线程名称，有利于我们定位问题。
>  */
> public final class NamingThreadFactory implements ThreadFactory {
> 
>     private final AtomicInteger threadNum = new AtomicInteger();
>     private final String name;
> 
>     /**
>      * 创建一个带名字的线程池生产工厂
>      */
>     public NamingThreadFactory(String name) {
>         this.name = name;
>     }
> 
>     @Override
>     public Thread newThread(Runnable r) {
>         System.out.println("hahahaha");
>         Thread t = new Thread(r);
>         t.setName(name + " [#" + threadNum.incrementAndGet() + "]");
>         return t;
>     }
> }
> 
> class test10 {
>     public static void main(String[] args) throws InterruptedException {
> 
>         ThreadPoolExecutor pool = new ThreadPoolExecutor(2, 2, 1, TimeUnit.MINUTES, new LinkedBlockingQueue<Runnable>(), new NamingThreadFactory("haha"));
>         pool.execute(() -> System.out.println("first..."));
>         Thread.sleep(3000);
>         pool.execute(() -> System.out.println("second..."));
>     }
> }
> 
> 根据输出结果就可以判断出确实是懒加载过程
> ```

但是突然发现为什么程序运行之后没停下来，一想就知道是线程池中创建了那两个线程并没有销毁停止运行，所以程序不会结束。然后就想到了之前的守护线程和非守护线程（用户线程）

[Java守护线程和main线程整理_main 线程和main里面的线程-CSDN博客](https://blog.csdn.net/qq_33098039/article/details/78179501)

>main线程并不一定是最后一个退出的线程

[关于python中守护线程的认识 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/115270781)

>线程会继承当前线程的daemon的值，当前线程为守护线程，那么在该线程中新建的线程默认为守护线程：
>
>* **主线程**是非守护线程，因此在主线程中新建线程默认都是非守护线程
>* 在一个守护线程中，再创建新的线程时，就会继承当前线程的Daemon值，成为守护线程

[面试突击：什么是守护线程？它和用户线程有什么区别？_threadpoolexecutor 是守护线程-CSDN博客](https://blog.csdn.net/HongYu012/article/details/123527376)

[JAVA多线程基础篇--守护线程(Daemon Thread)-CSDN博客](https://blog.csdn.net/qq_33479841/article/details/121950775)

> 当主线程运行结束后，JVM也不会退出，因为名称为**子线程**的用户线程还在后台运行



# 线程池的命名

**1、利用 guava 的 `ThreadFactoryBuilder`**

```java
ThreadFactory threadFactory = new ThreadFactoryBuilder()
                        .setNameFormat(threadNamePrefix + "-%d")
                        .setDaemon(true).build();
ExecutorService threadPool = new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, TimeUnit.MINUTES, workQueue, threadFactory);
```

2、自定义ThreadFactory

见上

# 确定线程池的大小

如果我们设置的线程池数量太小的话，如果同一时间有大量任务/请求需要处理，可能会导致大量的请求/任务在任务队列中排队等待执行，甚至会出现任务队列满了之后任务/请求无法处理的情况，或者大量任务堆积在任务队列导致 OOM。这样很明显是有问题的，CPU 根本没有得到充分利用。

如果我们设置线程数量太大，大量线程可能会同时在争取 CPU 资源，这样会导致大量的上下文切换，从而增加线程的执行时间，影响了整体执行效率。

**公式**

**CPU 密集型任务(N+1)：** 这种任务消耗的主要是 CPU 资源，可以将线程数设置为 N（CPU 核心数）+1。比 CPU 核心数多出来的一个线程是为了防止线程偶发的缺页中断，或者其它原因导致的任务暂停而带来的影响。一旦任务暂停，CPU 就会处于空闲状态，而在这种情况下多出来的一个线程就可以充分利用 CPU 的空闲时间。

**I/O 密集型任务(2N)：** 这种任务应用起来，系统会用大部分的时间来处理 I/O 交互，而线程在处理 I/O 的时间段内不会占用 CPU 来处理，这时就可以将 CPU 交出给其它线程使用。因此在 I/O 密集型任务的应用中，我们可以多配置一些线程，具体的计算方法是 2N。



# 设计一个根据任务优先级来执行的线程池

使用PriorityBlockingQueue，和PriorityQueue的区别就是一个阻塞一个不阻塞。

[阻塞队列与非阻塞队列的比较研究 - CSDN文库](https://wenku.csdn.net/column/27g0dvv8xm#:~:text=阻塞队列 ：当队列为空时，从队列中获取元素的操作会被阻塞；当队列已满时，向队列中添加元素的操作会被阻塞。,阻塞队列可以通过线程的等待%2F通知机制实现阻塞。 非阻塞队列 ：在队列为空时，获取元素的操作会返回空值或抛出异常，而不是被阻塞；在队列已满时，添加元素的操作也不会被阻塞。)

- **阻塞队列**：当队列为空时，从队列中获取元素的操作会被阻塞；当队列已满时，向队列中添加元素的操作会被阻塞。阻塞队列可以通过线程的等待/通知机制实现阻塞。
- **非阻塞队列**：在队列为空时，获取元素的操作会返回空值或抛出异常，而不是被阻塞；在队列已满时，添加元素的操作也不会被阻塞。非阻塞队列通常采用一些特殊的算法实现，如CAS（Compare and Swap）等。

**缺点：**

`PriorityBlockingQueue` 是无界的，可能堆积大量的请求，从而导致 OOM。

可能会导致饥饿问题，即低优先级的任务长时间得不到执行。

> 饥饿问题这个可以通过优化设计来解决（比较麻烦），比如等待时间过长的任务会被移除并重新添加到队列中，但是优先级会被提升。

由于需要对队列中的元素进行排序操作以及保证线程安全（并发控制采用的是可重入锁 `ReentrantLock`），因此会降低性能。



# Future&CompletableFuture

`Future` 类是异步思想的典型运用

Future获取得线程执行结果前，我们的主线程get()得到结果需要一直阻塞等待，即使我们使用isDone()方法轮询去查看线程执行状态，但是这样也非常浪费cpu资源。

[异步编程利器：CompletableFuture详解 ｜Java 开发实战 - 掘金 (juejin.cn)](https://juejin.cn/post/6970558076642394142)

[10张图让你彻底理解回调函数 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/326902537)

>回调函数是指一段以参数的形式传递给其它代码的可执行代码。

[一网打尽：异步神器 CompletableFuture 万字详解！-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2330070)

[Future和CompletableFuture的用法和区别_completablefuture和future的区别-CSDN博客](https://blog.csdn.net/qq_28908085/article/details/108267347)

[CompletableFuture 使用详解 - 简书 (jianshu.com)](https://www.jianshu.com/p/6bac52527ca4)

[⭐并发编程 | 从Future到CompletableFuture - 简化 Java 中的异步编程-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2354278)

> 对completetableFuture中获取返回结果的方法的complete不是很理解
>
> [Java CompletableFuture的complete（7）_completablefuture complete-CSDN博客](https://blog.csdn.net/zhangphil/article/details/80731593)



CompletableFuture 使用建议：

* 使用自定义线程池
* 尽量避免使用 get()

* 正确进行异常处理
* 合理组合多个异步任务







# AQS

使用 AQS 能简单且高效地构造出应用广泛的大量的同步器，比如我们提到的 `ReentrantLock`，`Semaphore`，其他的诸如 `ReentrantReadWriteLock`，`SynchronousQueue`等等皆是基于 AQS 的。



# Semphore

`synchronized` 和 `ReentrantLock` 都是一次只允许一个线程访问某个资源，而`Semaphore`(信号量)可以用来控制同时访问特定资源的线程数量。

当初始的资源个数为 1 的时候，`Semaphore` 退化为排他锁。

`Semaphore` 有两种模式：。

- **公平模式：** 调用 `acquire()` 方法的顺序就是获取许可证的顺序，遵循 FIFO；
- **非公平模式：** 抢占式的。

`Semaphore` 是共享锁的一种实现，它默认构造 AQS 的 `state` 值为 `permits`，你可以将 `permits` 的值理解为许可证的数量，只有拿到许可证的线程才能执行。

[（2.1.27.12）Java并发编程：Lock之Semaphore共享式不可重入锁_semaphore tryacquire 可重入吗-CSDN博客](https://blog.csdn.net/fei20121106/article/details/83268604)

>通常而言”AQS里的state在[重入锁](https://so.csdn.net/so/search?q=重入锁&spm=1001.2101.3001.7020)里代表线程重入的次数，state=1代表重入锁当前已被某个线程独占，这个线程每重入一次，state++“，但是在Semaphore中：
>
>* state在AQS被实例化时构建为 **允许共享的次数**
>* state>0 表示 **还可以被共享占有**



# CountDownLatch

`CountDownLatch` 允许 `count` 个线程阻塞在一个地方，直至所有线程的任务都执行完毕。

`CountDownLatch` 是**一次性**的，计数器的值只能在构造方法中初始化一次，之后没有任何机制再次对其设置值，当 `CountDownLatch` 使用完毕后，它不能再次被使用。

**原理：**

`CountDownLatch` 是共享锁的一种实现,它默认构造 AQS 的 `state` 值为 `count`。当线程使用 `countDown()` 方法时,其实使用了`tryReleaseShared`方法以 CAS 的操作来减少 `state`,直至 `state` 为 0 。当调用 `await()` 方法的时候，如果 `state` 不为 0，那就证明任务还没有执行完毕，`await()` 方法就会一直阻塞，也就是说 `await()` 方法之后的语句不会被执行。直到`count` 个线程调用了`countDown()`使 state 值被减为 0，或者调用`await()`的线程被中断，该线程才会从阻塞中被唤醒，`await()` 方法之后的语句得到执行。

**应用场景：**

`CountDownLatch` 的作用就是 允许 count 个线程阻塞在一个地方，直至所有线程的任务都执行完毕。



# CyclicBarrier

`CyclicBarrier` 和 `CountDownLatch` 非常类似，它也可以实现线程间的技术等待，但是它的功能比 `CountDownLatch` 更加复杂和强大。主要应用场景和 `CountDownLatch` 类似。

> `CountDownLatch` 的实现是基于 AQS 的，而 `CycliBarrier` 是基于 `ReentrantLock`(`ReentrantLock` 也属于 AQS 同步器)和 `Condition` 的。

`CyclicBarrier` 的字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是：让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。

**原理：**

`CyclicBarrier` 内部通过一个 `count` 变量作为计数器，`count` 的初始值为 `parties` 属性的初始化值，每当一个线程到了栅栏这里了，那么就将计数器减 1。如果 count 值为 0 了，表示这是这一代最后一个线程到达栅栏，就尝试执行我们构造方法中输入的任务。

>注意 CyclicBarrier 与 CountDownLatch 的主要区别在于 CyclicBarrier 是可以重用的 CyclicBarrier 可以被比 喻为『人满发车』



# 乐观锁

乐观锁总是假设最好的情况，认为共享资源每次被访问的时候不会出现问题，线程可以不停地执行，无需加锁也无需等待，只是在提交修改的时候去验证对应的资源（也就是数据）是否被其它线程修改了（具体方法可以使用版本号机制或 CAS 算法）。

高并发的场景下，乐观锁相比悲观锁来说，不存在锁竞争造成线程阻塞，也不会有死锁的问题，在性能上往往会更胜一筹。但是，如果冲突频繁发生（写占比非常多的情况），会频繁失败和重试（悲观锁的开销是固定的），这样同样会非常影响性能，导致 CPU 飙升。

不过，大量失败重试的问题也是可以解决的，像我们前面提到的 `LongAdder`以空间换时间的方式就解决了这个问题

乐观锁通常多于写比较少的情况下（多读场景，竞争较少），这样可以避免频繁加锁影响性能。不过，乐观锁主要针对的对象是单个共享变量（参考`java.util.concurrent.atomic`包下面的原子变量类）。



**实现方式**

* CAS

   Java 中`java.util.concurrent.atomic`包下面的原子变量类（比如`AtomicInteger`、`LongAdder`）就是使用了乐观锁的一种实现方式 **CAS** 实现的。

  CAS 的全称是 **Compare And Swap（比较与交换）** ，用于实现乐观锁，被广泛应用于各大框架中。CAS 的思想很简单，就是用一个预期值和要更新的变量值进行比较，两值相等才会进行更新。

  CAS 是一个原子操作，底层依赖于一条 CPU 的原子指令。

  > **原子操作** 即最小不可拆分的操作，也就是说操作一旦开始，就不能被打断，直到操作完成。
  >
  > 百度： 所谓原子操作是指不会被线程调度机制打断的操作；这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch [1]（切换到另一个线程）。
  >
  > [原子操作-CSDN博客](https://blog.csdn.net/just_kong/article/details/99289539)
  >
  > [Linux内核并发机制----原子操作_lkmoop-CSDN博客](https://songqiang.blog.csdn.net/article/details/81408652?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-81408652-blog-106068606.235^v43^pc_blog_bottom_relevance_base8&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-81408652-blog-106068606.235^v43^pc_blog_bottom_relevance_base8&utm_relevant_index=2)
  >
  > 补充百度：在单处理器上，一旦启动原子操作，则从操作开始到结束的这段 时间内，不会中断线程。此外，在多处理器系统中，原子操作所针对的变量被锁定，以免被其他进程访问，直到原子操作结束。

* 版本号机制

  一般是在数据表中加上一个数据版本号 `version` 字段，表示数据被修改的次数。当数据被修改时，`version` 值会加一。当线程 A 要更新数据值时，在读取数据的同时也会读取 `version` 值，在提交更新时，若刚才读取到的 version 值为当前数据库中的 `version` 值相等时才更新，否则重试更新操作，直到更新成功。

  **乐观锁存在的问题：**

  * **ABA问题：**

    如果一个变量 V 初次读取的时候是 A 值，并且在准备赋值的时候检查到它仍然是 A 值，那我们就能说明它的值没有被其他线程修改过了吗？很明显是不能的，因为在这段时间它的值可能被改为其他值，然后又改回 A，那 CAS 操作就会误认为它从来没有被修改过。这个问题被称为 CAS 操作的 **"ABA"问题。**

    ABA 问题的解决思路是在变量前面追加上**版本号或者时间戳**。JDK 1.5 以后的 `AtomicStampedReference` 类就是用来解决 ABA 问题的，其中的 `compareAndSet()` 方法就是首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志，如果全部相等，则以原子方式将该引用和该标志的值设置为给定的更新值。

  * **循环时间长开销大**

    CAS 经常会用到自旋操作来进行重试，也就是不成功就一直循环执行直到成功。如果长时间不成功，会给 CPU 带来非常大的执行开销。

    如果 JVM 能支持处理器提供的 pause 指令那么效率会有一定的提升，pause 指令有两个作用：

    1. 可以延迟流水线执行指令，使 CPU 不会消耗过多的执行资源，延迟的时间取决于具体实现的版本，在一些处理器上延迟时间是零。
    2. 可以避免在退出循环的时候因内存顺序冲突而引起 CPU 流水线被清空，从而提高 CPU 的执行效率。
  
  * **只能保证一个共享变量的原子操作**

    CAS 只对单个共享变量有效，当操作涉及跨多个共享变量时 CAS 无效。但是从 JDK 1.5 开始，提供了`AtomicReference`类来保证引用对象之间的原子性，你可以把多个变量放在一个对象里来进行 CAS 操作.所以我们可以使用锁或者利用`AtomicReference`类把多个共享变量合并成一个共享变量来操作。



**总结**

- 高并发的场景下，激烈的锁竞争会造成线程阻塞，大量阻塞线程会导致系统的上下文切换，增加系统的性能开销。并且，悲观锁还可能会存在死锁问题，影响代码的正常运行。乐观锁相比悲观锁来说，不存在锁竞争造成线程阻塞，也不会有死锁的问题，在性能上往往会更胜一筹。不过，如果冲突频繁发生（写占比非常多的情况），会频繁失败和重试，这样同样会非常影响性能，导致 CPU 飙升。
- 乐观锁一般会使用版本号机制或 CAS 算法实现，CAS 算法相对来说更多一些，这里需要格外注意。
- CAS 的全称是 **Compare And Swap（比较与交换）** ，用于实现乐观锁，被广泛应用于各大框架中。CAS 的思想很简单，就是用一个预期值和要更新的变量值进行比较，两值相等才会进行更新。
- 乐观锁的问题：ABA 问题、循环时间长开销大、只能保证一个共享变量的原子操作。



# cpu缓存模型&重排序

**CPU Cache 缓存的是内存数据用于解决 CPU 处理速度和内存不匹配的问题，内存缓存的是硬盘数据用于解决硬盘访问速度过慢的问题。**

![](https://oss.javaguide.cn/github/javaguide/java/concurrent/cpu-cache.png)

**CPU Cache 的工作方式：** 先复制一份数据到 CPU Cache 中，当 CPU 需要用到的时候就可以直接从 CPU Cache 中读取数据，当运算完成后，再将运算得到的数据写回 Main Memory 中。但是，这样存在 **内存缓存不一致性的问题** ！比如我执行一个 i++ 操作的话，如果两个线程同时执行的话，假设两个线程从 CPU Cache 中读取的 i=1，两个线程做了 i++ 运算完之后再写回 Main Memory 之后 i=2，而正确结果应该是 i=3。

**CPU 为了解决内存缓存不一致性问题可以通过制定缓存一致协议（比如 [MESI 协议open in new window](https://zh.wikipedia.org/wiki/MESI协议)）或者其他手段来解决。** 这个缓存一致性协议指的是**在 CPU 高速缓存与主内存交互的时候需要遵守的原则和规范**。不同的 CPU 中，使用的缓存一致性协议通常也会有所不同。

我们的程序运行在操作系统之上，操作系统屏蔽了底层硬件的操作细节，将各种硬件资源虚拟化。于是，操作系统也就同样需要解决内存缓存不一致性问题。

操作系统通过 **内存模型（Memory Model）** 定义一系列规范来解决这个问题。无论是 Windows 系统，还是 Linux 系统，它们都有特定的内存模型

**重排序**

为了使处理器内部的运算单元能够尽量的被充分利用，处理器可能会对输入代码进行乱序执行处理。这就是**处理器优化**。

除了现在很多流行的处理器会对代码进行优化乱序处理，很多编程语言的编译器也会有类似的优化，比如Java虚拟机的即时编译器（JIT）也会做**指令重排**。https://www.jianshu.com/p/8420ade6ff76


# JMM

[终于有人把Java内存模型(JMM)说清楚了 - 简书 (jianshu.com)](https://www.jianshu.com/p/8420ade6ff76)

[JMM——Java内存模型 - 掘金 (juejin.cn)](https://juejin.cn/post/6919350421685288973#heading-0)

[从线程三大特性深入理解JMM（Java 内存模型） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/416172908)

[⭐Java面试官告诉你JMM是什么和面什么 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/345041865)

[Java内存模型(Java Memory Model，JMM)-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1906257)

[从 JMM 透析 volatile 与 synchronized 原理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/333386921)

Java内存模型（Java Memory Model ,JMM）就是一种符合内存模型规范的，屏蔽了各种硬件和操作系统的访问差异的，保证了Java程序在各种平台下对内存的访问都能保证效果一致的机制及规范。

记住JMM是一种机制和规范，结合三个并发编程的特性来记，比如通过什么保证了可见性，原子性和有序性。



网上的信息游龙混杂，导致自己对JMM始终不理解，不知道为什么明明说是一种规范，怎么反过头来直接提供了volatile和synchronized还有内存屏障实现。真正的意思是JMM提供了一种规范，最后由Java语言的设计者真正设计出来。

```java
我：
JMM是什么

Poe：
JMM（Java内存模型）是Java中定义了线程之间如何交互以及如何访问共享内存的规范。它规定了在多线程环境下，程序中的操作如何在各个线程之间进行同步和通信。

JMM定义了一组规则和约束，确保多线程程序的正确性和可预测性。它提供了一种抽象的内存模型，使得程序员可以通过使用同步机制（如锁、volatile关键字、synchronized关键字等）来控制线程之间的可见性和有序性。

JMM的主要特性包括：

主内存（Main Memory）：主内存是所有线程共享的内存区域，用于存储对象的字段、静态变量等数据。

工作内存（Working Memory）：每个线程都有自己的工作内存，用于存储线程执行过程中需要使用的数据。

内存间的交互：线程之间的数据交互通过主内存进行，线程将数据从主内存读取到工作内存，进行操作后再写回主内存。

原子性、可见性和有序性：JMM定义了操作的原子性、可见性和有序性的规则。原子性确保操作不会被中断，可见性确保一个线程对共享数据的修改对其他线程可见，有序性确保操作按照一定的顺序进行。

通过遵循JMM的规则，开发者可以编写线程安全的代码，保证多线程程序的正确性。同时，JMM也为编译器、运行时和硬件提供了实现的自由度，允许进行一定程度的优化，同时遵守JMM规范以保证程序的正确性。

我：
volatile关键字、synchronized关键字这些都是JMM中定义的吗

Poe：
是的，volatile 关键字和 synchronized 关键字都是 JMM 中定义的同步机制。

volatile 关键字：volatile 关键字用于修饰变量，确保对该变量的读写操作具有可见性。当一个变量被声明为 volatile，线程在对该变量进行读操作时，会直接从主内存中读取最新的值，而不是使用线程的工作内存中的缓存值。对 volatile 变量的写操作会立即刷新到主内存中，以保证其他线程能够看到最新的值。

synchronized 关键字：synchronized 关键字用于实现线程的互斥访问和共享对象的可见性。通过在方法或代码块上使用 synchronized 关键字，可以保证同一时间只有一个线程执行被保护的代码区域。当一个线程进入 synchronized 代码块时，会自动获取对象的锁，并且在释放锁之前，其他线程无法进入该代码块。同时，synchronized 关键字还具有保证可见性的特性，即在释放锁之前，会将对共享变量的修改刷新到主内存中，以便其他线程能够看到最新的值。

这些关键字的使用可以保证在多线程环境下的原子性、可见性和有序性，从而确保程序的正确性。它们是 JMM 提供的重要工具，帮助开发者编写线程安全的代码。

我：
什么叫规范

Poe：
在计算机科学中，规范指的是一组定义了特定行为、规则和约定的规则集合。它们用于规定系统、语言、协议或接口的正确使用和交互方式。

规范通常由标准化组织、开发者社区或厂商制定，并用于确保系统、软件或者协议在不同的实现中具有一致的行为和互操作性。规范的目的是为了提供一个共同的框架，使得不同的参与者能够理解和遵循相同的规则，以达到预期的结果。

在软件开发中，规范可以涉及编程语言的语法和语义、API 的设计和用法、通信协议的格式和行为、数据交换的格式等。通过遵循规范，开发者可以编写出可靠、兼容和可维护的软件，同时保证不同组件之间的互操作性。

规范对于确保系统的正确性、安全性和可靠性至关重要。它们为开发者提供了一种共同的语言和准则，使得不同的实现能够相互协作，减少了潜在的错误和冲突，并促进了软件的可移植性和可扩展性。

我：
那JMM是不是也是一个具体的实现

Poe：
JMM（Java内存模型）并不是一个具体的实现，而是Java语言规范中定义的一种内存模型。它描述了Java程序中线程如何与内存进行交互，以及如何保证多线程程序的可见性、原子性和有序性。

JMM规定了线程之间的数据交互方式、同步机制的语义，以及对共享变量的访问规则。它定义了一组规则，确保在多线程环境下程序的行为是可预测的。

具体的JVM（Java虚拟机）实现需要遵循JMM规范来实现线程的同步和内存访问。不同的JVM实现可能在实现上有所差异，但它们都必须遵循JMM规范，以保证Java程序在不同的平台和环境中的一致性。

JMM规范为JVM实现者提供了一些灵活性，允许对内存操作进行优化和重排序，但必须保证在JMM规定的语义下，程序的行为与预期一致。

因此，JMM是一个规范，而具体的JVM实现则是根据该规范来实现线程的同步和内存访问。不同的JVM实现可能在实现细节上有所不同，但它们都必须遵循JMM规范，以确保Java程序的正确性和可移植性。

我：
那volatile关键字、synchronized关键字和JMM是个什么关系

Poe：
volatile 关键字、synchronized 关键字和 JMM（Java内存模型）之间存在密切的关系。

volatile 关键字与 JMM：volatile 关键字是 JMM 提供的同步机制之一。它确保了被修饰的变量在多线程环境下的可见性和有序性。当一个变量被声明为 volatile，对该变量的写操作会立即刷新到主内存中，而对该变量的读操作会从主内存中获取最新的值。这样，所有的线程都能看到最新的值，从而避免了线程之间的数据不一致性。

synchronized 关键字与 JMM：synchronized 关键字是 JMM 提供的另一种同步机制。它用于实现线程之间的互斥访问和共享对象的可见性。通过在方法或代码块上使用 synchronized 关键字，可以确保同一时间只有一个线程执行被保护的代码区域。同时，synchronized 关键字具有保证可见性的特性，即在释放锁之前，会将对共享变量的修改刷新到主内存中，以便其他线程能够看到最新的值。

JMM 规定了 volatile 关键字和 synchronized 关键字的语义和行为，以及对于多线程程序的可见性、原子性和有序性的规则。它确保了使用 volatile 关键字或 synchronized 关键字进行的操作在各个线程之间具有一致的行为。JMM规范定义了这些关键字的使用方式，以保证程序在多线程环境下的正确性。

总结而言，volatile 关键字和 synchronized 关键字是 JMM 提供的同步机制，用于保证多线程程序的可见性、原子性和有序性。它们遵循 JMM 规范，确保线程之间的数据一致性和正确性。

我：
JMM有具体的代码吗

Poe：
JMM（Java内存模型）本身不是具体的代码，而是一种规范或抽象的概念，描述了线程如何与内存进行交互和操作的规则。

JMM规范并没有提供具体的代码实现，而是定义了一组规则和语义，确保多线程程序的正确性和一致性。这些规则包括对于内存操作的原子性、可见性和有序性的要求，以及线程之间的同步机制的语义。

具体的JVM（Java虚拟机）实现会根据JMM规范来实现线程的同步和内存访问。不同的JVM实现可能在实现细节上有所差异，但它们都必须遵循JMM规范，以保证Java程序在不同平台和环境中的一致性。

开发者编写的Java程序可以根据JMM规范来编写线程安全的代码，使用 volatile 关键字、synchronized 关键字或其他同步机制来保证内存操作的可见性和有序性。但具体的代码实现会依赖于JVM的实现和具体的编程场景。

因此，JMM并没有提供具体的代码实现，而是提供了一套规范和约束，为JVM实现者和开发者提供了一个共同的框架，以确保多线程程序的正确性和可移植性。

我：
JMM是不是类似于提供了一种接口，然后由jvm实现

Poe：
可以将JMM看作是Java语言规范中定义的一种接口或规范，而JVM（Java虚拟机）则是对该接口的具体实现。

JMM定义了线程与内存之间的交互规则和语义，确保多线程程序的可见性、原子性和有序性。它规定了各种内存操作的行为，如读取、写入、同步操作等，并定义了对共享变量的访问和修改规则。

JVM作为Java程序的执行环境，负责解释和执行Java字节码。JVM需要遵循JMM规范，以确保在多线程环境下，程序的行为与JMM规范一致。JVM实现了JMM规定的内存操作和同步机制，保证了多线程程序的正确性。

具体而言，JVM实现了JMM规范中定义的内存模型，包括线程的工作内存、主内存、内存屏障等。它通过对指令重排序、内存可见性、原子性操作等进行控制，确保多线程程序的行为满足JMM规范的要求。

因此，可以说JMM提供了一种接口或规范，而JVM是对该接口的具体实现。JMM规范定义了多线程程序的行为和规则，而JVM实现了这些规则，以确保Java程序在多线程环境下的正确性和可移植性。

我：
那是不是JMM提出了volatile和synchronized的思想，具体实现还是jvm实现的

Poe：
JMM（Java内存模型）确实定义了volatile和synchronized的语义和行为，但具体的实现是由JVM（Java虚拟机）来实现的。

JMM规范指定了volatile关键字和synchronized关键字的使用方式和效果，以保证多线程程序的可见性、原子性和有序性。它规定了对于被volatile修饰的变量的读写操作具有特定的语义，以及使用synchronized关键字进行同步的规则。

具体的JVM实现根据JMM规范来实现volatile和synchronized的语义和效果。JVM负责确保被volatile修饰的变量的修改对其他线程可见，以及通过synchronized关键字实现的互斥访问和可见性保证。

JVM实现了一些机制来实现volatile关键字的语义，例如使用内存屏障（memory barrier）来确保变量的写入操作及时刷新到主内存，以及使用缓存一致性协议来保证可见性。

对于synchronized关键字，JVM实现了相应的锁机制（如对象锁、类锁），以及通过监视器（monitor）来实现线程的互斥访问和可见性保证。

因此，JMM规范提出了volatile和synchronized的思想和语义，而具体的实现是由JVM来实现的，以确保多线程程序在不同平台和环境中的正确性和一致性。

我：
拿volatile举例，JMM和JVM之间是怎样一个关系

Poe：
volatile 关键字是 JMM（Java内存模型）提供的一种同步机制，用于确保被修饰的变量在多线程环境下的可见性和有序性。JVM（Java虚拟机）作为 Java 程序的执行环境，负责实现 JMM 规范中对 volatile 关键字的语义和行为。

JMM 规范定义了 volatile 关键字的语义：当一个变量被声明为 volatile，对该变量的写操作会立即刷新到主内存中，而对该变量的读操作会从主内存中获取最新的值。这样，所有的线程都能看到最新的值，从而避免了线程之间的数据不一致性。

JVM 在实现 volatile 关键字时需要遵循 JMM 规范的要求。具体地，JVM 会在编译器和运行时进行一些优化和重排序，但必须保证 volatile 变量的读写操作遵循 JMM 规定的语义。

为了实现 volatile 的语义，JVM 会在生成的指令序列中插入特定的内存屏障（memory barrier）或者锁操作，以确保 volatile 变量的写操作及时刷新到主内存，以及读操作从主内存中获取最新的值。这样，不论是编译器优化还是处理器的指令重排序，都必须遵循 volatile 的语义，以保证线程之间的可见性和有序性。

因此，可以说 JMM 规范定义了 volatile 关键字的语义和行为，而 JVM 则负责实现并确保 volatile 变量的可见性和有序性。JVM 在编译器和运行时会插入相应的指令或操作，以满足 JMM 规范对 volatile 的要求。这样，JVM 实现了 JMM 规范中 volatile 的具体行为和效果。

我：
那之所以将这个名词定义为volatile，是JMM做的，还是JVM做的

Poe：
将关键字定义为 volatile 是由Java语言的设计者和JMM规范制定者决定的，而不是由JVM实现者决定的。

JMM规范定义了 volatile 关键字的语义和行为，以确保被修饰的变量在多线程环境下的可见性和有序性。这是JMM规范制定者为了满足多线程程序的需求而引入的。

Java语言的设计者将JMM规范中的 volatile 关键字引入到Java语言中，作为一种同步机制的关键字。他们选择了 volatile 这个关键字的名称来表示被修饰变量的特性，即在多线程环境中的易变性和可见性。

JVM实现者则需要根据JMM规范来实现 volatile 关键字的语义和行为。他们需要确保在JVM中对 volatile 变量的读写操作遵循JMM规定的语义，并插入相应的指令或操作来保证可见性和有序性。

因此，可以说将关键字定义为 volatile 是由JMM规范和Java语言的设计者决定的，而JVM实现者负责根据JMM规范来实现 volatile 关键字的具体行为和效果。

我：
就是JMM提出需要用一个关键字修饰变量，然后这个变量需要满足上述条件，然后Java语言设计者就选择将这个关键字命名为volatile，然后实现它

Poe：
是的，您的理解是正确的。

JMM规范提出了一种同步机制，用于确保多线程程序中被修饰的变量的可见性和有序性。这个机制要求使用一个关键字来修饰变量，并对该关键字修饰的变量施加特定的语义和行为。

Java语言的设计者在实现多线程编程的时候，选择使用关键字修饰变量，并将这个关键字命名为 volatile。他们选择了 volatile 这个关键字的名称来表示被修饰变量的特性，即在多线程环境中的易变性和可见性。

JVM实现者根据JMM规范来实现 volatile 关键字的语义和行为。他们需要确保在JVM中对 volatile 变量的读写操作遵循JMM规定的语义，并插入相应的指令或操作来保证可见性和有序性。

因此，JMM规范提出了需要使用一个关键字修饰变量，并满足特定的条件，而Java语言设计者选择了 volatile 这个关键字的名称，并将其作为Java语言中的同步机制。JVM实现者根据JMM规范来实现 volatile 关键字的具体行为和效果。

我：
内存屏障是不是也是如此

Poe：
对于内存屏障（memory barrier），它是一种硬件或者软件层面的机制，用于控制指令的执行顺序，保证内存操作的可见性和有序性。内存屏障的具体实现是由处理器和编译器来负责的，而不是由JMM规范或Java语言设计者直接定义和命名的。

JMM规范中确实定义了内存屏障的概念和作用，它在多线程环境中起到了重要的作用。JMM规范中指定了在特定的情况下需要插入内存屏障来确保内存操作的可见性和有序性，从而满足多线程程序的需求。

JVM在实现时需要根据JMM规范来插入内存屏障，以确保内存操作的顺序和可见性。具体的内存屏障的实现是由JVM和处理器共同合作完成的。在编译器层面，JVM会生成适当的指令序列来插入内存屏障。在处理器层面，处理器会根据指令序列中的内存屏障指令来进行相应的操作，以保证内存操作的可见性和有序性。

因此，JMM规范定义了内存屏障的概念和作用，但具体的内存屏障的实现是由JVM和处理器共同合作完成的。JVM负责根据JMM规范生成适当的指令序列，并与处理器合作来实现内存屏障的效果。
```



# AQS

AQS 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是基于 **CLH 锁** （Craig, Landin, and Hagersten locks） 实现的。



# Atomic原子类

`AtomicInteger` 类主要利用 CAS (compare and swap) + volatile 和 native 方法来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升。

CAS 的原理是拿期望的值和原本的一个值作比较，如果相同则更新成新的值。UnSafe 类的 `objectFieldOffset()` 方法是一个本地方法，这个方法是用来拿到“原来的值”的内存地址。另外 value 是一个 volatile 变量，在内存中可见，因此 JVM 可以保证任何时刻任何线程总能拿到该变量的最新值。





























