---
layout:  post
title:   ThreadLocal全面解析
date:   章于 2024-02-27 20:36:53 发布
author:  'zhangtao'
header-img: 'img/post-bg-2015.jpg'
catalog:   false
tags:
-java
-多线程
-面试
https://blog.csdn.net/Beyondczn/article/details/107132337
---


## ThreadLocal全面解析

 *注：本学习资料来自黑马程序员* 

**学习目标**

- **了解ThreadLocal的介绍** 
- **掌握ThreadLocal的运用场景** 
- **了解ThreadLocal的内部结构** 
- **了解ThreadLocal的核心方法源码** 
- **了解ThreadLocalMap的源码**

### 1. ThreadLocal介绍

#### 1.1 官方介绍

```java
/**
 * This class provides thread-local variables.  These variables differ from
 * their normal counterparts in that each thread that accesses one (via its
 * {@code get} or {@code set} method) has its own, independently initialized
 * copy of the variable.  {@code ThreadLocal} instances are typically private
 * static fields in classes that wish to associate state with a thread (e.g.,
 * a user ID or Transaction ID).
 *
 * <p>For example, the class below generates unique identifiers local to each
 * thread.
 * A thread's id is assigned the first time it invokes {@code ThreadId.get()}
 * and remains unchanged on subsequent calls.
 * <pre>
 * import java.util.concurrent.atomic.AtomicInteger;
 *
 * public class ThreadId {
 *     // Atomic integer containing the next thread ID to be assigned
 *     private static final AtomicInteger nextId = new AtomicInteger(0);
 *
 *     // Thread local variable containing each thread's ID
 *     private static final ThreadLocal&lt;Integer&gt; threadId =
 *         new ThreadLocal&lt;Integer&gt;() {
 *             &#64;Override protected Integer initialValue() {
 *                 return nextId.getAndIncrement();
 *         }
 *     };
 *
 *     // Returns the current thread's unique ID, assigning it if necessary
 *     public static int get() {
 *         return threadId.get();
 *     }
 * }
 * </pre>
 * <p>Each thread holds an implicit reference to its copy of a thread-local
 * variable as long as the thread is alive and the {@code ThreadLocal}
 * instance is accessible; after a thread goes away, all of its copies of
 * thread-local instances are subject to garbage collection (unless other
 * references to these copies exist).
 *
 * @author  Josh Bloch and Doug Lea
 * @since   1.2
 */
public class ThreadLocal<T> {
   
    ...
```

 从Java官方文档中的描述：ThreadLocal类用来提供线程内部的局部变量。这种变量在多线程环境下访问（通过get和set方法访问）时能保证各个线程的变量相对独立于其他线程内的变量。ThreadLocal实例通常来说都是private static类型的，用于关联线程和线程上下文。

我们可以得知 ThreadLocal 的作用是：提供线程内的局部变量，不同的线程之间不会相互干扰，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或组件之间一些公共变量传递的复杂度。

```java
总结:
1. 线程并发: 在多线程并发的场景下
2. 传递数据: 我们可以通过ThreadLocal在同一线程，不同组件中传递公共变量
3. 线程隔离: 每个线程的变量都是独立的，不会相互影响
```

#### 1.2 基本使用

##### 1.2.1 常用方法

 在使用之前,我们先来认识几个ThreadLocal的常用方法

| 方法声明                  | 描述                       |
| ------------------------- | -------------------------- |
| ThreadLocal()             | 创建ThreadLocal对象        |
| public void set( T value) | 设置当前线程绑定的局部变量 |
| public T get()            | 获取当前线程绑定的局部变量 |
| public void remove()      | 移除当前线程绑定的局部变量 |


##### 1.2.2 使用案例

我们来看下面这个案例

```java
public class MyDemo {
   
    private String content;

    private String getContent() {
   
        return content;
    }

    private void setContent(String content) {
   
        this.content = content;
    }

    public static void main(String[] args) {
   
        MyDemo demo = new MyDemo();
        for (int i = 0; i < 5; i++) {
   
            Thread thread = new Thread(new Runnable() {
   
                @Override
                public void run() {
   
                    demo.setContent(Thread.currentThread().getName() + "的数据");
                    System.out.println("-----------------------");
             		System.out.println(Thread.currentThread().getName() + "--->" + demo.getContent());
                }
            });
            thread.setName("线程" + i);
            thread.start();
        }
    }
}
```

打印结果:


![img](https://img-blog.csdnimg.cn/20200704232547657.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70)

 从结果可以看出多个线程在访问同一个变量的时候出现的异常，线程间的数据没有隔离。下面我们来看下采用 ThreadLocal 的方式来解决这个问题的例子。

```java
public class MyDemo {
   

    private static ThreadLocal<String> tl = new ThreadLocal<>();

    private String content;

    private String getContent() {
   
        return tl.get();
    }

    private void setContent(String content) {
   
         tl.set(content);
    }

    public static void main(String[] args) {
   
        MyDemo demo = new MyDemo();
        for (int i = 0; i < 5; i++) {
   
            Thread thread = new Thread(new Runnable() {
   
                @Override
                public void run() {
   
                    demo.setContent(Thread.currentThread().getName() + "的数据");
                    System.out.println("-----------------------");
                    System.out.println(Thread.currentThread().getName() + "--->" + demo.getContent());
                }
            });
            thread.setName("线程" + i);
            thread.start();
        }
    }
}
```

打印结果:


![img](https://img-blog.csdnimg.cn/20200704232617238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70)

从结果来看，这样很好的解决了多线程之间数据隔离的问题，十分方便。

#### 1.3 ThreadLocal类与synchronized关键字

##### 1.3.1 synchronized同步方式

 这里可能有的朋友会觉得在上述例子中我们完全可以通过加锁来实现这个功能。我们首先来看一下用synchronized代码块实现的效果:

```java
public class Demo02 {
   
    
    private String content;

    public String getContent() {
   
        return content;
    }

    public void setContent(String content) {
   
        this.content = content;
    }

    public static void main(String[] args) {
   
        Demo02 demo02 = new Demo02();
        
		  for (int i = 0; i < 5; i++) {
   
		        Thread t = new Thread(){
   
		            @Override
		            public void run() {
   
		                synchronized (Demo02.class){
   
		                    demo02.setContent(Thread.currentThread().getName() + "的数据");
		                    System.out.println("-------------------------------------");
                        String content = demo02.getContent();
                        System.out.println(Thread.currentThread().getName() + "--->" + content);
                    }
                }
            };
            t.setName("线程" + i);
            t.start();
        }
    }
}
```

打印结果:


![img](https://img-blog.csdnimg.cn/20200704232729744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70)

 从结果可以发现, 加锁确实可以解决这个问题，但是在这里我们强调的是线程数据隔离的问题，并不是多线程共享数据的问题, 在这个案例中使用synchronized关键字是不合适的。

##### 1.3.2 ThreadLocal与synchronized的区别

 虽然ThreadLocal模式与synchronized关键字都用于处理多线程并发访问变量的问题, 不过两者处理问题的角度和思路不同。


总结： 在刚刚的案例中，虽然使用ThreadLocal和synchronized都能解决问题,但是**使用ThreadLocal更为合适,因为这样可以使程序拥有更高的并发性。**

### 2. 运用场景_事务案例

 通过以上的介绍，我们已经基本了解ThreadLocal的特点。但是它具体的应用是在哪里呢？ 现在让我们一起来看一个ThreadLocal的经典运用场景： 事务。

#### 2.1 转账案例

##### 2.1.1 场景构建

 这里我们先构建一个简单的转账场景： 有一个数据表account，里面有两个用户Jack和Rose，用户Jack 给用户Rose 转账。

 案例的实现就简单的用mysql数据库，JDBC 和 C3P0 框架实现。以下是详细代码 ：

 （1） 项目结构


![img](https://img-blog.csdnimg.cn/20200704233208642.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70)

 （2） 数据准备

```java
-- 使用数据库
use test;
-- 创建一张账户表
create table account(
	id int primary key auto_increment,
	name varchar(20),
	money double
);
-- 初始化数据
insert into account values(null, 'Jack', 1000);
insert into account values(null, 'Rose', 1000);
```

 （3） C3P0配置文件和工具类

```java
<c3p0-config>
  <!-- 使用默认的配置读取连接池对象 -->
  <default-config>
  	<!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/test</property>
    <property name="user">root</property>
    <property name="password">1234</property>
    
    <!-- 连接池参数 -->
    <property name="initialPoolSize">5</property>
    <property name="maxPoolSize">10</property>
    <property name="checkoutTimeout">3000</property>
  </default-config>

</c3p0-config>
```

 （4） 工具类 ： JdbcUtils

```java
package com.itheima.transfer.utils;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import java.sql.Connection;
import java.sql.SQLException;

public class JdbcUtils {
   
    // c3p0 数据库连接池对象属性
    private static final ComboPooledDataSource ds = new ComboPooledDataSource();
    // 获取连接
    public static Connection getConnection() throws SQLException {
   
        return ds.getConnection();
    }
    //释放资源
    public static void release(AutoCloseable... ios){
   
        for (AutoCloseable io : ios) {
   
            if(io != null){
   
                try {
   
                    io.close();
                } catch (Exception e) {
   
                    e.printStackTrace();
                }
            }
        }
    }
    
    
    public static void commitAndClose(Connection conn) {
   
        try {
   
            if(conn != null){
   
                //提交事务
                conn.commit();
                //释放连接
                conn.close();
            }
        } catch (SQLException e) {
   
            e.printStackTrace();
        }
    }

    public static void rollbackAndClose(Connection conn) {
   
        try {
   
            if(conn != null){
   
                //回滚事务
                conn.rollback();
                //释放连接
                conn.close();
            }
        } catch (SQLException e) {
   
            e.printStackTrace();
        }
    }
}
```

 （5） dao层代码 ： AccountDao

```java
package com.itheima.transfer.dao;

import com.itheima.transfer.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class AccountDao {
   

    public void out(String outUser, int money) throws SQLException {
   
        String sql = "update account set money = money - ? where name = ?";

        Connection conn = JdbcUtils.getConnection();
        PreparedStatement pstm = conn.prepareStatement(sql);
        pstm.setInt(1,money);
        pstm.setString(2,outUser);
        pstm.executeUpdate();

        JdbcUtils.release(pstm,conn);
    }

    public void in(String inUser, int money) throws SQLException {
   
        String sql = "update account set money = money + ? where name = ?";

        Connection conn = JdbcUtils.getConnection();
        PreparedStatement pstm = conn.prepareStatement(sql);
        pstm.setInt(1,money);
        pstm.setString(2,inUser);
        pstm.executeUpdate();

        JdbcUtils.release(pstm,conn);
    }
}
```

 （6） service层代码 ： AccountService

```java
package com.itheima.transfer.service;

import com.itheima.transfer.dao.AccountDao;
import java.sql.SQLException;

public class AccountService {
   

    public boolean transfer(String outUser, String inUser, int money) {
   
        AccountDao ad = new AccountDao();
        try {
   
            // 转出
            ad.out(outUser, money);
            // 转入
            ad.in(inUser, money);
        } catch (Exception e) {
   
            e.printStackTrace();
            return false;
        }
        return true;
    }
}
```

 （7） web层代码 ： AccountWeb

```java
package com.itheima.transfer.web;

import com.itheima.transfer.service.AccountService;

public class AccountWeb {
   

    public static void main(String[] args) {
   
        // 模拟数据 : Jack 给 Rose 转账 100
        String outUser = "Jack";
        String inUser = "Rose";
        int money = 100;

        AccountService as = new AccountService();
        boolean result = as.transfer(outUser, inUser, money);

        if (result == false) {
   
            System.out.println("转账失败!");
        } else {
   
            System.out.println("转账成功!");
        }
    }
}
```

##### 2.1.2 引入事务

 案例中的转账涉及两个DML操作： 一个转出，一个转入。这些操作是需要具备原子性的，不可分割。不然就有可能出现数据修改异常情况。

```java
public class AccountService {
   
    public boolean transfer(String outUser, String inUser, int money) {
   
        AccountDao ad = new AccountDao();
        try {
   
            // 转出
            ad.out(outUser, money);
            // 模拟转账过程中的异常
            int i = 1/0;
            // 转入
            ad.in(inUser, money);
        } catch (Exception e) {
   
            e.printStackTrace();
            return false;
        }
        return true;
    }
}
```

 所以这里就需要操作事务，来保证转出和转入操作具备原子性，要么同时成功，要么同时失败。

（1） JDBC中关于事务的操作的api

| Connection接口的方法      | 作用                         |
| ------------------------- | ---------------------------- |
| void setAutoCommit(false) | 禁用事务自动提交（改为手动） |
| void commit();            | 提交事务                     |
| void rollback();          | 回滚事务                     |


**（2） 开启事务的注意点:**

-  为了保证所有的操作在一个事务中,案例中使用的连接必须是同一个: service层开启事务的connection需要跟dao层访问数据库的connection保持一致  
-  线程并发情况下, 每个线程只能操作各自的 connection 

#### 2.2 常规解决方案

##### 2.2.1 常规方案的实现

基于上面给出的前提， 大家通常想到的解决方案是 ：

- 从service层将connection对象向dao层传递 
- 加锁

以下是代码实现修改的部分：

 （1 ) AccountService 类

```java
package com.itheima.transfer.service;

import com.itheima.transfer.dao.AccountDao;
import com.itheima.transfer.utils.JdbcUtils;
import java.sql.Connection;

public class AccountService {
   

    public boolean transfer(String outUser, String inUser, int money) {
   
        AccountDao ad = new AccountDao();
        //线程并发情况下,为了保证每个线程使用各自的connection,故加锁
        synchronized (AccountService.class) {
   

            Connection conn = null;
            try {
   
                conn = JdbcUtils.getConnection();
                //开启事务
                conn.setAutoCommit(false);
                // 转出
                ad.out(conn, outUser, money);
                // 模拟转账过程中的异常
//            int i = 1/0;
                // 转入
                ad.in(conn, inUser, money);
                //事务提交
                JdbcUtils.commitAndClose(conn);
            } catch (Exception e) {
   
                e.printStackTrace();
                //事务回滚
                JdbcUtils.rollbackAndClose(conn);
                return false;
            }
            return true;
        }
    }
}
```

 （2) AccountDao 类 （这里需要注意的是： connection不能在dao层释放，要在service层，不然在dao层释放，service层就无法使用了）

```java
package com.itheima.transfer.dao;

import com.itheima.transfer.utils.JdbcUtils;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class AccountDao {
   

    public void out(Connection conn, String outUser, int money) throws SQLException{
   
        String sql = "update account set money = money - ? where name = ?";
        //注释从连接池获取连接的代码,使用从service中传递过来的connection
//        Connection conn = JdbcUtils.getConnection();
        PreparedStatement pstm = conn.prepareStatement(sql);
        pstm.setInt(1,money);
        pstm.setString(2,outUser);
        pstm.executeUpdate();
        //连接不能在这里释放,service层中还需要使用
//        JdbcUtils.release(pstm,conn);
        JdbcUtils.release(pstm);
    }

    public void in(Connection conn, String inUser, int money) throws SQLException {
   
        String sql = "update account set money = money + ? where name = ?";
//        Connection conn = JdbcUtils.getConnection();
        PreparedStatement pstm = conn.prepareStatement(sql);
        pstm.setInt(1,money);
        pstm.setString(2,inUser);
        pstm.executeUpdate();
//        JdbcUtils.release(pstm,conn);
        JdbcUtils.release(pstm);
    }
}
```

##### 2.2.2 常规方案的弊端

上述方式我们看到的确按要求解决了问题，但是仔细观察，会发现这样实现的弊端：

1.  直接从service层传递connection到dao层, 造成代码耦合度提高  
2.  加锁会造成线程失去并发性，程序性能降低 

#### 2.3 ThreadLocal解决方案

##### 2.3.1 ThreadLocal方案的实现

像这种需要在项目中进行**数据传递**和**线程隔离**的场景，我们不妨用ThreadLocal来解决：

 （1） 工具类的修改： 加入ThreadLocal

```java
package com.itheima.transfer.utils;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import java.sql.Connection;
import java.sql.SQLException;

public class JdbcUtils {
   
    //ThreadLocal对象 : 将connection绑定在当前线程中
    private static final ThreadLocal<Connection> tl = new ThreadLocal();

    // c3p0 数据库连接池对象属性
    private static final ComboPooledDataSource ds = new ComboPooledDataSource();

    // 获取连接
    public static Connection getConnection() throws SQLException {
   
        //取出当前线程绑定的connection对象
        Connection conn = tl.get();
        if (conn == null) {
   
            //如果没有，则从连接池中取出
            conn = ds.getConnection();
            //再将connection对象绑定到当前线程中
            tl.set(conn);
        }
        return conn;
    }

    //释放资源
    public static void release(AutoCloseable... ios) {
   
        for (AutoCloseable io : ios) {
   
            if (io != null) {
   
                try {
   
                    io.close();
                } catch (Exception e) {
   
                    e.printStackTrace();
                }
            }
        }
    }

    public static void commitAndClose() {
   
        try {
   
            Connection conn = getConnection();
            //提交事务
            conn.commit();
            //解除绑定
            tl.remove();
            //释放连接
            conn.close();
        } catch (SQLException e) {
   
            e.printStackTrace();
        }
    }

    public static void rollbackAndClose() {
   
        try {
   
            Connection conn = getConnection();
            //回滚事务
            conn.rollback();
            //解除绑定
            tl.remove();
            //释放连接
            conn.close();
        } catch (SQLException e) {
   
            e.printStackTrace();
        }
    }
}
```

 （2） AccountService类的修改：不需要传递connection对象

```java
package com.itheima.transfer.service;

import com.itheima.transfer.dao.AccountDao;
import com.itheima.transfer.utils.JdbcUtils;
import java.sql.Connection;

public class AccountService {
   

    public boolean transfer(String outUser, String inUser, int money) {
   
        AccountDao ad = new AccountDao();

        try {
   
            Connection conn = JdbcUtils.getConnection();
            //开启事务
            conn.setAutoCommit(false);
            // 转出 ： 这里不需要传参了 ！
            ad.out(outUser, money);
            // 模拟转账过程中的异常
//            int i = 1 / 0;
            // 转入
            ad.in(inUser, money);
            //事务提交
            JdbcUtils.commitAndClose();
        } catch (Exception e) {
   
            e.printStackTrace();
            //事务回滚
           JdbcUtils.rollbackAndClose();
            return false;
        }
        return true;
    }
}
```

 （3） AccountDao类的修改：照常使用

```java
package com.itheima.transfer.dao;

import com.itheima.transfer.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class AccountDao {
   

    public void out(String outUser, int money) throws SQLException {
   
        String sql = "update account set money = money - ? where name = ?";
        Connection conn = JdbcUtils.getConnection();
        PreparedStatement pstm = conn.prepareStatement(sql);
        pstm.setInt(1,money);
        pstm.setString(2,outUser);
        pstm.executeUpdate();
        //照常使用
//        JdbcUtils.release(pstm,conn);
        JdbcUtils.release(pstm);
    }

    public void in(String inUser, int money) throws SQLException {
   
        String sql = "update account set money = money + ? where name = ?";
        Connection conn = JdbcUtils.getConnection();
        PreparedStatement pstm = conn.prepareStatement(sql);
        pstm.setInt(1,money);
        pstm.setString(2,inUser);
        pstm.executeUpdate();
//        JdbcUtils.release(pstm,conn);
        JdbcUtils.release(pstm);
    }
}
```

##### 2.3.2 ThreadLocal方案的好处

从上述的案例中我们可以看到， 在一些特定场景下，ThreadLocal方案有两个突出的优势：

1.  传递数据 ： 保存每个线程绑定的数据，在需要的地方可以直接获取, 避免参数直接传递带来的代码耦合问题  
2.  线程隔离 ： 各线程之间的数据相互隔离却又具备并发性，避免同步方式带来的性能损失 

### 3. ThreadLocal的内部结构

 通过以上的学习，我们对ThreadLocal的作用有了一定的认识。现在我们一起来看一下ThreadLocal的内部结构，探究它能够实现线程数据隔离的原理。

#### 3.1 常见的误解

 通常，如果我们不去看源代码的话，我猜ThreadLocal是这样子设计的：每个ThreadLocal类都创建一个Map，然后用线程的ID threadID作为Map的key，要存储的局部变量作为Map的value，这样就能达到各个线程的局部变量隔离的效果。这是最简单的设计方法，JDK最早期的ThreadLocal就是这样设计的。

#### 3.2 核心结构

 但是，JDK后面优化了设计方案，现时JDK8 ThreadLocal的设计是：每个Thread维护一个ThreadLocalMap哈希表，这个哈希表的key是ThreadLocal实例本身，value才是真正要存储的值Object。

 （1） 每个Thread线程内部都有一个Map (ThreadLocalMap) ​ （2） Map里面存储ThreadLocal对象（key）和线程的变量副本（value） ​ （3）Thread内部的Map是由ThreadLocal维护的，由ThreadLocal负责向map获取和设置线程的变量值。 ​ （4）对于不同的线程，每次获取副本值时，别的线程并不能获取到当前线程的副本值，形成了副本的隔离，互不干扰。


![img](https://img-blog.csdnimg.cn/20200704233807616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70#pic_center)

#### 3.3 这样设计的好处

 这个设计与我们一开始说的设计刚好相反，这样设计有如下两个优势：

（1） 这样设计之后每个Map存储的Entry数量就会变少，因为之前的存储数量由Thread的数量决定，现在是由ThreadLocal的数量决定。

（2） 当Thread销毁之后，对应的ThreadLocalMap也会随之销毁，能减少内存的使用。

### 4. ThreadLocal的核心方法源码

 基于ThreadLocal的内部结构，我们继续探究一下ThreadLocal的核心方法源码，更深入的了解其操作原理。

除了构造之外， ThreadLocal对外暴露的方法有以下4个：


其实get，set和remove逻辑是比较相似的，我们要研究清楚其中一个，其他也就明白了。

#### 4.1 get方法

**（1 ) 源码和对应的中文注释**

```java
/**
     * 返回当前线程中保存ThreadLocal的值
     * 如果当前线程没有此ThreadLocal变量，
     * 则它会通过调用{@link #initialValue} 方法进行初始化值
     *
     * @return 返回当前线程对应此ThreadLocal的值
     */
    public T get() {
   
        // 获取当前线程对象
        Thread t = Thread.currentThread();
        // 获取此线程对象中维护的ThreadLocalMap对象
        ThreadLocalMap map = getMap(t);
        // 如果此map存在
        if (map != null) {
   
            // 以当前的ThreadLocal 为 key，调用getEntry获取对应的存储实体e
            ThreadLocalMap.Entry e = map.getEntry(this);
            // 找到对应的存储实体 e 
            if (e != null) {
   
                @SuppressWarnings("unchecked")
                // 获取存储实体 e 对应的 value值
                // 即为我们想要的当前线程对应此ThreadLocal的值
                T result = (T)e.value;
                return result;
            }
        }
        // 如果map不存在，则证明此线程没有维护的ThreadLocalMap对象
        // 调用setInitialValue进行初始化
        return setInitialValue();
    }

    /**
     * set的变样实现，用于初始化值initialValue，
     * 用于代替防止用户重写set()方法
     *
     * @return the initial value 初始化后的值
     */
    private T setInitialValue() {
   
        // 调用initialValue获取初始化的值
        T value = initialValue();
        // 获取当前线程对象
        Thread t = Thread.currentThread();
        // 获取此线程对象中维护的ThreadLocalMap对象
        ThreadLocalMap map = getMap(t);
        // 如果此map存在
        if (map != null)
            // 存在则调用map.set设置此实体entry
            map.set(this, value);
        else
            // 1）当前线程Thread 不存在ThreadLocalMap对象
            // 2）则调用createMap进行ThreadLocalMap对象的初始化
            // 3）并将此实体entry作为第一个值存放至ThreadLocalMap中
            createMap(t, value);
        // 返回设置的值value
        return value;
    }

    /**
     * 获取当前线程Thread对应维护的ThreadLocalMap 
     * 
     * @param  t the current thread 当前线程
     * @return the map 对应维护的ThreadLocalMap 
     */
    ThreadLocalMap getMap(Thread t) {
   
        return t.threadLocals;
    }
	/**
     *创建当前线程Thread对应维护的ThreadLocalMap 
     *
     * @param t 当前线程
     * @param firstValue 存放到map中第一个entry的值
     */
	void createMap(Thread t, T firstValue) {
   
        //这里的this是调用此方法的threadLocal
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }
```

**（2 ) 代码执行流程**

 A. 首先获取当前线程

 B. 根据当前线程获取一个Map

 C. 如果获取的Map不为空，则在Map中以ThreadLocal的引用作为key来在Map中获取对应的value e，否则转到E

 D. 如果e不为null，则返回e.value，否则转到E

 E. Map为空或者e为空，则通过initialValue函数获取初始值value，然后用ThreadLocal的引用和value作为firstKey和firstValue创建一个新的Map

总结: **先获取当前线程的 ThreadLocalMap 变量，如果存在则返回值，不存在则创建并返回初始值。**

#### 4.2 set方法

**（1 ) 源码和对应的中文注释**

```java
/**
     * 设置当前线程对应的ThreadLocal的值
     *
     * @param value 将要保存在当前线程对应的ThreadLocal的值
     */
    public void set(T value) {
   
        // 获取当前线程对象
        Thread t = Thread.currentThread();
        // 获取此线程对象中维护的ThreadLocalMap对象
        ThreadLocalMap map = getMap(t);
        // 如果此map存在
        if (map != null)
            // 存在则调用map.set设置此实体entry
            map.set(this, value);
        else
            // 1）当前线程Thread 不存在ThreadLocalMap对象
            // 2）则调用createMap进行ThreadLocalMap对象的初始化
            // 3）并将此实体entry作为第一个值存放至ThreadLocalMap中
            createMap(t, value);
    }
```

**（2 ) 代码执行流程**

 A. 首先获取当前线程，并根据当前线程获取一个Map

 B. 如果获取的Map不为空，则将参数设置到Map中（当前ThreadLocal的引用作为key）

 C. 如果Map为空，则给该线程创建 Map，并设置初始值

#### 4.3 remove方法

**（1 ) 源码和对应的中文注释**

```java
/**
     * 删除当前线程中保存的ThreadLocal对应的实体entry
     */
     public void remove() {
   
        // 获取当前线程对象中维护的ThreadLocalMap对象
         ThreadLocalMap m = getMap(Thread.currentThread());
        // 如果此map存在
         if (m != null)
            // 存在则调用map.remove
            // 以当前ThreadLocal为key删除对应的实体entry
             m.remove(this);
     }
```

**（2 ) 代码执行流程**

 A. 首先获取当前线程，并根据当前线程获取一个Map

 B. 如果获取的Map不为空，则移除当前ThreadLocal对象对应的entry

#### 4.4 initialValue方法

```java
/**
  * 返回当前线程对应的ThreadLocal的初始值
  
  * 此方法的第一次调用发生在，当线程通过{@link #get}方法访问此线程的ThreadLocal值时
  * 除非线程先调用了 {@link #set}方法，在这种情况下，
  * {@code initialValue} 才不会被这个线程调用。
  * 通常情况下，每个线程最多调用一次这个方法。
  *
  * <p>这个方法仅仅简单的返回null {@code null};
  * 如果程序员想ThreadLocal线程局部变量有一个除null以外的初始值，
  * 必须通过子类继承{@code ThreadLocal} 的方式去重写此方法
  * 通常, 可以通过匿名内部类的方式实现
  *
  * @return 当前ThreadLocal的初始值
  */
protected T initialValue() {
   
    return null;
}
```

 此方法的作用是 返回该线程局部变量的初始值。

（1） 这个方法是一个延迟调用方法，从上面的代码我们得知，在set方法还未调用而先调用了get方法时才执行，并且仅执行1次。

（2）这个方法缺省实现直接返回一个null。

（3）如果想要一个除null之外的初始值，可以重写此方法。（备注： 该方法是一个protected的方法，显然是为了让子类覆盖而设计的）

### 5. ThreadLocalMap源码分析

#### 5.1 基本结构

 ThreadLocalMap是ThreadLocal的内部类，没有实现Map接口，用独立的方式实现了Map的功能，其内部的Entry也是独立实现。


![img](https://img-blog.csdnimg.cn/20200704233123599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70)

**（1） 成员变量**

```java
/**
     * 初始容量 —— 必须是2的整次幂
     */
    private static final int INITIAL_CAPACITY = 16;

    /**
     * 存放数据的table，Entry类的定义在下面分析
     * 同样，数组长度必须是2的冥。
     */
    private Entry[] table;

    /**
     * 数组里面entrys的个数，可以用于判断table当前使用量是否超过负因子。
     */
    private int size = 0;

    /**
     * 进行扩容的阈值，表使用量大于它的时候进行扩容。
     */
    private int threshold; // Default to 0
    
    /**
     * 阈值设置为长度的2/3
     */
    private void setThreshold(int len) {
   
        threshold = len * 2 / 3;
    }
```

**（2） 存储结构 - Entry**

```java
// 在ThreadLocalMap中，也是用Entry来保存K-V结构数据的。但是Entry中key只能是ThreadLocal对象，这点被Entry的构造方法已经限定死了
// 另外，Entry继承WeakReference,使用弱引用，可以将ThreadLocal对象的生命周期和线程生命周期解绑，持有对ThreadLocal的弱引用，可以使得ThreadLocal在没有其他强引用的时候被回收掉，这样可以避免因为线程得不到销毁导致ThreadLocal对象无法被回收

static class Entry extends WeakReference<ThreadLocal> {
   
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal k, Object v) {
   
        super(k);
        value = v;
    }
}
```

#### 5.2 hash冲突的解决

ThreadLocal使用的是自定义的ThreadLocalMap，接下来我们来探究一下ThreadLocalMap的hash冲突解决方式。

（1） 先回顾ThreadLocal的set() 方法

```java
public void set(T value) {
   
        Thread t = Thread.currentThread();
        ThreadLocal.ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
    
    ThreadLocal.ThreadLocalMap getMap(Thread t) {
   
        return t.threadLocals;
    }

    void createMap(Thread t, T firstValue) {
   
        t.threadLocals = new ThreadLocal.ThreadLocalMap(this, firstValue);
    }
```

- 代码很简单，获取当前线程，并获取当前线程的ThreadLocalMap实例（从getMap(Thread t)中很容易看出来）。 
- 如果获取到的map实例不为空，调用map.set()方法，否则调用构造函数 ThreadLocal.ThreadLocalMap(this, firstValue)实例化map。

可以看出来线程中的ThreadLocalMap使用的是延迟初始化，在第一次调用get()或者set()方法的时候才会进行初始化。

（2） 下面来看看构造函数ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue)

```java
ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
   
        //初始化table
        table = new ThreadLocal.ThreadLocalMap.Entry[INITIAL_CAPACITY];
        //计算索引
        int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
        //设置值
        table[i] = new ThreadLocal.ThreadLocalMap.Entry(firstKey, firstValue);
        size = 1;
        //设置阈值
        setThreshold(INITIAL_CAPACITY);
    }
```

主要说一下计算索引，firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1)。

- 关于& (INITIAL_CAPACITY - 1),这是取模的一种方式，对于2的幂作为模数取模，用此代替%(2^n)，这也就是为啥容量必须为2的冥，在这个地方也得到了解答。 
- 关于firstKey.threadLocalHashCode：

```java
private final int threadLocalHashCode = nextHashCode();
    
    private static int nextHashCode() {
   
        return nextHashCode.getAndAdd(HASH_INCREMENT);
    }
    private static AtomicInteger nextHashCode =  new AtomicInteger();
            
    private static final int HASH_INCREMENT = 0x61c88647;
```

 这里定义了一个AtomicInteger类型，每次获取当前值并加上HASH_INCREMENT，HASH_INCREMENT = 0x61c88647,这个值和斐波那契散列有关（这是一种乘数散列法，只不过这个乘数比较特殊，是32位整型上限2^32-1乘以黄金分割比例0.618…的值2654435769，用有符号整型表示就是-1640531527，去掉符号后16进制表示为0x61c88647），其主要目的就是为了让哈希码能均匀的分布在2的n次方的数组里, 也就是Entry[] table中，这样做可以尽量避免hash冲突。

（3） ThreadLocalMap中的set()

 ThreadLocalMap使用开发地址-线性探测法来解决哈希冲突，线性探测法的地址增量di = 1, 2, … 其中，i为探测次数。该方法一次探测下一个地址，直到有空的地址后插入，若整个空间都找不到空余的地址，则产生溢出。假设当前table长度为16，也就是说如果计算出来key的hash值为14，如果table[14]上已经有值，并且其key与当前key不一致，那么就发生了hash冲突，这个时候将14加1得到15，取table[15]进行判断，这个时候如果还是冲突会回到0，取table[0],以此类推，直到可以插入。

按照上面的描述，可以把table看成一个环形数组。

先看一下线性探测相关的代码，从中也可以看出来table实际是一个环：

```java
/**
     * 获取环形数组的下一个索引
     */
    private static int nextIndex(int i, int len) {
   
        return ((i + 1 < len) ? i + 1 : 0);
    }

    /**
     * 获取环形数组的上一个索引
     */
    private static int prevIndex(int i, int len) {
   
        return ((i - 1 >= 0) ? i - 1 : len - 1);
    }
```

ThreadLocalMap的set()代码如下：

```java
private void set(ThreadLocal<?> key, Object value) {
   
        ThreadLocal.ThreadLocalMap.Entry[] tab = table;
        int len = tab.length;
        //计算索引，上面已经有说过。
        int i = key.threadLocalHashCode & (len-1);

        /**
         * 根据获取到的索引进行循环，如果当前索引上的table[i]不为空，在没有return的情况下，
         * 就使用nextIndex()获取下一个（上面提到到线性探测法）。
         */
        for (ThreadLocal.ThreadLocalMap.Entry e = tab[i];
             e != null;
             e = tab[i = nextIndex(i, len)]) {
   
            ThreadLocal<?> k = e.get();
            //table[i]上key不为空，并且和当前key相同，更新value
            if (k == key) {
   
                e.value = value;
                return;
            }
            /**
             * table[i]上的key为空，说明被回收了
             * 这个时候说明改table[i]可以重新使用，用新的key-value将其替换,并删除其他无效的entry
             */
            if (k == null) {
   
                replaceStaleEntry(key, value, i);
                return;
            }
        }
```

#### 5.3 弱引用和内存泄漏

有些程序员在使用ThreadLocal的过程中会发现有内存泄漏的情况发生,就猜测这个内存泄漏跟Entry中使用了弱引用的key有关系。这个理解其实是不对的。

我们先来回顾这个问题中涉及的几个名词概念,再来分析问题。

（1）内存泄漏相关概念

- **Memory overflow**:内存溢出,没有足够的内存提供申请者使用。 
- **Memory leak**:内存泄漏是指程序中己动态分配的堆内存由于某种原因程序未释放或无法释放,造成系统内存的浪费,导致程序运行速度减慢甚至系统崩溃等严重后果。I内存泄漏的堆积终将导致内存溢出。

（2）弱引用相关概念 Java中的引用有4种类型: 强、软、弱、虚。当前这个问题主要涉及到强引用和弱引用:

**强引用( “Strong” Reference)** , 就是我们最常见的普通对象引用,只要还有强引用指向一个对象,就能表明对象还“活着”,垃圾回收器就不会回收这种对象。

**弱引用( WeakReference)** ,垃圾回收器一旦发现了 只具有弱弓|用的对象,不管当前内存空间足够与否,都会回收它的内存。



(3)如果key使用强引用 假设ThreadLocalMap中的key使用了强引用,那么会出现内存泄漏吗? 此时ThreadLocal的内存图(实线表示强引用)如下: ![img](https://img-blog.csdnimg.cn/20200704234925473.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70#pic_center) (7)为什么使用弱引用 ![img](https://img-blog.csdnimg.cn/20200704235226590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JleW9uZGN6bg==,size_16,color_FFFFFF,t_70#pic_center)

根据刚才的分析,我们知道了:无论使用ThreadLocalMap中的key使用哪种类型引用都无法完全避免内存泄漏,跟使用弱引用没有关系。要避免内存泄漏有两种方式:

1. 使用完ThreadLocal ,调用其remove方法删除对应的Entry 
2. 使用完ThreadLocal ,当前Thread也随之运行结束相对第一种方式， 第二种方式显然更不好控制,特别是使用线程池的时候,线程结束是不会销毁的。

也就是说,只要记得在使用完ThreadLocal及时的调用remove ,无论key是强弓|用还是弱引用都不会有问题。

那么为什么key要用弱引用呢?

事实上，在ThreadLocalMap中的set/getEntry方法中,会对key为null (也即是ThreadLocal为null )进行判断,如果为nul的话,那么是会对value置为null的。 这就意味着使用完ThreadLocal , CurrentThread依然运行的前提下,就算忘记调用remove方法,弱引用比强引用可以多一层保障:弱引用的ThreadLocal会被回收,对应的value在下一-次ThreadLocalMap调用set,get,remove中的任一方法的时候会被清除,从而避免内存泄漏。|

