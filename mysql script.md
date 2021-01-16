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

```

### **配置文件**

```mysql
-- 默认配置
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
8 rows in set (0.00 sec)

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

```



### 表相关操作（DQL）

```mysql

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

