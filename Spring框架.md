### Spring

> 开源的轻量级 Java 开发框架

**Spring,Spring MVC,Spring Boot 之间什么关系?**

>Spring 包含了多个功能模块（上面刚刚提到过），其中最重要的是 Spring-Core（主要提供 IoC 依赖注入功能的支持） 模块， Spring 中的其他模块（比如 Spring MVC）的功能实现基本都需要依赖于该模块。
>
>Spring MVC 是 Spring 中的一个很重要的模块，主要**赋予 Spring 快速构建 MVC 架构的 Web 程序的能力**。MVC 是模型(Model)、视图(View)、控制器(Controller)的简写
>
>Spring Boot 旨在简化 Spring 开发（减少配置文件）。

#### Spring IoC（Bean）

> **IoC（Inversion of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。
>
> IoC 的思想就是 将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。

- **控制**：指的是对象创建（实例化、管理）的权力
- **反转**：控制权交给外部环境（Spring 框架、IoC 容器）

由IOC容器来负责对象的实例化以及管理**对象之间的依赖关系**，并由 IoC 容器完成对象的注入。具体来说就是不通过 new 关键字来创建对象，而是通过 IoC 容器(Spring 框架) 来帮助我们实例化对象。我们需要哪个对象，直接从 IoC 容器里面去取即可。不用再考虑对象的创建、管理等一系列的事情

> 假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了

**使用IoC的好处**：

1. 降低对象之间的耦合度；
2. 资源变的容易管理；

> **DI（依赖注入）**：指的是容器在实例化对象的时候把它依赖的类注入给它。和IOC是一回事

 **Spring Bean**:Bean 代指的就是那些被 IoC 容器所管理的对象。

@Component 和 @Bean 的区别:

1. `@Component `修饰类，表示这个类的实例将由 Spring 容器进行管理。Spring 容器会**自动扫描类路径**，找到被 `@Component` 注解 修饰 的类，并将其**实例化为 Spring 容器中的 bean。**在使用时可以通过 `@Autowired` 或其他自动装配注解，将 `@Component` 注解修饰的类的 bean **注入**到需要的地方。

   > 通过在启动类（`@SpringBootApplication`）上使用 `@ComponentScan` 注解，可以显式地指定要扫描的包及其子包。`@ComponentScan` 注解可以用于配置类上，也可以用于其他带有 `@Configuration` 注解的类上。
   >
   > 如果主配置类上没有显式地使用 `@ComponentScan` 注解指定要扫描的包，那么默认情况下，Spring 容器会扫描主配置类所在的包及其子包。

2. `@Bean`修饰方法，可以将方法的返回值作为一个 bean 注册到 Spring 容器中。`@Bean` 注解比 `@Component` 注解的自定义性更强，可以通过方法的逻辑来自定义对象的创建和初始化过程，可以手动构造对象、设置属性、调用初始化方法等。引用第三方库中的类需要装配到 `Spring`容器时，则只能通过 `@Bean`来实现。

   >`@Bean`需要来配置类中
   >
   >Spring 中的 Bean 默认都是单例的，这样有利于容器对 Bean 的管理。

注入bean的注解：`@Autowired` 、`@Resource`. 

@Autowired是Spring框架提供的注解，默认按类型装配，在容器中**先根据类型（byType）查找，如果存在多个（Bean）再根据名称（byName）进行查找**，（与`@Qualifier`一起使用，以实现使用名称装配。

```java
//实现使用名称装配
@Autowired () @Qualifier ( "baseDao" )
private BaseDao baseDao;

//如果找不到匹配的Bean时不会抛出异常，字段将被设置为null。找到就注入
@Autowired(required = false)

//构造函数上使用：
public class MyClass {
    private SomeDependency someDependency;

    @Autowired
    public MyClass(SomeDependency someDependency) {
        this.someDependency = someDependency;
    }

}
```

@Resource是Java EE提供的注解，默认按照名称装配，在IOC容器中**先根据名称（byName）查找，如果（根据名称）查找不到，再根据类型（byType）进行查找。**可以通过`name`属性指定要装配的Bean的名称。（如果name属性一旦指定，就只会按照名称进行装配。）

```Java
@Resource(name = "userinfo", type = UserInfo.class)
private UserInfo user;
```

 @Autowired 支持**属性注入、构造方法注入和 Setter 注入**，而 @Resource 只支持属性注入和 Setter 注入

**配置 bean 的作用域**：

> 作用域（Scope）指的是定义Bean实例的生命周期以及在应用程序中的可见范围。

```java
//注解方式
@Bean
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Person personPrototype() {
    return new Person();
}
//xml文件方式
<bean id="..." class="..." scope="singleton"></bean>
```

**singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。

**prototype** : 每次获取都会创建一个新的 bean 实例。

> **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean,该 bean 仅在当前 HTTP request 内有效。
>
> **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。等等

**Bean 是线程安全的吗？**

取决于bean的作用域和状态。

比如在prototype 作用域下，每次获取都会创建一个新的 bean 实例，不存在资源竞争问题，所以不存在线程安全问题。

singleton 作用域下，IoC 容器中只有唯一的 bean 实例，如果这个 bean 是有状态的话，那就存在线程安全问题（有状态 Bean 是**指包含可变的成员变量的对象**）。不过，大部分 Bean 实际都是无状态（没有**定义可变的成员变量**）。这种情况下， Bean 是线程安全的。

对于有状态单例 Bean 的线程安全问题，解决方法是：在类中定义一个 `ThreadLocal` 成员变量，将可变成员变量保存在 `ThreadLocal` 中。`ThreadLocal`为每个线程提供了独立的状态副本，避免了线程安全问题

**Bean 的生命周期了解么?**（七步）

1. 实例化，Bean 容器找到配置文件中 Spring Bean 的定义，通过反射创建bean的实例
2. 依赖注入。如果涉及到一些属性值 ，利用 `set()`方法设置一些属性值。
   1. 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()`方法，传入 Bean 的名字。
3. 初始化前执行。如果存在与 加载这个 Bean 的 Spring 容器 相关的 `BeanPostProcessor` 对象，执行`postProcessBeforeInitialization()` 方法
   1. 如果 Bean 实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法。
4. 初始化。如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
5. 初始化后，如果存在与 加载这个 Bean 的 Spring 容器 相关的 `BeanPostProcessor` 对象，执行`postProcessAfterInitialization()` 方法
6. 使用bean
7. 销毁bean。
   1. 如果 Bean 实现了 `DisposableBean` 接口，执行重写的 `destroy()` 方法。
   2. 之后如果 Bean 在配置文件中的**定义包含 destroy-method 属性**，执行指定的方法。

**Spring Bean 的定义包含哪些内容？**：

也就是`BeanDefinition`:

- 全限定类名， 指向一个具体存在的Java类。通常是Bean的实际实现类
- bean的名字，默认是类名首字母小写。
- bean的作用域（默认单例
- bean是否延迟加载
- bean的依赖关系
- 初始化完成和销毁时的回调函数
- 给指定的属性赋予的默认值
- 指定有参构造时需要注入的参数值`constructorArgumentValues `

>Spring 容器启动之后，会调用 BeanDefinitionReader 工具类的loadBeanDefinitions()方法，启动对配置文件的加载和解析。 BeanDefinitionReader 的主要作用是读取Spring 配置文件中的内容，将其转换为 BeanDefinition 对象。
>
>然后，所有的 BeanDefinition 对象都会保存到一个叫做 beanDefinitionMap 的容器中，这个容器是 Map 类型，以 Bean的唯一标识作为 key，以BeanDefinition 对象实例作为值。这样 Spring 容器创建 Bean时，就不需要再次读取和解析配置文件，只需要根据 Bean 的唯一标识，去beanDefinitionMap 中取到对应的 BeanDefinition 对象即可。

**BeanFactory 和 ApplicantContext**

`BeanFactory`（提供了对Bean的创建、配置和管理的基本功能） 是spring的基础接口，它最主要的方法就是 getBean(String var1)，返回特定名称的 Bean

`ApplicationContext`(应用上下文)是BeanFactory接口的子接口，包含了BeanFactory的所有功能并且 在 BeanFactory基础上增加了很多功能（如国际化、事件传播、资源加载等）。一般在实际开发中使用ApplicationContext

> BeanFactory在启动的时候不会去实例化Bean，中有从容器中拿Bean的时候才会去实例化；(应用启动的时候占用资源很少；对资源要求较高的应用，比较有优势； )
>
> ApplicationContext在启动的时候就把所有的Bean全部实例化了。它还可以为Bean配置lazy-init=true来让Bean延迟实例化；(一方面，在系统启动的时候，尽早的发现系统中的配置问题 。另一方面，所有的Bean在启动的时候都加载，系统运行的速度快；)

```java
//使用bean工厂：
public class MainApp {
    public static void main(String[] args) {
        // 创建BeanFactory并加载配置文件
        BeanFactory factory = new XmlBeanFactory(new ClassPathResource("beans.xml"));
        HelloWorld helloWorld = (HelloWorld) factory.getBean("helloWorld");
        helloWorld.printMessage();
    }
}
//使用应用上下文：
public class HelloWorldApp{
   public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
      HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
      obj.getMessage();
   }
}
```

**Spring 容器启动阶段在做什么？**：

> Spring 的 IOC 容器工作的过程，其实可以划分为两个阶段：**容器启动阶段**和**Bean 实例化阶段**。

Spring容器会加载应用程序的配置信息（这些配置信息可以是XML配置文件、注解配置或Java配置类）并分析，然后将分析后的信息组为相应的 BeanDefinition。最后把这些保存了 Bean 定义必要信息的 BeanDefinition，注册到相应的 BeanDefinitionRegistry。容器启动结束

#### Spring AOP

> 面向切面编程，AOP 是 OOP（面向对象编程）的一种延续

AOP 之所以叫面向切面编程，是因为它的核心思想就是将 **横切关注点 (多个类或对象中的公共行为)** 从核心业务逻辑中分离出来，形成一个个的**切面（对 公共行为 进行封装的类 | 一个切面就是一个类）**。实现代码的复用和解耦，提高代码的可维护性和可扩展性。

> **连接点（JoinPoint）**：方法调用或者方法执行时的某个**特定时刻**
>
> **通知（Advice）**：**切面在某个连接点要执行的操作**。
>
> **切点（Pointcut）**：一个切点是一个表达式，它用来**匹配哪些连接点需要被切面所增强。**
>
> **织入（Weaving）**：将 通知 作用到 切点 匹配的 连接点 上。

经常用于日志记录：自定义日志记录注解，利用 AOP，一行代码即可实现日志记录、权限控制、事务管理（`@Transactional`）

Spring AOP 就是基于动态代理的.，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理, 代理对象在执行某些方法的时候会在该方法基础上增加一些切面逻辑。

Spring AOP采用**运行时织入**：切面在应用运行的某个时刻被织入。（在织入切面时，AOP 容器会**为目标对象动态地创建一个代理对象**。

**AOP工作流程**：

1. spring工程启动后，spring读取切面配置中的切入点
2. 初始化bean，此时会判断bean中的方法是否匹配到任意切入点。如果匹配成功，就创建代理对象；匹配失败创建原始对象。
3. 使用bean，调用bean中的方法完成操作。

> Spring容器中的对象都是代理对象吗？
>
> 不是,spring的ioc 容器中默认都是原生对象，只有通过aop增强的对象才是代理对象。配置了aop的类或者类中方法上有@Transactional注解的

**Spring AOP 和 AspectJ AOP 有什么区别？**

> Spring AOP和AspectJ AOP是两种常用的AOP（面向切面编程）实现方式，Spring AOP 已经集成了 AspectJ 

Spring AOP 属于运行时增强,通过动态代理技术来实现,它可以代理目标对象，将切面逻辑织入到目标对象的方法调用中。Spring AOP**只支持Spring管理的Bean**，并且只能在方法级别进行切面行为的织入。

> Aspect J 出现比 Spring AOP早，Spring AOP没有直接用的A J的注解的定义，但是具体实现二者不同。

 AspectJ 是编译时增强，基于字节码操作来实现，可以直接织入到任何Java类中。

AspectJ **定义的通知类型**有哪些？

@**Before**（前置通知）、@**After** （后置通知）、@**Around** （环绕通知）

- @**AfterReturning**（返回通知）：目标对象的方法**调用完成，在返回结果值之后**触发
- @**AfterThrowing**（异常通知）：目标对象的方法运行中抛出异常后触发。

**多个切面的执行顺序如何控制？**

1. 使用`@Order` 注解直接定义切面顺序

   ```java
   // 值越小优先级越高 直接定义在切面类上
   @Order(3)
   ```

2. 实现Ordered 接口重写 getOrder 方法。

   ```java
   @Component
   @Aspect
   public class LoggingAspect implements Ordered {
   
       @Override
       public int getOrder() {
           // 返回值越小优先级越高
           return 1;
       }
   }
   ```

#### Spring MVC

> MVC 是模型(Model)、视图(View)、控制器(Controller)的简写。 Spring MVC 使用简单和方便，开发效率更高

MVC 是一种设计模式，核心思想是将业务流程、数据和用户交互界面分离，MVC 下我们一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层(控制层，返回数据给前台页面)。

**Spring MVC 的核心组件 以及 工作流程**

![img](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/de6d2b213f112297298f3e223bf08f28.png)

1. 客户端（浏览器）发送请求， `DispatcherServlet`拦截请求。
   1. **`DispatcherServlet`**：前端控制器，是整个流程控制的**核心**，负责接收请求、控制其他组件的执行，进行统一调度，并给予客户端响应。
2. `DispatcherServlet` 调用 `HandlerMapping` 。`HandlerMapping` 负责根据 URL 来确定处理该请求的 `Handler` ，并会将请求涉及到的拦截器和 `Handler` 一起封装。
   1. **`Handler`**：**请求处理器**，处理实际请求的处理器。（也就是平常说的 `Controller` 控制器
3. `DispatcherServlet` 调用 `HandlerAdapter` 执行 `Handler` 的处理方法
   1. **`HandlerAdapter`**：**处理器适配器**，根据 `HandlerMapping` 找到的 `Handler` ，调用 `Handler`的处理方法
   2. `ModelAndView` ，包含了数据模型以及相应的逻辑视图的信息。
4. `Handler` 完成对用户请求的处理后，会返回一个 `ModelAndView` 对象给`DispatcherServlet`
5. `DispatcherServlet`使用`ViewResolver` ，根据逻辑 `View` 查找实际的 `View`
   1. `ViewResolver`：视图解析器，根据 `Handler` 返回的逻辑视图，解析并渲染真正的视图，并传递给 `DispatcherServlet`
6. `DispaterServlet` 把收到的的 数据对象(`model`) 传给 `View` ，`View`使用`model`渲染页面，生成最终的页面内容
7. `DispaterServlet`把 视图结果 返回给请求者

**统一异常处理怎么做？**

可以定义一个全局异常处理器，用一个专门的类去处理异常。当 `Controller` 中的方法抛出异常的时候会被异常处理器捕捉，由被`@ExceptionHandler` 注解修饰的方法进行处理。

如果一个异常可以匹配多个方法，那么ExceptionHandler会取匹配度最高的哪个，也就是范围最小的。

```java
@ControllerAdvice //用于定义一个全局的异常处理器和全局数据绑定设置。
@ResponseBody		
public class GlobalExceptionHandler {

    @ExceptionHandler(BaseException.class)
    public ResponseEntity<?> handleAppException(BaseException ex, HttpServletRequest request) {
      //......
    }

    @ExceptionHandler(value = ResourceNotFoundException.class)
    public ResponseEntity<ErrorReponse> handleResourceNotFoundException(ResourceNotFoundException ex, HttpServletRequest request) {
      //......
    }
}
```

`@ResponseBody`注解用于**指示控制器方法的返回值应该直接作为HTTP响应的内容**，而不是视图名称或模型数据。
比如：通过在控制器方法上添加@ResponseBody注解，方法返回的对象将会被转换为JSON或者其他可以用于响应其他类型的数据，并作为HTTP响应的内容返回给客户端。使用`@ResponseBody`注解可以方便地编写RESTful风格的控制器方法。因为控制器方法可以直接返回数据对象，而无需通过视图解析器渲染视图。

为了使`@ResponseBody`注解生效，还需要在Spring MVC配置中启用消息转换器（MessageConverter）。消息转换器负责将Java对象转换为指定的数据格式，以及将接收到的请求数据转换为Java对象。比如：`Jackson JSON`转换器

**Restful 风格的接口的流程**

1.在 `HandlerAdapter` 调用处理器的处理方法执行结束后，`HandlerAdapter `会调用 方法返回值处理器`HandlerMethodReturnValueHandler `处理返回值，它会把**要响应的结果**封装并将返回值写入其中，并且在写入过程中，会对返回值进行 Json 序列化

2.处理完请求后，返回的 `ModealAndView` 为 null，ServletServerHttpResponse 里也已经写入了响应，所以不用关心 View 的处理

#### Spring 事务

> Spring 事务的本质其实就是数据库对事务的支持

1. 编程式事务管理：

在代码中硬编码,通过 `TransactionTemplate`或者`TransactionManager`手动管理事务，通过 execute 方法来执行事务，这样就可以在方法内部实现事务的控制。

```java
@Autowired
private TransactionTemplate transactionTemplate;
public void testTransaction() {
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                try {
                    // ....  业务代码
                } catch (Exception e){
                    //回滚
                    transactionStatus.setRollbackOnly();
                }
            }
        });
}
```

2. 声明式事务管理：

在 XML 配置文件中配置或者直接基于`@Transactional`注解。其本质是通过 AOP 功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中

> 也就是在目标方法开始之前启动一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务
>
> 优点：不需要在业务逻辑代码中掺杂事务管理的代码。
>
> 缺点：最细粒度只能作用到方法级别，无法做到像编程式事务那样可以作用到代码块级别。

`@Transactional`,**该注解只能应用到 public 方法上，否则不生效。**如果这个注解使用在类上的话，表明该注解对该类中所有的 public 方法都生效。事务管理通常由代理对象来完成，代理对象只能拦截公共方法的调用

> Spring 默认使用基于 JDK 的动态代理（当接口存在时）或基于 CGLIB 的代理（当只有类时）来实现事务。
>
> 在spring中，有两种动态代理的方式，一种是jdk，它是将原始对象放入代理对象内部，通过调用内含的原始对象来实现原始的业务逻辑，这是一种装饰器模式；而另一种是cglib，它是通过生成原始对象的子类，子类复写父类的方法，从而实现对父类的增强。

[一口气说出 6种，@Transactional注解的失效场景 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/114461128)

`@Transactional` 注解默认回滚策略是只有在遇到`RuntimeException`(运行时异常) 或者 `Error` 时才会回滚事务，而不会回滚 `Checked Exception`（受检查异常 可以 在方法签名中进行声明（抛出），或者在方法内部进行捕获和处理）。

可以手动指定

```java
//指定哪些异常需要回滚，哪些不需要回滚
@Transactional(rollbackFor = Exception.class, noRollbackFor = CustomException.class)
public void someMethod() {
// some business logic
}
```

**事务传播行为** `propagation` ：当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。

**required**  :  如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

supports  mandatory  **required_new**  not_supported  

nested : 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行，如果外部方法无事务，则单独开启一个事务

> 当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。

![7种事务传播机制](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/spring-a6e2a8dc-9771-4d8b-9d91-76ddee98af1a.png)

**Spring 事务中的隔离级别** `isolation`

1. `ISOLATION_DEFAULT`：使用后端数据库默认的隔离界别，**MySQL 默认可重复读，Oracle 默认读已提交**。
2. `ISOLATION_READ_UNCOMMITTED`：读未提交
3. `ISOLATION_READ_COMMITTED`：读已提交
4. `ISOLATION_REPEATABLE_READ`：可重复读
5. `ISOLATION_SERIALIZABLE`：串行化

事务超时属性 `timeout`：默认值为-1（ 值的单位是秒，没有超时时间）

只读属性`readOnly`:  指定事务是否为只读事务，默认值为 false。

> 对于只有读取数据查询的事务，可以指定事务类型为 readonly，即只读事务。

如果你给方法加上了`Transactional`注解的话，这个方法执行的所有`sql`会被放在一个事务中。如果不加`Transactional`，每条`sql`会开启一个单独的事务，中间被其它事务改了数据，都会实时读取到最新值。

* 如果你一次执行单条查询语句，则没有必要启用事务支持，数据库默认支持 SQL 执行期间的读一致性；

- 如果你**一次执行多条查询语句**，例如统计查询，报表查询，在这种场景下，**多条查询 SQL 必须保证整体的读一致性**，否则，在前条 SQL 查询之后，后条 SQL 查询之前，数据被其他用户改变，则该次整体的统计查询将会出现读数据不一致的状态，此时，应该启用事务支持

> 公共方法A没有被`@Transactional`修饰，而公共方法B被`@Transactional`修饰，那么在方法A内部调用方法B时，方法B的事务将不会生效。
>
> 解决办法是，让B方法变成代理调用就会生效，也就是不直接调用B（this.B() )而是 proxy.B() (注入当前类获取)

```java
@Service
public class MyService {
//或者用这种方法获取本类的代理
private void method1() {
    // 先获取该类的代理对象，然后通过代理对象调用method2。
     ((MyService)AopContext.currentProxy()).method2(); 
     //......
}
@Transactional
 public void method2() {
     //......
  }
}
```

声明式事务的实现原理：

一般来说，在外部调用一个事务方法的时候，需要通过自动注入拿到事务方法所在的类的bean，这个bean实际上是原始类的一个代理对象，该代理对象是spring通过jdk动态代理或者cglib来实现的。当通过代理对象调用事务方法的时候，会被spring AOP 增强拦截器拦截到，在方法开始前会开启一个事务，在方法结束后根据执行结果决定提交事务还是回滚事务。

#### Spring Data JPA

指定实体类实例在持久化到数据库的时候某个字段非持久化：

```Java
transient String transient3; 
@Transient
String transient4;
```

#### Spring 框架中的设计模式？

1. DI(Dependency Inject,**依赖注入**)是**实现控制反转（IOC）的一种设计模式**，

   通过将对象的依赖关系外部化，由 容器或框架 来负责创建和注入依赖对象，以实现对象之间的解耦。

2. 工厂设计模式

   Spring 使用工厂模式可以通过 `BeanFactory` 或 `ApplicationContext` 创建 bean 对象。

   > `BeanFactory`：延迟注入(使用到某个 bean 的时候才会注入),相比于`ApplicationContext` 来说会占用更少的内存，程序启动速度更快。
   >
   > `ApplicationContext`：容器启动的时候，不管你用没用到，一次性创建所有 bean 。`ApplicationContext` 扩展了 `BeanFactory` ,除了有`BeanFactory`的功能还有额外更多功能。
   >
   > * `ClassPathXmlApplication`：把上下文文件当成类路径资源。

3. 代理设计模式

   Spring AOP 就是基于动态代理的。

4. 单例设计模式

   - 对于频繁使用的对象，可以省略创建对象所花费的时间
   - 由于 new 操作的次数减少，因而对系统内存的使用频率也会降低，这将减轻 GC 压力

   Spring 中 bean 的默认作用域就是 singleton(单例)的。

   [Spring 中的设计模式详解 | JavaGuide](https://javaguide.cn/system-design/framework/spring/spring-design-patterns-summary.html#spring-mvc-中的适配器模式)

#### 自动装配

Spring IoC 容器知道所有 Bean 的配置信息，Spring IoC 容器可以按照某种规则对容器中的 Bean 进行自动装配，而无须通过显式的方式进行依赖配置。

Spring 提供了 4 种自动装配类型：

1. **byName**：根据名称进行自动匹配 
2. **byType**：根据类型进行自动匹配
3. 使用构造器自动装配：通过构造函数自动装配。（根据类型在容器中寻找
4. **autodetect**：根据 Bean 的默认机制。该模式自动探测使用构造器自动装配或者byType自动装配。首先，首先会尝试找合适的带参数的构造器，如果找到的话就是用构造器自动装配，如果在bean内部没有找到相应的构造器或者是无参构造器，容器就会自动选择byTpe的自动装配方式。

#### 循环依赖

对象A依赖了对象B，对象B又依赖了对象A

> Spring 能解决 setter 注入的循环依赖了，因为实例化和属性赋值是分开

通过三级缓存解决了spring实验aop时的循环依赖问题。Singleton 的 Bean 要被创建完成，需要经历这三步：实例化、属性填充、初始化。

注入这个动作发生在第二步，**属性填充**，Spring 可以在这一步通过**三级缓存**来解决了循环依赖：

1. 一级缓存 : `Map<String,Object>` **singletonObjects**，单例池，用于保存实例化、属性赋值（注入）、初始化完成的 bean 实例。**key是Bean的名称，value是完整的单例实例对象**，
2. 二级缓存 : `Map<String,Object>` **earlySingletonObjects**，早期曝光对象，用于保存实例化完成的 bean 实例
3. 三级缓存 : `Map<String,ObjectFactory<?>>` **singletonFactories**，早期曝光对象工厂，key是Bean的名称，value是单例工厂，工厂用于创建key所对应的Bean对象。

创建 A 实例，实例化的时候把 A 的对象⼯⼚放⼊三级缓存。A 注⼊属性时，发现依赖 B，此时 B 还没有被创建出来，所以去实例化 B。B 注⼊属性时发现依赖 A，它就从缓存里找 A 对象。依次从⼀级到三级缓存查询 A。最终在三级缓存中通过对象⼯⼚拿到 A，虽然 A 不太完善，但是存在，就把 A 放⼊⼆级缓存。同时删除三级缓存中的 A，此时，B 已经实例化并且初始化完成了，把 B 放入⼀级缓存。接着 A 继续属性赋值，顺利从⼀级缓存拿到实例化且初始化完成的 B 对象，A 对象创建也完成，删除⼆级缓存中的 A，同时把 A 放⼊⼀级缓存。最后，⼀级缓存中保存着实例化、初始化都完成的 A、B 对象。

如果都是构造器注入的话，那么都得在实例化这一步完成注入，没有可操作的空间。也就无法解决循环依赖的问题。

> 循环依赖只发生在 Singleton 作用域的 Bean 之间，因为**如果是 Prototype 作用域的 Bean，Spring 会直接抛出异常**。AB 循环依赖，A 实例化的时候，发现依赖 B，创建 B 实例，创建 B 的时候发现需要 A，创建 A1 实例……无限套娃。。。。
>
> 解决循环依赖：最好的方法就是开发人员做好设计，别让 Bean 循环依赖。

**为什么要三级缓存？⼆级不⾏吗？**：

不行，spring基于AOP的思想，在属性填充的时候，**注入的是其他类的代理对象**

> 如果是没有代理的情况下，可以使用二级缓存解决循环依赖。
>
> 只有二级缓存的话，第一级缓存存放完整的bean对象，第二级缓存存放的是实例化之后还不完整的bean对象。

一般来说 Bean 在初始化完成后，通过 回调函数 去⽣成代理对象之后，假设没有第三级缓存，B是取不到A的代理对象的。如果是三级缓存的话，在第三级的缓存中存的bean工厂可以提前生成类的代理对象，通过这种方式拿到了代理对象解决了循环依赖的问题。

### mybatis

Mybatis 是一个半自动的 对象-关系映射框架，可以将Java的实体类映射到数据库的表上。它内部封装了 JDBC，开发时只需要关注 SQL 语句本身，不需要花费精力去处理加载驱动、创建连接等工作。（映射信息可以通过xml和注解来进行配置）

> Mybatis 在查询关联对象或关联集合对象时，需要手动编写 SQL 来完成，所以，被称之为半自动 ORM 映射工具。

[MyBatis常见面试题总结 | JavaGuide](https://javaguide.cn/system-design/framework/mybatis/mybatis-interview.html)

> mybatis 在 spring 的使用中，只需要定义接口，就可以和 xml 中的配置 的 sql 语句，进行关联，执行数据库增删改查操作。怎么实现的

**JDBC 的执行步骤？**JDBC： Java 数据库连接 ，是一个用于执行 SQL 语句的 Java API

1. 加载驱动`Class.forName("com.mysql.cj.jdbc.Driver");`、

2. 建立连接

   Connection对象代表了应用程序和数据库的一个连接会话。可以使用它来创建执行 SQL 语句的`Statement`或`PreparedStatement`和`CallableStatement`对象以及管理事务等

   `Connection conn = DriverManager.getConnection("url", "username", "password");`

3. 创建执行语句

   - 在对数据库只执行**一次性存取的时侯**，用 Statement 对象进行处理。每次执行`Statement`对象的`executeQuery`或`executeUpdate`方法时，SQL 语句在数据库端都需要重新编译和执行。

     > 不支持参数化查询。如果需要在 SQL 语句中插入变量，通常需要通过字符串拼接的方式来实现，这会增加 SQL 注入攻击的风险。

   - 而PreparedStatement对象执行的语句会在数据库进行预处理（ SQL 语句在`PreparedStatement`对象创建时就被发送到数据库进行预编译）。对于同一个SQL命令，PreparedStatement都只对它解析和编译一次。对于批量处理可以大大提高效率.对于动态查询有很好的支持。

     > 支持参数化查询，即可以在 SQL 语句中使用问号（?）作为参数占位符。通过`setXxx`方法（如`setString`、`setInt`）设置参数

   `Statement stmt = conn.createStatement();`

4. 执行 SQL 语句、

   `ResultSet rs = stmt.executeQuery("SELECT * FROM tableName");`

5. 处理结果集

   `while (rs.next()) {
       String data = rs.getString("columnName");
       // 处理每一行数据
   }`

6. 关闭资源。释放数据库连接等资源

   `if(rs != null){rs.close();}if(stmt != null){stmt.close();}if(conn != null){conn.close();}`

**mybatis功能架构**：

分为三层：

- API 接口层：提供接口供外部调用
- 数据处理层：负责具体的 SQL 查找、SQL 解析、SQL 执行和执行结果映射处理等。
- 基础支撑层：负责最基础的功能支撑（连接管理、事务管理、配置加载）

**mybatis工作原理**：

按工作原理，可以分为两大步：**会话工厂的生成**、会话运行。

1. **读取MyBatis的配置**文件 和 mybatis的映射文件。

   这些文件中的信息被解析后会被保存在一个Configuration类的实例中，这个实例会作为一个参数 传入`SqlSessionFactoryBuilder的build方法中` 参与会话工厂构建

   > 配置文件:mybatis-config.xml为MyBatis的全局配置文件，用于配置数据库连接信息。
   >
   > 映射文件:xxxMapper.xml，该文件中配置了操作数据库的SQL语句。该文件需要在MyBatis配置文件mybatis-config.xml中加载。mybatis-config.xml 文件可以加载多个映射文件，每个文件对应数据库中的一张表。

2. **构建 SqlSessionFactory(会话工厂)**：通过调用`SqlSessionFactoryBuilder.build`方法来获得一个SqlSessionFactory对象。（单例 包含Configuration以及Executor）

**会话运行**：

3. 由会话工厂**创建SqlSession(会话)对象**，该对象中包含了执行SQL语句的所有方法。

4. 当 SqlSession 的方法被调用时，SqlSession 将会**委托 Executor 来执行相应的 SQL 语句**。Executor 是MyBatis底层定义的一个操作数据库的接口。它将根据SqlSession传递的参数动态地生成需要执行的SQL语句，负责将 SQL 语句发送给数据库并获取执行结果。执行结果返回时，Executor 将负责将结果集中的数据映射到 Java 对象上。同时还负责缓存的管理。Executor 会根据配置和使用情况来判断是否使用缓存，以及何时更新缓存。

   - Executor 在工作的时候，（没有启用二级缓存或者缓存中不存在结果）会先创建一个 **StatementHandler** 对象，它负责处理 SQL 语句的**预处理、参数设置**等工作。同时也会创建出`ParameterHandler`(负责设置sql中的参数)和`ResultSetHandler`

   - SQL语句在被处理后会被发送到数据库；//StatementHandler 会调用 Connection 对象的相关方法，**创建 PreparedStatement 对象，并将 SQL 语句发送给数据库**。

     > MappedStatement对象，存储的是映射文件中解析SQL语句得到的信息（SQL语句的内容，id{指唯一标识某一sql语句的字符串}，类型），每个SQL语句对应一个MappedStatement对象，该对象会作为参数被传入到Executor 的方法中。

5. **数据库执行 SQL 语句**，返回结果集。
6. Executor 使用 ResultSetHandler 对象**处理执行结果**。ResultSetHandler 负责将结果集中的数据映射为 Java 对象，可以使用配置文件中的映射规则进行对象属性的赋值。Executor 将处理后的结果**返回给调用方（SqlSession）**。

为什么**Mapper 接口不需要实现类**？

**因为 MyBatis使用JDK的了动态代理的机制，可以在运行时动态生成 Mapper 接口的实现类。**

> spring是不会注册只有接口没有实现类的这种Mapper类成为bean的

通过`sqlSession.getMapper(xxxMapper.class)`获取Mapper实例的时候，会先获取 MapperProxyFactory——Mapper 代理工厂; 通过代理工厂`mapperProxyFactory.newInstance(sqlSession);`生成 MapperProxy（Mapper 代理对象，它实现了Mapper接口，并重写了接口中的方法）并返回。当调用mapper接口中的方法的时候，会进入到MapperProxy的invoke方法中，在该方法中执行sql语句。

invoke方法的内容是什么？

先通过 `cachedMapperMethod` 方法初始化一个`MapperMethod `对象；接下来调用MapperMethod 里的 `excute` 方法，会去执行 sql。这个方法的底层是 通过 SqlSession 实例去运行对象的 sql。

**MyBatis 都有哪些 Executor 执行器**？MyBatis 有三种基本的 `Executor` 执行器：

- **`SimpleExecutor`：** 每执行一次 update 或 select，就开启一个 Statement 对象，用完立刻关闭 Statement 对象。

- **`ReuseExecutor`：** 执行 update 或 select，以 sql 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后，不关闭 Statement 对象，而是放置于 Map<String, Statement>内，供下一次使用。

- **`BatchExecutor`**：执行 批处理语句的时候（JDBC中的批处理只支持 insert、update 、delete 等类型的SQL语句，不支持select类型的SQL语句。）

  使用`Statement.addBatch()`方法在Statement对象上添加SQL语句到批处理中。底层会调用Statement的 `executeBatch()`方法实现批量操作,执行所有sql语句，执行结束提交更改.一次批量操作只使用一个statement

> Executor 的这些特点，都严格限制在 SqlSession 生命周期范围内。
>
> **指定使用哪一种 Executor 执行器？**：在配置文件-设置（settings）中指定，也可以手动给 DefaultSqlSessionFactory 的创建 SqlSession 的方法传递 ExecutorType 类型参数

**#{}和${}的区别?**：

- `${}`属于原样文本替换，$ {}将传入的数据会直接拼接在在sql中

- Mybatis 在处理`#{}`时，会将 SQL 中的`#{}`替换为?号，调用 PreparedStatement 的 set 方法来赋值。`#{}`传入参数是以字符串传入,会对自动传入的数据加一个双引号，

- **#{}方式能够很大程度防止sql注入(安全)，${}方式无法防止Sql注入**。

  > 在`JDBC`能使用占位符的地方,最好优先使用`#{}`,在`JDBC`不支持使用占位符的地方,就只能使用`${}`
  >
  > `#{item.name}` 的取值方式为使用反射从参数对象中获取 item 对象的 name 属性值，相当于 `param.getItem().getName()`。

- PrepareStatement的占位符也就是#{}，不可用于表名、字段名，只可用于参数

**TypeHandler**:

> TypeHandler是MyBatis中用于处理Java对象与数据库类型之间转换的机制。
>
> 我们通过mybatis对数据库的存取都要通过TypeHandler进行类型转换.

TypeHandler可以将 自定义的Java对象 转换为数据库可以存储的类型，并在从数据库中检索数据时将其转换回对应的Java对象。

```java
@MappedTypes(UserInfo.class)	//声明需要处理Java中的什么类型
@MappedJdbcTypes(JdbcType.VARCHAR)	//声明需要转换数据库中的什么类型
public class UserInfoTypeHandler extends BaseTypeHandler<UserInfo>
```

[【保姆级】使用Mybatis的TypeHandler优雅的存取自定义类型_setnonnullparameter-CSDN博客](https://blog.csdn.net/qq_42874315/article/details/119854298)

- 在resultMap ，<association>用于一对一映射和<collection>用于一对多映射。

- 新增标签中添加：keyProperty=" ID " ，用于获取主键，获取方式是调用原对象的getId方法获取。

**Mybatis 是支持延迟加载**：

在 Mybatis 配置文件中默认关闭，可以配置是否启用延迟加载 lazyLoadingEnabled=true|false。Mybatis **支持 association 关联对象和 collection 关联集合对象**的延迟加载。延迟加载就是按需加载，在需要查询的时候再去查询，可以在一定程度上避免连接查询，提高单单次询的效率；缺点是：需要多次与数据库进行交互

原理：当调用 主实体对象的关联对象时，MyBatis会生成一个代理对象来代替真正的关联对象。这个代理对象持有一个特殊的标记，表示该对象还没有被完全加载。当访问代理对象的属性或方法时，MyBatis会（通过拦截器）拦截这个调用，并检查代理对象是否已经加载了关联对象的数据。如果代理对象还没有加载关联对象的数据，MyBatis会根据需要的关联关系和相关配置，执行额外的SQL查询来加载关联对象的数据，并将其填充到代理对象中。一旦关联对象的数据被加载，后续访问将直接返回已加载的数据，而不再执行额外的查询。

> MyBatis 的延迟加载只是对关联对象的查询有迟延设置，对于主加载对象都是直接执行查询语句的。

mybatis的批量操作可以使用**使用 foreach 标签**{适合小规模批量插入，一次性插入超过一千条的时候MyBatis开始报错} 或者 修改ExecutorTYPE为BATCH，使用时关闭自动提交，手动控制每多少条提交一次并且每次提交清空缓存，目的在于防止*内存溢出*。

默认的是simple，该模式下它为每个语句的执行**创建一个新的预处理语句**，单条提交sql；而batch模式重复使用已经预处理的语句，并且批量执行所有语句，显然batch性能将更优；但batch模式也有自己的问题，比如在Insert操作时，在事务没有提交之前，是没有办法获取到自增的id，这在某型情形下是不符合业务要求的；
[MyBatis批量插入大量数据（1w以上）--解决方案_mybatis添加1万条数据耗时-CSDN博客](https://blog.csdn.net/xlecho/article/details/102474146)

**Mybatis一级缓存和二级缓存**：

[Mybatis一级缓存和二级缓存原理区别(图文详解)_mybatis二级缓存-CSDN博客](https://blog.csdn.net/ChenRui_yz/article/details/126967007)

- 一级缓存：SqlSession级别的缓存，缓存的数据只在SqlSession内有效。各个 SqlSession 之间的缓存相互隔离，默认打开。

使用Mybatis进行数据库的操作时候，会创建一个SqlSession来进行一次数据库的会话，会话结束则关闭SqlSession对象。Mybatis对**每一次会话**都添加了缓存操作，会话中相同的SQL查询不用重复查询数据库，只需要从缓存中获取。当执行SQL时候两次查询中间发生了增删改的操作，则SQLSession的缓存会被清空。

- 二级缓存：mapper 级别（全局）的缓存，同一个namespace公用这一个缓存，所以对SqlSession是共享的，二级缓存需要我们手动开启。当开启缓存后，数据的查询执行的流程就是 **二级缓存 -> 一级缓存 -> 数据库**。

MyBatis 一级缓存最大的共享范围就是一个SqlSession内部，那么如果多个 SqlSession 需要共享缓存，则需要开启二级缓存。要开启二级缓存，使用二级缓存属性类需要实现 Serializable 序列化接口

**Mybatis 的插件运行原理**：

> 允许你在MyBatis的运行时过程中修改或扩展其行为。插件可以用于拦截MyBatis的方法调用，并在方法执行前后执行自定义逻辑。

原理在于MyBatis插件通过动态代理机制拦截MyBatis的方法调用，并在方法执行前后执行自定义的逻辑；

在项目启动的时候判断 组件 是否有被拦截，如果没有直接返回原对象。如果有被拦截，mybatis会使用Plugin工具类，为**目标对象生成一个代理对象**。当使用组件（bean）的时候，如果组件不是通过Plugin生成的代理对象，那就执行原方法，如果是通过Plugin生成的代理对象，使用代理对象执行方法的时候就会进入`Plugin. invoke` 方法，在 invoke 方法中，执行插件自定义的 intercept 方法。

> `Plugin`类所代理的目标对象通常是由MyBatis通过动态代理生成的Mapper接口的实现类。
>
> mybatis的插件都需要实现 Mybatis 下的 Interceptor 接口并重写 intercept()方法，在这个方法中实现对对目标方法进行自定义的增强或修改

MyBatis 仅可以编写针对 `ParameterHandler`、 `ResultSetHandler`、 `StatementHandler`、 `Executor` 这 4 种接口的插件

- 实现一个插件：

  实现 Mybatis 中的 Interceptor 接口并重写 intercept()方法；在实现类上添加注解，确定要拦截的对象，要拦截的方法；最后再 MyBatis 配置文件里面配置该插件

- **分页插件的基本原理是**使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 sql，然后重写 sql，添加对应的物理分页语句和物理分页参数。

### SpringBoot 

> 为了解决Spring**需要大量 XML 配置**的缺陷
>
> 开启某些 Spring 特性时，比如事务管理和 Spring MVC，还是需要用 XML 或 Java 进行显式配置。启用第三方库时也需要显式配置

主要优点：在于实现了自动配置

1. 用 SpringBoot 框架可以快速开发一个Java项目。简化了Java应用程序的开发和部署过程。提供了一套默认配置，采用**约定优于配置**这样一个思想，来帮助我们快速搭建 Spring 项目骨架。相比于原生的Spring工程，不需要编写大量样板代码、XML 配置和注释。
2. Spring Boot **内置了常用的Web容器**，如Tomcat、Jetty等，使得应用程序可以以独立的方式运行，不需要额外安装和配置外部容器。
2. Spring Boot 提供了多种插件，可以使用内置工具(如 Maven 和 Gradle)开发和测试 Spring Boot 应用程序。
2. Spring Boot 提供了一系列的 Starter，可以快速集成常用的框架，例如 Spring Data JPA、Spring Security、MyBatis 等。

> 什么是 Spring Boot Starters?

Spring Boot Starters 是一系列依赖关系的集合.比如**spring-boot-starter-web**就包含了开发一个Web应用程序所必需的一些依赖，比如Spring MVC，Tomcat 和 Jackson 

没有 Spring Boot 的情况下，如果我们需要引入第三方依赖，需要手动配置，非常麻烦。但是，Spring Boot 中，我们直接引入一个 starter 即可。比如你想要在项目中使用 redis 的话，直接在项目中引入对应的 `spring-boot-starter-data-redis` 即可。引入 starter 之后，我们通过少量注解和一些简单的配置就能使用第三方组件提供的功能了。

> **Spring Boot 支持哪些内嵌 Servlet 容器？**Tomcat、 Jetty 、Undertow 

**@SpringBootApplication**：spring boot 启动类上的注解

可以看成是以下三个注解的集合：

- `@EnableAutoConfiguration`：启用 SpringBoot 的**自动配置机制**，Spring Boot采用根据约定大于配置的原则，会根据项目的依赖和类路径上的内容来自动配置你的应用程序。（不需要手动配置大部分常见的组件，减少了繁琐的配置工作，提高了开发效率。
  `@ComponentScan`： 使用`@ComponentScan`注解时，Spring会自动扫描指定的包及其子包下的类。扫描被@Component (@Service,@Controller)注解类，找出这些**需要装配**的类自动装配到spring的bean容器中，注解默认会扫描当前类所在的包下及其子包下的所有的类。
  `@Configuration`：本类是个配置类。允许在上下文中注册额外的 bean 或导入其他配置类

> 除此之外还有：@SpringBootConfiguration 表明该类**是Spring的一个配置类**

#### Spring Boot 的自动配置是如何实现的?

> 自动装配： 自动把第三方的bean注册到IOC容器中，不需要开发人员手动写bean相关的配置
>
> SpringBoot 在启动时会扫描外部引用 jar 包中的`META-INF/spring.factories`文件，将文件中配置的类型信息加载到 Spring 容器，并执行类中定义的各种操作。对于外部 jar 来说，只需要按照 SpringBoot 定义的标准，就能将自己的功能装置进 SpringBoot。

通过`@SpringBootApplication`引入了`@EnableAutoConfiguration`，这个注解就是启用 SpringBoot 的**自动配置机制**。在该注解中，通过@Import注解导入 AutoConfigurationImportSelector 这样一个实现了 `ImportSelector`接口的类，这个接口就是用来批量获取要导入到IOC容器中的bean，通过实现这个接口就可以获取要导入的bean，这个操作的底层实际上是调用spring factory提供的方法（`SpringFactoriesLoader`）去扫描MATE-INF包下面spring.factories中定义的那些要注入的组件，扫描到之后，通过反射机制实例化，把这些组件注入到当前的spring IOC容器，之后在工程中就可以通过依赖注入的方式从容器中获取bean进行使用。`@Import(AutoConfigurationImportSelector.class)`

`spring.factories`中这么多配置，每次启动都要全部加载么？

如果配置类上有`@ConditionalOnXXX`这个注解，只有这个注解 中的所有要求的条件都满足，该类才会生效。

> @Import这个类简单来说就是 把导入的类，都交给Spring容器管理
>
> @AutoConfigurationPackage 添加该注解的类所在的包作为自动配置包进行管理。（将main包下的所有组件注册到容器中）所以SpringBoot启动类所在的包作为自动配置的包。
>
> [Spring中@Import注解的使用和实现原理（超详细！！！）_spring import-CSDN博客](https://blog.csdn.net/gongsenlin341/article/details/113281596)

#### 如何实现一个 Starter

1. 创建一个项目，命名为 demo-spring-boot-starter，引入 SpringBoot 相关依赖
2. 编写配置类，在配置类中定义要注入到容器的组件
3. 在/resources/META-INF/spring.factories文件中添加自动配置类路径
4. 创建一个工程，引入自定义 starter 依赖，使用start

**常用注解**：

`@RestController` : 是@Controller和@ResponseBody的合集,返回 JSON 或 XML 形式数据.使用 `@Controller` 不加 `@ResponseBody`的话一般是用在要返回一个视图的情况

`@RequestParam `从请求中提取参数（还有@PathVariable:获取路径参数，@RequestBody，json转实体类）。不添加该注解则参数为非必传，反之参数默认为必传。可以指定参数名和参数默认值

`@Validated` 声明了要进行参数校验的位置。在方法参数上用于触发对参数的校验。

> `@Validated`注解是Spring框架提供的，并且通常与Spring MVC一起使用。`@Validated` 通过 `groups` 属性实现验证分组。不支持嵌套检验
>
> 可以在需要校验的实体类对象后边添加 BindingResult类对象收手动进行错误处理 
>
> `@Valid`注解是Java标准库中的注解，也可以与Spring框架一起使用。`@Valid` 注解用于对整个对象进行验证。支持嵌套检验，不支持分组校验

`@Param`注解，通常和持久层框架一起使用。

`@Transient`：声明不需要与数据库映射的字段，在保存的时候不需要保存进数据库 。

`@JsonFormat`作用:将日期类型数据在JSON格式和java.util.Date对象之间转换。约束时间的**接收格式**和**响应格式** (接收和响应的都是**JSON字符串**) .与传输方向没有关系（前端到后端or后端到前端都可以使用）

```java
@Data
public class pojo{
    @JsonFormat(pattern = "yyyy-MM-dd hh", timezone = "GMT+8")
    private Date date;
}
```

`@DateTimeFormat`只能约束前端传入的时间类型参数格式，且如果单独使用@DateTimeFormat时，响应给前端的时间会比实际时间晚8个小时（时区原因）。

**Spring Boot 如何做请求参数校验？**

**JSR 提供了很多参数校验的注解**，（JSR-303是一项规范）使用时，直接加在要校验的属性上。

[Validated数据校验，看这一篇就够了_validated校验-CSDN博客](https://blog.csdn.net/weixin_43990804/article/details/112974137?ops_request_misc=%7B%22request%5Fid%22%3A%22171189187416800186552746%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=171189187416800186552746&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-112974137-null-null.142^v100^control&utm_term=Validated &spm=1018.2226.3001.4187)

可以用`@Valid` 或者`@Validated`注解加在 实体类对象参数 前校验对象内的字段。或者如果不是对象，可以在参数前直接加要校验的规则`@NotNull String username `.进行校验。

*如果POJO中包含了自定义的实体类，就需要用到嵌套校验。*

#### 读取配置

常用注解：

`@Value`

`@ConfigurationProperties(prefix = "……")`:将配置信息与某个类绑定在一起。

`@PropertySource（"classpath:配置文件名"）`：读取指定的 properties 文件

YAML 配置的优势：

- YAML 文件就更加结构化，直观清晰，简洁明了，有层次感。
- 缺点：不支持`@PropertySource` 注解导入自定义的 YAML 配置。

配置文件的优先级：优先级最高：命令行参数；

1. 优先级低的配置会被先加载，所以优先级高的配置会覆盖优先级低的配置。
2. 在同一级目录下
   不同后缀配置文件的优先级：properties(最高) > yml > yaml(最低)
   相同后缀配置文件的优先级：application-xxx.yml > application.yml
3. 项目中优先级
   项目名/config/xxx.xml (优先级最高)
   项目名/xxx.xml
   项目名/src/main/resources/config/xxx.properties
   项目名/src/main/resources/xxx.yml (优先级最低)
4. 内外部优先级：
   项目外部配置文件 > 项目内部配置文件

bootstrap（一些固定的不能被覆盖的属性） 由spring父上下文加载，比application配置文件优先加载。bootstrap加载的配置信息不能被application的相同配置覆盖

bootstrap.properties > bootstrap.yml > application.properties > application.yml

**Spring Boot 中如何实现定时任务 ?**

```java
@Component
public class ScheduledTasks {
    /**
     * fixedRate：固定速率执行。每5秒执行一次。
     */
    @Scheduled(fixedRate = 5000)
    public void reportCurrentTimeWithFixedRate() {
        ……
    }
}
```

在方法上使用 `@Scheduled `注解,在启动类上加上`@EnableScheduling `注解

> 使用 Spring Boot 实现全局异常处理？：使用 @ControllerAdvice 和 @ExceptionHandler 处理全局异常。

 **Spring Boot 如何监控系统实际运行状况？**

可以使用 Spring Boot Actuator 来对 Spring Boot 项目进行简单的监控。

先集成spring-boot-starter-actuator，通过访问 Actuator提供的接口，来获取应用程序的实际运行状况。例如，可以使用浏览器或命令行工具访问`http://localhost:8080/actuator/health`来获取应用程序的健康状态。该默认路径可以自定义。

#### SpringApplication.run是怎么运行的

> 当运行一个 Spring Boot 程序时，通常会调用主类中的`main`方法，这个方法会执行`SpringApplication.run()`
>
> 该方法接收两个参数：第一个是主应用类（即包含`main`方法的类），第二个是命令行参数。

SpringApplication.run(xxx.class,args)是spring 程序启动的入口。

run()在执行的时候，首先创建了一个SpringApplication实例，创建`SpringApplicaiton`实例的时候SpringApplication 会做一些初始化工作：（构造函数）

1. **推断应用的类型**是普通的项目还是 Web 项目

2. 查找并加载所有可用初始化器 

   在`META-INF/spring.factories`配置文件里获取所有**初始化器**的名称集合，然后实例化、排序后再设置到`initializers`属性中。

   `Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));`

   `配置文件中前缀是 org.springframework.context.ApplicationContextInitializer `

3. 找出所有的应用程序**监听器**，设置到 listeners 属性中

   读取springboot下的META-INFO/spring.factories文件，获取对应的ApplicationListener装配到集合

   和初始化的操作是基本一样

4. 找到main方法所在的类

在此之后，就可以实际去执行run方法的主体。

> 第一步调用的run方法是静态方法，那个时候还没实例化SpringApplication对象，现在调用的run方法是非静态的，是需要实例化后才可以调用的

[SpringBoot启动流程解析（总结的非常好，很清晰！）_springboot的启动流程-CSDN博客](https://blog.csdn.net/u014252478/article/details/88789852)

主要工作就是 **初始化监听器并启动监听**，用于监听run方法的执行、 **准备环境变量**、创建**应用上下文**(applicationContext)以及准备上下文环境等工作，在这些工作结束之后，接下来会执行`refreshContext(context)`方法,在该方法中会触发自动配置机制，自动配置starter依赖中的bean到当前容器中，底层是通过bean工厂生产bean，bean工厂是由监听器通过反射手段拿到的。

![SpringBoot 启动大致流程-图片来源网络](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/spring-68744556-a1ba-4e1f-a092-1582875f0da6.png)

#### **spring程序的启动流程**：

每个SpringBoot程序都有一个主入口，也就是main方法，main里面调用SpringApplication.run()启动整个spring-boot程序，该方法所在类需要使用@SpringBootApplication注解。

在SpringBoot启动流程中，主要的两个阶段是**初始化SpringApplication对象**以及**SpringApplication.run方法执行的内容**

