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



### 索引

#### 索引

```mysql
-- 查看某张表的索引
 show index from stu;  
 
 -- 创建表的索引
 create index idx_stu_01 on stu(uuid);
```

#### 索引创建情况

```mysql
-- 需要创建索引的情况
1. 主键自动建立唯一索引
2. 频繁作为查询条件的字段
3. 查询中与其他表关联的字段，外检关系需要建立索引
4. where 条件中用不到的字段不创建索引
5. 组合索引（复合索引），倾向于创建复合索引
6. 查询汇总的排序字段，若通过索引去访问将大大提高排序速度
7. 查询中统计或者分组字段

-- 不适合创建索引的情况
1. 频繁更新的字段不适合创建索引
2. 表结构数据量少的时候（< 500w-800w）
3. 区分度不是很大的字段，比如性别、国籍等（该列的不同值/所有记录行的比值越接近1 ，建索引的效果会越好）
```

### 执行计划

#### 构建数据源

```mysql
-- 构建数据源
-- 创建数据库：
CREATE DATABASE Company;
-- 创建employee表：
CREATE TABLE employee (
    employeeId INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    birth DATE,
    joblevel VARCHAR(10),
    salary DECIMAL(10 , 2 ),
    phone VARCHAR(11),
    departmentId INT NOT NULL
);
-- department表
CREATE TABLE department (
    departmentId INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
);
-- 设置外键
ALTER TABLE employee
ADD CONSTRAINT fk_departmentId
FOREIGN KEY (departmentId)
REFERENCES department(departmentId);

-- 插入数据
-- ----------------------------
-- Records of department
-- ----------------------------
INSERT INTO department VALUES ('1', '商务部');
INSERT INTO department VALUES ('2', '行政部');
INSERT INTO department VALUES ('3', '财务部');
INSERT INTO department VALUES ('4', '研发部');
-- ----------------------------
-- Records of employee
-- ----------------------------
INSERT INTO employee VALUES ('1001', '张强', '1986-02-03', '一级', '8000.00', '13585422655', '1');
INSERT INTO employee VALUES ('1003', '萌萌', '1990-04-19', '二级', '6000.00', '18548775264', '2');
INSERT INTO employee VALUES ('1004', '李小峰', '1973-07-20', '二级', '5700.00', '18625489512', '1');
INSERT INTO employee VALUES ('1006', '刘珊', '1976-06-28', '一级', '7500.00', '18524811174', '2');
INSERT INTO employee VALUES ('1007', '李梅', '1980-01-29', '二级', '5500.00', '13958621455', '3');
INSERT INTO employee VALUES ('1008', '张宝玉', '1982-09-23', '二级', '5600.00', '13715620210', '1');
INSERT INTO employee VALUES ('1009', '陈大壮', '1978-05-21', '一级', '7700.00', '15848562585', '4');
INSERT INTO employee VALUES ('1010', '张天琪', '1980-09-15', '二级', '5000.00', '13965815822', '4');
```

#### id

```mysql
-- 查询某条SQL的执行计划
mysql> explain select * from stu;
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
|  1 | SIMPLE      | stu   | ALL  | NULL          | NULL | NULL    | NULL |    9 | NULL  |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+

-- 执行计划中 id 列详解
-- select 查询的序列号，包含一组数字，表示查询中执行select字句或操作表的顺讯
1. id相同，执行顺序由上至下
2. id不同，如果有子查询，id值会递增，越内层的子查询id值越大，优先级越高，越先被执行
3. id有相同有不同，id值越大的越先执行，相同id值得从上至下执行
mysql> explain SELECT
    -> * 
    -> FROM
    -> employee a
    -> INNER JOIN ( SELECT DISTINCT b.departmentId , b.`name` FROM department b ) b ON a.departmentId = b.departmentId;
+----+-------------+------------+------+-----------------+-----------------+---------+----------------+------+-------+
| id | select_type | table      | type | possible_keys   | key             | key_len | ref            | rows | Extra |
+----+-------------+------------+------+-----------------+-----------------+---------+----------------+------+-------+
|  1 | PRIMARY     | <derived2> | ALL  | NULL            | NULL            | NULL    | NULL           |    4 | NULL  |
|  1 | PRIMARY     | a          | ref  | fk_departmentId | fk_departmentId | 4       | b.departmentId |    1 | NULL  |
|  2 | DERIVED     | b          | ALL  | NULL            | NULL            | NULL    | NULL           |    4 | NULL  |
+----+-------------+------------+------+-----------------+-----------------+---------+----------------+------+-------+
-- 根据上述原则，id=2 即子查询b会先执行，然后执行id=1 ， table = <derived2> （表示取id=2的子查询结构作为驱动表），最后执行id=1中的a表 

-- 执行计划结果行显示
mysql> explain SELECT      * FROM     employee a         INNER JOIN     (SELECT DISTINCT         b.departmentId, b.`name`     FROM         department b) b ON a.departmentId = b.departmentId\G
*************************** 1. row ***************************
           id: 1
  select_type: PRIMARY
        table: <derived2>
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 4
        Extra: NULL
*************************** 2. row ***************************
           id: 1
  select_type: PRIMARY
        table: a
         type: ref
possible_keys: idx_emp_01
          key: idx_emp_01
      key_len: 4
          ref: b.departmentId
         rows: 1
        Extra: NULL
*************************** 3. row ***************************
           id: 2
  select_type: DERIVED
        table: b
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 4
        Extra: NULL
```

#### select_type

```mysql

-- select_type 查询的类型，主要用于区别普通查询、联合查询、子查询等复杂查询的类型
1. SIMPLE  -- 简单的select查询，不包含子查询或者union
2. PRIMARY -- 查询中若包含任何复杂的子部分，最外层查询被标记为
3. SUBQUERY -- select 中或者where列表中包含子查询
4. DERIVED -- from 出现的子查询，会被标记为衍生表，结果放在临时表中。注意where条件后出现的子查询属于simple查询
5. UNION   -- 出现在union后的第一个select被标记为union ， 若union出现在from子查询中，外层select被标记为DERIVED
6. UNION RESULT -- 获取临时表中union结果

mysql> explain SELECT
    -> * 
    -> FROM
    -> employee a
    -> INNER JOIN ( SELECT a.departmentId FROM employee a UNION SELECT b.departmentId FROM department b ) b ON a.departmentId = b.departmentId;
+----+--------------+------------+-------+-----------------+-----------------+---------+------------------------+------+-----------------+
| id | select_type  | table      | type  | possible_keys   | key             | key_len | ref                    | rows | Extra           |
+----+--------------+------------+-------+-----------------+-----------------+---------+------------------------+------+-----------------+
|  1 | PRIMARY      | a          | ALL   | fk_departmentId | NULL            | NULL    | NULL                   |    8 | NULL            |
|  1 | PRIMARY      | <derived2> | ref   | <auto_key0>     | <auto_key0>     | 4       | Company.a.departmentId |    2 | Using index     |
|  2 | DERIVED      | a          | index | NULL            | fk_departmentId | 4       | NULL                   |    8 | Using index     |
|  3 | UNION        | b          | index | NULL            | PRIMARY         | 4       | NULL                   |    4 | Using index     |
| NULL | UNION RESULT | <union2,3> | ALL   | NULL            | NULL            | NULL    | NULL                   | NULL | Using temporary |
+----+--------------+------------+-------+-----------------+-----------------+---------+------------------------+------+-----------------+
```

#### type

```mysql

-- type 查询访问类型从最好到最差
system  > const > eq_ref > ref > range > index > all 
system (单表只有一行数据，等于MySQL系统表)
const 表示通过一次索引就查找到数据，const用于primary key 和unique 索引，因为通过主键或唯一索引只匹配一行数据，所以很快，如将主键置于where列表中，MySQL就能将该查询转换为一个常量
eq_ref 唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常用于主键或唯一索引扫描
ref 非唯一性索引扫描，返回匹配某个单独值得所有行（本质上是一种索引访问，会返回多个符合条件的行）
range 索引范围扫描，常用语between < > in 等
mysql> explain select * from employee where employeeId between 1001 and 1006  ;
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
| id | select_type | table    | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
|  1 | SIMPLE      | employee | range | PRIMARY       | PRIMARY | 4       | NULL |    4 | Using where |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+

mysql> explain select * from employee where employeeId in (1001 ,1006)  ;
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
| id | select_type | table    | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
|  1 | SIMPLE      | employee | range | PRIMARY       | PRIMARY | 4       | NULL |    2 | Using where |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
mysql> explain select * from employee where employeeId > 1001  ;
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
| id | select_type | table    | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
|  1 | SIMPLE      | employee | range | PRIMARY       | PRIMARY | 4       | NULL |    7 | Using where |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
1 row in set (0.00 sec)

mysql> explain select * from employee where employeeId < 1006  ;
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
| id | select_type | table    | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+
|  1 | SIMPLE      | employee | range | PRIMARY       | PRIMARY | 4       | NULL |    3 | Using where |
+----+-------------+----------+-------+---------------+---------+---------+------+------+-------------+

index： full index scan ，index与all的区别是index类型只会遍历索引树，索引文件比数据文件小。index和all都是读全表，但是index从索引中读取，all是从硬盘全扫描。
mysql> explain select employeeId from employee; 
+----+-------------+----------+-------+---------------+-----------------+---------+------+------+-------------+
| id | select_type | table    | type  | possible_keys | key             | key_len | ref  | rows | Extra       |
+----+-------------+----------+-------+---------------+-----------------+---------+------+------+-------------+
|  1 | SIMPLE      | employee | index | NULL          | fk_departmentId | 4       | NULL |    8 | Using index |
+----+-------------+----------+-------+---------------+-----------------+---------+------+------+-------------+

-- all full table scan
全表扫描，遍历数据文件每一行


```

**总结：一般来说，查询至少得达到range级别，最好能达到ref**

#### possible_keys

```mysql
-- 查询涉及到字段上存在索引，推测出本次查询可能用到的索引（一个或多个），列出所有可能索引，但不一定是实际使用
```

#### key

```mysql
-- 实际使用的索引，如果为null，则该索引仅出现在key列表中
-- 查询中如果使用了覆盖索引，则该索引出现在key列表中

mysql> explain select * from employee where departmentId = 1 and employeeId = 1004;
+----+-------------+----------+-------+--------------------+---------+---------+-------+------+-------+
| id | select_type | table    | type  | possible_keys      | key     | key_len | ref   | rows | Extra |
+----+-------------+----------+-------+--------------------+---------+---------+-------+------+-------+
|  1 | SIMPLE      | employee | const | PRIMARY,idx_emp_01 | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+----------+-------+--------------------+---------+---------+-------+------+-------+

-- 覆盖索引指的是select后面出现的字段和创建的复合索引顺序和个数一致，此时会走full index scan。
```

#### key_len

```mysql
-- 表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度，在不损失查询精确度的前提下，长度越短越好
-- key_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检索出的。

mysql> explain select * from employee where departmentId = 1 and employeeId = 1004;
+----+-------------+----------+-------+--------------------+---------+---------+-------+------+-------+
| id | select_type | table    | type  | possible_keys      | key     | key_len | ref   | rows | Extra |
+----+-------------+----------+-------+--------------------+---------+---------+-------+------+-------+
|  1 | SIMPLE      | employee | const | PRIMARY,idx_emp_01 | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+----------+-------+--------------------+---------+---------+-------+------+-------+
```

#### ref

```mysql
-- 显示索引的哪一列被使用了，如果可能得话，是一个常数，哪些列或常量被用于查找索引列的值。
mysql> explain SELECT
    ->      * 
    ->     FROM
    ->      employee a
    ->     INNER JOIN ( SELECT DISTINCT b.departmentId , b.`name` FROM department b ) b ON a.departmentId = b.departmentId;
+----+-------------+------------+------+---------------+------------+---------+----------------+------+-------+
| id | select_type | table      | type | possible_keys | key        | key_len | ref            | rows | Extra |
+----+-------------+------------+------+---------------+------------+---------+----------------+------+-------+
|  1 | PRIMARY     | <derived2> | ALL  | NULL          | NULL       | NULL    | NULL           |    4 | NULL  |
|  1 | PRIMARY     | a          | ref  | idx_emp_01    | idx_emp_01 | 4       | b.departmentId |    1 | NULL  |
|  2 | DERIVED     | b          | ALL  | NULL          | NULL       | NULL    | NULL           |    4 | NULL  |
+----+-------------+------------+------+---------------+------------+---------+----------------+------+-------+
```

#### rows

```mysql
-- 根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数
mysql> explain SELECT
    ->      * 
    ->     FROM
    ->      employee a
    ->     INNER JOIN ( SELECT DISTINCT b.departmentId , b.`name` FROM department b ) b ON a.departmentId = b.departmentId;
+----+-------------+------------+------+---------------+------------+---------+----------------+------+-------+
| id | select_type | table      | type | possible_keys | key        | key_len | ref            | rows | Extra |
+----+-------------+------------+------+---------------+------------+---------+----------------+------+-------+
|  1 | PRIMARY     | <derived2> | ALL  | NULL          | NULL       | NULL    | NULL           |    4 | NULL  |
|  1 | PRIMARY     | a          | ref  | idx_emp_01    | idx_emp_01 | 4       | b.departmentId |    1 | NULL  |
|  2 | DERIVED     | b          | ALL  | NULL          | NULL       | NULL    | NULL           |    4 | NULL  |
+----+-------------+------------+------+---------------+------------+---------+----------------+------+-------+
```



#### Extra

**不在执行计划正常分析列中显示的信息，同时也是非常重要的额外信息**

##### Using filesort

```mysql
-- 额外补充信息
-- 强制走某一复合索引idx_emp_dep_name(employeeId ,departmentId,name)
mysql> explain SELECT * FROM employee T force index(idx_emp_dep_name)  where T.employeeId = 1008
    -> order by name desc ;
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-----------------------------+
| id | select_type | table | type | possible_keys    | key              | key_len | ref   | rows | Extra                       |
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-----------------------------+
|  1 | SIMPLE      | T     | ref  | idx_emp_dep_name | idx_emp_dep_name | 4       | const |    1 | Using where; Using filesort |
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-----------------------------+

-- MySQL中无法利用内部索引顺序完成的排序操作为文件排序
mysql> explain SELECT * FROM employee T force index(idx_emp_dep_name)  
    -> where T.employeeId = 1008
    -> order by departmentId , name  ;
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-------------+
| id | select_type | table | type | possible_keys    | key              | key_len | ref   | rows | Extra       |
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-------------+
|  1 | SIMPLE      | T     | ref  | idx_emp_dep_name | idx_emp_dep_name | 4       | const |    1 | Using where |
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-------------+
```



##### Using temporary

```mysql
-- 使用临时表进行中间结果的保存，常见MySQL中对查询结果进行排序使用的临时表，用于order by 和 group by操作
-- group  by 后的字段顺序和索引字段顺序不一致，导致Using temporary
mysql> explain SELECT * FROM employee T force index(idx_emp_dep_name)  
    -> where T.employeeId in (1008,1009,1006)
    -> group  by departmentId   ;
+----+-------------+-------+-------+-----------------------------+------------------+---------+------+------+--------------------------------------------------------+
| id | select_type | table | type  | possible_keys               | key              | key_len | ref  | rows | Extra                                                  |
+----+-------------+-------+-------+-----------------------------+------------------+---------+------+------+--------------------------------------------------------+
|  1 | SIMPLE      | T     | range | idx_emp_01,idx_emp_dep_name | idx_emp_dep_name | 4       | NULL |    3 | Using index condition; Using temporary; Using filesort |
+----+-------------+-------+-------+-----------------------------+------------------+---------+------+------+--------------------------------------------------------+

-- group  by employeeId , departmentId 顺序和索引顺序一致，消除Using temporary
mysql> explain SELECT * FROM employee T force index(idx_emp_dep_name)  
    -> where T.employeeId in (1008,1009,1006)
    -> group  by employeeId , departmentId   ;
+----+-------------+-------+-------+-----------------------------+------------------+---------+------+------+-----------------------+
| id | select_type | table | type  | possible_keys               | key              | key_len | ref  | rows | Extra                 |
+----+-------------+-------+-------+-----------------------------+------------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | T     | range | idx_emp_01,idx_emp_dep_name | idx_emp_dep_name | 4       | NULL |    3 | Using index condition |
+----+-------------+-------+-------+-----------------------------+------------------+---------+------+------+-----------------------+
```



##### Using index

```mysql
-- 表示相应的select中使用了覆盖索引，避免访问表的数据行
-- 如果同时出现where ，表明索引被用来执行索引键的查找
-- 如果没有同时出现where，表明索引用来读取数据而非执行查找动作。
mysql> explain SELECT employeeId,departmentId,name  FROM employee T force index(idx_emp_dep_name)  
    -> where T.employeeId = 1008   ;
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-------------+
| id | select_type | table | type | possible_keys    | key              | key_len | ref   | rows | Extra       |
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-------------+
|  1 | SIMPLE      | T     | ref  | idx_emp_dep_name | idx_emp_dep_name | 4       | const |    1 | Using index |
+----+-------------+-------+------+------------------+------------------+---------+-------+------+-------------+

```

##### Using where

```mysql
-- 表示是否使用了where过滤
```

##### Using join buffer

```mysql
-- 表示是否使用了连接缓存
```

##### Using Impossible WHERE

```mysql
-- where 过滤条件的值总是false，无法获取任何结果

mysql> explain SELECT employeeId,departmentId,name  FROM employee T
    -> where T.employeeId = 1001 
    -> and T.employeeId = 1002;
+----+-------------+-------+------+---------------+------+---------+------+------+------------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra            |
+----+-------------+-------+------+---------------+------+---------+------+------+------------------+
|  1 | SIMPLE      | NULL  | NULL | NULL          | NULL | NULL    | NULL | NULL | Impossible WHERE |
+----+-------------+-------+------+---------------+------+---------+------+------+------------------+
```

#### 索引优化

```mysql
1. 左右连接，多表查询中，索引字段一般建立在与连接方式相反的表中，因为左右连接的特性决定，左表或者右表一定会被都包含
2. 索引建立在经常用户查询的字段中
3. join中使用小表驱动大表（小的结果集驱动大的结果集），减少Nested Loop的总次数
   确保Nested Loop内层循环上的join字段一定要加索引
   当无法确保被驱动表的join条件被索引且内存资源充足的前提下，可以尽量把join buffer的内存大小调整大一点
```

#### 索引失效

1.   尽量保证等值匹配或者全值匹配

   ```mysql
   mysql> SELECT * FROM Company.employee where employeeId = 1006;
   +------------+--------+------------+----------+---------+-------------+--------------+
   | employeeId | name   | birth      | joblevel | salary  | phone       | departmentId |
   +------------+--------+------------+----------+---------+-------------+--------------+
   |       1006 | 刘珊   | 1976-06-28 | 一级     | 7500.00 | 18524811174 |            2 |
   +------------+--------+------------+----------+---------+-------------+--------------+
   ```

2. 最左前缀原则

   ```mysql
   -- idx_emp_dep_name  三列的复合索引
   mysql> explain SELECT 
       ->     *
       -> FROM
       ->     Company.employee 
       -> where employeeId = 1008 
       -> and departmentId = 1
       -> and name = '张宝玉' ;
   +----+-------------+----------+-------+-------------------------------------+---------+---------+-------+------+-------+
   | id | select_type | table    | type  | possible_keys                       | key     | key_len | ref   | rows | Extra |
   +----+-------------+----------+-------+-------------------------------------+---------+---------+-------+------+-------+
   |  1 | SIMPLE      | employee | const | PRIMARY,idx_emp_01,idx_emp_dep_name | PRIMARY | 4       | const |    1 | NULL  |
   +----+-------------+----------+-------+-------------------------------------+---------+---------+-------+------+-------+
   
   -- 如果复合索引中有多列，需要遵守最左前缀法，查询从索引的最左前列开始并且不跳过索引中的列
   ```

3. 索引列上不做任何操作，包括函数、计算、类型转换等操作，会导致索引失效转向全表扫描

   ```mysql
   -- 索引列上使用函数
   mysql> explain SELECT 
       ->     *
       ->      FROM
       ->          Company.employee 
       ->      where left(employeeId,4) = 1008 ;
   +----+-------------+----------+------+---------------+------+---------+------+------+-------------+
   | id | select_type | table    | type | possible_keys | key  | key_len | ref  | rows | Extra       |
   +----+-------------+----------+------+---------------+------+---------+------+------+-------------+
   |  1 | SIMPLE      | employee | ALL  | NULL          | NULL | NULL    | NULL |    8 | Using where |
   +----+-------------+----------+------+---------------+------+---------+------+------+-------------+
   ```

4. 尽量查询覆盖索引中的所有列，避免select *

   ```mysql
   -- 索引列和查询列尽量一致，性能最好
   ```

5. 使用<> !=等符号

   ```mysql
   -- 使用范围查询(!=,<=>,in)会导致右边列失效。因为二叉树的查找是 = 查找，若是一个范围的话无法继续下探。
   
       -- 最左列用范围，该列也不会使用索引，全部索引列失效
       -- 其余列用范围，当前列仍会使用索引，但右边索引列失效
   SELECT * FROM user_innodb where name='张三' and age > 22；
   ```

6. is null  ， is not null 也会无法使用索引

   ```mysql
   SELECT * FROM user_innodb where name is null；
   ```

7. like 已%通配符开头的，索引会变成全表扫描 （%mysql%）

   ```mysql
   -- like %写在右边
   -- 若要使用两边都加%abc% ，需要使用覆盖索引解决 like %abc% ，索引失效的问题
   -- 覆盖索引时（即select 后面字段都添加到索引里） ， 使用like '%xxx%' 也是会走索引的，如果不是覆盖索引时，建议使用like 'xxx%'
   -- ike以通配符开头（’%abc…’），mysql索引失效会变成全表扫描操作
   mysql> explain select c1,c2,c3,c4,c5 from test where c3 like '%a3%';
   +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
   | id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
   +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
   |  1 | SIMPLE      | test  | ALL  | NULL          | NULL | NULL    | NULL |    5 | Using where |
   +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
   
   -- 方案一：like (‘abc%’)
   SELECT * FROM user_innodb where name like '三%'；
   
   -- 方案二：覆盖索引
   -- select 后边的字段都出现在复合索引中
   mysql> explain select c1,c2,c3 from test where c3 like '%a3%';
   +----+-------------+-------+-------+---------------+----------------+---------+------+------+--------------------------+
   | id | select_type | table | type  | possible_keys | key            | key_len | ref  | rows | Extra                    |
   +----+-------------+-------+-------+---------------+----------------+---------+------+------+--------------------------+
   |  1 | SIMPLE      | test  | index | NULL          | idx_test_c1234 | 124     | NULL |    5 | Using where; Using index |
   +----+-------------+-------+-------+---------------+----------------+---------+------+------+--------------------------+
   ```

   

8. 字符串不加单引号索引失效

   ```mysql
   -- 字符串不加’ '索引失效。因为会出现出现隐式转换，相当于给索引列做了操作。
   SELECT * FROM user_innodb where name = 007；-- "007"从字符串变成了数字007
   ```

   

9. 少用or ， 用它来连接时索引会失效

   ```mysql
   -- 使用or索引失效
   mysql> explain SELECT 
       ->     *
       ->      FROM
       ->          Company.employee 
       ->      where left(employeeId,4) = 1008 ;
   +----+-------------+----------+------+---------------+------+---------+------+------+-------------+
   | id | select_type | table    | type | possible_keys | key  | key_len | ref  | rows | Extra       |
   +----+-------------+----------+------+---------------+------+---------+------+------+-------------+
   |  1 | SIMPLE      | employee | ALL  | NULL          | NULL | NULL    | NULL |    8 | Using where |
   +----+-------------+----------+------+---------------+------+---------+------+------+-------------+
   1 row in set (0.00 sec)
   
   mysql> explain SELECT * FROM mydb2.stu 
       -> where mydb2.stu.uuid = '20141102054' or mydb2.stu.uuid = 'wanghuan'
       -> ;
   +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
   | id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
   +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
   |  1 | SIMPLE      | stu   | ALL  | idx_01        | NULL | NULL    | NULL |    9 | Using where |
   +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
   1 row in set (0.00 sec)
   
   -- 把or 使用uinon 改写
   mysql> explain
       -> SELECT 
       ->     *
       -> FROM
       ->     mydb2.stu
       -> WHERE
       ->     mydb2.stu.uuid = '20141102054' 
       -> UNION SELECT 
       ->     *
       -> FROM
       ->     mydb2.stu
       -> WHERE
       ->     mydb2.stu.uuid = 'wanghuan';
   +----+--------------+------------+------+---------------+--------+---------+-------+------+-----------------------+
   | id | select_type  | table      | type | possible_keys | key    | key_len | ref   | rows | Extra                 |
   +----+--------------+------------+------+---------------+--------+---------+-------+------+-----------------------+
   |  1 | PRIMARY      | stu        | ref  | idx_01        | idx_01 | 48      | const |    3 | Using index condition |
   |  2 | UNION        | stu        | ref  | idx_01        | idx_01 | 48      | const |    1 | Using index condition |
   | NULL | UNION RESULT | <union1,2> | ALL  | NULL          | NULL   | NULL    | NULL  | NULL | Using temporary       |
   +----+--------------+------------+------+---------------+--------+---------+-------+------+-----------------------+
   ```

   

#### 索引优化案例

##### 数据源

```mysql
create table test(
  id int primary key not null auto_increment ,
  c1 char(10),
  c2 char(10),
  c3 char(10),
  c4 char(10),
  c5 char(10)
);

insert into test(c1,c2,c3,c4,c5) values('a1','a2','a3','a4','a5');
insert into test(c1,c2,c3,c4,c5) values('b1','b2','b3','b4','b5');
insert into test(c1,c2,c3,c4,c5) values('c1','c2','c3','c4','c5');
insert into test(c1,c2,c3,c4,c5) values('d1','d2','d3','d4','d5');
insert into test(c1,c2,c3,c4,c5) values('e1','e2','e3','e4','e5');


select * from test ;

-- 兴建索引
create index idx_test_c1234 on test(c1,c2,c3,c4);

show index from test;
```

##### 索引分析

```mysql
-- 走索引
explain select * from test where c1 = 'a1' ;

explain select * from test where c1 = 'a1' and c2='a2' ;

explain select * from test where c1 = 'a1' and c2='a2'  and c3='a3';

explain select * from test where c1 = 'a1' and c2='a2' and c3='a3' and c4='a4';

explain select * from test where c1 = 'a1' and c2='a2' and c3='a3' and c4='a4' and c5='a5';

-- 不使用索引
explain select * from test where  c2='a2' ;

-- 索引范围扫描 ， c3使用了范围比较，导致之后的索引c4不能使用
mysql> explain select * from test where c1 = 'a1' and c2='a2' and c3 > 'a3' and c4='a4';
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
| id | select_type | table | type  | possible_keys  | key            | key_len | ref  | rows | Extra                 |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | test  | range | idx_test_c1234 | idx_test_c1234 | 93      | NULL |    1 | Using index condition |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+

-- 索引范围扫描 ， c4使用了范围比较 ， 索引都使用到了
mysql> explain select * from test where c1 = 'a1' and c2='a2' and c4 > 'a4' and c3='a3';
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
| id | select_type | table | type  | possible_keys  | key            | key_len | ref  | rows | Extra                 |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | test  | range | idx_test_c1234 | idx_test_c1234 | 124     | NULL |    1 | Using index condition |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+

-- c3使用索引进行了排序，不是查找
mysql>  explain select * from test where c1 = 'a1' and c2='a2' and c4 = 'a4' order by c3;
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+------------------------------------+
| id | select_type | table | type | possible_keys  | key            | key_len | ref         | rows | Extra                              |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+------------------------------------+
|  1 | SIMPLE      | test  | ref  | idx_test_c1234 | idx_test_c1234 | 62      | const,const |    1 | Using index condition; Using where |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+------------------------------------+

-- 使用到了外部排序
mysql>  explain select * from test where c1 = 'a1' and c2='a2' order by c4;
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+----------------------------------------------------+
| id | select_type | table | type | possible_keys  | key            | key_len | ref         | rows | Extra                                              |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+----------------------------------------------------+
|  1 | SIMPLE      | test  | ref  | idx_test_c1234 | idx_test_c1234 | 62      | const,const |    1 | Using index condition; Using where; Using filesort |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+----------------------------------------------------+

-- order by顺序和索引顺序不一致，使用到外部文件排序
mysql> explain select * from test where c1 = 'a1' and c5='a5' order by c3,c2;
+----+-------------+-------+------+----------------+----------------+---------+-------+------+----------------------------------------------------+
| id | select_type | table | type | possible_keys  | key            | key_len | ref   | rows | Extra                                              |
+----+-------------+-------+------+----------------+----------------+---------+-------+------+----------------------------------------------------+
|  1 | SIMPLE      | test  | ref  | idx_test_c1234 | idx_test_c1234 | 31      | const |    1 | Using index condition; Using where; Using filesort |
+----+-------------+-------+------+----------------+----------------+---------+-------+------+----------------------------------------------------+

-- order by顺序和索引顺序不一致，但没有使用到外部文件排序（原因c2出现在索引查找列中，是一个常量了，排序时候某一列为定值不起作用）
-- SQL 类似于explain select * from test where c1 = 'a1' and c2='a2' and c5='a5' order by c3；
mysql> explain select * from test where c1 = 'a1' and c2='a2' and c5='a5' order by c3,c2;
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+----------------------------------------------------+
| id | select_type | table | type | possible_keys  | key            | key_len | ref         | rows | Extra                                              |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+----------------------------------------------------+
|  1 | SIMPLE      | test  | ref  | idx_test_c1234 | idx_test_c1234 | 62      | const,const |    1 | Using index condition; Using where; Using filesort |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+----------------------------------------------------+

--  group by 使用临时表进行排序
-- explain select * from test where c1 = 'a1' and c5='a5' group by c2,c3（索引顺序和覆盖索引顺序一致）; 则不会产生Using temporary; Using filesort 
mysql> explain select * from test where c1 = 'a1' and c5='a5' group by c3,c2;
+----+-------------+-------+------+----------------+----------------+---------+-------+------+---------------------------------------------------------------------+
| id | select_type | table | type | possible_keys  | key            | key_len | ref   | rows | Extra                                                               |
+----+-------------+-------+------+----------------+----------------+---------+-------+------+---------------------------------------------------------------------+
|  1 | SIMPLE      | test  | ref  | idx_test_c1234 | idx_test_c1234 | 31      | const |    1 | Using index condition; Using where; Using temporary; Using filesort |
+----+-------------+-------+------+----------------+----------------+---------+-------+------+---------------------------------------------------------------
```

##### like

```mysql
-- 使用 > ，会屏蔽后面索引的使用
-- 索引范围扫描 ， c3使用了范围比较，导致之后的索引c4不能使用
mysql> explain select * from test where c1 = 'a1' and c2='a2' and c3 > 'a3' and c4='a4';
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
| id | select_type | table | type  | possible_keys  | key            | key_len | ref  | rows | Extra                 |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | test  | range | idx_test_c1234 | idx_test_c1234 | 93      | NULL |    1 | Using index condition |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+

--  like 'a3%' ， % 在最右边会使用索引范围扫描，不会屏蔽后面索引使用
mysql> explain select * from test where c1 = 'a1' and c2='a2' and c3 like 'a3%' and c4='a4';
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
| id | select_type | table | type  | possible_keys  | key            | key_len | ref  | rows | Extra                 |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | test  | range | idx_test_c1234 | idx_test_c1234 | 124     | NULL |    1 | Using index condition |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+

-- like '%a3'  ， %在左边，只会走c1、c2索引，c3 c4索引不会使用
mysql>  explain select * from test where c1 = 'a1' and c2='a2' and c3 like '%a3' and c4='a4'; 
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+-----------------------+
| id | select_type | table | type | possible_keys  | key            | key_len | ref         | rows | Extra                 |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+-----------------------+
|  1 | SIMPLE      | test  | ref  | idx_test_c1234 | idx_test_c1234 | 62      | const,const |    1 | Using index condition |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+-----------------------+
1 row in set (0.00 sec)

-- like '%a3%' ， like '%a3'  ， %在左边右边，只会走c1、c2索引，c3 c4索引不会使用
mysql>  explain select * from test where c1 = 'a1' and c2='a2' and c3 like '%a3%' and c4='a4'; 
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+-----------------------+
| id | select_type | table | type | possible_keys  | key            | key_len | ref         | rows | Extra                 |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+-----------------------+
|  1 | SIMPLE      | test  | ref  | idx_test_c1234 | idx_test_c1234 | 62      | const,const |    1 | Using index condition |
+----+-------------+-------+------+----------------+----------------+---------+-------------+------+-----------------------+
1 row in set (0.00 sec)

-- like 'a%a3%' ， % 在右边不是开头位置，会使用索引范围扫描，不会屏蔽后面索引使用
mysql>  explain select * from test where c1 = 'a1' and c2='a2' and c3 like 'a%a3%' and c4='a4';
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
| id | select_type | table | type  | possible_keys  | key            | key_len | ref  | rows | Extra                 |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | test  | range | idx_test_c1234 | idx_test_c1234 | 124     | NULL |    1 | Using index condition |
+----+-------------+-------+-------+----------------+----------------+---------+------+------+-----------------------+
```

#### 小表驱动大表

```mysql
-- 小表驱动大表，小的数据集驱动大的数据集，类似于Nested Loop
-- department and employee  ID列都建立了索引
-- 当department表的数据集远远小于employee表时，使用IN的效率会优于Exists
explain SELECT 
    *
FROM
    employee
WHERE
    departmentId IN (SELECT 
            departmentId
        FROM
            department
        WHERE
            departmentId <= 1);
+----+-------------+------------+-------+---------------+------------+---------+---------------------------------+------+--------------------------+
| id | select_type | table      | type  | possible_keys | key        | key_len | ref                             | rows | Extra                    |
+----+-------------+------------+-------+---------------+------------+---------+---------------------------------+------+-------
|  1 | SIMPLE      | department | index | PRIMARY       | PRIMARY    | 4       | NULL                            |    4 | Using where; Using index |
|  1 | SIMPLE      | employee   | ref   | idx_emp_01    | idx_emp_01 | 4       | Company.department.departmentId |    1 | NULL                     |
+----+-------------+------------+-------+---------------+------------+---------+---------------------------------+------+-------
-- 类似于for循环
for SELECT departmentId FROM department
   for  SELECT * FROM employee where employee.departmentId = department.departmentId

-- 当department表的数据集远远大于employee表时，使用Exists的效率会优于 IN
explain SELECT 
    *
FROM
    employee
WHERE
    exists  (SELECT 
            departmentId
        FROM
            department
        WHERE
            employee.departmentId = department.departmentId);
+----+--------------------+------------+--------+---------------+---------+---------+-------------------------------+------+-------------+
| id | select_type        | table      | type   | possible_keys | key     | key_len | ref                           | rows | Extra       |
+----+--------------------+------------+--------+---------------+---------+---------+-------------------------------+------+----
|  1 | PRIMARY            | employee   | ALL    | NULL          | NULL    | NULL    | NULL                          |    8 | Using where |
|  2 | DEPENDENT SUBQUERY | department | eq_ref | PRIMARY       | PRIMARY | 4       | Company.employee.departmentId |    1 | Using index |
+----+--------------------+------------+--------+---------------+---------+---------+-------------------------------+------+---
-- 类似于for循环
for SELECT * FROM employee
   for  SELECT * FROM department where employee.departmentId = department.departmentId
```

#### order by 

```mysql
-- mysql 支持两种方式排序， index和filesort
-- index利用了扫描索引之后的顺序进行排序效率高，filesort方式效率低

--order by 后面的列满足索引最左前缀
explain SELECT 
   *
FROM
    employee
    order by employeeId;
    
-- where 与order by字句条件组合满足索引最左前列    
explain SELECT 
   *
FROM
    employee 
    where employeeId = 1001
    order by departmentId;

-- filesort 参数调优
-- order by 算法 
-- 双路排序
/*
   第一次取出order by的列值和rowid， 对排序字段进行排序后
   按照排序后的rowid再返回磁盘拿所有select后的值，至少发生两次磁盘扫描
   总结：从磁盘读取排序字段，在buffer中排序，再从磁盘取其他字段
*/
-- 单路排序
/*
   第一次从磁盘取出所有查询用到的列，按照order by 列在buffer中进行排序，然后扫描排序后的列表进行输出
   将至少两次从磁盘的IO降低为至少一次，提升IO效率，把随机IO转换为顺序IO，但是缺点是会使用更多的sort buffer空间（因为它需要在buffer中对所有的结果列进行保存排序）
*/

-- 单路排序算法整体由于双路，但是需要配合调整数据库参数进行优化
1. innodb_sort_buffer_size参数 ， 可能会出现取出数据的大小超过sort buffer容量，会导致每次只能取sort buffer容量大小的数据，进行排序后将temp结果输出到磁盘临时文件保存，继续读取下一批sort buffer容量大小的数据，如此反复直到所有的数据经过sort buffer排完序后，从磁盘读取多个temp文件进行多路归并输出结果。
整体提高innodb_sort_buffer_size值会提升效率，但是要评估系统的能力去调整这个参数，因为这个值是针对每个线程去分配的
2. max_length_for_sort_data参数 ， 
当select后的字段大小总和大于max_length_for_sort_data参数，而且排序字段不是text或者blob类型时，会使用单路排序算法，否则会使用多路算法。
提高这个参数会增加使用单路排序算法的概率，但是如果设置过高，数据总容量超过innodb_sort_buffer_size的概率就会增大，明显的表现就是磁盘IO很高，但是CPU IDLE率很高。

-- 如何查看这两个参数的值
mysql> show variables like '%sort%';
+--------------------------------+---------------------+
| Variable_name                  | Value               |
+--------------------------------+---------------------+
| innodb_disable_sort_file_cache | OFF                 |
| innodb_ft_sort_pll_degree      | 2                   |
| innodb_sort_buffer_size        | 1048576             |
| max_length_for_sort_data       | 1024                |
| max_sort_length                | 1024                |
| myisam_max_sort_file_size      | 9223372036853727232 |
| myisam_sort_buffer_size        | 8388608             |
| sort_buffer_size               | 262144              |
+--------------------------------+---------------------+

总结：
-- mysql两种排序方式：文件排序和扫描有序索引排序
-- mysql能为排序与查询使用相同的索引
复合索引为(a,b,c)

-- order by 能使用索引最左前缀情况，不产生filesort
order by a 
order by a,b
order by a,b,c
order by a DESC , b DESC , c DESC
explain SELECT 
   employeeId , departmentId , name 
FROM
    employee 
    order by employeeId desc, departmentId desc , name desc;

-- where 使用索引最左前缀定义为常量，则order by能使用索引
where a = const order by b,c
where a = const and  b = const order by c
where a = const and  b > const order by b,c

-- 不能使用索引排序的情况
order by a asc , b DESC , c DESC ， 排序升降序不一致
where d = const order by b,c , 丢失索引列a，不满足最左前缀匹配
where a = const order by c  ， 丢失b索引
where a = const order by a,d ， d不是索引的一部分
where a in(....) order by b,c   对于排序，多个相等条件也是范围查询
```

#### group by 

```mysql
-- group by 是先排序后分组，遵照索引的最左前缀匹配
-- where 高于 having 	，能优先where优化的条件，就不要置于having中
```



#### 慢查询日志

```mysql
-- 查询慢查询日志是否开启（默认关闭，因为日志功能会影响性能）
mysql> show variables like '%slow_query_log%';
+---------------------+------------------------------+
| Variable_name       | Value                        |
+---------------------+------------------------------+
| slow_query_log      | OFF                          |
| slow_query_log_file | /var/lib/mysql/test-slow.log |
+---------------------+------------------------------+

-- 如何开启慢查询日志功能
-- mysql重启该参数会失效，退出shell重进入不会失效
mysql> set global slow_query_log=1;
Query OK, 0 rows affected (2.20 sec)

-- 永久生效，需要设置my.cnf配置文件
[mysqld]下设置
slow_query_log=1
slow_query_log_file=/var/lib/mysql/test-slow.log

mysql> show variables like '%slow_query_log%';
+---------------------+------------------------------+
| Variable_name       | Value                        |
+---------------------+------------------------------+
| slow_query_log      | ON                           |
| slow_query_log_file | /var/lib/mysql/test-slow.log |
+---------------------+------------------------------+

-- 如何查询慢查询日志设置阈值 ， 默认查询超时的阈值是10s ， 系统判断是>10 ， 不是大等于
mysql> show variables like '%long_query%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+

-- 修改慢查询判断时间
mysql> set global long_query_time=3;
-- 直接修改后看不到变化，需要重新进入shell
mysql> show variables like '%long_query%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)

-- 或者使用如下命令
mysql> show global variables like '%long_query%';
+-----------------+----------+
| Variable_name   | Value    |
+-----------------+----------+
| long_query_time | 3.000000 |
+-----------------+----------+

-- select  sleep
mysql> select sleep(4);
+----------+
| sleep(4) |
+----------+
|        0 |
+----------+

-bash-4.1$ more test-slow.log 
/usr/sbin/mysqld, Version: 5.6.24 (MySQL Community Server (GPL)). started with:
Tcp port: 3306  Unix socket: /var/lib/mysql/mysql.sock
Time                 Id Command    Argument
# Time: 210128  7:31:50
# User@Host: root[root] @ localhost []  Id:    87
# Query_time: 4.008059  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 0
SET timestamp=1611790310;
select sleep(4);
```



##### mysqldumpslow

```mysql
-- mysqldumpslow经常使用的参数：
/*
-s，是order的顺序
----- al 平均锁定时间
-----ar 平均返回记录时间
-----at 平均查询时间（默认）
-----c 计数
-----l 锁定时间
-----r 返回记录
-----t 查询时间
-t，是top n的意思，即为返回前面多少条的数据
-g，后边可以写一个正则匹配模式，大小写不敏感的
*/
-- 对慢查询日志的统计查询
```

##### show profile

```mysql
-- mysql提供可以用来分析当前会话中语句执行的资源消耗情况，用于SQL调优测量
-- 该参数默认数据关闭状态 
-- 查询该参数是否开启
-- 只记录最近的15次结果
mysql>  show VARIABLES like 'profiling';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| profiling     | OFF   |
+---------------+-------+

-- 关闭参数
mysql> set profiling = OFF;
-- 打开参数
mysql> set profiling = ON;

-- 查看结果
mysql> show profiles;
+----------+------------+---------------------------------+
| Query_ID | Duration   | Query                           |
+----------+------------+---------------------------------+
|        1 | 0.00330825 | show VARIABLES like 'profiling' |
+----------+------------+---------------------------------+

-- 执行SQL
mysql> show profiles;
+----------+------------+---------------------------------+
| Query_ID | Duration   | Query                           |
+----------+------------+---------------------------------+
|        1 | 0.00278475 | show variables like 'profiling' |
|        2 | 0.00039525 | select * from dept              |
+----------+------------+---------------------------------+
2 rows in set (0.13 sec)

mysql> select count(*) from emp;
+----------+
| count(*) |
+----------+
|   500000 |
+----------+
1 row in set (0.18 sec)

-- 查看profiles
mysql> show profiles;
+----------+------------+---------------------------------+
| Query_ID | Duration   | Query                           |
+----------+------------+---------------------------------+
|        1 | 0.00593550 | show VARIABLES like 'profiling' |
|        2 | 0.03437150 | select count(*) from emp        |
|        3 | 0.21396425 | select * from emp limit 1000    |
+----------+------------+---------------------------------+

-- 如何查询指定Query_ID 的sql具体执行步骤
-- 查询耗费cpu和block io
mysql> show profile cpu,block io for query 2;
+--------------------------------+----------+----------+------------+--------------+---------------+
| Status                         | Duration | CPU_user | CPU_system | Block_ops_in | Block_ops_out |
+--------------------------------+----------+----------+------------+--------------+---------------+
| starting                       | 0.000076 | 0.000068 | 0.000000   |            0 |             0 |
| Executing hook on transaction  | 0.000005 | 0.000004 | 0.000000   |            0 |             0 |
| starting                       | 0.000008 | 0.000008 | 0.000000   |            0 |             0 |
| checking permissions           | 0.000006 | 0.000006 | 0.000000   |            0 |             0 |
| Opening tables                 | 0.000182 | 0.000184 | 0.000000   |            0 |             0 |
| init                           | 0.000006 | 0.000005 | 0.000000   |            0 |             0 |
| System lock                    | 0.000007 | 0.000006 | 0.000000   |            0 |             0 |
| optimizing                     | 0.000005 | 0.000005 | 0.000000   |            0 |             0 |
| statistics                     | 0.000010 | 0.000010 | 0.000000   |            0 |             0 |
| preparing                      | 0.000012 | 0.000012 | 0.000000   |            0 |             0 |
| executing                      | 0.033988 | 0.036445 | 0.000000   |            0 |             0 |
| end                            | 0.000013 | 0.000007 | 0.000000   |            0 |             0 |
| query end                      | 0.000004 | 0.000004 | 0.000000   |            0 |             0 |
| waiting for handler commit     | 0.000009 | 0.000009 | 0.000000   |            0 |             0 |
| closing tables                 | 0.000008 | 0.000007 | 0.000000   |            0 |             0 |
| freeing items                  | 0.000031 | 0.000031 | 0.000000   |            0 |             0 |
| cleaning up                    | 0.000003 | 0.000003 | 0.000000   |            0 |             0 |
+--------------------------------+----------+----------+------------+--------------+---------------+

show profile用法
type
　　all 显示所有的开销信息
　　block io 显示块io相关的开销
　　context switches 上下文切换相关
　　cpu 显示cpu相关开销
　　ipc 显示发送和接收相关开销
　　memory 显示内存相关开销信息
　　page faults 显示页面错误相关开销
　　source 显示和Source_function,Source_file,Source_line相关的开销信息
　　swaps 显示交换次数相关开销的信息

出现以下信息时其中一个,说明得优化
　　converting HEAP to MyISAM查询结果太大,内存都不够用了往磁盘上搬了
　　Creating tmp table 创建临时表,拷贝数据到临时表,用完再删除
　　copying to tmp table 把内存中临时表复制到磁盘,危险!!!
　　locked

```

##### 全局日志

```mysql
-- 该功能只在测试环境使用，生产环境不要开启
-- 查看全局日志是否开启
mysql> show variables like '%general_log%';
+------------------+--------------------------------------------------+
| Variable_name    | Value                                            |
+------------------+--------------------------------------------------+
| general_log      | OFF                                              |
| general_log_file | /home/mysql/data/dbs/iZhp3d60mk2np8cf90lbyuZ.log |
+------------------+--------------------------------------------------+

-- 配置启用 
-- Variable 'general_log' is a GLOBAL variable and should be set with SET GLOBAL
set global general_log=1;

#记录日志的路径
set global general_log_file=/path/logfile;
#输出格式,file,table等
set global log_output=table;

-- 开启后,你所编写的SQL语句都将会记录到mysql库里的general_log表里
-- 查看SQL记录
```

### 锁

```mysql
--  建表
CREATE TABLE mylock (
    id int not null PRIMARY KEY auto_increment ,
		name VARCHAR(20)
)ENGINE myisam ;


INSERT INTO mylock(name) values('a') ;
INSERT INTO mylock(name) values('b') ;
INSERT INTO mylock(name) values('c') ;
INSERT INTO mylock(name) values('d') ;
INSERT INTO mylock(name) values('w') ;
INSERT INTO mylock(name) values('g') ;

SELECT * FROM mylock ;

-- 如何查看表上是否有锁 ， In_use = 1  代表有锁 ， 0无锁
mysql> show open tables;
+--------------------+----------------------------------------------------+--------+-------------+
| Database           | Table                                              | In_use | Name_locked |
+--------------------+----------------------------------------------------+--------+-------------+
| kettleRepo         | R_CONDITION                                        |      0 |           0 |
| mysql              | innodb_index_stats                                 |      0 |           0 |
| performance_schema | rwlock_instances                                   |      0 |           0 |
| performance_schema | events_waits_history_long                          |      0 |           0 |
| mysql              | servers                                            |      0 |           0 |
| performance_schema | mutex_instances                                    |      0 |           0 |
| performance_schema | events_waits_summary_by_host_by_event_name         |      0 |           0 |

-- 给表加锁
mysql> lock table test read , mylock write;        
Query OK, 0 rows affected (0.00 sec)

+--------------------+----------------------------------------------------+--------+-------------+
| Database           | Table                                              | In_use | Name_locked |
| Company            | test                                               |      1 |           0 |
| Company            | mylock                                             |      1 |           0 |

-- 释放表锁
mysql> unlock tables; 	
Query OK, 0 rows affected (0.00 sec)
```

#### 读阻塞写的例子

```mysql
-- 给lock表加读锁，共享锁
mysql> lock table mylock read ;
Query OK, 0 rows affected (0.00 sec)

-- 自己session可以查询数据
mysql> select * from mylock ;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  4 | d    |
|  5 | w    |
|  6 | g    |
+----+------+
6 rows in set (0.00 sec)

-- 本session不可以插入数据，读锁阻塞自己写操作
mysql> insert into mylock(name) values('wanghaun');
ERROR 1099 (HY000): Table 'mylock' was locked with a READ lock and can't be updated
-- 本session未解锁mylock表之前，不可以查询其他表
mysql> select * from employee ;
ERROR 1100 (HY000): Table 'employee' was not locked with LOCK TABLES
mysql> unlock tables;
Query OK, 0 rows affected (0.00 sec)

-- 其它session可以查看mylock表，因为read锁是共享锁
mysql> select * from mylock ;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  4 | d    |
|  5 | w    |
|  6 | g    |
+----+------+
6 rows in set (0.00 sec)

-- 其它session也可以查看别的表，因为该session没有read锁
mysql> select * from employee ;
+------------+-----------+------------+----------+---------+-------------+--------------+
| employeeId | name      | birth      | joblevel | salary  | phone       | departmentId |
+------------+-----------+------------+----------+---------+-------------+--------------+
|       1001 | 张强      | 1986-02-03 | 一级     | 8000.00 | 13585422655 |            1 |
|       1003 | 萌萌      | 1990-04-19 | 二级     | 6000.00 | 18548775264 |            2 |
|       1004 | 李小峰    | 1973-07-20 | 二级     | 5700.00 | 18625489512 |            1 |
|       1006 | 刘珊      | 1976-06-28 | 一级     | 7500.00 | 18524811174 |            2 |
|       1007 | 李梅      | 1980-01-29 | 二级     | 5500.00 | 13958621455 |            3 |
|       1008 | 张宝玉    | 1982-09-23 | 二级     | 5600.00 | 13715620210 |            1 |
|       1009 | 陈大壮    | 1978-05-21 | 一级     | 7700.00 | 15848562585 |            4 |
|       1010 | 张天琪    | 1980-09-15 | 二级     | 5000.00 | 13965815822 |            4 |
+------------+-----------+------------+----------+---------+-------------+--------------+
8 rows in set (0.09 sec)

-- 其它session对mylock表进行DML操作会被原session加锁 造成阻塞
mysql>  insert into mylock(name) values('session2');
Query OK, 1 row affected (12.67 sec)

-- 待加锁session加锁后，上述阻塞语句执行成功，成功查询到插入结果
mysql> select * from mylock;
+----+----------+
| id | name     |
+----+----------+
|  1 | a        |
|  2 | b        |
|  3 | c        |
|  4 | d        |
|  5 | w        |
|  6 | g        |
|  7 | session2 |
+----+----------+
```



### 函数和存储过程

#### 函数

```mysql
-- 创建数据库名为bigdata
CREATE DATABASE bigdata;

-- 使用该数据库
USE DATABASE ;

-- 创建表dept
CREATE TABLE dept (
	id INT UNSIGNED PRIMARY KEY auto_increment,
	depno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
	dname VARCHAR ( 20 ) NOT NULL DEFAULT '',
  loc VARCHAR ( 13 ) NOT NULL DEFAULT '' 
) ENGINE = INNODB DEFAULT CHARSET = GBK;

-- 创建表dept
CREATE TABLE emp (
	id INT UNSIGNED PRIMARY KEY auto_increment,
	empno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
	ename VARCHAR ( 20 ) NOT NULL DEFAULT '',
	job VARCHAR ( 9 ) NOT NULL DEFAULT '',
	mgr MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
	hiredate DATE NOT NULL , 
	sal DECIMAL(7,2) NOT NULL ,
	comm DECIMAL(7,2) NOT NULL ,
  deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0 
) ENGINE = INNODB DEFAULT CHARSET = GBK;

-- 创建函数， 默认需要开启的参数 ， 8.0 版本默认开启
-- 当开启二进制日志后，如果变量log_bin_trust_function_creators为OFF，那么创建或修改存储函数就会报error
SHOW VARIABLES LIKE 'log_bin_trust_function_creators';
SHOW VARIABLES LIKE 'log_bin'；


-- MySQL5.6.24 中默认都是关闭
mysql> SHOW VARIABLES LIKE 'log_bin_trust_function_creators';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| log_bin_trust_function_creators | OFF   |
+---------------------------------+-------+
1 row in set (0.00 sec)

mysql> SHOW VARIABLES LIKE 'log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | OFF   |
+---------------+-------+

-- 创建函数，产生随机字符串
-- returns定义的是函数返回值的类型 ， 而return则返回的是返回值
CREATE FUNCTION rand_string(n int) RETURNS VARCHAR(255)
BEGIN
  DECLARE chars_set VARCHAR(100) DEFAULT 'abcdefghijklmnopqrstuvwxyz1234567890ABCDEFGHIJKLMNOPQRSTUVWXYZ';
	DECLARE return_str VARCHAR(255) DEFAULT '';
	DECLARE i INT DEFAULT 0 ;
	WHILE i < n DO
	 SET return_str = CONCAT(return_str,SUBSTR(chars_set,FLOOR(RAND()*52),1));
	 SET i = i + 1 ;
	END WHILE ;
 RETURN return_str;
END

-- 创建产生随机数的函数
CREATE FUNCTION rand_num() RETURNS INT(5)
BEGIN
	DECLARE i INT DEFAULT 0 ;
	SET i = FLOOR(100+RAND()*10) ;
 RETURN i;
END

-- 删除函数
drop FUNCTION rand_num;

-- 调用函数 , 类似于调用MySQL库函数一样
SELECT rand_num() ; 
SELECT rand_string(5) ; 
```

#### 存储过程

```mysql
--  查看自动提交是否开启的两种方式
mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+

mysql> show variables like 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+

-- 创建出插入emp 表的存储过程
CREATE PROCEDURE insert_emp (
	IN START INT ( 10 ),
	IN max_num INT ( 10 )) BEGIN
	DECLARE
		i INT DEFAULT 0;
	
	SET autocommit = 0;
	REPEAT
			
			SET i = i + 1;
		INSERT INTO emp ( empno, ename, job, mgr, hiredate, sal, comm, deptno )
		VALUES
		  ((START+i),rand_string(6),'SALESMAN',0001,CURDATE(),2000,400,rand_num());
			UNTIL i = max_num 
		END REPEAT;
	COMMIT;

END

-- 创建出插入dept 表的存储过程
CREATE PROCEDURE insert_dept (
	IN START INT ( 10 ),
	IN max_num INT ( 10 )) BEGIN
	DECLARE
		i INT DEFAULT 0;
	
	SET autocommit = 0;
	REPEAT
			
			SET i = i + 1;
		INSERT INTO dept ( depno, dname, loc )
		VALUES
		  ((START+i),rand_string(10),rand_string(8));
			UNTIL i = max_num 
		END REPEAT;
	COMMIT;

END

-- 删除存储过程
DROP PROCEDURE insert_dept

-- 调用存储过程
-- 编号从10000开始，插入10条数据
call insert_dept(10000,10) ;
-- 编号从1开始，插入50万条数据
call insert_emp(1,500000);
```



### **查看数据文件空间大小**

```mysql
-- 查看数据文件挂载空间使用率
[root@0daycrack mysql]# df -k
文件系统                   1K-块     已用     可用 已用% 挂载点
/dev/mapper/centos-
root 49166336 31593092 17573244   65% /
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

