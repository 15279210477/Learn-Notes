# Mysql Notes

-----

## 一、基本命令

```sql
mysql -u root -proot	-- 连接数据库
mysql -h 服务器ip地址 -P 3306 -u root -p
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

## 三、三大范式

**第一范式 (1st NF)**

第一范式的目标是确保每列的==原子性==,如果每列都是==不可再分的最小数据单元==,则满足第一范式

**第二范式(2nd NF)**

第二范式（2NF）是在第一范式（1NF）的基础上建立起来的，即满足第二范式（2NF）必须先满足第一范式（1NF）。

==第二范式要求每个表只描述一件事情。==

**第三范式(3rd NF)**

如果一个关系满足第二范式,并且除了主键以外的其他列都不传递依赖于主键列,则满足第三范式.

第三范式需要确保==数据表中的每一列数据都和主键直接相关==，而不能间接相关。

--------------

## 四、表操作

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

## 五、DML语言

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

## 六、常用函数

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
 SELECT CHAR_LENGTH('abc'); /*返回字符串包含的字符数*/
 SELECT CONCAT('我','爱','程序');  /*合并字符串,参数可以有多个*/
 SELECT INSERT('我爱编程helloworld',1,2,'超级热爱');  /*替换字符串,从某个位置开始替换某个长度*/
 SELECT LOWER('KuangShen'); /*小写*/
 SELECT UPPER('KuangShen'); /*大写*/
 SELECT LEFT('hello,world',5);   /*从左边截取*/
 SELECT RIGHT('hello,world',5);  /*从右边截取*/
 SELECT REPLACE('abc','b','a');  /*替换字符串*/
 SELECT SUBSTR('abcdef',4,6); /*截取字符串,开始和长度*/
 SELECT REVERSE('abc'); /*反转
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

## 七、事务

#### 6.1、ACID原则

**原子性(Atomic)**

- ==整个事务中的所有操作，要么全部完成，要么全部不完成==，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（ROLLBACK）到事务开始前的状态，就像这个事务从来没有执行过一样。

**一致性(Consist)**

- ==执⾏事务前后，数据保持⼀致，多个事务对同⼀个数据读取的结果是相同的==。

**隔离性(Isolated)**

- 并发访问数据库时，==⼀个⽤户的事务不被其他事务所⼲扰==，各并发事务之间数据库是独⽴的。

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

## 八、索引

#### 7.1、概述

> 索引（index）是帮助MYSQL高效获取数据的==数据结构==。

> 索引的优点：大大加快数据查询速度。
>
> 索引的缺点：
>
> 1. 维护索引需要耗费数据库资源
> 2. 索引需要占用磁盘空间
> 3. 当对表进行增删改操作时，因为要维护索引，速度会受到影响

#### 7.2、分类

- 主键索引 (Primary Key)
  - 设置为主键后数据库会自动建立索引，innodb为聚簇索引
- 唯一索引 (Unique)
  - 索引列的值必须为唯一，可为空
- 常规索引 (Index)
  - 一个索引只包含单个列，一个表可以有多个常规索引
- 复合索引
  - 即一个索引包含多个列
  - name age bir 支持最左前缀原则，mysql在查询过程中会自动调整查询字段顺序以便利用索引

- 全文索引 (FullText)
  - 希望通过关键字的匹配来进行查询过滤，那么就需要基于相似度的查询，而不是原来的精确数值比较
  - MySQL 5.6 以前的版本，只有 MyISAM 存储引擎支持全文索引
  - 只有字段的数据类型为 char、varchar、text 及其系列才可以建全文索引

#### 7.3、结构

![image-20210320130018815](../Images%5Cimage-20210320130018815.png)

从上图中，可以看到每个节点中不仅包含数据的key值，还有data值。而每一个页的存储空间是有限的，如果data数据较大时将会导致每个节点（即一个页）能存储的key的数量很小。当存储的数据量很大时同样会导致B-Tree的深度较大，增大查询时的磁盘I/O次数，从而影响效率。在B+Tree中，==所有数据记录节点都是按照键值大小顺序存放在同一层的叶子节点上，而非叶子节点上只有存储key值信息==，这样可以大大加大每个节点存储的key值数量，降低B+Tree的高度。

**B+tree相对于B-Tree有几点不同**：

1. 非叶子节点只存储键值信息。
2. 所有叶子节点之间都有一个链指针。
3. 数据记录都存放在叶子节点中。

**聚簇索引和非聚簇索引**：

- 聚簇索引：将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据
- 非聚簇索引：将数据与索引分开存储，索引结构的叶子节点指向了数据对应的位置

==注意==：在innodb中，在聚簇索引之上创建的索引称之为辅助索引，非聚簇索引都是辅助索引。辅助索引叶子节点存储的不再是行的物理位置，而是主键值，辅助索引访问数据总是需要二次查找。

![image-20210320131730143](../Images%5Cimage-20210320131730143.png)

#### 7.4、Mysql索引失效的场景

1. like 以%开头，索引无效；当like前缀没有%，后缀有%时，索引有效。
2. or语句前后没有同时使用索引。当or左右查询字段只有一个是索引，该索引失效，只有当or左右查询字段均为索引时，才会生效。
3. 复合索引，不是使用第一列索引，索引失效。
4. 数据类型出现隐式转化。如varchar不加单引号的话可能会自动转换为int型，使索引无效，产生全表扫描。
5. 在索引字段上使用not，<>，!=。不等于操作符是永远不会用到索引的，因此对它的处理只会产生全表扫描。 优化方法： key<>0 改为 key>0 or key<0。
6. 对索引字段进行计算操作、字段上使用函数。
7. 当全表扫描速度比索引速度快时，mysql会使用全表扫描，此时索引失效。

----------

## 九、Mysql日志

> 默认情况下，这些日志都是出于未激活的状态。当激活日志时，所有的日志都默认配置在数据文件的目录下。

#### 9.1、二进制日志（binlog）

> 主要用于记录数据库的变化情况，即 SQL 语句的 DDL 和 DML 语句，不包含数据记录查询操作。
>
> 如果 MySQL 数据库意外停止，可以通过二进制日志文件来查看用户执行了哪些操作，对数据库服务器文件做了哪些修改，然后根据二进制日志文件中的记录来恢复数据库服务器。
>
> 默认情况下，二进制日志功能是关闭的。
>
> ==使用 mysqlbinlog 命令进行还原操作时，必须是编号（number）小的先还原。==

```sql
-- 查看日志是否开启
SHOW VARIABLES LIKE 'log_bin';

-- 配置文件my.cnf属性来开启binlog日志
[mysqld]
log-bin=dir/[filename]
-- 自动删除的天数
expire_logs_days = 10
-- 最大单个日志文件大小
max_binlog_size = 100M

-- 查看binlog日志文件
SHOW binary logs;
-- 查看当前正在写入的二进制日志文件
SHOW master status;

-- 删除所有二进制日志文件
RESET MASTER;
-- 删除指定编号之前的二进制日志
PURGE MASTER LOGS TO 'mylog.000004';

-- 暂停0/开启1二进制日志功能
SET SQL_LOG_BIN=0/1;

-- 恢复数据库
mysqlbinlog [options] log_file
-- --start-datetime="yyyy-MM-dd hh:mm:ss" 从二进制日志中读取指定等于时间戳或者晚于本地计算机的时间
-- --stop-datetime="yyyy-MM-dd hh:mm:ss" 从二进制日志中读取指定小于时间戳或者等于本地计算机的时间
-- --start-position="" 从二进制日志中读取指定position 事件位置作为开始
-- --stop-position="" 从二进制日志中读取指定position 事件位置作为截至

-- 生成至指定sql文件
mysqlbinlog --stop-position=1709 /var/mysql/data/mysql-bin.000004 > /tmp/binlog.sql
-- 导入sql文件
mysql > source xxx.sql
```

#### 9.2、错误日志（error log）

> 用于记录MYSQL服务进程mysqld在启动/关闭或运行过程中遇到的错误信息。

```sql
-- 配置错误日志位置
log-error=/data/mysql/error.err

-- 查询日志参数配置
show variables like ‘log_error%’;
```

#### 9.3、慢查询日志（slow query log）

> 慢查询日志（slow query log）就是记录执行时间超出指定值（long_query_time）或其他指定条件（例如，没有使用到索引，结果集大于n行(指定行））的SQL语句。慢查询日志的参数对于数据库的优化是非常重要的，也是sql优化的前提。

![2019051719011743.png](../images/2019051719011743.png)

#### 9.4、中继日志（relay log）

> relay log是复制过程中产生的日志，很多方面都跟binary log差不多，区别是: relay log是从库服务器I/O线程将主库服务器的二进制日志读取过来记录到从库服务器本地文件，然后从库的SQL线程会读取relay-log日志的内容并应用到从库服务器上。==用于保存主服务器的二进制日志中读取到的事件==。

```sql
-- 定义relay_log的位置和名称，如果值为空，则默认位置在数据文件的目录，文件名为host_name-relay-bin.nnnnnn
relay_log

-- 标记relay log 允许的最大值，如果该值为0，则默认值为max_binlog_size(1G)；如果不为0，max_relay_log_size则为最大的relay_log文件大小
max_relay_log_size

-- 同relay_log，定义relay_log的位置和名称；
relay_log_index

-- 设置relay-log.info的位置和名称（relay-log.info记录MASTER的binary_log的恢复位置和relay_log的位置）
relay_log_info_file

-- 是否自动清空不再需要中继日志时。默认值为1(启用)。
relay_log_purge

-- 当slave从库宕机后，假如relay-log损坏了，导致一部分中继日志没有处理，则自动放弃所有未执行的relay-log，并且重新从master上获取日志，这样就保证了relay-log的完整性。默认情况下该功能是关闭的，将relay_log_recovery的值设置为 1时，可在slave从库上开启该功能，建议开启。
relay_log_recovery

-- 防止中继日志写满磁盘，这里设置中继日志最大限额。但此设置存在主库崩溃，从库中继日志不全的情况，不到万不得已，不推荐使用；
relay_log_space_limit

-- 这个参数和sync_binlog是一样的，当设置为1时，slave的I/O线程每次接收到master发送过来的binlog日志都要写入系统缓冲区，然后刷入relay log中继日志里，这样是最安全的，因为在崩溃的时候，你最多会丢失一个事务，但会造成磁盘的大量I/O。当设置为0时，并不是马上就刷入中继日志里，而是由操作系统决定何时来写入，虽然安全性降低了，但减少了大量的磁盘I/O操作。这个值默认是0，可动态修改。
sync_relay_log

-- 这个参数和sync_relay_log参数一样，当设置为1时，slave的I/O线程每次接收到master发送过来的binlog日志都要写入系统缓冲区，然后刷入http://relay-log.info里，这样是最安全的，因为在崩溃的时候，你最多会丢失一个事务，但会造成磁盘的大量I/O。当设置为0时，并不是马上就刷入http://relay-log.info里，而是由操作系统决定何时来写入，虽然安全性降低了，但减少了大量的磁盘I/O操作。这个值默认是0，可动态修改。
sync_relay_log_info
```

#### 9.5、查询日志（general log）

> 查询日志在mysql中称为general log（通用日志），==查询日志记录了数据库执行的命令==，不管这些语句是否正确，都会被记录。由于数据库操作命令有可能非常多而且执行比较频繁，所以开启了查询日志以后，数据库可能需要不停的写入查询日志，这样会==增大服务器的IO压力==，增加很多系统开销，影响数据库的性能，所以默认情况下是关闭的，也不建议开启。

```sql
general_log：                指定是否开启查询日志（ON表示开启，OFF表示未开启，默认OFF）
general_log_file：           当log_output设置为“FILE”时，指定将查询日志保存成哪个文件、叫什么名，默认与主机名相同，一般位于/var/lib/mysql目录下
log_output： 　　　　　　　　　 指定将记录到的查询保存到什么位置（“NONE”、“FILE”、“TABLE”、“FILE,TABLE”）
file：                       保存成一个文件
table：                      保存成一张表
none：                       不记录
```

#### 9.6、重做日志（redo log）

> **作用**：
>
> ​		==确保事务的持久性。redo日志记录事务执行后的状态，用来恢复未写入data file的已成功事务更新的数据==。防止在发生故障的时间点，尚有脏页未写入磁盘，在重启[mysql](https://www.2cto.com/database/MySQL/)服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性。
>
> **什么时候产生：**
>
> 　　事务开始之后就产生redo log，redo log的落盘并不是随着事务的提交才写入的，而是在事务的执行过程中，便开始写入redo log文件中。
>
> **什么时候释放：**
>
> 　　当对应事务的脏页写入到磁盘之后，redo log的使命也就完成了，重做日志占用的空间就可以重用（被覆盖）。

#### 9.7、回滚日志（undo log）

> **作用：**
>
> 　　保证数据的原子性，保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读。
>
> **内容：**
>
> 　　逻辑格式的日志，在执行undo的时候，仅仅是将数据从逻辑上恢复至事务之前的状态，而不是从物理页面上操作实现的，这一点是不同于redo log的。
>
> **什么时候产生：**
>
> 　　事务开始之前，将当前是的版本生成undo log，undo 也会产生 redo 来保证undo log的可靠性。
>
> **什么时候释放：**
>
> 　　当事务提交之后，undo log并不能立马被删除，而是放入待清理的链表，由purge线程判断是否由其他事务在使用undo段中表的上一个事务之前的版本信息，决定是否可以清理undo log的日志空间。















