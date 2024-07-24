# 使用JDBC的步骤

1. 注册驱动
2. 创建连接
3. 创建发送SQL语句对象
4. 发送SQL语句，获取结果
5. 结果解析
6. 释放资源

![image-20230711135340160](C:\Users\痞妖\AppData\Roaming\Typora\typora-user-images\image-20230711135340160.png)

```java
package test;


import com.mysql.cj.jdbc.Driver;

import java.sql.*;

/**
 * TODO：
 *      DriverManger
 *      Connection
 *      Statement
 *      ResultSet
 *
 * */
public class demo1 {
    public static void main(String[] args) throws Exception {
        //1. 注册驱动
        DriverManager.registerDriver(new Driver());
        //2. 创建连接
        /**
         * 参数一:url
         *       jdbc：数据库厂商名://ip地址:port/数据库名
         *       jdbc:mysql://127.0.0.1:3306/atguigu
         *  参数二:username 数据库软件的账号 root
         *  参数三:password 数据库软件的密码 123456
         *
         * */
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu","root","123456");
        //3. 创建发送SQL语句对象
        Statement statement = connection.createStatement();
        //4. 发送SQL语句，获取结果
        String sql="select * from t_user;";
        ResultSet resultSet = statement.executeQuery(sql);
        //5. 结果解析
        //看看有没有下一行，有，就可以获取
        while(resultSet.next()){
            int id = resultSet.getInt("id");
            String account = resultSet.getString("account");
            String password = resultSet.getString("password");
            String nickname = resultSet.getString("nickname");
            System.out.println(id+"\r\n"+account+"\r\n"+password+"\r\n"+nickname);
        }
        //6. 释放资源

        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

# statement使用步骤详解

```java
package test;
import com.mysql.cj.jdbc.Driver;

import java.sql.*;
import java.util.Properties;
import java.util.Scanner;

public class demo2 {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        //1.获取用户输入信息
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入账号,密码");
        String account,password;
        account = sc.next();
        password=sc.next();
        //2.注册驱动
        /**
         * 方案一：
         *      DriverManager.registerDriver(new com.mysql.cj.Driver());
         *      注意：不同的mysql驱动版号选择不同的包
         *           8+ com.mysql.cj.jdbc.Driver
         *           5+ com.mysql.jdbc.Driver
         *      问题：内部注册两次驱动
         *           1. DriverManager.registerDriver(new com.mysql.cj.Driver) 方法本身会注册一次
         *           2. Driver.static{ DriverManager.registerDriver(new com.mysql.cj.Driver) } 静态代码块注册一次
         *       解决：只想注册一次驱动
         *           只触发静态代码块即可 Driver
         *       触发静态代码块：
         *            类加载机制：类加载时刻，会触发静态代码块
         *            加载 [class文件 -> jvm虚拟机的class对象  ]
         *            链接 [验证（检查文件类型）-> 准备（静态变量默认值）-> 解析（触发静态代码块）]
         *            初始化 （静态属性赋真实值）
         *        触发类加载：
         *             1. new 关键字
         *             2. 调用静态方法
         *             3. 调用静态属性
         *             4. 接口 1.8 default默认实现
         *             5. 反射
         *             6. 子类触发父类
         *             7. 程序的入口main
         * */
        //方案一不可取
        //DriverManager.registerDriver(new Driver());

        //方案二：不优雅，如果驱动换了就要修改代码
        //new Driver();

        //方案三：反射 字符串 -> 提取到外部的配置文件（将字符串放在配置文件中保存）-> 可以在不改变代码额情况下，完成数据库的切换（改变配置文件的内容）
        Class.forName("com.mysql.cj.jdbc.Driver");

        /**
         * 重写： 为了子类扩展父类的方法！父类也间接的规范了子类方法的参数和返回！
         * 重载： 重载一般应用在第三方的工具类上，为了方便用户多种方式传递参数形式！简化形式！
         */
        /**
         * 三个参数：
         *    String URL: 连接数据库地址
         *    String user: 连接数据库用户名
         *    String password: 连接数据库用户对应的密码
         * 数据库URL语法：
         *    JDBC:
         *        ip 和 port
         *        语法格式为 数据库管理软件名称[mysql , oracle] :// ip地址或者主机名[127.0.0.1 , localhost] : 端口号port / 数据库名
         *        具体： mysql://localhost:3306/day01
         *              mysql://127.0.0.1:3306/day01
         *        ip为192.168.33.45
         *              mysql://192.168.33.45/3306/day01
         *        当前电脑的省略写法！ 注意：，而且要省略就一起省略
         *        mysql://localhost:3306/day01 = jdbc:mysql:///day01
         *
         * 两个参数：
         *     String URL : 写法还是jdbc的路径写法！
         *     Properties : 就是一个参数封装容器！key和value都是字符串形式的，至少要包含 user / password key!存储连接账号信息！
         *
         * 一个参数：
         *    String URL: URl可以携带目标地址，可以通过?分割，在后面key=value&key=value形式传递参数
         *                jdbc:mysql:///day01?user=root&password=123456
         * 扩展路径参数(了解):
         *    serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf8&useSSL=true
         *
         */
        //获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu","root","123456");

        Properties info = new Properties();
        info.put("user","root");
        info.put("password","123456");
        Connection connection1 = DriverManager.getConnection("jdbc:mysql:///atguigu", info);

        Connection connection2 = DriverManager.getConnection("jdbc:mysql:///atguigu?user=root&password=123456");

        //执行SQL语句 [动态SQL语句,需要字符串拼接]
        String sql = "select * from t_user where account = '"+account+"' and password = '"+password+"' ;";

        Statement statement = connection.createStatement();

        /**
         *  ResultSet 结果集对象 = executeQuery(DQL语句)
         *  int       响应行数  = executeUpdate(非DQL语句)
         *  SQL语句的分类:DDL（容器的创建，修改，删除）DML（插入，删除，修改）DQL（查询）DCL（权限控制）TPL（事务控制语言）
         *
         *  executedUpdate 返回int 代表 响应行数  = executeUpdate(非DQL语句)
         *      情况一：DML 返回影响的行数，例如：删除了三条数据 return 3；插入了两条 return 2
         *      情况二：非DML return 0
         *  executedQuery  返回ResultSet 结果集对象 = executeQuery(DQL语句)
         */
        ResultSet resultSet = statement.executeQuery(sql);
        //ResultSet == 小海豚  你必须有面向对象的思维：Java是面向对象编程的语言 OOP！
        /**
         *
         * TODO:1.需要理解ResultSet的数据结构和小海豚查询出来的是一样，都是一个表有行列，需要在脑子里构建结果表！
         * TODO:2.有一个光标指向的操作数据行，默认指向第一行的上边！
         *        我们需要移动光标，指向行，再获取列即可！（重点）
         *        boolean = next()
         *              false: 没有数据，也不移动了！
         *              true:  有更多行，并且移动到下一行！
         *       推荐：推荐使用if 或者 while循环，嵌套next方法，循环和判断体内获取数据！
         *       if(next()){获取列的数据！} ||  while(next()){获取列的数据！}
         *
         *TODO：3.获取当前行列的数据！
         *         get类型(int columnIndex | String columnLabel)
         *        列名获取  //lable 如果没有别名，等于列名， 有别名label就是别名，他就是查询结果的标识！
         *        列的角标  //从左到右 从1开始！ 数据库全是从1开始！
         */

        //while(resultSet.next()){
        //     int id=resultSet.getInt(1);
        //     String account1 = resultSet.getString("account");
        //     String password1 = resultSet.getString(3);
        //     String nickname = resultSet.getString("nickname");
        //     System.out.println(nickname+"->"+account1);
        // }
        //进行结果集对象解析
        if (resultSet.next()){
            //只要向下移动，就是有数据 就是登录成功！
            System.out.println("登录成功！");
        }else{
            System.out.println("登录失败！");
        }

        //关闭资源
        resultSet.close();
        statement.close();
        connection.close();


    }
}
```

# statement存在的问题

1. SQL语句需要拼接，比较麻烦
2. 只能拼接字符串类型，其他数据库类型无法处理

3. **可能发生注入攻击**

   > 动态值充当了SQL语句结构,影响了原有的查询结果!

比如输入的密码为: '  or  '1'='1  哪怕用户名不存在也会成功登录

# preparedStatement

```java
package test;

import java.sql.*;
import java.util.Scanner;

/**
 * Description: 使用预编译Statement解决注入攻击问题
 */
public class demo3{
    public static void main(String[] args) throws ClassNotFoundException, SQLException, SQLException {

        //输入账号和密码
        Scanner scanner = new Scanner(System.in);
        String account = scanner.nextLine();
        String password = scanner.nextLine();
        scanner.close();

        //jdbc的查询使用
        //1.注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");

        //2.获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql:///atguigu", "root", "123456");

        //创建preparedStatement
        //connection.createStatement();
        //TODO 需要传入SQL语句结构
        //TODO 要的是SQL语句结构，动态值的部分使用占位符 ? 代替,  占位符?只能代替动态值！
        //TODO 创建preparedStatement，并且传入动态值
        //TODO ?  不能加 '?'  ? 只能替代值，不能替代关键字和容器名

        //3.编写SQL语句结果
        String sql = "select * from t_user where account = ? and password = ? ;";

        //4.创建预编译Statement并设置SQL语句结果
        PreparedStatement preparedStatement = connection.prepareStatement(sql);

        //5.占位符赋值
        //给占位符赋值！ 从左到右，从1开始！
        /**
         *  int 占位符的下角标
         *  object 占位符的值
         */
        preparedStatement.setObject(2,password);
        preparedStatement.setObject(1,account);

        //这哥们内部完成SQL语句拼接！
        //6.执行SQL语句
        ResultSet resultSet = preparedStatement.executeQuery();
        //preparedStatement.executeUpdate()

        //进行结果集对象解析
        if (resultSet.next()){
            //只要向下移动，就是有数据 就是登录成功！
            System.out.println("登录成功！");
        }else{
            System.out.println("登录失败！");
        }

        //关闭资源
        resultSet.close();
        preparedStatement.close();
        connection.close();
    }

}
```

<img src="C:\Users\痞妖\AppData\Roaming\Typora\typora-user-images\image-20230712114201149.png" alt="image-20230712114201149" style="zoom:50%;" />

# CURD

- 数据库数据插入

```java
/**
 * 插入一条用户数据!
 * 账号: test
 * 密码: test
 * 昵称: 测试
 */
@Test
public void testInsert() throws Exception{

    //注册驱动
    Class.forName("com.mysql.cj.jdbc.Driver");

    //获取连接
    Connection connection = DriverManager.getConnection("jdbc:mysql:///atguigu", "root", "root");

    //TODO: 切记, ? 只能代替 值!!!!!  不能代替关键字 特殊符号 容器名
    String sql = "insert into t_user(account,password,nickname) values (?,?,?);";
    PreparedStatement preparedStatement = connection.prepareStatement(sql);

    //占位符赋值
    preparedStatement.setString(1, "test");
    preparedStatement.setString(2, "test");
    preparedStatement.setString(3, "测试");

    //发送SQL语句
    int rows = preparedStatement.executeUpdate();

    //输出结果
    System.out.println(rows);

    //关闭资源close
    preparedStatement.close();
    connection.close();
}
```

- 数据库数据修改

```java
/**
 * 修改一条用户数据!
 * 修改账号: test的用户,将nickname改为tomcat
 */
@Test
public void testUpdate() throws Exception{

    //注册驱动
    Class.forName("com.mysql.cj.jdbc.Driver");

    //获取连接
    Connection connection = DriverManager.getConnection("jdbc:mysql:///atguigu", "root", "root");

    //TODO: 切记, ? 只能代替 值!!!!!  不能代替关键字 特殊符号 容器名
    String sql = "update t_user set nickname = ? where account = ? ;";
    PreparedStatement preparedStatement = connection.prepareStatement(sql);

    //占位符赋值
    preparedStatement.setString(1, "tomcat");
    preparedStatement.setString(2, "test");

    //发送SQL语句
    int rows = preparedStatement.executeUpdate();

    //输出结果
    System.out.println(rows);

    //关闭资源close
    preparedStatement.close();
    connection.close();
}
```

- 数据库数据删除

```java
/**
 * 删除一条用户数据!
 * 根据账号: test
 */
@Test
public void testDelete() throws Exception{

    //注册驱动
    Class.forName("com.mysql.cj.jdbc.Driver");

    //获取连接
    Connection connection = DriverManager.getConnection("jdbc:mysql:///atguigu", "root", "root");

    //TODO: 切记, ? 只能代替 值!!!!!  不能代替关键字 特殊符号 容器名
    String sql = "delete from t_user where account = ? ;";
    PreparedStatement preparedStatement = connection.prepareStatement(sql);

    //占位符赋值
    preparedStatement.setString(1, "test");

    //发送SQL语句
    int rows = preparedStatement.executeUpdate();

    //输出结果
    System.out.println(rows);

    //关闭资源close
    preparedStatement.close();
    connection.close();
}
```

- **数据库数据查询**

```java
/**
 * 查询全部数据!
 *   将数据存到List<Map>中
 *   map -> 对应一行数据
 *      map key -> 数据库列名或者别名
 *      map value -> 数据库列的值
 * TODO: 思路分析
 *    1.先创建一个List<Map>集合
 *    2.遍历resultSet对象的行数据
 *    3.将每一行数据存储到一个map对象中!
 *    4.将对象存到List<Map>中
 *    5.最终返回
 *
 * TODO:
 *    初体验,结果存储!
 *    学习获取结果表头信息(列名和数量等信息)
 */
@Test
public void testQueryMap() throws Exception{

    //注册驱动
    Class.forName("com.mysql.cj.jdbc.Driver");

    //获取连接
    Connection connection = DriverManager.getConnection("jdbc:mysql:///atguigu", "root", "root");

    //TODO: 切记, ? 只能代替 值!!!!!  不能代替关键字 特殊符号 容器名
    String sql = "select id,account,password,nickname from t_user ;";
    PreparedStatement preparedStatement = connection.prepareStatement(sql);

    //占位符赋值 本次没有占位符,省略

    //发送查询语句
    ResultSet resultSet = preparedStatement.executeQuery();

    //创建一个集合
    List<Map> mapList = new ArrayList<>();

    //获取列信息对象
    ResultSetMetaData metaData = resultSet.getMetaData();
    int columnCount = metaData.getColumnCount();
    while (resultSet.next()) {
        Map map = new HashMap();
        for (int i = 1; i <= columnCount; i++) {
            //一定要用getColumnLabel,这会返回别名，而不是返回真名
            map.put(metaData.getColumnLabel(i), resultSet.getObject(i));
        }
        mapList.add(map);
    }

    System.out.println(mapList);

    //关闭资源close
    preparedStatement.close();
    connection.close();
    resultSet.close();
}
```

# preparedStatement使用方式总结

- 使用步骤总结

```java
//1.注册驱动
方案1: 调用静态方法,但是会注册两次
DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
方案2: 反射触发
Class.forName("com.mysql.cj.jdbc.Driver");

//2.获取连接

Connection connection = DriverManager.getConnection();

3 (String url,String user,String password)
2 (String url,Properties info(user password))
1 (String url?user=账号&password=密码 )
    
//3.编写SQL语句

//4.创建statement

//静态
Statement statement = connection.createStatement();
//预编译
PreparedStatement preparedstatement = connection.preparedStatement(sql语句结构);

//5.占位符赋值

preparedstatement.setObject(?的位置 从左到右 从1开始,值)

//6.发送sql语句获取结果

int rows = executeUpdate(); //非DQL
Resultset = executeQuery(); //DQL

//7.查询结果集解析

//移动光标指向行数据 next();  if(next())  while(next())
//获取列的数据即可   get类型(int 列的下角标 从1开始 | int 列的label (别名或者列名))
//获取列的信息   getMetadata(); ResultsetMetaData对象 包含的就是列的信息
                getColumnCount(); | getCloumnLebal(index)
//8.关闭资源
close(); 
```

# 自增长主键回显实现

- 功能需求

  1. **java程序**获取**插入**数据时mysql维护**自增长**维护的主键**id值**,这就是主键回显
  2. 作用: 在多表关联插入数据时,一般主表的主键都是自动生成的,所以在插入数据之前无法知道这条数据的主键,但是从表需要在插入数据之前就绑定主表的主键,这是可以使用主键回显技术:

- 功能实现

  > 继续沿用之前的表数据

```java
package test;

import java.sql.*;

public class demo4 {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.cj.jdbc.Driver");
        Connection conn = DriverManager.getConnection("jdbc:mysql:///atguigu?user=root&password=123456");
        String sql = "insert into t_user(account,password,nickname) values (?,?,?)";

        /**
         * 1. 创建preparedStatement的时候，告知，携带回数据库自增长的主键 （sql，Statement.RETURN_GENERATED_KEYS）;
         * 2. 取主键值的结果集对象，一行一列，获取对应的数据即可 ResultSet resultSet = statement.getGeneratedKeys();
         *
         * */
        PreparedStatement ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);

        ps.setString(1,"zhangsan");
        ps.setString(2,"123456");
        ps.setString(3,"张三");
        int i=ps.executeUpdate();
        if(i>=1){
            System.out.println("插入成功");

            //可以获取回显的主键
            //获取司机装主键的结果集对象，一行 一列    id = 值
            ResultSet rs = ps.getGeneratedKeys();
            if(rs.next()){
                int id = rs.getInt(1);
                System.out.println(id);
            }
        }else{
            System.out.println("插入数据失败");
        }
        ps.close();
    }
}
```

# 批量数据插入性能提升

- 功能需求
  1. 批量数据插入优化
  2. 提升大量数据插入效率
- 功能实现

```Java
/**
 *
 * 批量细节：
 *    1.url?rewriteBatchedStatements=true
 *    2.insert 语句必须使用 values
 *    3.语句后面不能添加分号;
 *    4.语句不能直接执行，每次需要装货  addBatch() 最后 executeBatch();
 *
 * 批量插入优化！
 * @throws Exception
 */
@Test
public void  batchInsertYH() throws Exception{

    //1.注册驱动
    Class.forName("com.mysql.cj.jdbc.Driver");
    //2.获取连接
    Connection connection = DriverManager.getConnection("jdbc:mysql:///atguigu?rewriteBatchedStatements=true",
            "root","root");
    //3.编写SQL语句结构
    String sql = "insert into t_user (account,password,nickname) values (?,?,?)";
    //4.创建预编译的statement，传入SQL语句结构
    /**
     * TODO: 第二个参数填入 1 | Statement.RETURN_GENERATED_KEYS
     *       告诉statement携带回数据库生成的主键！
     */
    long start = System.currentTimeMillis();
    PreparedStatement statement = connection.prepareStatement(sql);
    for (int i = 0; i < 10000; i++) {

        //5.占位符赋值
        statement.setObject(1,"ergouzi"+i);
        statement.setObject(2,"lvdandan");
        statement.setObject(3,"驴蛋蛋"+i);
        //6.装车
        statement.addBatch();//不执行，追加到values后边
    }

    //发车！ 批量操作！
    statement.executeBatch();

    long end = System.currentTimeMillis();

    System.out.println("消耗时间："+(end - start));


    //7.结果集解析

    //8.释放资源
    connection.close();
}
```

# jdbc中数据库事务实现

**业务传递的connection和减钱是同一个! 才可以在一个事务中!**

```java
package test.Bank;

import org.junit.Test;

import java.sql.SQLException;

//测试类
public class BankTest {
    @Test
    public void test() throws SQLException, ClassNotFoundException {
        BankService service = new BankService();
        service.transfer("ergouzi", "lvdandan",500);
    }
}
```

```java
package test.Bank;

import java.sql.*;

public class BankService {
    public void transfer(String addAccount, String subAccount, int money) throws ClassNotFoundException, SQLException {
        System.out.println("addAccount: " + addAccount+", subAccount: " + subAccount+", money: " + money);
        //注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        //获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql:///atguigu","root","123456");
        boolean flag=false;
        //用try catch代码块，调用dao
        try {
            //开启事务
            connection.setAutoCommit(false);
            BankDao bankDao = new BankDao();
            //调用加钱和减钱
            bankDao.addMoney(addAccount,money,connection);
            System.out.println("--------------------------------");
            bankDao.subMoney(subAccount,money,connection);
            flag=true;
            //不报错，提交事务
            connection.commit();
        } catch (Exception e) {
            connection.rollback();
            throw new RuntimeException(e);
        }finally {
            connection.close();
        }

        if(flag)
            System.out.println("转账成功");
        else
            System.out.println("转账失败");
    }
}
```

```java
package test.Bank;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class BankDao {
    //数据库访问dao类
    public int addMoney(String account, int money, Connection connection) throws SQLException {
        String sql = "update t_bank set money = money +? where account = ?;";
        PreparedStatement ps = connection.prepareStatement(sql);
        //占位符赋值
        ps.setObject(1,money);
        ps.setObject(2,account);
        //发送SQL语句
        int rows = ps.executeUpdate();
        //输出结果
        System.out.println("加钱完毕");
        //关闭资源
        ps.close();
        return rows;
    }
    public int subMoney(String account, int money, Connection connection) throws SQLException {
        String sql = "update t_bank set money = money -? where account = ?;";
        PreparedStatement ps = connection.prepareStatement(sql);
        //占位符赋值
        ps.setObject(1,money);
        ps.setObject(2,account);
        //发送SQL语句
        int rows = ps.executeUpdate();
        //输出结果
        System.out.println("减钱完毕");
        //关闭资源
        ps.close();
        return rows;
    }
}
```

# 国货之光Druid连接池技术使用

```java
package test;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.junit.Test;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

public class demo6 {
    public static void main(String[] args) throws SQLException {//硬编码方式
        //创建连接池对象
        DruidDataSource dataSource = new DruidDataSource();
        //必须设置的四个参数
        dataSource.setUrl("jdbc:mysql:///atguigu");
        dataSource.setUsername("root");
        dataSource.setPassword("123456");
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        //非必须 初始化连接数量，最大连接数量
        dataSource.setInitialSize(5);//初始化连接数量
        dataSource.setMaxActive(10); //最大的数量

        //获取连接
        Connection connection = dataSource.getConnection();
        //关闭连接
        connection.close();
    }
    @Test
    public void test() throws Exception {//软编码方式
        //1.读取外部配置文件 properties
        Properties properties=new Properties();
        //src下的文件，可以使用类加载器提供的方法
        InputStream ips = demo6.class.getClassLoader().getResourceAsStream("druid.properties");
        properties.load(ips);
        //2.使用连接池的工具类的工程模式，创建连接池
        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        Connection connection = dataSource.getConnection();
        //数据库crud
        connection.close();
    }
}
```

# jdbc工具类封装

> 优化工具类v1.0版本,考虑事务的情况下!如何一个线程的不同方法获取同一个连接!

ThreadLocal的介绍：

JDK 1.2的版本中就提供java.lang.ThreadLocal，为解决多线程程序的并发问题提供了一种新的思路。
使用这个工具类可以很简洁地编写出优美的多线程程序。通常用来在在多线程中管理共享数据库连接、Session等

**ThreadLocal用于保存某个线程共享变量，原因是在Java中**，每一个线程对象中都有一个ThreadLocalMap<ThreadLocal, Object>，其key就是一个ThreadLocal，而Object即为该线程的共享变量。而这个map是通过ThreadLocal的set和get方法操作的。对于同一个static ThreadLocal，不同线程只能从中get，set，remove自己的变量，而不会影响其他线程的变量。

1、ThreadLocal对象.get: 获取ThreadLocal中当前线程共享变量的值。

2、ThreadLocal对象.set: 设置ThreadLocal中当前线程共享变量的值。

3、ThreadLocal对象.remove: 移除ThreadLocal中当前线程共享变量的值。

```java
package test.utils;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;
//一个线程不同方法获取的不是同一个连接，有问题的代码
/*
public class JdbcUtils {
    private static DataSource ds;
    static{
        //初始化连接池对象
        try {
            Properties pro = new Properties();
            pro.load(ClassLoader.getSystemResourceAsStream("druid.properties"));
            ds= DruidDataSourceFactory.createDataSource(pro);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();//如果这么写，不能保证同一个线程，两次getConnection()得到的是同一个对象
        //如果不能保证是同一个连接对象，就无法保证事务的管理
    }
    public static void free(Connection c) throws SQLException {
        c.setAutoCommit(true);
        c.close();
    }
}
*/
//正确的写法
/*
这个工具类的作用就是用来给所有的SQL操作提供“连接”，和释放连接。
这里使用ThreadLocal的目的是为了让同一个线程，在多个地方getConnection得到的是同一个连接。
这里使用DataSource的目的是为了（1）限制服务器的连接的上限（2）连接的重用性等
 */
public class JdbcUtils {
    private static DataSource ds;
    private static ThreadLocal<Connection> tl = new ThreadLocal<>();
    static{
        //初始化连接池对象
        try {
            Properties pro = new Properties();
            pro.load(ClassLoader.getSystemResourceAsStream("druid.properties"));
            ds= DruidDataSourceFactory.createDataSource(pro);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static Connection getConnection() throws SQLException {
        Connection conn = tl.get();
        if (conn == null) {
            conn = ds.getConnection();
            tl.set(conn);
        }
        return conn;
    }
    public static void free() throws SQLException {
        Connection c = tl.get();
        if(c!=null){
            tl.remove();
            c.setAutoCommit(true);
            c.close();
        }
    }
}
```

# 应用层封装BaseDao

基本上每一个数据表都应该有一个对应的DAO接口及其实现类，发现对所有表的操作（增、删、改、查）代码重复度很高，所以可以**抽取公共代码**，给这些DAO的实现类可以抽取一个公共的父类，我们称为BaseDao

```java
package test.utils;

import java.lang.reflect.Field;
import java.sql.*;
import java.util.ArrayList;

/**
 * 一共封装两个方法
 * 1. DQL
 * 2. 非DQL
 * */
public class BaseDao {
    public int executeUpdate(String sql, Object... params) throws Exception {
        Connection connection = JdbcUtils.getConnection();
        PreparedStatement ps = connection.prepareStatement(sql);

        //占位符赋值
        for (int i = 1; i <= params.length; i++) {
            ps.setObject(i, params[i-1]);
        }
        ps.close();
        //是否回收连接，需要考虑是不是事务
        if (connection.getAutoCommit()) {
            //没有开启事务，正常回收连接
            JdbcUtils.free();
        }
        //开启了事务就不要管了，由业务层处理
        return ps.executeUpdate();
    }



    protected <T> ArrayList<T> query(Class<T> clazz, String sql, Object... args) throws Exception {
        //        创建PreparedStatement对象，对sql预编译
        Connection connection = JdbcUtils.getConnection();
        PreparedStatement ps = connection.prepareStatement(sql);
        //设置?的值
        if(args != null && args.length>0){
            for(int i=0; i<args.length; i++) {
                ps.setObject(i+1, args[i]);//?的编号从1开始，不是从0开始，数组的下标是从0开始
            }
        }

        ArrayList<T> list = new ArrayList<>();
        ResultSet res = ps.executeQuery();

        /*
        获取结果集的元数据对象。
        元数据对象中有该结果集一共有几列、列名称是什么等信息
         */
        ResultSetMetaData metaData = res.getMetaData();
        int columnCount = metaData.getColumnCount();//获取结果集列数

        //遍历结果集ResultSet，把查询结果中的一条一条记录，变成一个一个T 对象，放到list中。
        while(res.next()){
            //循环一次代表有一行，代表有一个T对象
            T t = clazz.newInstance();//要求这个类型必须有公共的无参构造
            //把这条记录的每一个单元格的值取出来，设置到t对象对应的属性中。
            for(int i=1; i<=columnCount; i++){
                //for循环一次，代表取某一行的1个单元格的值
                Object value = res.getObject(i);

                //这个值应该是t对象的某个属性值
                //获取该属性对应的Field对象
//                String columnName = metaData.getColumnName(i);//获取第i列的字段名
                String columnName = metaData.getColumnLabel(i);//获取第i列的字段名或字段的别名
                Field field = clazz.getDeclaredField(columnName);
                field.setAccessible(true);//这么做可以操作private的属性

                field.set(t, value);
            }

            list.add(t);
        }

        res.close();
        ps.close();
        //这里检查下是否开启事务,开启不关闭连接,业务方法关闭!
        //没有开启事务的话,直接回收关闭即可!
        if (connection.getAutoCommit()) {
            //回收
            JdbcUtils.free();
        }
        return list;
    }

    protected <T> T queryBean(Class<T> clazz,String sql, Object... args) throws Exception {
        ArrayList<T> list = query(clazz, sql, args);
        if(list == null || list.size() == 0){
            return null;
        }
        return list.get(0);
    }
}
```

更加理解了泛型和反射，为什么Class后边突然加上了<T>,之前为什么没有，因为泛型是jdk5之后引入的，所以要兼容之前的代码，所以不写泛型，那么默认T是Object，写上了泛型就约束了T的类型。这里写不写都行，只要把后边用到T的地方换成Object，应该就没问题了（个人认为，没实践过）。

反射理解了一个重要作用，就是提高代码的灵活性，因为获取反射的Class.forName(“全类名”)，全类名是从配置文件里边找的，所以如果这个全类名换了，就是本来是test换成了test1，就只需要在配置文件里边改就可以了。









