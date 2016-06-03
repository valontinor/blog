---
title: MySQL与MariaDB数据库的备份与恢复
date: 2016-06-03 10:02:33
tags: mysql mariadb
categories: Database
---
### 备份简介
备份是数据库的最后一道防线，对于任何数据丢失的场景，备份虽然不一定能恢复百分百的数据（取决于备份周期和策略），但至少能将损失降到最低。衡量备份恢复有两个重要指标：恢复点目标（RPO）和恢复时间目标（RTO），前者重点关注能恢复到什么程度，而后者则重点关注恢复需要多长时间。一般而言，数据库备份有冷备份和热备份两种方法，前者比较慢，须关闭数据库，恢复在时间点上进行；后者比较快，无须关闭数据库，恢复亦较快。本文重点关注热备份。
<!--more-->

### 冷备份
冷备份是指在正常关闭数据库的情况下将关键性数据库文件拷贝到另外位置（备份位置）的备份策略。冷备份发生在已经正常关闭数据库的情况下，当正常关闭数据库时，关键性文件会提供给我们一个完整的数据库，对于备份数据库而言，冷备份是最快和最安全的备份方法。
冷备份的优点：
* 非常快的备份方法，只需拷贝文件
* 容易归档，简单拷贝即可
* 容易恢复到某个时间点上，只需再将文件拷贝回去即可
* 能与归档方法相结合，作数据库“最新状态”的恢复
* 低度维护，高度安全

冷备份的缺点：
* 单独使用时，只能提供到“某一时间点上“的恢复
* 在实施备份的全过程中，数据库必须要作备份而不能做其它工作。亦即，在备份过程中，数据库必须时关闭状态。
* 若磁盘空间有限，则只能拷贝到磁带等其它外部存储设备上，速度相对会较慢。
* 不能按表或按用户恢复
值得注意的是冷备份必须在数据库关闭的情况下进行，当数据库处于打开状态时，执行数据库文件系统备份是无效的。而且在恢复后一定要把数据库的属组（group）和属主（owner）改为mysql。
如果可能的话（主要看效率），应将数据库文件备份到磁盘上，然后启动数据库（使用户可以工作）并将备份的文件拷贝到磁带或外部存储设备上（拷贝的同时，数据库已经可以工作）。

### 热备份
热备份是在数据库运行的情况下，备份数据库操作的sql语句。当数据库发生问题时，执行备份的sql语句即可恢复。数据库进行热备份时，数据库的读写均不受影响（其实也不是绝对的不受影响，因为进行备份时根据所采用引擎的不通数据库会锁表或锁列）。
热备份的优点：
* 可在表空间或数据文件级备份，备份时间短
* 备份时数据库仍可使用
* 可达到秒级恢复（恢复到某一时间点上）
* 可对几乎所有数据实体作恢复
* 恢复是快速的，在大多数情况下可在数据库仍工作时恢复

热备份的缺点：
* 不能出错，否则后果严重
* 若热备不成功，所得结果不可用于时间点的恢复
* 因难以维护，所以要特别细心，难度较大

### 备份准备
定制备份策略前，还需要做一些前期准备工作和规划。
1. 需要备份什么
	一般情况下，需要备份的数据分为以下几种：
	* 数据
	* 二进制日志，InnoDB事务日志
	* 代码（存储过程、存储函数、触发器、事件调度器）
	* 服务器配置文件

2. 备份工具
	常用的备份工具如下：
	* cp，tar等归档复制工具：物理备份工具，适用于所有的存储引擎，冷备份、完全备份、部分备份
	* lvm2 snapshot：几乎热备，借助文件系统管理工具进行备份
	* mysqldump：逻辑备份工具，适用于所有的存储引擎，支持温备、完全备份、部分备份、对于InnoDB存储引擎支持热备
	* xtrabackup：一款非常强大的InnoDB/XtraDB热备工具，支持完全备份、增量备份，由percona提供

### 备份策略
针对不通的应用场景，应该制定不同的数据库备份策略。一般而言，备份策略可分为以下几种：
* 直接cp，tar复制数据库文件
* mysqldump+复制BIN LOGS
* lvm2快照+复制BIN LOGS
* xtrabackup

以上几种备份策略分别针对于不用的应用场景：
* 如果数据量较小，可以使用第一种方式，直接复制数据库文件
* 如果数据量一般，可以使用第二种方式，先使用mysqldump对数据库进行完全备份，然后定期备份BINARY LOG达到增量备份的效果
* 如果数据量一般而又不过分影响业务运行，可以使用第三种方式，使用lvm2的快照对数据文件进行备份，而后定期备份BINARY LOG达到增量备份的效果。
* 如果数据量很大而又不过分影响业务，可以使用第四种方式，使用xtrabackup进行完全备份后，定期使用xtrabackup进行增量备份或差异备份。

下面将着重介绍第一种和第二种备份策略。
### cp，tar备份
cp，tar属物理备份，直接备份数据库文件到文件系统，一般而言，除了存储到本地文件系统外，还会在远程服务器上存储一份（异机备份）或磁带等外部存储设备上存储一份。MySQL预设的存储目录是/var/lib/mysql（Linux），具体备份过程如下：
```
mysqld stop    #停止数据库服务
cd /var/lib/mysql/    #进入到数据库文件所在目录
tar zxcf dbname_backup.tgz db    #压缩数据库文件
mysqld start    #启动数据服务
cp dbname_backup.tgz /path/of/backup/    #拷贝压缩文件到备份目录
scp dbname_backup.tgz remote_username@remote_ip:remote_folder    #拷贝压缩文件到远程机器目录（异机备份）
```
### mysqldump备份
1. 备份整个数据库
这是最简单的备份方式，备份数据库中的所有表结构和数据，具体过程如下：
	```
	mysqldump --user=admin_back\
 	         --password --lock-all-tables
 	         --all-databases > /path/of/backup/all-dbs.sql
	```
	* 如果省略备份文件路径，则默认在当前目录创建备份文件。你可以任何顺序列出当中的选项，但输出至（STDOUT）备份文件一定要在最后面。
	* 当对InnoDB引擎或其它事务性表进行备份时，最好包含'--single-transaction'选项，这将提高备份数据的一致性，当备份未完成时，表中的数据将不会改变。但这个选项将会屏蔽'--lock-tables'选项。
	* 对整个数据库进行备份将会多所有的表进行备份，这也包括mysql默认的表，如user表，这些表里面存储有数据库的用户和密码信息，最好屏蔽这些表。可以通过'--ignore-table=mysql.user'选项来屏蔽对user表的备份。
2. 对具体数据库进行备份
对某一具体的数据库进行备份而不是所有的数据库，使用'--databases'选项替代'--all-databases'选项即可：
	```
	mysqldump --user=admin_backup --password --lock-tables \
	          --verbose --databases dbname > dbname.sql
	```
	* 如果你仅想对数据库的结构进行备份而不想备份数据，可使用'--no-data'选项。
	* '--verbose'选项会将备份过程中的主要步骤信息输出到控制台，由此你可以更详细的了解备份情况，出现意外时也可以更高的进行定位。
	* 如果需要对多个数据库进行备份，在'--databases'选项后面列出所需备份的数据库即可，用空格分隔。
3. 对具体的表进行备份
对于很大、较活跃的数据库，你可能想备份其中一个单独的表而不是整个数据库。你可能每周备份一次整个数据库，但每天备份一次活跃的表。备份某个具体的表操作如下：
	```
	mysqldump --user=admin_backup --password --lock-tables \
	          --databases dbname --tables tablename > db-table.sql
	```
上述备份命令将为每一个数据库的表备份生成一个备份文件，如果我们要把几个数据库的表备份到一个文件中，则可以使用'>>'替代'>'，备份文件名保持一样就可以了。

### 数据恢复
当数据丢失后，可以通过备份的sql文件恢复数据，也可以通过冷备份的文件恢复，冷备份恢复比较容易，拷贝备份的文件到相应的文件目录，重启mysql服务就可以了，但这样要停止mysql服务，带来业务的中断。而通过sql备份文件则可以在保持mysql服务的情况下恢复数据，可以恢复整个数据库，亦可以恢复部分表数据，甚至恢复某几行数据。
1. 恢复整个数据库
数据库层面的数据恢复实现某一个数据库的整个数据的恢复，现实中一般使用不多，除非出现大规模的遭难，如磁盘损坏、误删数据库等。恢复命令如下：
```
mysql --user=admin_restore --password < db_backup.sql
```
	其和备份数据库的命令主要有两点不一样，一是命令是mysql而备份时mysqldump，二是恢复时是输入（STDIN）符号'<'而备份时是输出(STDOUT)'>'。另外，不需要额外的参数，恢复后所有表会创建，所有数据都会插入表中，所以在整个恢复数据库时，最好确保前数据库已经删除，或者表数据已经清除，不然会出现冗余数据，或者相关字段重复无法插入错误而导致数据无法恢复。
2. 恢复表
	整个数据库恢复带来的问题是所有数据库的表都会被重写，如果备份后有些表改变了，而你只想恢复其中的某个表，那整个数据库恢复就会丢失备份后的数据。如果你之前的备份策略是单个表的备份，那直接从备份文件恢复相应文件就可以了，但这样备份起来就是否麻烦，产生的文件也较多，下面介绍如何从整个数据库备份文件中恢复单个表数据。
	* 修改备份文件法
	用文本工具打开备份文件就会发现除了一些注释性的条件性的语句外其它语句还是很容易理解的，下面是一个备份文件的部分片段：
```
-- MySQL dump 10.14  Distrib 5.5.39-MariaDB, for Linux (i686)    --    -- Host: localhost    Database: rookery    -- ------------------------------------------------------    -- Server version       5.5.39-MariaDB    /*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;    /*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;    /*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;    /*!40101 SET NAMES utf8 */;    /*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;    /*!40103 SET TIME_ZONE='+00:00' */;    /*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;    /*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS,FOREIGN_KEY...=0*/;    /*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;    /*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;    --    -- Current Database: `rookery`    --    CREATE DATABASE /*!32312 IF NOT EXISTS*/ `rookery`    /*!40100 DEFAULT CHARACTER SET latin1 COLLATE latin1_bin */;USE `rookery`; -- [ snip ]    --    -- Table structure for table `conservation_status`    --    DROP TABLE IF EXISTS `conservation_status`;    /*!40101 SET @saved_cs_client     = @@character_set_client */;    /*!40101 SET character_set_client = utf8 */;    CREATE TABLE `conservation_status` (      `conservation_status_id` int(11) NOT NULL AUTO_INCREMENT,      `conservation_category` char(10) COLLATE latin1_bin DEFAULT NULL,      `conservation_state` char(25) COLLATE latin1_bin DEFAULT NULL,      PRIMARY KEY (`conservation_status_id`)    ) ENGINE=MyISAM AUTO_INCREMENT=10      DEFAULT CHARSET=latin1 COLLATE=latin1_bin;    /*!40101 SET character_set_client = @saved_cs_client */;    --    -- Dumping data for table `conservation_status` 294| Chapter 14: Backing Up and Restoring Databases
--    LOCK TABLES `conservation_status` WRITE;    /*!40000 ALTER TABLE `conservation_status` DISABLE KEYS */;    INSERT INTO `conservation_status` VALUES    (1,'Extinct','Extinct'),    (2,'Extinct','Extinct in Wild'),    (3,'Threatened','Critically Endangered'),    (4,'Threatened','Endangered'),    (5,'Threatened','Vulnerable'),    (6,'Lower Risk','Conservation Dependent'),    (7,'Lower Risk','Near Threatened'),    (8,'Lower Risk','Least Concern'),    (9,NULL,'Unknown');/*!40000 ALTER TABLE `conservation_status` ENABLE KEYS */; UNLOCK TABLES;-- [ snip ]/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;    /*!40101 SET SQL_MODE=@OLD_SQL_MODE */;    /*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;    /*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;    /*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;    /*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;    /*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;    /*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;    -- Dump completed on 2014-09-15  6:48:27
```
	其中'-- [ snip ]'部分是省去的片段，可以看见整个文件还是很清晰的：删除数据库（如果存在）、创建数据库、删除表（如果存在）、创建表，锁表，插入数据，解锁。找到你要恢复的表的部分，删除其它的内容（注意不要删除前面的注释部分内容），然后执行数据恢复命令，这样就会只恢复文件中的表。
	这种方法在备份文件较小时比较适用，但一旦文件较大，操作就比较麻烦，还有可能带来误操作，误删除了其它的表。
	* 通过临时数据库恢复
	这种方法的原理就是先把整个数据库恢复到一个临时的数据库，然后从临时数据库的对应表中恢复数据。
	要从备份文件中将数据库恢复到临时数据库，需先修改备份文件，在文件开始部分将创建数据库和切换数据库部分的语句改成临时数据库的名称：
```
--...-- Current Database: `rookery`--CREATE DATABASE /*!32312 IF NOT EXISTS*/`rookery_backup` /*!40100 DEFAULT CHARACTER SET latin1 COLLATE latin1_bin */;USE `rookery_backup`; ...
```
	其中，主要是'CREATE DATABASE'和'USE'语句后面部分的数据库名称。
	然后删除现在使用的数据库中的相应的表，并重建同名新表：
```
DROP TABLE database.table;
CREATE TABLE database.table LIKE temp_database.table;
```
	上面使用CREATE TABLE ... LIKE语句，这样无需输入具体的字段语句就可以创建同样结构的表。
	最后，从临时数据库的呃表中恢复数据，删除临时数据库：
```
INSERT INTO database.table SELECT * FROM temp_database.table;
DROP DATABASE temp_database;
```
	使用INSERT...SELECT语句就可以通过SELECT子语句的条件选择需要插入到表中的数据，在这里我们选取所有数据。
	* 使用受限的用户账号
	还有一种简便的从整个数据库备份文件中恢复单一表的方式是通过受限的用户账号来恢复数据，使用的用户账号只具有所要恢复的数据的表权限。当你使用此账号执行备份文件时，只有用户账号具有权限的表才会恢复，不具有权限的表操作都会失败。步骤如下：
```
GRANT SELECT on database.* to 'user_temp'@'localhost' IDENTIFIED BY 'password';     #创建临时账号

GRANT ALL ON database.table TO 'user_temp'@'localhost';    #赋予相应表的操作权限

mysql --user user_temp --password --force < db_backup.sql;    #从备份文件中恢复数据
```
	注意上述中的'--force'选项，一般情况下当执行数据恢复时，如果执行过程中产生错误，将会马上停止执行并回滚之前执行的语句，从而不执行任何操作。使用'--force'选项后将忽略相关的错误继续执行没有错误产生的表语句。
3. 恢复列或行
如果要恢复单独的列或行数据如何操作呢？这个可以借鉴之前的恢复表数据方法中的“通过临时数据库恢复”方法，即先将整个数据恢复到一个临时数据库中，然后通过INSERT...SELECT语句及WHERE条件字句选择相应的行数据恢复。
恢复数据库到临时表的方法不再赘述，以下是选择相应行数据的方法：
```
REPLACE INTO database.table SELECT * FROM temp_database.table WHERE condition1 AND contition2 AND ...;

UPDATE database.table SET column1 = value WHERE condition1 AND condition2 AND ...;
```
上面用REPLACE替代了INSERT，是因为当发现同样满足条件的语句时，备份的数据将替换当前表的数据，避免产生冗余。


