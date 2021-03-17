# Mysql Notes

-----

## 一、基本命令

```sql
mysql -u root -proot	-- 连接数据库
update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost';	-- 修改密码
flush privileges;	-- 刷新权限
show databases;	   -- 查看所有数据库
use dbname;		-- 切换数据库
show tables;	   -- 查看数据库中的表
describe tablename;		-- 显示数据库表信息
create database dbname;		-- 创建数据库
show create table tablename;	-- 查看表的定义语句
desc tablename;		-- 显示表的结构
SELECT VERSION();	-- 版本
SELECT USER();		-- 用户
```

------

## 二、Mysql的列类型和字段属性

#### 2.1、列类型

> 数值

- **tinyint**：十分小的整数					1个字节
- **smallint**：较小的整数					2个字节
- **mediumint**：中等大小的整数		3个字节
- **int**：标准的整数							4个字节
- **bigint**：较大的整数						8个字节
- **float**：浮点数								4个字节
- **double**：浮点数							8个字节
- **decimal**：字符串形式的浮点数		金融计算使用

> 字符串

- **char**：字符串						0~255字节
- **varchar**：可变字符串			0~65535字节
- **tinytext**：微型文本				2^8-1
- **text**：文本串						2^16-1

> 日期

- **date**：YYYY-MM-DD			日期格式
- **time**：HH:mm:ss				时间格式
- **datetime**：YYYY-MM-DD HH:mm:ss	最常用的时间日期格式
- **year**：年份

#### 2.2、字段属性

**Unsigned**：

- 无符号的整数
- 声明了该列不能声明为负数

**zerofill**：

- 0填充的
- 不足的位数，使用0来填充。如005

---------

## 三、表操作

#### 3.1、创建修改表

```sql
-- auto_increment 自增
-- primary key 主键
create table if not exists `student` (
	`id` int(4) not null auto_increment comment '',
    `name` varchar(30) not null default '' comment '',
    primary key(`id`)
)engine=innodb default charset=utf8;

修改表名 :ALTER TABLE 旧表名 RENAME AS 新表名
添加字段 : ALTER TABLE 表名 ADD字段名 列属性[属性]
修改字段 :
ALTER TABLE 表名 MODIFY 字段名 列类型[属性]
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 列属性[属性]
删除字段 :  ALTER TABLE 表名 DROP 字段名
DROP TABLE [IF EXISTS] 表名
```

#### 3.2、表类型

- MYISAM：节约空间，速度较快
- INNODB：安全性高，事务的处理，多表多用户操作

-----

## 四、DML语言

#### 4.1、INSERT命令

**语法**：

```sql
INSERT INTO 表名[(字段1,字段2,字段3,...)] VALUES('值1','值2','值3')
```

#### 4.2、UPDATE命令

```sql\
UPDATE 表名 SET column_name=value [,column_name2=value2,...] [WHERE condition];
```

#### 4.3、DELETE命令

```sql
DELETE FROM 表名 [WHERE condition];
TRUNCATE [TABLE] table_name;
```

------

## 五、常用函数

**数据函数**

```sql
 SELECT ABS(-8);  /*绝对值*/
 SELECT CEILING(9.4); /*向上取整*/
 SELECT FLOOR(9.4);   /*向下取整*/
 SELECT RAND();  /*随机数,返回一个0-1之间的随机数*/
 SELECT SIGN(0); /*符号函数: 负数返回-1,正数返回1,0返回0*/
```

**字符串函数**

```sql
 SELECT CHAR_LENGTH('狂神说坚持就能成功'); /*返回字符串包含的字符数*/
 SELECT CONCAT('我','爱','程序');  /*合并字符串,参数可以有多个*/
 SELECT INSERT('我爱编程helloworld',1,2,'超级热爱');  /*替换字符串,从某个位置开始替换某个长度*/
 SELECT LOWER('KuangShen'); /*小写*/
 SELECT UPPER('KuangShen'); /*大写*/
 SELECT LEFT('hello,world',5);   /*从左边截取*/
 SELECT RIGHT('hello,world',5);  /*从右边截取*/
 SELECT REPLACE('狂神说坚持就能成功','坚持','努力');  /*替换字符串*/
 SELECT SUBSTR('狂神说坚持就能成功',4,6); /*截取字符串,开始和长度*/
 SELECT REVERSE('狂神说坚持就能成功'); /*反转
 -- 查询姓周的同学,改成邹
 SELECT REPLACE(studentname,'周','邹') AS 新名字
 FROM student WHERE studentname LIKE '周%';
```

**日期和时间函数**

```sql
 SELECT CURRENT_DATE();   /*获取当前日期*/
 SELECT CURDATE();   /*获取当前日期*/
 SELECT NOW();   /*获取当前日期和时间*/
 SELECT LOCALTIME();   /*获取当前日期和时间*/
 SELECT SYSDATE();   /*获取当前日期和时间*/
 -- 获取年月日,时分秒
 SELECT YEAR(NOW());
 SELECT MONTH(NOW());
 SELECT DAY(NOW());
 SELECT HOUR(NOW());
 SELECT MINUTE(NOW());
 SELECT SECOND(NOW());
```

**聚合函数**

| 函数名称 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| COUNT()  | 返回满足Select条件的记录总和数，如 select count(*) 【不建议使用 *，效率低】 |
| SUM()    | 返回数字字段或表达式列作统计，返回一列的总和。               |
| AVG()    | 通常为数值字段或表达列作统计，返回一列的平均值               |
| MAX()    | 可以为数值字段，字符字段或表达式列作统计，返回最大的值。     |
| MIN()    | 可以为数值字段，字符字段或表达式列作统计，返回最小的值。     |

-----

## 六、事务

#### 6.1、ACID原则

**原子性(Atomic)**

- ==整个事务中的所有操作，要么全部完成，要么全部不完成==，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（ROLLBACK）到事务开始前的状态，就像这个事务从来没有执行过一样。

**一致性(Consist)**

- 一个事务可以封装状态改变（除非它是一个只读的）。事务必须始终保持系统处于一致的状态，不管在任何给定的时间并发事务有多少。也就是说：如果事务是并发多个，系统也必须如同串行事务一样操作。其主要特征是保护性和不变性(Preserving an Invariant)，以转账案例为例，假设有五个账户，每个账户余额是100元，那么五个账户总额是500元，如果在这个5个账户之间同时发生多个转账，无论并发多少个，比如在A与B账户之间转账5元，在C与D账户之间转账10元，在B与E之间转账15元，五个账户总额也应该还是500元，这就是保护性和不变性。

**隔离性(Isolated)**

- 隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。这种属性有时称为串行化，为了防止事务操作间的混淆，必须串行化或序列化请求，使得在同一时间仅有一个请求用于同一数据。

**持久性(Durable)**

- 在事务完成以后，该事务对数据库所作的更改便持久的保存在数据库之中，并不会被回滚。

> 基本语法

```sql
-- 使用set语句来改变自动提交模式
SET autocommit = 0;   /*关闭*/
SET autocommit = 1;   /*开启*/
-- 注意:
--- 1.MySQL中默认是自动提交
--- 2.使用事务时应先关闭自动提交
-- 开始一个事务,标记事务的起始点
START TRANSACTION  
-- 提交一个事务给数据库
COMMIT
-- 将事务回滚,数据回到本次事务的初始状态
ROLLBACK
-- 还原MySQL数据库的自动提交
SET autocommit =1;
-- 保存点
SAVEPOINT 保存点名称 -- 设置一个事务保存点
ROLLBACK TO SAVEPOINT 保存点名称 -- 回滚到保存点
RELEASE SAVEPOINT 保存点名称 -- 删除保存点
```

------

## 七、索引

> 索引（index）是帮助MYSQL高效获取数据的==数据结构==。

- 主键索引 (Primary Key)
- 唯一索引 (Unique)
- 常规索引 (Index)
- 全文索引 (FullText)

-------

## 八、三大范式

**第一范式 (1st NF)**

第一范式的目标是确保每列的==原子性==,如果每列都是==不可再分的最小数据单元==,则满足第一范式

**第二范式(2nd NF)**

第二范式（2NF）是在第一范式（1NF）的基础上建立起来的，即满足第二范式（2NF）必须先满足第一范式（1NF）。

==第二范式要求每个表只描述一件事情。==

**第三范式(3rd NF)**

如果一个关系满足第二范式,并且除了主键以外的其他列都不传递依赖于主键列,则满足第三范式.

第三范式需要确保==数据表中的每一列数据都和主键直接相关==，而不能间接相关。



























