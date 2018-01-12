# 架构实践
## MySQL Master/Slave 配置
### Master 配置：

1. 数据库添加Slave Server：

     grant replication slave on *.* to 'admin'@'slave_ip' identified by 'password';

2. 配置 Master my.cnf
 
	server-id        = 1  
	binlog_do_db     = db1  
	binlog_ignore_db = mysql,information_schema,performance_schema,..... *--不需要同步的db*
 

 
### Slave 配置：

1. 数据库添加Master Server：
	
	change master to master_host='master_ip',master_user='admin',master_port=3306, master_password='<master password>';

2. 配置 Slave my.cnf
	
	server-id = 2  *-- 跟Master不能一致*

### 常用操作：

- 创建数据库

	create database character set utf8 collate utf8_bin;

- 更改读取MASTER BIN LOG 位置

	change master to master_log_file='mysql-bin.000180';

- 跳过一条同步错误

  stop slave;  
  set global sql_slave_skip_counter=1;  
  start slave;
	
 

### 遇到的错误

* ERROR 1051
	
	Can't DROP TABLE because unknown table
	
	原因可能该TABLE不在SLAVE上存在

* Error 1032
	
	Could not execute Update_rows event on table bfly.iar_async_email_sequence;  
	Can't find record in 'iar_async_email_sequence'  
	原因可能是该记录不再SLAVE上存在

* Error: 1539
  
  SQLSTATE: HY000 (ER_EVENT_DOES_NOT_EXIST)
  
  原因可能是EVENT不在SLAVE上存在


以上错误可以跳过

```slave_skip_errors = 1051,1032,1539```
