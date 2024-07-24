[聊聊同步与异步、阻塞与非阻塞、I/O模型-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1635325)

[一文为你讲解清楚并发，同步，异步，互斥，阻塞，非阻塞-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1829301)

[彻底搞懂同步与异步，阻塞/非阻塞-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/1432287)

[并发、同步、异步、阻塞、非阻塞的理解_并发,同步,异步,互斥,阻塞,非阻塞的理解-CSDN博客](https://blog.csdn.net/weixin_44226857/article/details/107360278)

[15分钟读懂进程线程、同步异步、阻塞非阻塞、并发并行，太实用了！-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1592939)





[从Java BIO到NIO再到多路复用，看这篇就够了 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/635283967)

[头条面试官：NIO 是不是就是I/O多路复用？我：不是-CSDN博客](https://blog.csdn.net/weixin_43753797/article/details/115274744)

[NIO原理（1）-----为什么需要内核缓冲区和用户缓冲区_为什么要有用户缓冲区和内核缓冲区-CSDN博客](https://blog.csdn.net/allowancedebug/article/details/96698753#:~:text=用户进程通过系统调用访问系统资源的时候，需要切换到内核态，而这对应一些特殊的堆栈和内存环境，必须在系统调用前建立好。 而在系统调用结束后，cpu会从核心模式切回到用户模式，而堆栈又必须恢复成用户进程的上下文。 而这种切换就会有大量的耗时。 你看一些程序在读取文件时，会先申请一块内存数组，称为,buffer ，然后每次调用read，读取设定字节长度的数据，写入buffer。 （用较小的次数填满buffer）。 之后的程序都是从buffer中获取数据，当buffer使用完后，在进行下一次调用，填充buffer。 所以说：用户缓冲区的目的是为了减少系统调用次数，从而降低操作系统在用户态与核心态切换所耗费的时间。)

[100%弄明白5种IO模型 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/115912936)

>我们通常会说到同步阻塞IO、同步非阻塞IO，异步IO几种术语，通过上面的内容，那么我想你现在肯定已经理解了什么是阻塞什么是非阻塞了，所谓阻塞就是发起读取数据请求的时，当数据还没准备就绪的时候，这时请求是即刻返回，还是在这里等待数据的就绪，如果需要等待的话就是阻塞，反之如果即刻返回就是非阻塞。
>
>我们区分了阻塞和非阻塞后再来分别下同步和异步，在IO模型里面如果请求方从发起请求到数据最后完成的这一段过程中都需要自己参与，那么这种我们就称为同步请求；反之，如果应用发送完指令后就不再参与过程了，只需要等待最终完成结果的通知，那么这就属于异步。

[Netty学习前基本知识 — BIO 、NIO 、AIO 总结 (qq.com)](https://mp.weixin.qq.com/s/7DrH3vdl0xVJp97Q-fjTAA)

[Java NIO （图解+秒懂+史上最全）_java nio 图解-CSDN博客](https://blog.csdn.net/crazymakercircle/article/details/120946903)

[Java 中 NIO 看这一篇就够了 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/369062109)

[JAVA BIO与NIO、AIO的区别(容易理解) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/419345478)

[4个常见的IO模型——阻塞、非阻塞、多路复用、异步 - 小小怪医芙兰 - 博客园 (cnblogs.com)](https://www.cnblogs.com/Franken-Fran/p/io_model.html)

>最后还是说一个比喻吧。如果把IO模型比作网购，在你提交订单的一刻到快递员将货物带到你家小区门口期间，可看作一阶段IO。之后小哥给你打电话：“你的快递，快开门”，这相当于数据准备好了，并发送了通知。之后，你跑到门口将快递一件一件搬到家里，这是第二阶段。
>
>阻塞模型相当于你提了订单，就不吃不喝，只等着快递，直到小哥给你打电话，然后吭哧吭哧把快递搬到家，你才开始吃喝、睡觉等。非阻塞模型下，你提交订单后就开始自由行动了。但是仍然会每隔几分钟跑到小区门口看看快递到了没（轮询），一直把自己搞得精疲力尽。直到小哥给你打电话，你把快递搬回家才算消停。多路复用模型下，你同时提交了多个订单，但不再傻傻的朝门口来回跑了，而是紧紧盯着菜鸟裹裹信息（阻塞在select），直到其上显示至少有一个快递到了，你会嗖的跑过去，当然，快递仍然自己搬（recvfrom阻塞）。异步模型下，你雇了一个万能管家吉福斯，你只要下完订单，就不用操心了，他会打点好一切，最后他会把快递按照你们约定的地点、方式，直接送到你的手上。

[深入浅出IO多路复用：提升系统性能的关键技术-百度开发者中心 (baidu.com)](https://developer.baidu.com/article/detail.html?id=3277649)

>在计算机科学中，IO多路复用是一种技术，它允许单个线程同时监听多个输入流（如[网络](https://cloud.baidu.com/product/et.html)套接字、文件描述符等），并在有数据可读或可写时进行相应的处理。这种技术通过将多个IO通道注册到一个事件管理器中，然后阻塞等待事件的发生，从而实现了高效的IO处理。



# streams流

用于简化集合和数组操作的API。

核心思想(流水线)

* 先得到集合或者数组的Stream流（就是一根传送带）
* 把元素放上去
* **然后就用这个Stream流简化的API来方便的操作元素。**

下面的文章随便挑一个看就可以：

[Java---Stream流技术（全网最详细）_java stream-CSDN博客](https://blog.csdn.net/qq_64847107/article/details/131997896)

> Stream流是对集合（Collection）对象功能的增强，与[Lambda表达式](https://so.csdn.net/so/search?q=Lambda表达式&spm=1001.2101.3001.7020)结合，可以提高编程效率、间接性和程序可读性。

[全面吃透JAVA Stream流操作，让代码更加的优雅-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2048817)

>**Stream相较于传统的foreach的方式处理stream，到底有啥优势**？
>
>根据前面的介绍，我们应该可以得出如下几点答案：
>
>- **代码更简洁**、偏声明式的编码风格，更容易体现出代码的逻辑意图
>- **逻辑间解耦**，一个stream中间处理逻辑，无需关注上游与下游的内容，只需要按约定实现自身逻辑即可
>- 并行流场景**效率**会比迭代器逐个循环更高
>- 函数式接口，**延迟执行**的特性，中间管道操作不管有多少步骤都不会立即执行，只有遇到终止操作的时候才会开始执行，可以避免一些中间不必要的操作消耗

[【java基础】吐血总结Stream流操作_java stream流操作-CSDN博客](https://blog.csdn.net/qq_43410878/article/details/123716629)

>1. stream不存储数据，而是按照特定的规则对数据进行计算，一般会输出结果。
>2. stream不会改变数据源，通常情况下会产生一个新的集合或一个值。
>3. stream具有延迟执行特性，只有调用终端操作时，中间操作才会执行。









