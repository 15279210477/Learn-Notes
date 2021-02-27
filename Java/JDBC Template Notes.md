# JDBC Template Notes

## 一、JDBC基础知识

> Java数据库连接，（Java Database Connectivity）是java语言中用来规范客户端程序如何来访问数据库的应用程序接口，提供了诸如查询和更新数据库中数据的方法。JDBC也是Sun Microsystems的商标。
>
> 我们通常说的JDBC是面向关系型数据库的。

说白了jdbc就是一套接口，来帮助开发者能够以一种比较优雅的方法去连接并且操作数据库，但是我们在操作数据库的时候是需要用到具体数据库厂商对应的驱动，所以我们需要去拿到对应的驱动jar包才能操作数据库。

#### 1.1、jdbc基本步骤

```java
// 1.注册驱动
Class.forName("com.mysql.jdbc.Driver");
// 2.获取连接
Connection conn = DriverManager.getConnection("jdbc:mysql:///xxx","root","123456");
String sql = "SELECT * FROM CONTACT WHERE ID > ?";
// 3.获取执行sql的对象
PreparedStatement statement = conn.prepareStatement(sql);
statement.setObject(1, 1);
// 4.执行sql获取结果
ResultSet rs = statemnet.executeQuery();
// 5.对结果进行处理
while (rs.next()) {
    Object id = rs.getObject(1);
}
// 6.释放资源
xx.close;
```

#### 1.2、jdbc元数据

什么是元数据？

> 描述数据库或其组成部分的数据称为元数据。

元数据类型：

> 数据库元数据	DatabaseMetaData
>
> SQL语句参数元数据	ParmeterMetaData
>
> 结果集元数据	ResultSetMetaData

如何获取元数据对象？

```java
// 数据库元数据获取方式
DatabaseMetaData metaData = connection.getMetaDta();
// SQL语句参数元数据
ParameterMetaData parameterMetaData = pstmt.getParameterMetaData();
// 结果集元数据
ResultSetMetaData metaData = resultSet.getMetaData();
```

--------

## 二、数据源DataSource

#### 2.1、DataSource基本概念

> SUN公司提供了java.sql.DataSource接口，他负责建立与数据库的连接，当应用程序在访问数据库时，不必编写连接数据库的代码，直接引用DataSource获取数据库的连接对象即可。用于操作数据Connection对象。
>
> 数据库连接池负责分配、管理和释放数据库连接，允许应用程序使用同一个现有的数据库连接，而不是在重新建立一个；释放空间时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏。

常见的数据库连接池

```xml
<dependency>
	<groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```

#### 2.2、druid使用步骤

##### 2.2.1、提供db.properties

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///srcurity
username=root
password=123456
```

##### 2.2.2、获取连接池对象

```java
// 1.读取配置文件
InputStream is = Test.class.getClassLoader().getResourceAsStream("db.properties");
// 2.将is导入properties
Properties pp = new Properties();
pp.load(is);
// 3.创建连接池对象
DataSource ds = DruidDataSourceFactory.createDataSource(pp);
```

-----

## 三、JDBC Template API 简单使用

#### 3.1、概念与配置

> jdbcTemplate是Spring为传统的jdbc api进行封装，让我们的数据进行增删改查更加灵活和简单。

```xml
<dependency>
	<groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
```

#### 3.2、简单上手

```java
// 获取template对象
JdbcTemplate jdbcTemplate = new JdbcTemplate(DataSource);
// 增删改
jdbcTemplate.update("INSERT INTO USER VALUES(?,?,?)", "1","2","3");
// 查询多条
jdbcTemplate.query("SELECT * FROM USER", new BeanPropertyRowMapper<User>(User.class));
// 查询一条
jdbcTemplate.queryByObject("SELECT * FROM USER WHERE ID = ?", new BeanPropertyRowMapper<User>(User.class), 1);
```

-------

## 四、JDBC Template API 源码分析

#### 4.1、update方法流程

```java
// 通过内部重载的最终调用
// 1.封装了PreparedStatementCreator（sql）
// 2.封装了PreparedStatementSetter（参数）
protected int update(PreparedStatementCreator psc, @Nullable PreparedStatementSetter pss) throws DataAccessException {
    return updateCount((Integer)this.execute(psc, (ps) -> {...}));
}
// 通过判断，内部执行是通过execute方法
// 1.获取数据库链接对象
// 2.初始化preparedStatement对象
// 3.通过回调函数执行update函数中最后的ps->{}的逻辑
ps -> {
    pss.setValues(ps);	// 将preparedStatement对象和我们传入的参数进行封装
    int rows = ps.executeUpdate();	// 执行sql获取结果
    return rows;
}
```

#### 4.2、query方法流程

```java
// 查看源码发现都会执行query方法，传参数和不传参数的执行流程不一样，但最终都会执行execute方法
// 通过判断，内部执行是通过execute方法
// 1.获取数据库链接对象
// 2.初始化preparedStatement对象
// 3.action实际对象是匿名内部类
new PreparedStatementCallback<T>() {
    @Nullable
    public T doInPreparedStatement(PreparedStatement ps) throws SQLException {
        ResultSet rs = null;
        Object var3;
        try {
            pss.setValues(ps);	// 将preparedStatement对象和我们传入的参数进行封装
            rs = ps.executeQuery();	// 执行查询操作获取结果集
            var3 = rse.extractData(rs);	// 通过反射和内省对结果集进行处理
        } finally {
            JdbcUtils.closeResultSet(rs);
            if (pss instanceof ParameterDisposer) {
                ((ParameterDisposer)pss).cleanupParameters();
            }
        }
        return var3;
    }
}
```