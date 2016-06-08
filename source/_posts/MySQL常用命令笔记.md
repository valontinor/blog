---
title: MySQL常用命令笔记
date: 2016-06-06 15:01:03
tags: mysql mariadb
categories: 技术
---
本文归纳总结了一些MySQL/MariaDB常用的语句和命令，便于以后查询使用。
<!--more-->
### 创建数据库和表
* 创建数据库
```
CREATE DATABASE db_name;

CREATE DATABASE db_name, CHARACTER SET charset_name, COLLATE collation_name;

CREATE SCHEMA db_name, CHARACTER SET charset_name, COLLATE collation_name;

CREATE DATABASE IF NOT EXISTS db_name, DEFAULT CHARACTER SET = charset_name, DEFAULT COLLATE = collation_name;

CREATE DATABASE IF NOT EXISTS db_name, DEFAULT CHARACTER SET = utf8, DEFAULT COLLATE = utf8_general_ci;    #创建默认编码为utf8的数据库
```

* 创建表
```
CREATE TABLE tbl_name;

CREATE TABLE tbl_name (col_name data_type, col_name data_type, ...);

CREATE TABLE tbl_name (col_name data_type [NOT NULL | NULL] ) [DEFAULT default_value] [AUTO_INCREMENT] [UNIQUE | PRIMARY [KEY]], ...) ENGINE = value, DEFAULT CHARACTER SET = charset_name, DEFAULT COLLATE = collation_name;

CREATE TABLE tbl_name (id INT AUTO_INCREMENT PRIMARY, column1 VARCHAR(255) UNIQUE, column2 CHAR(1) DEFAULT 0)DEFAULT CHARSET=utf8 COLLATE=utf8_general_ci;
```
完整的命令详见：  
&nbsp;&nbsp;&nbsp;&nbsp;[Create Database](http://dev.mysql.com/doc/refman/5.7/en/create-database.html)  
&nbsp;&nbsp;&nbsp;&nbsp;[Create Table](http://dev.mysql.com/doc/refman/5.7/en/create-table.html)

### 修改数据库和表
* 修改数据库
```
ALTER DATABASE db_name CHARACTER SET = charset_name COLLATE = collation_name;

ALTER SCHEMA db_name CHARACTER SET = charset_name COLLATE = collation_name;

ALTER DATABASE db_name CHARACTER SET = utf8 COLLATE = utf8_general_ci;    #修改数据库编码为utf8
```

* 修改表
```
ALTER TABLE tbl_name new_tbl_name;

ALTER TABLE tbl_name RENAME [TO | AS] new_tbl_name;

ALTER TABLE tbl_name ADD COLUMN col_name data_type, ADD COLUMN col_name data_type, ...;    #增加表字段

ALTER TABLE tbl_name CHANGE COLUMN old_col_name new_col_name data_type, CHANGE COLUMN old_col_name new_col_name, ...;    #修改表字段

ALTER TABLE tbl_name ADD COLUMN col_name data_type, CHANGE COLUMN old_col_name new_col_name;    #增加并修改表字段

ALTER TABLE tbl_name ADD COLUMN col_name data_type [FIRST|AFTER col_name], CHANGE COLUMN col_name data_type [FRIST|AFTER] col_name;    #在特定字段后增加或修改表字段

ALTER TABLE tbl_name ALTER COLUMN col_name SET DEFAULT literal;

ALTER TABLE tbl_name ALTER col_name DROP DEFAULT;

ALTER TABLE tbl_name ADD INDEX (index_col_name, index_col_name, ...);

ALTER TABLE tbl_name DROP COLUMN col_name;

```
详细命令见：  
&nbsp;&nbsp;&nbsp;&nbsp;[Alter Database](http://dev.mysql.com/doc/refman/5.7/en/alter-database.html)  
&nbsp;&nbsp;&nbsp;&nbsp;[Alter Table](http://dev.mysql.com/doc/refman/5.7/en/alter-table.html)

### 插入
```
INSERT INTO tbl_name VALUES(expr, expr, ...);

INSERT INTO tbl_name(col_name, col_name, ...) VALUES(expr, expr, ...);

INSERT INTO tbl_name(col_name, col_name, ...) VALUES(expr, expr, ...), (expr, expr, ...), ...;    #插入多行数据

INSERT INTO tbl_name SET col_name=[expr | DEFAULT], col_name=[expr | DEFAULT], ...;

INSERT INTO tbl_name(col_name, col_name, ...) SELECT col_name, col_name, ... FROM tbl_name WHERE col_name = expr, ...;    #从别的表中插入数据
```
详细命令见：  
&nbsp;&nbsp;&nbsp;&nbsp;[Insert](http://dev.mysql.com/doc/refman/5.7/en/insert.html)

### 查询
```
SELECT * FROM table_references;

SELECT col_name, col_name, ... FROM table_references WHERE where_condition;

SELECT select_expr, select_expr, ... FROM table_references WHERE where_condition;

SELECT select_expr, select_expr, ... FROM table_references WHERE where_condition GROUP BY [col_name | expr | position];

SELECT select_expr, select_expr, ... FROM table_references WHERE where_condition LIMIT row_count;

SELECT select_expr, select_expr, ... FROM table_references WHERE where_condition LIMIT offset, row_conut;    #查询从offset开始的row_count行数据
```
详细命令见：  
&nbsp;&nbsp;&nbsp;&nbsp;[SELECT](http://dev.mysql.com/doc/refman/5.7/en/select.html)

### 更新和删除
* 更新
```
UPDATE table_reference SET col_name1=[expr1 | DEFAULT], col_name2=[expr2 | DEFAULT], ... ;

UPDATE table_reference SET col_name1=[expr1 | DEFAULT], col_name2=[expr2 | DEFAULT], ... WHERE where_condition;

UPDATE table_reference SET col_name1=[epxr1 | DEFAULT], col_name2=[expr2 | DEFAULT] WHERE where_condition ORDER BY ...;

UPDATE table_reference SET col_name1=[expr1 | DEFAULT], col_name2=[expr2 | DEFAULT] WHERE where_condition ORDER BY ... LIMIT row_count;
```
详细命令见：
&nbsp;&nbsp;&nbsp;&nbsp;[UPDATE](http://dev.mysql.com/doc/refman/5.7/en/update.html)

* 删除
```
DELETE FROM tbl_name;

DELETE FROM tbl_name WHERE where_condition;

DELETE FROM tbl_name WHERE where_condition ORDER BY ...;

DELETE FROM tbl_name WHERE where_condition LIMIT row_count;

DELETE FROM tbl_name WHERE where_condition ORDER BY ... LIMIT row_count;

DELETE FROM tbl_name1, tbl_name2, ... USING table_references;

DELETE FROM tbl_name1, tbl_name2, ... USING table_references WHERE where_condition;
```
详细命令见：
&nbsp;&nbsp;&nbsp;&nbsp;[DELETE](http://dev.mysql.com/doc/refman/5.7/en/delete.html)

### 连接和子查询
* 连接(JOIN)
```
SELECT select_expr FROM table_reference JOIN table_reference ON conditional_expr;

SELECT select_expr FROM table_reference JOIN table_reference USING (column_list);

SELECT select_expr FROM table_reference [LEFT | RIGHT] JOIN table_reference ON conditional_expr;

SELECT select_expr FROM table_reference [LEFT | RIGHT] JOIN table_reference USING (column_list);
```
上面只列出了查询语句的连接，删除语句及其它语句也可以用连接。详细命令见：
&nbsp;&nbsp;&nbsp;&nbsp;[JOIN](http://dev.mysql.com/doc/refman/5.7/en/join.html)

* 子查询(SUBQUERY)
> A subquery is a SELECT statement within another statement.  

	> All subquery forms and operations that the SQL standard requires are supported, as well as a few features that are MySQL-specific.

### 其它
```
USING db_name;

SHOW DATABASES;

SHOW CREATE TABLE tbl_name \G

SHOW INDEX FROM db_name.tbl_name \G

SHOW COLUMNS FROM tbl_name;

SHOW FULL COLUMNS FROM tbl_name;

SHOW PROCESSLIST;

SHOW BINARY LOGS;

SHOW MASTER STATUS;

SHOW VARIABLES LIKE 'pattern';

SHOW VARIABLES WHERE expr;

DESCRIBE tbl_name;
```

### 忘记MySQL/MariaDB密码
1. 新建一个文件（如rt-reset.sql）存放在相应的文件目录下（最好为protected目录），文件内容如下：
```
UPDATE mysql.user SET Password=PASSWORD('new_pwd') User='root';
FLUS PRIVILEGES;
```
2. 从命令行启动MySQL，使用'--init-file'选项：
```
sudo mysqld_safe --init-file='/path/to/file/rt-reset.sql
```
3. 修改成功后，删除文件rt-reset.sql
```
rm /path/tofile/rt-reset.sql
```
