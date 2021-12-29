## Dm Notes

## 一、命令

```sql
-- 创建数据库表空间及文件
create tablespace zh202 datafile '/home/dm/data/ZH202_VM/ZH202.DBF' size 128 autoextend on next 4 maxsize 2048;

-- 创建数据库实例（shell）
./dminit PATH=/home/dm/data PAGE_SIZE=16 CASE_SENSITIVE=N CHARSET=1 SYSDBA_PWD=ZH202123456! DB_NAME=ZH202_VM INSTANCE_NAME=ZH202_VM

-- 启动达梦数据库（shell）
./dmserver /home/dm/data/ZH202_VM/dm.ini -noconsole
-- 重启数据库
./DmServiceDMSERVER restart

-- 注册dm服务（/home/dm/script/root目录）
./dm_service_installer.sh -t dmserver -dm_ini /home/dm/data/ZH202_VM/dm.ini -p DMSERVER

-- 配置归档
-- 切换数据库到 MOUNT 状态
ALTER DATABASE MOUNT;
-- 配置本地归档路径
ALTER DATABASE ADD ARCHIVELOG 'DEST=/u01/dmdb/dmarchivelog,TYPE=local,FILE_SIZE=128,SPACE_LIMIT=1024';
-- 数据库设置为 OPEN 状态
ALTER DATABASE OPEN;
-- 设置为归档模式
alter database archivelog;
-- 停止数据库实例
shutdown immediate;

-- 批量删除指定时间之前的数据库备份集 存储过程
SP_DB_BAKSET_REMOVE_BATCH (
device_type varchar,	-- 设备类型，disk 或 tape。指定 NULL，则忽略存储设备的区分。
end_time datetime 	      --  删除备份集生成的结束时间，仅删除 end_time 之前的备份集。
)

-- 删除备份 存储过程
call SP_DB_BAKSET_REMOVE_BATCH(null, SYSDATE - 30); 	-- 删除前30天的备份目录

-- 全量备份
BACKUP DATABASE FULL BACKUPSET ‘/dbbak/dmbak/BACKUP_FILE_01’;

-- 新增增量备份
BACKUP DATABASE INCREMENT WITH BACKUPDIR 'D:\Programming\DM\backup' BACKUPSET 'D:\Programming\DM\backup0906';

-- 恢复与还原
dmrman CTLSTMT="RESTORE DATABASE 'D:\Programming\DM\data\ZH_202_LOCAL\dm.ini' FROM BACKUPSET 'D:\Programming\DM\backup'" 
dmrman CTLSTMT="RECOVER DATABASE 'D:\Programming\DM\data\ZH_202_LOCAL\dm.ini' FROM BACKUPSET 'D:\Programming\DM\backup'" 
dmrman CTLSTMT="RECOVER DATABASE 'D:\Programming\DM\data\ZH_202_LOCAL\dm.ini' UPDATE DB_MAGIC" 

# dm创建数据库实例（shell）
./dminit PATH=/home/dm/dmdbms PAGE_SIZE=16 CASE_SENSITIVE=N CHARSET=1 SYSDBA_PWD=ZH202123456! DB_NAME=ZH202_VM INSTANCE_NAME=ZH202_VM

# 查看系统作业执行日志
select * from SYSJOB.SYSJOBHISTORIES2;

# 查询锁表记录
select o.name,l.* from v$lock l,sysobjects o where l.table_id=o.id and blocked=1;
# 查询session
select a.*,b.NAME,c.SESS_ID from v$lock a left join sysobjects b on b.ID=a.TABLE_ID left join v$sessions c on a.TRX_ID=c.TRX_ID;
select o.name,c.sess_id,l.* from v$lock l, sysobjects o, v$sessions c where l.table_id=o.id and l.TRX_ID=c.TRX_ID and blocked=1;
# kill对应的session
sp_close_session(sess_id);
# 查询等待的事务
select * from v$trxwait;
# 查询当前用户连接数
select count(*) from v$sessions where state='ACTIVE';
# 查询最大连接数
select SF_GET_PARA_VALUE(2,'MAX_SESSIONS');
# 修改最大连接数
ALTER SYSTEM SET 'MAX_SESSIONS' =1000 spfile;

```

-----

## 二、解释

#### 2.1.归档模式

> 归档模式是与非归档模式对应的，这两种模式是DM8数据库的两种运行状态。
> 非归档模式下，数据库只会将重做日志吸入联机日志文件中进行存储；归档模式下，数据库会同时将重做日志写入联机日志文件和归档日志文件中分别进行存储。
> 无论是归档模式和非归档模式，联机日志文件都是存在的。

#### 2.2.CLUSTER

> CLUSTER PRIMARY KEY 主键约束，指明指定列作为基表的聚集索引（也叫聚簇索引）主关键字；
> NOT CLUSTER PRIMARY KEY 主键约束，指明指定列作为基表的非聚集索引主关键字；

--------

## 三、日志分类

#### 1.重做日志

> ​	重做日志（即 REDO 日志）指在 DM 数据库中添加、删除、修改对象，或者改变数据， DM 都会按照特定的格式，将这些操作执行的结果写入到当前的重做日志文件中。重做日志文 件以 log 为扩展名。每个 DM 数据库实例必须至少有 2 个重做日志文件，默认两个日志文件 为 DAMENG01.log、DAMENG02.log，这两个文件循环使用。

















