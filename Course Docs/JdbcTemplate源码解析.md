## 济南校区公开课-JdbcTemplate源码分析



### 1.回顾jdbc基础知识

> Java数据库连接，（Java Database Connectivity，简称JDBC）是Java语言中用来规范客户端程序如何来访问数据库的应用程序接口，提供了诸如查询和更新数据库中数据的方法。JDBC也是Sun Microsystems的商标。我们通常说的JDBC是面向关系型数据库的。

说白了jdbc就是一套接口,来帮助我们开发者能够以一种比较优雅的方式去连接并且操作数据库,但是我们在操作数据库的时候是需要用到具体的数据库厂商对应的驱动,所以我们需要去拿到对应的驱动jar包才能操作数据库

~~~java
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.38</version>
</dependency>
~~~

#### 1.1 jdbc基本步骤

~~~java
//        1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
//        2.获取数据库连接对象
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/security", "root", "123456");
//        3.通过连接对象获取执行sql的对象
        //Statement statement = connection.createStatement();//方式1，这种方式存在sql注入问题，不用
        String sql ="select * from users";
        PreparedStatement pstmt = connection.prepareStatement(sql);//方式2，预编译
//        4.执行sql获取结果
        ResultSet resultSet = pstmt.executeQuery();
//        5.对结果进行处理(增删改 / 查)
        while(resultSet.next()){
            Object id = resultSet.getObject("id");
            Object name = resultSet.getObject("username");
            Object pass = resultSet.getObject("password");
            System.out.println(id+"~"+name+"~"+pass);
        }
//        6.释放资源
        resultSet.close();
        pstmt.close();
        connection.close();
~~~

#### 1.2 jdbc元数据

什么是元数据?

> 描述数据库或其组成部分的数据成为元数据 

元数据类型

> 数据库元数据		DatabaseMetaData
>
> SQL语句参数元数据		ParameterMetaData
>
> 结果集元数据		ResultSetMetaData
>

如何获取元数据对象?

~~~java
DatabaseMetaData metaData = connection.getMetaData();//数据库元数据获取方式
ParameterMetaData parameterMetaData = pstmt.getParameterMetaData();//SQL语句参数元数据
ResultSetMetaData metaData = resultSet.getMetaData();//结果集元数据
~~~



### 2.回顾数据源DataSource

#### 2.1 DataSource基本概念

> SUN公司提供了javax.sql.DataSource接口，它负责建立与数据库的连接，当在应用程序中访问数据库时
>  不必编写连接数据库的代码，直接引用DataSource获取数据库的连接对象即可。用于获取操作数据Connection对象。
>
> 数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个；释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏。
>

常见的数据库连接池

>  c3p0,druid(德鲁伊)

```java
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```

#### 2.2 德鲁伊使用步骤

##### 2.2.1 提供db.properties

~~~properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///srcurity
username=root
password=123456
~~~

##### 2.2.2 获取连接池对象

~~~java
//1.读取配置文件
InputStream is = ATest.class.getClassLoader().getResourceAsStream("db.properties");
//2.将配文件文件内容家在到Properties对象种
Properties pp = new Properties();
pp.load(is);
//3.创建连接池对象
DataSource dataSource = DruidDataSourceFactory.createDataSource(pp);
~~~

##### 2.2.3 jdbc步骤改为

```java
//       1.获取连接池对象
InputStream is = ATest.class.getClassLoader().getResourceAsStream("db.properties");
Properties pp = new Properties();
pp.load(is);
DataSource dataSource = DruidDataSourceFactory.createDataSource(pp);
//        2.获取连接对象
Connection connection = dataSource.getConnection()
//        3.通过连接对象获取执行sql的对象
//Statement statement = connection.createStatement();//方式1，这种方式存在sql注入问题，不用
String sql ="select * from users";
PreparedStatement pstmt = connection.prepareStatement(sql);//方式2，预编译
//        4.执行sql获取结果
ResultSet resultSet = pstmt.executeQuery();
//        5.对结果进行处理(增删改 / 查)
while(resultSet.next()){
    Object id = resultSet.getObject("id");
    Object name = resultSet.getObject("username");
    Object pass = resultSet.getObject("password");
    System.out.println(id+"~"+name+"~"+pass);
}
//        6.释放资源
resultSet.close();
pstmt.close();
connection.close();
```

### 3.回顾JdbcTemplate相关api

> JdbcTemplate是Spring为传统的jdbc API进行封装,让我们对数据库的数据进行增删改查更加灵活和简单
>
> 虽然jdbcTemplate很灵活，但和ORM框架相比jdbcTemplate功能就显得力不从心了,学习jdbcTemplate是为学习ORM(对象关系映射)框架做铺垫

~~~java
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
~~~



```java
  1.创建对象
  JdbcTemplate template = new JdbcTemplate(dataSource)
  2.执行sql
  执行增删改
  template.update(sql,参数...)//参数就是?占位符对应的内容
  执行查询
  List<实体类>=template.query(sql,new BeanPropertyRowMapper<>(实体类.class),参数...)//多条数据结果
  实体类  = template.queryForObject(sql,new BeanPropertyRowMapper<>(实体类.class),参数...)//单挑记录
```



jdbctemplate优化查询代码

~~~java
//1.获取连接池
InputStream is = ATest.class.getClassLoader().getResourceAsStream("db.properties");
Properties pp = new Properties();
pp.load(is);
DataSource dataSource = DruidDataSourceFactory.createDataSource(pp);
//2.初始化JdbcTemplate对象
JdbcTemplate template = new JdbcTemplate(dataSource);
//3.查询
List<Users> users = template.query("select * from users where id>?", new BeanPropertyRowMapper<>(Users.class), 5);
users.forEach(user-> System.out.println(user));
~~~



### 4.源码分析JdbcTemplate(具体代码流程看我整理的笔记)

#### 4.1 JdbcTemplate增删改源码执行逻辑

#### 4.2 JdbcTemplate查询源码执行逻辑



底层实现逻辑的流程

```java
1.jdbcTemplate在初始化的时候要传入连接池
  为了获取连接对象
  
2.jdbcTemplate在调用update或者query或者queryForObject方法的时候最终都会执行到execute方法中
  内部的处理逻辑不一样

3.execute方法内部执行的逻辑

    public <T> T execute(PreparedStatementCreator psc, PreparedStatementCallback<T> action)
          throws DataAccessException {
        ...
        //获取连接
        Connection con = DataSourceUtils.getConnection(obtainDataSource());
        PreparedStatement ps = null;
        try {
          //初始化PreparedStatement对象
          ps = psc.createPreparedStatement(con);
          //配置Statement的fetchsize和maxrows
          applyStatementSettings(ps);
          //返回调用query方法中的new PreparedStatementCallback<T>() {...}逻辑
          T result = action.doInPreparedStatement(ps);
          ...
          return result;
        }
        catch (SQLException ex) {
          ...
        }
        finally {
          ...
        }
      }

4.在execute方法中最终都会回到action.doInPreparedStatement(ps)中,这个方法都是实现类(需要去对应的方法找他内部的匿名内部类)

5.匿名内部类中的方法逻辑中最终会调用PreparedStatement的executeUpdate或者executeQuery方法

6.如果是update那么数据直接返回,但是如果是query,内部还要执行BeanPropertyRowMapper中的mapRow方法,这个方法会将rs和实际的对象进行绑定
```

