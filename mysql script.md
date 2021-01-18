# MySQL

本文SQL脚本基于MySQL5.6 - MySQL5.7

目录： 

[toc]



## **常用SQL**

### **查询版本信息**

```mysql
-- 查询版本信息 Server version: 5.7.24 MySQL Community Server (GPL)
mysql -v
mysql

mysqladmin --version 
```

### **查询用户和用户组**

```shell
[hadoop@test ~]$ cat /etc/passwd | grep mysql
mysql:x:496:493:MySQL server:/var/lib/mysql:/bin/bash
[hadoop@test ~]$ cat /etc/group | grep mysql      
mysql:x:493:
```



### **配置文件**

```mysql
-- 默认配置 datadir 位于/var/lib/mysql
datadir=/var/lib/mysql  -- data文件保存路径
socket=/var/lib/mysql/mysql.sock
# new
max_connections = 600

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log -- log日志路径
pid-file=/var/run/mysqld/mysqld.pid

innodb_file_per_table = 1
innodb_status_file = 1
innodb_buffer_pool_size = 6G
innodb_flush_log_at_trx_commit = 0
innodb_log_buffer_size = 16M
innodb_log_file_size = 64M
innodb_support_xa = 0
default-storage-engine = innodb
bulk_insert_buffer_size = 8M
join_buffer_size = 16M
max_heap_table_size = 32M
tmp_table_size = 32M
max_tmp_tables = 48
read_buffer_size = 32M
read_rnd_buffer_size = 16M
key_buffer_size = 32M
thread_cache_size = 32
innodb_thread_concurrency = 8
innodb_flush_method = O_DIRECT
innodb_rollback_on_timeout = 1
query_cache_size = 16M
query_cache_limit = 16M
collation_server = utf8_bin
character_set_server = utf8

-- 默认mysql配置文件路径
/etc/my.cnf

--  mysql.server 命令及配置文件
/usr/share/mysql

-- mysqladmin mysqldump 等命令目录
/usr/bin

-- mysql 启停相关脚本 
[hadoop@test mysql]$ cd /etc/init.d/mysql
```

### **数据文件**

```mysql
-- frm文件 存放表结构

--  myd 存放表数据

-- myi 存放表索引
```

### **启动关闭服务**

```shell
-- mysql 的守护进程是mysqld
-- 查看服务状态
[root@0daycrack ~]# service mysqld status

-- 启动服务
[root@0daycrack ~]# service mysqld start

-- 停止服务
[root@0daycrack ~]# service mysqld stop

-- 设置开机自启动
chkconfig mysql on
-- 查看是否启动 , 系统为3的启动模式下，开启MySQL自动开启 
[hadoop@test ~]$ chkconfig --list | grep mysql
mysql     
      0:off   1:off   2:on    3:on    4:on    5:on    6:off

-- 查看对应数字对应启动级别 ， 设置3为启动模式
[hadoop@test ~]$ cat /etc/inittab
# Default runlevel. The runlevels used are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
id:3:initdefault:
```

### **登录MySQL**

```shell
-- 登录
root/root , root/Gg117664 , zabbix/Gg117664..
[root@0daycrack ~]# mysql -uroot -p
```

### **数据库查看&表查看**

```mysql
-- 显示当前可用库
mysql> show databases;

-- 切换数据库
mysql> use zabbix;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

-- 显示当前库下所有表
mysql> show tables;
```

### 创建数据库

```mysql
mysql> create database if not exists mydb1;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| kettleRepo         |
| log_db             |
| mydb1              |
| mysql              |
| performance_schema |
| test               |
+--------------------+
7 rows in set (0.00 sec)
```



### 删除数据库

```mysql
mysql> drop database if exists mydb1;
Query OK, 0 rows affected (0.41 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| kettleRepo         |
| log_db             |
| mysql              |
| performance_schema |
| test               |
+--------------------+
6 rows in set (0.00 sec)
```

### 数据库编码

```mysql
-- 查看数据库编码
mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+

mysql> show variables like '%character%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+

-- 查看表编码
mysql> show create table T
    -> ;
+-------+------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                               |
+-------+------------------------------------------------------------------------------------------------------------+
| T     | CREATE TABLE `T` (
  `c` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci |
+-------+------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
-- 查询字段
mysql> show full columns from T;
+-------+---------+-----------+------+-----+---------+-------+---------------------------------+---------+
| Field | Type    | Collation | Null | Key | Default | Extra | Privileges                      | Comment |
+-------+---------+-----------+------+-----+---------+-------+---------------------------------+---------+
| c     | int(11) | NULL      | YES  |     | NULL    |       | select,insert,update,references |         |
+-------+---------+-----------+------+-----+---------+-------+---------------------------------+---------+
1 row in set (0.00 sec)

-- 修改数据库编码
mysql> alter database test character set utf8;
Query OK, 1 row affected (0.00 sec)

-- 临时修改编码，当前会话中
set character_set_client=utf8

mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)

-- 修改表编码
mysql> alter table T character set utf8mb4; 
Query OK, 0 rows affected (0.00 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show create table T;
+-------+---------------------------------------------------------------------------------------+
| Table | Create Table                                                                          |
+-------+---------------------------------------------------------------------------------------+
| T     | CREATE TABLE `T` (
  `c` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 |
+-------+---------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

-- 永久修改数据库编码，配置文件中修改  重点关注client、results、connection编码
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

### 存储引擎

```mysql
-- 查看支持引擎
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+

mysql> show variables like '%storage_engine%';
+----------------------------+--------+
| Variable_name              | Value  |
+----------------------------+--------+
| default_storage_engine     | InnoDB |
| default_tmp_storage_engine | InnoDB |
| storage_engine             | InnoDB |
+----------------------------+--------+
```





### 表相关操作（DDL）

```mysql
-- 创建表
CREATE TABLE student (
    id CHAR(10),
    name VARCHAR(50),
    age INT,
    gender VARCHAR(10)
);
-- 显示建表语句
mysql> show create table student;
+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table   | Create Table                                                                                                                                                                                                                                                                                     |
+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| student | CREATE TABLE `student` (
  `id` char(10) COLLATE utf8_unicode_ci DEFAULT NULL,
  `name` varchar(50) COLLATE utf8_unicode_ci DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `gender` varchar(10) COLLATE utf8_unicode_ci DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci |
+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
-- 打印表结构
mysql> desc student;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | char(10)    | YES  |     | NULL    |       |
| name   | varchar(50) | YES  |     | NULL    |       |
| age    | int(11)     | YES  |     | NULL    |       |
| gender | varchar(10) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+

-- 查看表中列信息
show columns from product; 

-- 删除表
mysql> drop table student;

-- 增加表列
alter table student add (
    qq varchar(15) ,
    email varchar(20)
);
-- 查看表结构
desc student;
-- 修改列类型 , 若表中存在数据，修改列类型会影响预原有数据
alter table student modify id varchar(10) ; 
-- 查看表结构
desc student;
-- 修改列名, change 原列名 新列名 新类型
alter table student change id uuid varchar(15) ; 
-- 查看表结构
desc student;
-- 删除列
alter  table student drop qq;
-- 查看表结构
desc student;
-- 修改表名称
alter  table student rename to stu;
-- 查看表结构
desc stu;
```

### 表相关操作（DML）

```mysql
-- insert into table 写法1
insert into stu(uuid , name , age , gender,email)
values ('20141102054','wanghuan',24,'F','1234@qq.com');

-- insert into table 写法2 ，插入部分列，没有插入的列，默认为null
insert into stu(uuid , name)	
values ('20141105592','范欣桐');

-- insert into table 写法3 , 不指定表中具体列，则插入所有列。值得顺序要和创建表时的顺序一致
insert into stu
values ('wanghuan','20141102054',24,'F','1234@qq.com');


-- update 
update stu t set age = 30 where t.`name` = 'wanghuan' ;
commit;

```

### 表相关操作（DCL）

```mysql
-- 创建用户，指定IP地址登录
create user zhangsan@localhost IDENTIFIED by '123456';

-- 创建用户，任意IP地址可以登录
create user lisi@'%' IDENTIFIED by '123456';

-- root用户授权 , all 所有权限，mydb1.* 代表该数据库下所有表、视图、存储过程等
GRANT all ON mydb1.* to lisi@'%'; 

-- root用户撤销授权
REVOKE delete on mydb1.* from lisi@'%';

mysql> delete from stu where name='wanghuan';
ERROR 1142 (42000): DELETE command denied to user 'lisi'@'localhost' for table 'stu'

-- 查看指定用户的权限
SHOW GRANTS FOR lisi@'%';
SHOW GRANTS FOR zhangsan@'localhost';

-- 删除用户
DROP USER lisi@'%';

[hadoop@test ~]$ mysql -ulisi -p123456    
Warning: Using a password on the command line interface can be insecure.
ERROR 1045 (28000): Access denied for user 'lisi'@'localhost' (using password: YES)
```



### 表相关操作（DQL）

```mysql
-- IFNULL(expr1,expr2) 用法
SELECT
	t.uuid,
	t.NAME,
	IFNULL( t.gender, 'Know' ) AS A,
	IFNULL( t.email, 'default@qq.com' ) B 
FROM
	stu t;

-- 字符串连接,字段拼接
SELECT
	CONCAT( t.uuid, t.NAME ) 
FROM
	stu t;
	
-- 模糊查询 ,_代表单个字符 ， % 任意多个字符匹配
SELECT
	*
FROM
	stu t 
  where t.name like '范%';
	
-- LIMIT , limit 计算公式（当前页-1）*每页查看数
SELECT
	*
FROM
	stu t  LIMIT 3,10 ;
```



### 备份与恢复

```mysql
-- CMD mysqldump 备份mydb1数据库内容（数据库本身内容不备份）
[hadoop@test etc]$ mysqldump -uroot -p123456 mydb1 > /home/a.sql

-- 恢复方式1  mysql
-- 删除数据库，使用备份a.sql文件恢复
mysql> drop database mydb1;

-- 查看是否删除成功
mysql> show databases;

-- 重新创建数据库
mysql> create database mydb1;
mysql> show databases;

-- 使用备份文件恢复数据库内容
[hadoop@test ~]$ mysql -uroot -p123456 mydb1 < /home/hadoop/a.sql 

-- 重新登录数据库，查看内容是否恢复
[hadoop@test ~]$ mysql  -uroot -p
mysql> show databases;

mysql> use mydb1;
mysql> show tables;
mysql> select * from stu;

-- 恢复方式2  source
mysql> create database mydb2;
mysql> show databases;
mysql> use mydb2;
mysql> source /home/hadoop/a.sql;
mysql> show tables;
mysql> select * from stu
```



### 主键

#### 主键约束

```mysql
-- 创建表并指定主键方式1
CREATE TABLE score (
     no int PRIMARY KEY ,
		 name VARCHAR(20)
);

-- 主键不能插入NULL
insert into score values(NULL,'wanghuan') ;

insert into score values(1,'wanghuan') ;

insert into score values(2,'fanfan') ;
-- > 1062 - Duplicate entry '2' for key 'PRIMARY' , 主键唯一性，不能插入重复值
insert into score values(2,'wanghuan') ;

SELECT * FROM score ;

-- 创建表并指定主键方式2
CREATE TABLE test (
     no int  ,
		 name VARCHAR(20) ,
		 PRIMARY KEY(no)
);

-- 主键不能插入NULL
insert into test values(NULL,'wanghuan') ;

insert into test values(1,'wanghuan') ;

insert into test values(2,'fanfan') ;
-- > 1062 - Duplicate entry '2' for key 'PRIMARY' , 主键唯一性，不能插入重复值
insert into test values(2,'wanghuan') ;

SELECT * FROM test ;

-- 修改表时指定主键
CREATE TABLE test1 (
     no int  ,
		 name VARCHAR(20)
);
ALTER TABLE test1 add PRIMARY KEY (no);
insert into test1 values(1,'wanghuan') ;

insert into test1 values(2,'fanfan') ;

SELECT * FROM test1 ;

-- 删除主键
ALTER TABLE test1 DROP PRIMARY KEY ;
insert into test1 values(3,'fanfan') ;
insert into test1 values(3,'fanfan') ;
SELECT * FROM test1 ;
```

#### 主键自增ID

```mysql
-- 主键设置自增ID
DROP TABLE score ;
CREATE TABLE score ( NO INT PRIMARY KEY auto_increment, NAME VARCHAR ( 20 ) );

-- 自增主键指定ID时，从指定数字开始自增
insert into score values(2054,'wanghuan') ;

insert into score values(1,'wanghuan') ;

-- 插入式使用NULL,则插入使用自增ID(从表中最大ID除开始增长)
insert into score values(NULL,'fanfan') ;
insert into score values(NULL,'wanghuan') ;

SELECT * FROM score ;

-- 表内容删除时，自增ID不会从头计数
DELETE FROM score ;

insert into score values(2054,'wanghuan') ;
insert into score values(null,'wanghuan') ;
SELECT * FROM score ;
```

### SQL

#### SQL执行顺序

```mysql
-- Mysql 执行顺序 
FROM > ON > JOIN > WHERE > GROUP BY > HAVING > SELECT > DISTINCT > ORDER BY > LIMIT 
```

#### JOIN连接方式

```mysql
-- mysql不支持全连接全连接，使用union连接左连接和右连接，得到全连接
SELECT s.student_name，t.teacher_name FROM student s LEFT JOIN teacher t ON s.teacher_id = t.id;
union
SELECT s.student_name，t.teacher_name FROM student s RIGHT JOIN teacher t ON s.teacher_id = t.id;
```



### **查看数据文件空间大小**

```mysql
-- 查看数据文件挂载空间使用率
[root@0daycrack mysql]# df -k
文件系统                   1K-块     已用     可用 已用% 挂载点
/dev/mapper/centos-root 49166336 31593092 17573244   65% /
devtmpfs                 3983224        0  3983224    0% /dev
tmpfs                    3995136        0  3995136    0% /dev/shm
tmpfs                    3995136    20076  3975060    1% /run
tmpfs                    3995136        0  3995136    0% /sys/fs/cgroup
/dev/sda1                1038336   193040   845296   19% /boot
tmpfs                     799028        0   799028    0% /run/user/0 

-- 查看表空间信息、表空间模式
mysql>  show variables like 'innodb_data_file_path%';
--------------
show variables like 'innodb_data_file_path%'
--------------

+-----------------------+------------------------+
| Variable_name         | Value                  |
+-----------------------+------------------------+
| innodb_data_file_path | ibdata1:12M:autoextend |
+-----------------------+------------------------+
1 row in set (0.02 sec)


mysql>  show variables like '%innodb_file_per_table%';
--------------
show variables like '%innodb_file_per_table%'
--------------

+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_file_per_table | ON    |
+-----------------------+-------+
1 row in set (0.00 sec)

# ON表示独立表空间、OFF表示共享表空间

-- 查看表空间数据文件的大小：
[root@0daycrack mysql]# ls -lh


'''如果想知道mysql 数据库中的每个表占用的空间、表记录的行数的话，可以打开mysql的information_schema数据库。在该库中有一个tables表，这个表主要字段分别是：
table_schema:数据库名
table_name：表名
engine:所使用的存储引擎
table_rows:记录数
data_length:数据大小
index_length:索引大小
'''
-- 查看数据库占用空间
SELECT 
    CONCAT(TRUNCATE(SUM(data_length) / 1024 / 1024,
                2),
            'MB') AS data_size,
    CONCAT(TRUNCATE(SUM(max_data_length) / 1024 / 1024,
                2),
            'MB') AS max_data_size,
    CONCAT(TRUNCATE(SUM(data_free) / 1024 / 1024,
                2),
            'MB') AS data_free,
    CONCAT(TRUNCATE(SUM(index_length) / 1024 / 1024,
                2),
            'MB') AS index_size
FROM
    information_schema.tables
WHERE
    TABLE_SCHEMA = 'zabbix';

-- 查询所有库所有表大小
mysql> select concat(round(sum(DATA_LENGTH/1024/1024),2), 'MB') as data from information_schema.TABLES;
--------------
select concat(round(sum(DATA_LENGTH/1024/1024),2), 'MB') as data frominformation_schema. TABLES
--------------

+-----------+
| data      |
+-----------+
| 12938MB |
+-----------+
1 row in set (0.12 sec)

-- 切换schema
mysql> use information_schema;

-- 查询指定库大小
mysql> select concat(round(sum(DATA_LENGTH)/1024/1024+sum(INDEX_LENGTH)/1024/1024),'M') from information_schema.tables where table_schema='zabbix';
--------------
select concat(round(sum(DATA_LENGTH)/1024/1024+sum(INDEX_LENGTH)/1024/1024),'M') from information_schema.tables where table_schema='zabbix'
--------------

+---------------------------------------------------------------------------+
| concat(round(sum(DATA_LENGTH)/1024/1024+sum(INDEX_LENGTH)/1024/1024),'M') |
+---------------------------------------------------------------------------+
| 12931M                                                                    |
+---------------------------------------------------------------------------+
1 row in set (0.07 sec)

-- 查询指定表数据、索引、剩余空间  Top 10
SELECT 
    table_schema,
    table_name AS 'Table Name',
    table_rows AS 'Number of Rows',
    CONCAT(ROUND(data_length / (1024 * 1024 * 1024), 6),
            ' G') AS 'Data Size',
    CONCAT(ROUND(index_length / (1024 * 1024 * 1024), 6),
            ' G') AS 'Index Size',
    CONCAT(ROUND(data_free / (1024 * 1024 * 1024), 6),
            'G') AS data_free,
    CONCAT(ROUND((data_length + index_length + data_free) / (1024 * 1024 * 1024),
                    6),
            ' G') AS 'Total'
FROM
    information_schema.TABLES
WHERE
    table_schema = 'zabbix'
ORDER BY 3 DESC
LIMIT 10;
```

