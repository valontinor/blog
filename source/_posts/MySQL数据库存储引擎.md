---
title: MySQL数据库存储引擎
date: 2016-05-11 10:19:09
tags: mysql
categories: 技术
---
文自 [pangge](http://pangge.blog.51cto.com/6013757/1303893)。

#### 简单介绍
存储引擎就是值表的类型。数据库的存储引擎决定了表在计算机中的存储方式。存储引擎的概念是MySQL的特点，而且是一个插入式的存储引擎概念。这就决定了MySQL数据库中的表可以使用不同的存储方式存储。用户可以根据自己的不同要求，选择不同的存储方式、是否进行事务处理等等。  

#### 查询方式及内容解析
使用SHOW ENGINES语句可以查看MySQL数据库支持的存储引擎类型。查询方法如下：
```
SHOW ENGINES
```
SHOW ENGINES语句可以使用";"结束，也可以使用"\g"或"\G"结束，"\g"与";"的作用相同，"\G"可以让结果显示的更加美观。
```PLpgSQL
mysql> SHOW ENGINES\G
*************************** 1. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 2. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 3. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
###############中间已省略###################
*************************** 8. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
8 rows in set (0.11 sec)
```
解析：查询结果中，
* Engine参数指存储引擎名称
* Support参数说明MySQL是否支持该类引擎，YES表示支持
* Comment参数指对该引擎的评论
* Transactions参数表示是否支持事务处理，YES表示支持
* XA参数表示是否支持分布式交易处理规范，YES表示支持
* Savepoints参数表示是否支持保存点，以便失误回滚到保存点，YES表示支持

从以上查询结果中可以看出，MySQL支持的引擎参数包括MyISAM、MEMORY、InnoDB、ARCHIVE和MRG_MYISAM等。其中InnoDB为默认的存储引擎。可使用语句查询默认的存储引擎：
```
SHOW VARIABLES LIKE 'storge_engine';
```
执行结果如下：
```
mysql> SHOW VARIABLES LIKE 'storage_engine';
+----------------+--------+
| Variable_name  | Value  |
+----------------+--------+
| storage_engine | InnoDB |
+----------------+--------+
1 row in set (0.10 sec)
```
解析：结果显示默认的引擎存储为InnoDB。如果想修改默认的存储引擎，可以在配置文件my.ini中将"default-storge-engine=InnoDB"修改为"default-storge-engine=MyISAM"，然后重启服务，使修改生效。
使用"SHOW TABLESSTATUS"可以查看某个库中所有表支持的存储引擎，查询方法如下：
```
mysql> USE hellodb
Database changed
mysql> SHOW TABLE STATUS\G
*************************** 7. row ***************************
           Name: toc
         Engine: MyISAM
        Version: 10
     Row_format: Fixed
           Rows: 0
 Avg_row_length: 0
    Data_length: 0
Max_data_length: 2533274790395903
   Index_length: 1024
      Data_free: 0
 Auto_increment: 1
    Create_time: 2013-08-12 16:17:23
    Update_time: 2013-08-12 16:17:23
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options:
        Comment:
```
解析：结果显示，对于MySQL来说，当前所有表默认都是使用MyISAM存储引擎，其中：
* Name：表名
* Engine： 使用的存储引擎
* Version：所用存储引擎的版本
* Row_format：行格式；对于MyISAM存储引擎来说，常见的有Dynamic（变长记录）、Fixed（定长记录）、Compressed（压缩以后存放的行）、compat（InnoDB中常见的）
* Rows：表中的行数（对于其它存储引擎来说这个值是估算的）
* Avg_row_length：行的平均字节数
* Data_length：表的数据量，单位为字节
* Max_data_length：表的容量上限（不同的存储引擎容量上限不同）
* Index_length：索引数据量，单位为字节
* Data_free：已经分配出去但未存储数据的存储空间
* Auto_increment：具有自动增长属性的字段上，下一个自动增长的值
* Create_time：表的创建时间
* Update_time：数据最近一次的更新时间
* Check_time：使用CHECK命令最近一次检查表的时间
* Checksum：表的校验和
* Create_options：创建表时指定的其它选项
* Comment：对于MyISAM存储引擎，存储的是创建表时的comment表选项指定的信息，对于InnoDB存储引擎，存储的是对应的表空间剩余的表空间信息。

#### 存储引擎比较
在实际工作中，选择一个合适的存储引擎是一个很复杂的问题，每种存储引擎都有各自的优势，不弄笼统的说谁比谁更好。下面将详解不同环境经常用到的存储引擎和针对各个存储引擎的特点进行对比，给出不同的选择建议。
1. InnoDB存储引擎
	InnoDB是MySQL的一种存储引擎，它给MySQL表提供了事务、回滚、崩溃修复能力、多版本并发控制的事务安全、间隙锁（可以有效防止幻读的出现）、支持辅助索引、自适应hash索引、支持热备、行级锁等。另外，InnoDB是MySQL上唯一一个提供了外键约束的引擎。
	InnoDB存储引擎中，创建的表的结构是单独存储的，并且存储在.frm文件中。数据和索引存储在一起并存储在表空间中。但在默认情况下，MySQL会将数据库的所有InnoDB表存储在一个表空间中。这种方式管理起来非常的不方便而且还不支持高级功能，所以建议每个表存储为一个表空间，实现方式为：使用服务器变量innodb_file_pre_table = 1。
	如果你需要频繁的进行更新、删除操作，数据库也可以选择InnoDB存储引擎，因为该存储引擎可以实现事务提交和回滚。
	
2. MyISAM存储引擎
    MyISAM存储引擎是MySQL中常见的存储引擎，MyISAM存储引擎是基于ISAM存储引擎发展起来的，支持全文索引、压缩存放、空间索引（空间函数）、表级锁、延迟更新索引键。但是MyISAM不支持事务、行级锁，更无法忍受的是崩溃后不能保证完全恢复（只能手动修复）。
    MyISAM存储引擎的表存储成三个文件，文件的名字和表的名字相同，扩展名为frm、MYD、MYI。其中frm为扩展名的文件存储表的结构；MYD为扩展名的文件存储数据，其是MYData的缩写；MYI为扩展名的文件存储索引，其为MyIndex的缩写。
    MyISAM存储引擎的插入数据很快，空间和内存使用较低。如果表主要是用于插入新记录和读出记录，那么选择MyISAM存储引擎能够实现处理的高效率。如果应用的完整性、并发性要求很低，也可以选择MyISAM存储引擎。
    
3. ARCHIVE存储引擎
    ARCHIVE，见名之意可以看出是归档，所以归档之后很多的高级功能就不再支持了，仅支持插入（insert）和查询（select）两种功能，ARCHIVE存储引擎之前还不支持索引（在MySQL5.5以后开始支持），但是它拥有很好的压缩机制，通常用于做数据仓库。
    ARCHIVE存储引擎适用于存储日志信息或其它按时间序列实现的数据采集类的应用场景中。
    
4. CSV存储引擎
    CSV存储引擎将数据文件保存为CSV格式的文件，可以方便的导入到其它数据库中去（例如excel表格、SQL SERVER等等），由此需要在数据库间自由共享数据时才偶尔建议使用此存储引擎。并且它也不支持索引，个人认为它仅适用于数据交换。
    
5. BLACKHOME存储引擎
    BLACKHOME叫做黑洞，也就是说没有存储机制，任何数据都会被丢弃，但是会记录二进制日志。一般在MySQL复制（中继服务器）中经常用到。
    
6. FEDERATED存储引擎
    FEDERATED可以实现跨服务器整理表，简单说就是它可以访问远程服务器上数据的存储引擎，所以说它不在本地创建数据，只会自动的建立一个连接到其它服务器上的链接，有点类似于代理的功能，默认都是禁用的。
    
7. MEMORY存储引擎
    MEMORY存储引擎是MySQL中的一类特殊的存储引擎，其使用存储在内存中的内存来创建表，而且所有数据保存在内存中。数据安全性很低，但是查找和插入速度很快。如果内存出现异常就会影响到数据的完整性，如果重启或关机，表中的数据就会丢失，因此基于MEMORY存储引擎的表的生命周期很短，一般都是一次性的。适用于某些特殊场景，像查找和映射，缓存周期性的聚合数据等。
    
#### 扩展的第三方存储引擎
1. OLTP类
  * XtraDB： InnoDB的改进版
  * PBXT：支持复制、外键约束，而且支持固态存储（SSD硬盘）上快速存取数据，是一个不错的支持事务的存储引擎。
  * TokuDB：图库数据库，在存储海量数据的方面有着MySQL无法比拟的优势，也有MySQL版的，其最大的优势就是支持分形树索引结构，这个结构导致它和缓存无关，也就直接导致了就算索引在数据库文件中放不下也不会影响性能。一般只适用于大量插入数据的分析型场景。

2. 列式存储引擎
列式数据库：此种数据库最适合存储大数据，在数据检索上也很好但是在一定程度上需要反关系存储，因此可能无法满足我们关系型数据库范式的概念所以被称为NoSQL。
目前较成熟的NoSQL数据库有MongoDB、HBase、Redis等。

3. 社区存储引擎
  * Aria： Maria的下一代版本
  * Groona：可以精确的实现全文索引引擎，可以替代MyISAM在索引上的特性。
  * Sphinx：外在的服务器能够在MySQL基础上为MySQL构建一个高效的全文索引，通过C++研发。MySQL支持的一个社区引擎叫SphinxSE，就是让Sphinx直接支持My接口，在MariaDB5.32上被编译支持了。

选择标准：
1. 是否需要支持事务？
2. 是否需要使用热备？
3. 是否需要崩溃恢复？

个人建议：
* 存储日志或按时间增长的数据：MyISAM、ARCHIVE
* 数据量大：NoSQL、Sphinx
* 事务： InnoDB


