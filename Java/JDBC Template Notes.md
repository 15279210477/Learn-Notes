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

