## Dm Notes

## 一、命令

```sql
-- 创建数据库表空间及文件
create tablespace zh202 datafile 'D:\Programming\DM\data\ZH_202_LOCAL\ZH202.DBF' size 128 autoextend on next 4 maxsize 2048;

-- 配置归档
-- 切换数据库到 MOUNT 状态
ALTER DATABASE MOUNT;
-- 配置本地归档路径
ALTER DATABASE ADD ARCHIVELOG 'DEST=/u01/dmdb/dmarchivelog,TYPE=local,FILE_SIZE=128,SPACE_LIMIT=1024';
-- 数据库置为 OPEN 状态
ALTER DATABASE OPEN;

-- 批量删除指定时间之前的数据库备份集 存储过程
SP_DB_BAKSET_REMOVE_BATCH (
device_type varchar,	-- 设备类型，disk 或 tape。指定 NULL，则忽略存储设备的区分。
end_time datetime 	      --  删除备份集生成的结束时间，仅删除 end_time 之前的备份集。
)

-- 删除备份 存储过程
call SP_DB_BAKSET_REMOVE_BATCH(null, SYSDATE-30); 	-- 删除前30天的备份目录

-- 全量备份
BACKUP DATABASE FULL BACKUPSET ‘/dbbak/dmbak/BACKUP_FILE_01’;

-- 新增增量备份
BACKUP DATABASE INCREMENT WITH BACKUPDIR 'D:\Programming\DM\backup' BACKUPSET 'D:\Programming\DM\backup0906';

-- 恢复与还原
dmrman CTLSTMT="RESTORE DATABASE 'D:\Programming\DM\data\ZH_202_LOCAL\dm.ini' FROM BACKUPSET 'D:\Programming\DM\backup'" 
dmrman CTLSTMT="RECOVER DATABASE 'D:\Programming\DM\data\ZH_202_LOCAL\dm.ini' FROM BACKUPSET 'D:\Programming\DM\backup'" 
dmrman CTLSTMT="RECOVER DATABASE 'D:\Programming\DM\data\ZH_202_LOCAL\dm.ini' UPDATE DB_MAGIC" 

```



