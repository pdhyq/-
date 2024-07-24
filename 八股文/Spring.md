参考文件链接：

[Spring常见面试题总结 | JavaGuide](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#什么是-spring-框架)

[【Java面试题汇总】Spring篇（2023版）-CSDN博客](https://blog.csdn.net/qq_40991313/article/details/131207135)

[Spring面试题，37道Spring八股文（1.3万字63张手绘图），面渣逆袭必看👍 | 二哥的Java进阶之路 (javabetter.cn)](https://javabetter.cn/sidebar/sanfene/spring.html)

[Spring常见面试题(13个面试题，回答超详细)-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2179570)

[三天吃透Spring面试八股文（最新整理）_springboot八股-CSDN博客](https://blog.csdn.net/Tyson0314/article/details/129135396)

[【八股文】Spring篇_spring八股文-CSDN博客](https://blog.csdn.net/weixin_45325628/article/details/122907131)

[面试小炒](https://www.javalearn.cn/#/doc/Spring/面试题)



# 什么是Spring

Spring 是一款开源的轻量级 Java 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发，比如说 Spring 支持 IoC（Inversion of Control:控制反转） 和 AOP(Aspect-Oriented Programming:面向切面编程)、可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

> Spring是一款轻量级的java开发框架，提高开发人员的开发效率以及系统的可维护性。包含了众多模块，其中最核心的功能就是提供了IOC控制反转和AOP面向切面编程。
>
> Spring的优点/为什么选择Spring
>
> * 轻量级，简化开发
> * spring的DI机制将对象之间的依赖关系交由框架处理，通过控制反转和依赖注入实现松耦合。
> * Spring提供了AOP技术，支持面向切面的编程。
> * 声明式事务的支持，支持通过配置就来完成对事务的管理，而不需要通过硬编码的方式，支持多个隔离级别、传播策略
> * spring对于主流的应用框架提供了集成支持。内部提供了对各种优秀框架的直接支持（如：Hessian、Quartz、MyBatis等）。
> * Spring支持Junit4，方便程序的测试，添加注解便可以测试Spring程序。



# Spring和Springmvc和Springboot的关系

Spring MVC 是 Spring 中的一个很重要的模块，主要赋予 Spring 快速构建 MVC 架构的 Web 程序的能力。

使用 Spring 进行开发各种配置过于麻烦比如开启某些 Spring 特性时，需要用 XML 或 Java 进行显式配置。于是，Spring Boot 诞生了！

Spring 旨在简化 J2EE 企业应用程序开发。Spring Boot 旨在简化 Spring 开发（减少配置文件，开箱即用！）。

Spring Boot 只是简化了配置，如果你需要构建 MVC 架构的 Web 程序，你还是需要使用 Spring MVC 作为 MVC 框架，只是说 Spring Boot 帮你简化了 Spring MVC 的很多配置，真正做到开箱即用





# 谈谈你对IOC的理解

**IoC（Inversion of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。具体实现就是DI

- **控制**：指的是对象创建（实例化、管理）的权力
- **反转**：控制权交给外部环境（Spring 框架、IoC 容器）

它可以帮我们维护对象与对象之间的依赖关系，简化开发，降低对象之间的耦合度。

> 个人理解：为什么说降低了耦合，比如有一个A需要用到B，在使用A的时候就new一个B，A和B就出现了耦合关系，当时当有了IOC之后，Spring创建好了B，当A需要用到B时，只需要注入就可以了，不需要关系B是如何创建的，这就降低了耦合性

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value），Map 中存放的是各种对象。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

> IOC，Inversion of Control，控制反转，指将对象的控制权转移给Spring框架，由 Spring 来负责控制对象的生命周期（比如创建、销毁）和对象间的依赖关系。
>
> IOC是一种设计思想，DI是实现方式。
>
> 好处：资源不由使用资源者管理，而由不使用资源的第三方管理，这可以带来很多好处。第一，资源集中管理，实现资源的可配置和易管理。第二，帮助我们维护对象与对象之间的依赖关系，简化开发，降低对象之间的耦合度。



# Spring启动过程/IOC容器的初始化过程

[【一篇搞懂】Sping 容器启动过程详解 - 沙滩de流沙 - 博客园 (cnblogs.com)](https://www.cnblogs.com/imok520/p/16411315.html)

[Spring详解（五）——Spring IOC容器的初始化过程 - 唐浩荣 - 博客园 (cnblogs.com)](https://www.cnblogs.com/tanghaorong/p/13497223.html)

[Spring详解（四）----Spring IOC容器的设计与实现 - 唐浩荣 - 博客园 (cnblogs.com)](https://www.cnblogs.com/tanghaorong/p/13432008.html)

IOC容器的初始化过程

1. 从XML中读取配置文件。
2. 将bean标签解析成 BeanDefinition，如解析 property 元素， 并注入到 BeanDefinition 实例中。
3. 将 BeanDefinition 注册到容器 BeanDefinitionMap 中。
4. BeanFactory 根据 BeanDefinition 的定义信息创建实例化和初始化 bean。



单例bean的初始化以及依赖注入一般都在容器初始化阶段进行，只有懒加载（lazy-init为true）的单例bean是在应用第一次调用getBean()时进行初始化和依赖注入。多例bean 在容器启动时不实例化，即使设置 lazy-init 为 false 也没用，只有调用了getBean()才进行实例化。





- **扫描Bean：**Spring启动后，ApplicationContext扫描@ComponentScan注解配置的扫描路径下的所有.class文件，类加载器根据类名加载获取类的Class对象，通过判断@Component等注解找出Bean；

- **存到beanDefinitionMap：**调用getBean()方法，给每个Bean创建BeanDefinition对象存放Class对象、作用域等信息，并放进beanDefinitionMap，映射关系是"Bean名"--->“对应的BeanDefinition”。

- 遍历beanDefinitionMap，创建所有Bean，每个Bean的创建过程如下：

- 推断构造方法：

  - 根据BeanDefinition得到Class对象。
  - 如果BeanDefinition绑定了一个Supplier，那就调用Supplier的get方法得到一个对象并直接返回。
  - 如果BeanDefinition中存在factoryMethodName，那么就调用该工厂方法得到一个bean对象并返回。
  - 如果BeanDefinition已经自动构造过了，那就调用autowireConstructor()自动构造一个对象。
  - 如果有一个构造方法，则使用该构造方法实例化对象；
  - 如果有多个构造方法，有且仅有一个构造方法注解了@Autowired，则选用该构造方法实例化对象。
  - 如果有多个构造方法，并且都没有注解@Autowired，选用无参构造方法实例化对象。如果都带参，且有0个或多个构造方法注解了@Autowired”，则报错“Bean初始化异常”。

- **实例化：**基于反射获取推断出的构造器对象，通过这个Constructor对象的newInstance(xx) 方法实例化类，得到原始对象；

- **存入三级缓存：**基于原始对象生成Lambda表达式，并放入三级缓存singletonFactories。以后如果执行这个Lambda表达式，生成的将是代理对象。

- 属性填充：

  推断和实例化、存入三级缓存、属性填充（单例池找、判断循环依赖、二级缓存找、三级缓存找到，生成提前代理对象）、执行三级缓存、存入单例池

  - **按名称注入；**基于反射获取Bean的所有属性，将有@Autowired等注解的属性开启私有成员访问限制，通过getBean()方法（解决了单例循环依赖）给该属性填充它的Bean对象。
  - **按类型注入；**

- **处理Aware回调：**如果Bean实例实现了BeanNameAware接口（通过instanceof判断），调用Bean重写的setBeanName()方法，给Bean实例的beanName变量赋值。

- **执行所有BeanPostProcessor的初始化前方法：**遍历所有BeanPostProcessor实现类所在的列表，执行每个BeanPostProcessor对象里的postProcessBeforeInitialization()方法。本方法可以通过JDK的Proxy.newProxyInstance()实现动态代理返回目标对象的代理对象。

- **初始化：**如果Bean实例实现了InitializingBean接口（通过instanceof判断），调用Bean重写的afterPropertiesSet()方法，处理初始化逻辑。afterPropertiesSet译为“在属性填充之后”

- **执行所有BeanPostProcessor的初始化后方法：**遍历所有BeanPostProcessor实现类所在的列表，执行每个BeanPostProcessor对象里的postProcessAfterInitialization()方法。本方法可以通过JDK的Proxy.newProxyInstance()实现动态代理返回目标对象的代理对象。

- **存入单例池：**如果是单例，就将最终的代理对象存入单例池（一级缓存，singletonObjects）。

- **生存期：**Bean已就绪，可以被使用，所有Bean将一直驻留在应用上下文中，直到应用上下文被销毁。

- **销毁：**如果bean实现了DisposableBean接口，Spring将调用它的destory()接口方法，同样，如果有方法注解了@PreDestroy，该方法也会被调用。



# Spring Bean



Bean 代指的就是那些被 IoC 容器所管理的对象。

将一个类声明为Bean的注解有：

- `@Component`：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 `Service` 层返回数据给前端页面。



# @Component和@Bean的区别

`@Component` 注解作用于类，而`@Bean`注解作用于方法。



# 注入Bean的注解

Spring内置的@Autowired和jdk内置的@Resource和@Inject



# @Autowired和@Resource的区别

[面试突击78：@Autowired 和 @Resource 有什么区别？-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/1003903#slide-8)



* `Autowired` 属于 Spring 内置的注解，默认的注入方式为`byType`（根据类型进行匹配），也就是说会优先根据接口类型去匹配并注入 Bean （接口的实现类）。 

  当一个接口存在多个实现类的话，`byType`这种方式就无法正确注入对象了，注入方式会变为 `byName`（根据名称进行匹配），这个名称通常就是类名（首字母小写），但是一般不会直接依靠这个规则去匹配，通常是选择通过`@Qualifier`注解显式指定名称。

* `@Resource`属于 JDK 提供的注解，默认注入方式为 `byName`。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为`byType`。可以通过name属性直接指定名字

补充的区别：上面是它们的来源不同和默认匹配规则不同，下面是添加的内容

>[五个维度，解析 Spring 中 @Autowired 和 @Resource 的区别-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2315509)
>
>* 注解内部定义的参数不同
>
> @Autowired只包含一个required参数，默认为true，表示开启自动注入。@Resource 包含7个参数，其中最重要的两个是name和type。
>
>* 注解应用的范围不同
>
> @Autowired能够用在构造方法、成员变量、方法参数及注解上，而@Resource能用在类、成员变量和方法参数上
>
>* 装配顺序不同
>
> @Autowired默认先与byType进行匹配，如果发现找到多个Bean，则又按照byName方式进行匹配，如果还有多个Bean，则报出异常
>
> > Resource具体情况如下 ：可以看上面的腾讯云的博客，也可以看下面的
> >
> > [java - @Autowired, @Resource, @Inject 三个注解的区别你懂吗？别再乱用了！ - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000040865749)
> >
> > 1. 如果同时指定了`name`和`type`，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常。
> > 2. 如果指定了`name`，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。
> > 3. 如果指定了`type`，则从上下文中找到类型匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。
> > 4. 如果既没有指定`name`，又没有指定`type`，则默认按照`byName`方式进行装配；如果没有匹配，按照`byType`h进行装配。
>
>* 是否支持null值
>
>  也就是允不允许Bean注入为空，[@Resource](https://github.com/Resource)注解的字段或setter方法，如果找不到匹配的bean，会报错。而[@Autowired](https://github.com/Autowired)注解的字段或setter方法，如果找不到匹配的bean，会抛出一个异常，除非明确设置了[@Autowired](https://github.com/Autowired)注解的required属性为false。

应用场景：[@Autowired、@Resource区别详解及特殊应用场景](https://blog.csdn.net/maple05/article/details/134974441)

一般而言，如果项目使用的是Spring框架，优先选择使用 @Autowired，因为它是Spring的一部分。如果需要更广泛的可移植性，可以考虑使用 @Resource。



# Bean的作用域

- **singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- **prototype** : 每次获取都会创建一个新的 bean 实例。也就是说，连续 `getBean()` 两次，得到的是不同的 Bean 实例。
- **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- **session** （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- **application/global-session** （仅 Web 应用可用）：每个 Web 应用在启动时创建一个 Bean（应用 Bean），该 bean 仅在当前应用启动时间内有效。
- **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

配置作用域的方式：

```java
//注解方式
@Bean
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Person personPrototype() {
    return new Person();
}

//xml方式
<bean id="..." class="..." scope="singleton"></bean>
```



# Bean是线程安全的吗

Spring 框架中的 Bean 是否线程安全，取决于其作用域和状态。

* prototype 作用域下，每次获取都会创建一个新的 bean 实例，不存在资源竞争问题，所以不存在线程安全问题。

* singleton 作用域下，IoC 容器中只有唯一的 bean 实例，可能会存在资源竞争问题（取决于 Bean 是否有状态）。如果这个 bean 是有状态的话，那就存在线程安全问题（有状态 Bean 是指包含可变的成员变量的对象）。

  解决方法：

  * 在 Bean 中尽量避免定义可变的成员变量。

  * 在类中定义一个 `ThreadLocal` 成员变量，将需要的可变成员变量保存在 `ThreadLocal` 中（推荐的一种方式）

# Bean的生命周期

[Bean的生命周期（不要背了记思想）-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2184201)

[一文读懂 Spring Bean 的生命周期_spring bean的生命周期-CSDN博客](https://blog.csdn.net/riemann_/article/details/118500805)

[Spring Bean的生命周期总结（包含面试题）_springbean生命周期面试-CSDN博客](https://blog.csdn.net/Justw320/article/details/132366425)

[spring生命周期七个过程_面试官：请你讲解一下Spring Bean的生命周期-CSDN博客](https://blog.csdn.net/weixin_39911567/article/details/111039200)

* 创建Bean的实例：Bean 容器首先会找到配置文件中的 Bean 定义，然后使用 Java 反射 API 来创建 Bean 的实例。
* Bean的属性注入/填充：为 Bean 设置相关属性和依赖，例如`@Autowired` 等注解注入的对象、`@Value` 注入的值、`setter`方法或构造函数注入依赖和值、`@Resource`注入的各种资源。
* Bean初始化：判断实现了哪些接口，然后执行对应的方法
* Bean销毁



# 谈谈对Spring AOP的了解

AOP是面向切面编程，能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于**减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。**

Spring AOP 就是基于动态代理的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理

<img src="https://img-blog.csdnimg.cn/img_convert/14eb2dd78bac59281c7c84f291bdfb1b.png" alt="动态代理——JDK动态代理原理&示例解析（图文并茂）" style="zoom:50%;" />

当然你也可以使用 **AspectJ** ！Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。

>





# Spring AOP 和 AspectJ AOP 有什么区别？

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多。



# AspectJ 定义的通知类型

- **Before**（前置通知）：目标对象的方法调用之前触发
- **After** （后置通知）：目标对象的方法调用之后触发
- **AfterReturning**（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- **AfterThrowing**（异常通知）：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- **Around** （环绕通知）：编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法



# 多个切面的执行顺序如何控制？

1、通常使用`@Order` 注解直接定义切面顺序

```java
// 值越小优先级越高
@Order(3)
@Component
@Aspect
public class LoggingAspect implements Ordered {
```

2、实现`Ordered` 接口重写 `getOrder` 方法。

```java
@Component
@Aspect
public class LoggingAspect implements Ordered {

    // ....

    @Override
    public int getOrder() {
        // 返回值越小优先级越高
        return 1;
    }
}
```



# SpringMVC

MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。



# SpringMVC的核心组件

- **`DispatcherServlet`**：**核心的中央处理器**，负责接收请求、分发，并给予客户端响应。
- **`HandlerMapping`**：**处理器映射器**，根据 URL 去匹配查找能处理的 `Handler` ，并会将请求涉及到的拦截器和 `Handler` 一起封装。
- **`HandlerAdapter`**：**处理器适配器**，根据 `HandlerMapping` 找到的 `Handler` ，适配执行对应的 `Handler`；
- **`Handler`**：**请求处理器**，处理实际请求的处理器。
- **`ViewResolver`**：**视图解析器**，根据 `Handler` 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 `DispatcherServlet` 响应客户端



# SpringMVC 工作原理了解吗

**流程说明（重要）：**

1. 客户端（浏览器）发送请求， `DispatcherServlet`拦截请求。
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping` 。`HandlerMapping` 根据 URL 去匹配查找能处理的 `Handler`（也就是我们平常说的 `Controller` 控制器） ，并会将请求涉及到的拦截器和 `Handler` 一起封装。
3. `DispatcherServlet` 调用 `HandlerAdapter`适配器执行 `Handler` 。
4. `Handler` 完成对用户请求的处理后，会返回一个 `ModelAndView` 对象给`DispatcherServlet`，`ModelAndView` 顾名思义，包含了数据模型以及相应的视图的信息。`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
5. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
6. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）。
7. 把 `View` 返回给请求者（浏览器）



# Spring 框架中用到了哪些设计模式？

- **工厂设计模式** : Spring 使用工厂模式通过 `BeanFactory`、`ApplicationContext` 创建 bean 对象。
- **代理设计模式** : Spring AOP 功能的实现。
- **单例设计模式** : Spring 中的 Bean 默认都是单例的。
- **模板方法模式** : Spring 中 `jdbcTemplate`、`hibernateTemplate` 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- **观察者模式:** Spring 事件驱动模型就是观察者模式很经典的一个应用。
- **适配器模式** : Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配`Controller`。



# Spring 的循环依赖

[面试必杀技，讲一讲Spring中的循环依赖 - 明智说 - 博客园 (cnblogs.com)](https://www.cnblogs.com/daimzh/p/13256413.html)

循环依赖是指 Bean 对象循环引用，是两个或多个 Bean 之间相互持有对方的引用

1. **一级缓存（singletonObjects）**：存放最终形态的 Bean（已经实例化、属性填充、初始化），单例池，为“Spring 的单例属性”⽽⽣。一般情况我们获取 Bean 都是从这里获取的，但是并不是所有的 Bean 都在单例池里面，例如原型 Bean 就不在里面。
2. **二级缓存（earlySingletonObjects）**：存放过渡 Bean（半成品，尚未属性填充），也就是三级缓存中`ObjectFactory`产生的对象，与三级缓存配合使用的，可以防止 AOP 的情况下，每次调用`ObjectFactory#getObject()`都是会产生新的代理对象的。
3. **三级缓存（singletonFactories）**：存放`ObjectFactory`，`ObjectFactory`的`getObject()`方法（最终调用的是`getEarlyBeanReference()`方法）可以生成原始 Bean 对象或者代理对象（如果 Bean 被 AOP 切面代理）。三级缓存只会对单例 Bean 生效。



接下来说一下 Spring 创建 Bean 的流程：

1. 先去 **一级缓存 `singletonObjects`** 中获取，存在就返回；
2. 如果不存在或者对象正在创建中，于是去 **二级缓存 `earlySingletonObjects`** 中获取；
3. 如果还没有获取到，就去 **三级缓存 `singletonFactories`** 中获取，通过执行 `ObjectFacotry` 的 `getObject()` 就可以获取该对象，获取成功之后，从三级缓存移除，并将该对象加入到二级缓存中。



以上面的循环依赖代码为例，整个解决循环依赖的流程如下：

- 当 Spring 创建 A 之后，发现 A 依赖了 B ，又去创建 B，B 依赖了 A ，又去创建 A；
- 在 B 创建 A 的时候，那么此时 A 就发生了循环依赖，由于 A 此时还没有初始化完成，因此在 **一二级缓存** 中肯定没有 A；
- 那么此时就去三级缓存中调用 `getObject()` 方法去获取 A 的 **前期暴露的对象** ，也就是调用上边加入的 `getEarlyBeanReference()` 方法，生成一个 A 的 **前期暴露对象**；
- 然后就将这个 `ObjectFactory` 从三级缓存中移除，并且将前期暴露对象放入到二级缓存中，那么 B 就将这个前期暴露对象注入到依赖，来支持循环依赖。



# Bean的懒加载

@Lazy用来标识一个类需要懒加载，需要时才会去创建

如果一个 Bean 没有被标记为懒加载，那么它会在 Spring IoC 容器启动的过程中被创建和初始化。如果一个 Bean 被标记为懒加载，那么它不会在 Spring IoC 容器启动时立即实例化，而是在第一次被请求时才创建。这可以帮助减少应用启动时的初始化时间，也可以用来解决循环依赖问题。



# Spring事务

[Spring之事务详解_spring propagation-CSDN博客](https://blog.csdn.net/u012060033/article/details/87911330)

[Spring的事务详解_spring事务-CSDN博客](https://blog.csdn.net/h295928126/article/details/125530184)



[详解 spring 声明式事务(@Transactional)-CSDN博客](https://blog.csdn.net/zxlyx/article/details/120446177)

> **当spring容器启动的时候，发现有@EnableTransactionManagement注解，此时会拦截所有bean的创建，扫描看一下bean上是否有@Transaction注解（类、或者父类、或者接口、或者方法中有这个注解都可以），如果有这个注解，spring会通过aop的方式给bean生成代理对象，代理对象中会增加一个拦截器，拦截器会拦截bean中public方法执行，会在方法执行之前启动事务，方法执行完毕之后提交或者回滚事务。稍后会专门有一篇文章带大家看这块的源码。**



Spring支持的两种事务：

* 编程式事务：在代码中硬编码(在分布式系统中推荐使用) : 通过 `TransactionTemplate`或者 `TransactionManager` 手动管理事务，事务范围过大会出现事务未提交导致超时，因此事务要比锁的粒度更小。
* **声明式事务**：在 XML 配置文件中配置或者直接基于注解（单体应用或者简单业务系统推荐使用） : 实际是通过 AOP 实现（基于`@Transactional` 的全注解方式使用最多）



Spring事务的传播行为：

**事务传播行为是为了解决业务层方法之间互相调用的事务问题**。也就是当事务方法被另一个事务方法调用时,必须指定事务应该如何传播

* REQUIRED（默认）：当前有事务就加入事务，没事务就新开事务。
* REQUIRES_NEW：强制新开事务，不管当前有没有事务。
* MANDATORY：强制加入当前事务，没有事务则报错。mandatory译为强制的、义务的。
* SUPPORTS：当前有事务就加入事务，没有则非事务执行。
* NOT_SUPPORTED：强制非事务方式执行，若有事务则挂起。
* NEVER：强制非事务方式执行，若有事务则报错。
* NESTED：当前有事务就嵌套事务，失败不会影响外层事务。



事务隔离级别：

Spring的事务隔离级别（Transaction Isolation Level）是指当多个事务同时操作同一个数据库时，一个事务读取到其他事务已经提交但未持久化到数据库中的数据所带来的影响。Spring提供了五种标准的事务隔离级别

* TRANSACTION_READ_UNCOMMITTED（读取未提交内容）：隔离级别最低，事务可以读取到其他未提交事务的数据，可能出现脏读、不可重复读和幻读等问题。
* TRANSACTION_READ_COMMITTED（读取已提交内容）：保证其他事务提交后才能读取数据，解决了脏读问题，但依然存在不可重复读和幻读问题。
* TRANSACTION_REPEATABLE_READ（可重复读）：保证在一个事务内多次查询相同数据时，结果是一致的。解决了脏读、不可重复读问题，但仍会出现幻读问题。
* TRANSACTION_SERIALIZABLE（序列化）：隔离级别最高，在整个事务过程中将所有操作串行化执行，解决了以上所有问题，但对性能有较大影响。
* TRANSACTION_DEFAULT（与数据库默认设置保持一致）：使用数据库的默认隔离级别，该级别完全取决于数据库的实现。

选择相应的隔离级别需要考虑多方面的因素，包括应用程序的访问模式、数据的重要性、并发度等。默认情况下，Spring使用数据库默认的隔离级别设置。在编程或声明式事务管理时，可以通过@Transactional注解或相关配置来指定所需的事务隔离级别。

