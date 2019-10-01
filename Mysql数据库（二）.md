## Mysql数据库（二）

#### 一、库的相关操作

- 系统数据库：

  ```mysql
  mysql> show databases;  #查看当前库
  +--------------------+
  | Database           |
  +--------------------+
  | information_schema |
  | db1                |
  | mysql              |
  | performance_schema |
  | sys                |
  +--------------------+
  
  information_schema： 虚拟库，不占用磁盘空间，存储的是数据库启动后的一些参数，如用户表信息、列信息、权限信					息、字符信息等
  performance_schema： MySQL 5.5开始新增一个数据库：主要用于收集数据库服务器性能参数，记录处理查询请求时发生					  的各种事件、锁等现象
  mysql：        授权库，主要存储系统用户的权限信息
  ```

  ![1556091384117](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556091384117.png)

- **创建数据库**

  - 万能的`help`语法：`help create database;`

    ![1556091568371](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556091568371.png)

    创建数据语法：

    ```mysql
    create database 数据库名 charset utf8;
    
    #数据库命名规则：
        可以由字母、数字、下划线、＠、＃、＄
        区分大小写
        唯一性
        不能使用关键字如 create select
        不能单独使用数字
        最长128位
    # 基本上跟python或者js的命名规则一样
    ```

- 数据库相关操作：

  ```mysql
  show databases;   #  查看最外层数据库
  
  mysql> show create database db1;   #查看当前数据库，这里是查看db1数据库,可加\G
  +----------+--------------------------------------------------------------+
  | Database | Create Database                                              |
  +----------+--------------------------------------------------------------+
  | db1      | CREATE DATABASE db1 /*!40100 DEFAULT CHARACTER SET utf8 */ |
  +----------+--------------------------------------------------------------+
  ```

- 选择数据库

  ```mysql
  mysql> use db1;
  Database changed  #表示切换了路径
  ```

- 查看当前所在的库

  ```mysql
  mysql> select database();
  +------------+
  | database() |
  +------------+
  | db1        |
  +------------+
  ```
  
- 删除数据库

  ```mysql
  mysql> drop database db1;
  ```

- 修改数据库字符集

  ```mysql
  mysql> alter database db1 charset utf8;
  ```

- 了解内容

  ```
  SQL语言主要用于存取数据、查询数据、更新数据和管理关系数据库系统,SQL语言由IBM开发。SQL语言分为3种类型：
  1、DDL语句    数据库定义语言： 数据库、表、视图、索引、存储过程，例如CREATE DROP ALTER，也就是数据表、文件的创建相关
   
  2、DML语句    数据库操纵语言： 插入数据INSERT、删除数据DELETE、更新数据UPDATE、查询数据SELECT，也就是数据的填充，修改，删除
   
  3、DCL语句    数据库控制语言： 例如控制用户的访问权限GRANT、REVOKE，也就是给管理员权限，等
  ```

  

#### 二、存储引擎：

​	前面我们已经学习了库的创建，下面我们要对文件（表）进行操作，在此之前我们必须 了解数据库的存储引擎。现实生活中我们用来存储数据的文件有不同的类型，每种文件类型对应各自不同的处理机制：比如处理文本用txt类型，处理表格用excel，处理图片用png等。

​	**数据库中的表也应该有不同的类型，表的类型不同，会对应mysql不同的存取机制，表类型又称为存储引擎。**存储引擎说白了就是如何存储数据、如何为存储的数据建立索引和如何更新、查询数据等技术的实现方法。因为在关系数据库中数据的存储是以表的形式存储的，所以存储引擎也可以称为表类型（即存储和操作此表的类型）

​	*在Oracle 和SQL Server等数据库中只有一种存储引擎，所有数据存储管理机制都是一样的。而MySql数据库提供了多种存储引擎。用户可以根据不同的需求为数据表选择不同的存储引擎，用户也可以根据自己的需要编写自己的存储引擎*

![ img](https://images2018.cnblogs.com/blog/1341090/201806/1341090-20180612152010504-366738745.png)

- **Mysql支持的存储引擎**：

  - Mysql 5.6支持的存储引擎包括InnoDB、MyISAM、MEMORY、CSV、BLACKHOLE、FEDERATED、MRG_MYISAM、ARCHIVE、PERFORMANCE_SCHEMA。其中NDB和InnoDB提供事务安全表，其他存储引擎都是非事务安全表。

    ```
    各种引擎具备的特性：
        并发性： 某些应用程序比其他应用程序具有很多的颗粒级锁定要求（如行级锁定）。
        事务支持：并非所有的应用程序都需要事务，但对的确需要事务的应用程序来说，有着定义良好的需求，如ACID 兼容等。
        引用完整性：通过DDL定义的外键，服务器需要强制保持关联数据库的引用完整性。
        物理存储：它包括各种各样的事项，从表和索引的总的页大小，到存储数据所需的格式，到物理磁盘。
        索引支持：不同的应用程序倾向于采用不同的索引策略，每种存储引擎通常有自己的编制索引方法，但某些索引方法（如B-tree索引）对几乎所有的存储引擎来说是共同的。
        内存高速缓冲：与其他应用程序相比，不同的应用程序对某些内存高速缓冲策略的响应更好，因此，尽管某些内存                高速缓冲对所有存储引擎来说是共同的（如用于用户连接的高速缓冲，MySQL的高速查询高速缓冲                  等），其他高速缓冲策略仅当使用特殊的存储引擎时才唯一定义。
        性能帮助：  包括针对并行操作的多I/O线程，线程并发性，数据库检查点，成批插入处理等。
        其他目标特性：可能包括对地理空间操作的支持，对特定数据处理操作的安全限制等。
        
    #引擎介绍：
    InnoDB
    MySql 5.6 版本默认的存储引擎。InnoDB 是一个事务安全的存储引擎，它具备提交、回滚以及崩溃恢复的功能以保护用户数据。InnoDB 的行级别锁定以及 Oracle 风格的一致性无锁读提升了它的多用户并发数以及性能。InnoDB 将用户数据存储在聚集索引中以减少基于主键的普通查询所带来的 I/O 开销。为了保证数据的完整性，InnoDB 还支持外键约束。
    
    MyISAM
    MyISAM既不支持事务、也不支持外键、其优势是访问速度快，但是表级别的锁定限制了它在读写负载方面的性能，因此它经常应用于只读或者以读为主的数据场景。
    
    Memory
    在内存中存储所有数据，应用于对非关键数据由快速查找的场景。Memory类型的表访问数据非常快，因为它的数据是存放在内存中的，并且默认使用HASH索引，但是一旦服务关闭，表中的数据就会丢失
    
    BLACKHOLE
    黑洞存储引擎，类似于 Unix 的 /dev/null，Archive 只接收但却并不保存数据。对这种引擎的表的查询常常返回一个空集。这种表可以应用于 DML 语句需要发送到从服务器，但主服务器并不会保留这种数据的备份的主从配置中。
    
    CSV
    它的表真的是以逗号分隔的文本文件。CSV 表允许你以 CSV 格式导入导出数据，以相同的读和写的格式和脚本和应用交互数据。由于 CSV 表没有索引，你最好是在普通操作中将数据放在 InnoDB 表里，只有在导入或导出阶段使用一下 CSV 表。
    
    NDB
    (又名 NDBCLUSTER)——这种集群数据引擎尤其适合于需要最高程度的正常运行时间和可用性的应用。注意：NDB 存储引擎在标准 MySql 5.6 版本里并不被支持。目前能够支持
    MySql 集群的版本有：基于 MySql 5.1 的 MySQL Cluster NDB 7.1；基于 MySql 5.5 的 MySQL Cluster NDB 7.2；基于 MySql 5.6 的 MySQL Cluster NDB 7.3。同样基于 MySql 5.6 的 MySQL Cluster NDB 7.4 目前正处于研发阶段。
    
    Merge
    允许 MySql DBA 或开发者将一系列相同的 MyISAM 表进行分组，并把它们作为一个对象进行引用。适用于超大规模数据场景，如数据仓库。
    
    Federated
    提供了从多个物理机上联接不同的 MySql 服务器来创建一个逻辑数据库的能力。适用于分布式或者数据市场的场景。
    
    #常用存储引擎及适用场景：
    	innoDB
    用于事务处理应用程序，支持外键和行级锁。如果应用对事物的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包括很多更新和删除操作，那么InnoDB存储引擎是比较合适的。InnoDB除了有效的降低由删除和更新导致的锁定，还可以确保事务的完整提交和回滚，对于类似计费系统或者财务系统等对数据准确要求性比较高的系统都是合适的选择。
    
    	MyISAM
    如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不高，那么可以选择这个存储引擎。
    
    	Memory
    将所有的数据保存在内存中，在需要快速定位记录和其他类似数据的环境下，可以提供极快的访问。Memory的缺陷是对表的大小有限制，虽然数据库因为异常终止的话数据可以正常恢复，但是一旦数据库关闭，存储在内存中的数据都会丢失。
    ```

    ![1556093154223](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556093154223.png)![1556093583921](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556093583921.png)

- 查看当前默认存储语句：

  ```mysql
  mysql> show variables like "default_storage_engine";  # 查看当前默认引擎
  
  mysql> show variables like '%storage_engine%';  # 当前正在使用的引擎
  
  mysql> show engines \G; #查询当前数据库支持的存储引擎
  
  mysql> create table t1(id int) engine=innodb;  #z 指定表数据/存储引擎
  
  mysql> alter table ai engine = innodb; #也可以修改引擎
  ```

- 配置文件中指定

  ```
  #my.ini文件
  [mysqld]
  default-storage-engine=INNODB
  ```



#### 三、数据类型

存储引擎决定了表的类型，而表内存放的数据也要有不同的类型，每种数据类型都有自己的宽度，但宽度是可选的，[详细参考](http://www.runoob.com/mysql/mysql-data-types.html)

- #### 一、数字类型：

  这些类型包括严格数值数据类型(INTEGER、SMALLINT、DECIMAL和NUMERIC)，以及近似数值数据类型(FLOAT、REAL和DOUBLE PRECISION)。

  MySQL支持的整数类型有TINYINT、MEDIUMINT和BIGINT。下面的表显示了需要的每个整数类型的存储和范围。

  对于小数的表示，MYSQL分为两种方式：浮点数和定点数。浮点数包括`float(单精度)和double(双精度)`,而定点数只有decimal一种，在mysql中以字符串的形式存放，比浮点数更精确，适合用来表示货币等精度高的数据。

  BIT数据类型保存位字段值，并且支持MyISAM、MEMORY、InnoDB和BDB表。

![1556095678045](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556095678045.png)

- int实列

  ```mysql
  # 创建表一个是默认宽度的int，一个是指定宽度的int(5)
  mysql> create table t1 (id1 int,id2 int(5));
  Query OK, 0 rows affected (0.02 sec)
  
  # 像t1中插入数据1，1
  mysql> insert into t1 values (1,1);
  Query OK, 1 row affected (0.01 sec)
  
  # 可以看出结果上并没有异常
  mysql> select * from t1;
  +------+------+
  | id1  | id2  |
  +------+------+
  |    1 |    1 |
  +------+------+
  1 row in set (0.00 sec)
  
  # 那么当我们插入了比宽度更大的值，会不会发生报错呢？
  mysql> insert into t1 values (111111,111111);
  Query OK, 1 row affected (0.00 sec)
  
  # 答案是否定的，id2仍然显示了正确的数值，没有受到宽度限制的影响
  mysql> select * from t1;
  +------------+--------+
  | id1        | id2    |
  +------------+--------+
  | 0000000001 |  00001 |
  | 0000111111 | 111111 |
  +------------+--------+
  
  # 修改id1字段 给字段添加一个unsigned表示无符号
  mysql> alter table t1 modify id1 int unsigned;
  Query OK, 0 rows affected (0.01 sec)
  Records: 0  Duplicates: 0  Warnings: 0
  
  mysql> desc t1;
  +-------+------------------+------+-----+---------+-------+
  | Field | Type             | Null | Key | Default | Extra |
  +-------+------------------+------+-----+---------+-------+
  | id1   | int(10) unsigned | YES  |     | NULL    |       |
  | id2   | int(5)           | YES  |     | NULL    |       |
  +-------+------------------+------+-----+---------+-------+
  
  # 当给id1添加的数据大于214748364时，可以顺利插入，因为id1使用的无符号化，就两倍
  mysql> insert into t1 values (2147483648,2147483647);
  Query OK, 1 row affected (0.00 sec)
  
  # 当给id2添加的数据大于214748364时，会报错
  mysql> insert into t1 values (2147483647,2147483648);
  ERROR 1264 (22003): Out of range value for column 'id2' at row 1
  ```

- 使用方法：

  ```
  int[(m)][unsigned][zerofill]   # tinyint 和 bigint 都是这种方法
  
  整数，数据类型用于保存一些范围的整数数值范围：
  有符号：
          -2147483648 ～ 2147483647
  无符号：
          0 ～ 4294967295
  ```

- tinyint的使用

  ```mysql
  #创建t1 规定x字段为tinyint数据类型（默认是有符号的）
  mysql> create table t1(x tinyint);    # 有符号化
  Query OK, 0 rows affected (0.27 sec)
  
  #验证，插入-1这个数
  mysql> insert into t1 values(-1);
  Query OK, 1 row affected (0.11 sec)
  
  #查询 表记录，查询成功（证明默认是有符号类型）
  mysql> select * from t1;
  +------+
  | x    |
  +------+
  |   -1 |
  +------+
  
  #执行如下操作，会发现报错。因为有符号范围在(-128,127)
  mysql> insert into t1 values(-129),(128);
  ERROR 1264 (22003): Out of range value for column 'x' at row 1
  
  #zerofill 用0填充
  mysql> create table t4(id int(5) unsigned zerofill);
  Query OK, 0 rows affected (0.31 sec)
  
  #插入1
  mysql> insert into t4 value(1);
  Query OK, 1 row affected (0.09 sec)
  
  #插入的记录是1，但是显示的宽度是00001
  mysql> select * from t4;
  +-------+
  | id    |
  +-------+
  | 00001 |
  +-------+
  
  注意：为该类型指定宽度时，仅仅只是指定查询结果的显示宽度，与存储范围无关，存储范围如下
  其实我们完全没必要为整数类型指定显示宽度，使用默认的就可以了
  默认的显示宽度，都是在最大值的基础上加1
  ```

- 小数实列

  ```mysql
  # 创建表的三个字段分别为float，double和decimal参数表示一共显示5位，小数部分占2位
  mysql> create table t2 (id1 float(5,2),id2 double(5,2),id3 decimal(5,2));
  Query OK, 0 rows affected (0.02 sec)
  
  # 向表中插入1.23，结果正常
  mysql> insert into t2 values (1.23,1.23,1.23);
  Query OK, 1 row affected (0.00 sec)
  
  mysql> select * from t2;
  +------+------+------+
  | id1  | id2  | id3  |
  +------+------+------+
  | 1.23 | 1.23 | 1.23 |
  +------+------+------+
  
  # 向表中插入1.234，会发现4都被截断了
  mysql> insert into t2 values (1.234,1.234,1.234);
  Query OK, 1 row affected, 1 warning (0.00 sec)
  
  mysql> select * from t2;
  +------+------+------+
  | id1  | id2  | id3  |
  +------+------+------+
  | 1.23 | 1.23 | 1.23 |
  | 1.23 | 1.23 | 1.23 |
  +------+------+------+
  
  # 向表中插入1.235发现数据虽然被截断，但是遵循了四舍五入的规则
  mysql> insert into t2 values (1.235,1.235,1.235);
  Query OK, 1 row affected, 1 warning (0.00 sec)
  
  mysql> select * from t2;
  +------+------+------+
  | id1  | id2  | id3  |
  +------+------+------+
  | 1.23 | 1.23 | 1.23 |
  | 1.23 | 1.23 | 1.23 |
  | 1.24 | 1.24 | 1.24 |
  +------+------+------+
  
  # 建新表去掉参数约束
  mysql> create table t3 (id1 float,id2 double,id3 decimal);
  Query OK, 0 rows affected (0.02 sec)
  
  # 分别插入1.234
  mysql> insert into t3 values (1.234,1.234,1.234);
  Query OK, 1 row affected, 1 warning (0.00 sec)
  
  # 发现decimal默认值是(10,0)的整数
  mysql> select * from t3;
  +-------+-------+------+
  | id1   | id2   | id3  |
  +-------+-------+------+
  | 1.234 | 1.234 |    1 |
  +-------+-------+------+
  
  # 当对小数位没有约束的时候，输入超长的小数，会发现float和double的区别
  mysql> insert into t3 values (1.2355555555555555555,1.2355555555555555555,1.2355555555555555555555);
  Query OK, 1 row affected, 1 warning (0.00 sec)
  
  mysql> select * from t3;
  +---------+--------------------+------+
  | id1     | id2                | id3  |
  +---------+--------------------+------+
  |   1.234 |              1.234 |    1 |
  | 1.23556 | 1.2355555555555555 |    1 |
  +---------+--------------------+------+
  ```

  

- **总结相关约束**

  ```mysql
    # float精确到小数点后5位
      # double能多精确一些位数,但仍然存在不精确的情况  
    # decimal默认是整数,但是通过设置,最多可以表示到小数点后30位 
  decimal部分    
    #M最大值为65，D最大值为30，超过不允许创建，D为小数位，M表示总位数
      mysql> create table t7(x decimal(66,31));
    ERROR 1425 (42000): Too big scale 31 specified for column 'x'. Maximum is 30.
  
      #超过M最大值，不允许创建
      mysql> create table t7(x decimal(66,30));
      ERROR 1426 (42000): Too-big precision 66 specified for 'x'. Maximum is 65.
  
      #M最大值为65，D最大值为30
      mysql> create table t7(x decimal(65,30));
      Query OK, 0 rows affected (0.29 sec)
  
  float 部分
      #D最大值为30，超过不允许创建
      mysql> create table t5(x float(256,31));
      ERROR 1425 (42000): Too big scale 31 specified for column 'x'. Maximum is 30.
  
      #M最大值为255，超过不允许创建
      mysql> create table t5(x float(256,30));
      ERROR 1439 (42000): Display width out of range for column 'x' (max = 255)
  
      #建表成功
      mysql> create table t5(x float(255,30));
      Query OK, 0 rows affected (0.56 sec)
  
  double 部分
      #M最大值为255，D最大值为30。在这个范围内，就可以创建
      mysql> create table t6(x double(255,30));
      Query OK, 0 rows affected (0.53 sec)
      
  #也就是说float、double最多可以支持255个数、decimal只能支持65个数字、他们之中的小数都是精确到30位
  ```
  
  
  
- #### 二、时间类型

  - 表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。每个时间类型有一个有效值范围和一个"零"值，当指定不合法的MySQL不能表示的值时使用"零"值。

    TIMESTAMP类型有专有的自动更新特性，将在后面描述。

    ![1556096842016](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556096842016.png)

  - datatime/time/data实列

    ```mysql
    #创建t9表
    mysql> create table t9(d date,t time,dt datetime);
    Query OK, 0 rows affected (0.28 sec)
    
    #查看表的结构
    mysql> desc t9;
    +-------+----------+------+-----+---------+-------+
    | Field | Type     | Null | Key | Default | Extra |
    +-------+----------+------+-----+---------+-------+
    | d     | date     | YES  |     | NULL    |       |
    | t     | time     | YES  |     | NULL    |       |
    | dt    | datetime | YES  |     | NULL    |       |
    +-------+----------+------+-----+---------+-------+
    3 rows in set (0.00 sec)
    
    #调用mysql自带的now()函数，获取当前类型指定的时间 如下结构
    mysql> insert into t9 values(now(),now(),now());
    Query OK, 1 row affected, 1 warning (0.10 sec)
    
    #查看表记录
    mysql> select * from t9;
    +------------+----------+---------------------+
    | d          | t        | dt                  |
    +------------+----------+---------------------+
    | 2018-06-12 | 16:38:43 | 2018-06-12 16:38:43 |
    +------------+----------+---------------------+
    
    mysql> insert into t4 values (null,null,null);
    Query OK, 1 row affected (0.01 sec)
    
    mysql> select * from t4;
    +------------+----------+---------------------+
    | d          | t        | dt                  |
    +------------+----------+---------------------+
    | 2018-09-21 | 14:51:51 | 2018-09-21 14:51:51 |
    | NULL       | NULL     | NULL                |
    +------------+----------+---------------------+
    
    mysql> create table t8 (dt datetime);
    Query OK, 0 rows affected (0.01 sec)
    
    mysql> insert into t8 values ('2018-9-26 12:20:10');
    Query OK, 1 row affected (0.01 sec)
    
    mysql> insert into t8 values ('2018/9/26 12+20+10');
    Query OK, 1 row affected (0.00 sec)
    
    mysql> insert into t8 values ('20180926122010'); # 要使用0补全
    Query OK, 1 row affected (0.00 sec)
    
    mysql> insert into t8 values (20180926122010);
    Query OK, 1 row affected (0.00 sec)
    
    mysql> select * from t8;
    +---------------------+
    | dt                  |
    +---------------------+
    | 2018-09-26 12:20:10 |
    | 2018-09-26 12:20:10 |
    | 2018-09-26 12:20:10 |
    | 2018-09-26 12:20:10 |
    +---------------------+
    ```

  - **year实列**

    ```mysql
    #无论year指定何种宽度，最后都默认是year(4)
    mysql> create table t8(born_year year);
    Query OK, 0 rows affected (0.34 sec)
    
    #插入失败，超出范围（1901/2155）
    mysql> insert into t8 values(1900),(1901),(2155),(2156);
    ERROR 1264 (22003): Out of range value for column 'born_year' at row 1
    
    #查看表记录
    mysql> select * from t8;
    Empty set (0.00 sec)
    
    #插入2条正常的值
    mysql> insert into t8 values(1905),(2018);
    Query OK, 2 rows affected (0.05 sec)
    Records: 2  Duplicates: 0  Warnings: 0
    
    #查看表记录
    mysql> select * from t8;
    +-----------+
    | born_year |
    +-----------+
    |      1905 |
    |      2018 |
    +-----------+
    ```

  - 总结

    ```
        # year
        # date      now(),20191010 '2019-01-01'
        # time      now(),121212    '12:12:12'
        # datetime  now(),20191010121212,'2019-01-01 12:12:12'  # 可自动给你分隔，但是必须有时分秒
        # timestamp
    
        # create table time1(y year,d date,t time);
        # create table time2(dt datetime,ts timestamp);
            # datetime  能表示的时间范围大  可以为空,没有默认值
            # timestamp 能表示的时间范围小  不能为空,默认值是当前时间
        # create table time2(dt datetime default current_timestamp,ts timestamp);
            # 人为设置datetime类型的默认值是当前时间
    ```

  

- #### 三、字符类型

  - 字符串类型指CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM和SET。该节描述了这些类型如何工作以及如何在查询中使用这些类型。

    ![1556097400313](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556097400313.png)

    

  -  `char类型：定长，简单粗暴，浪费空间，存取速度快，总的来说只是相对慢，当char的长度刚刚好，反而比varchar快`

    ```
    字符长度范围：0-255（一个中文是一个字符，是utf8编码的3个字节）
    存储：
        存储char类型的值时，会往右填充空格来满足长度
        例如：指定长度为10，存>10个字符则报错，存<10个字符则用空格填充直到凑够10个字符存储
     
    检索：
        在检索或者说查询时，查出的结果会自动删除尾部的空格，除非我们打开pad_char_to_full_length SQL模式（设置SQL模式：SET sql_mode = 'PAD_CHAR_TO_FULL_LENGTH';
    　　　　　　查询sql的默认模式：select @@sql_mode;）
    ```

  - `varchar类型：变长，精准，节省空间，存取速度慢`

    ```
    字符长度范围：0-65535（如果大于21845会提示用其他类型 。mysql行最大限制为65535字节，字符编码为utf-8：https://dev.mysql.com/doc/refman/5.7/en/column-count-limit.html）
    存储：
        varchar类型存储数据的真实内容，不会用空格填充，如果'ab  ',尾部的空格也会被存起来
        强调：varchar类型会在真实数据前加1-2Bytes的前缀，该前缀用来表示真实数据的bytes字节数（1-2Bytes最大表示65535个数字，正好符合mysql对row的最大字节限制，即已经足够使用）
        如果真实的数据<255bytes则需要1Bytes的前缀（1Bytes=8bit 2**8最大表示的数字为255）
        如果真实的数据>255bytes则需要2Bytes的前缀（2Bytes=16bit 2**16最大表示的数字为65535）
     
    检索：
        尾部有空格会保存下来，在检索或者说查询时，也会正常显示包含空格在内的内容
    ```

    官网解释：

    ![img](https://images2018.cnblogs.com/blog/1341090/201806/1341090-20180612165031075-556972696.png)

    *CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等方面也不同。在存储或检索过程中不进行大小写转换。CHAR列的长度固定为创建表是声明的长度,范围(0-255);而VARCHAR的值是可变长字符串范围(0-65535)。*

  - **varchar 和 char比较**

    ```mysql
    #创建t1表,分别指明字段x为char类型，字段y为varchar类型
    mysql> create table t1(x char(5),y varchar(4));
    Query OK, 0 rows affected (0.51 sec)
    
    #char存放的是5个字符，而varchar存4个字符。注意:你瞅呢后面是2个空格！
    mysql> insert into t1 values('你瞅呢  ','你瞅啥 ');
    Query OK, 1 row affected (0.10 sec)
    
    #在检索时char很不要脸地将自己浪费的2个字符给删掉了，装的好像自己没浪费过空间一样，而varchar很老实，存了多少，就显示多少
    mysql> select x,char_length(x),y,char_length(y) from t1;
    +-----------+----------------+------------+----------------+
    | x         | char_length(x) | y          | char_length(y) |
    +-----------+----------------+------------+----------------+
    | 你瞅呢    |              3 | 你瞅啥     |              4 |
    +-----------+----------------+------------+----------------+
    
    #查看当前mysql的mode模式
    mysql> select @@sql_mode;
    +--------------------------------------------+
    | @@sql_mode                                 |
    +--------------------------------------------+
    | STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION |
    +--------------------------------------------+
    
    #略施小计，让char现原形
    mysql> SET sql_mode = 'PAD_CHAR_TO_FULL_LENGTH';
    Query OK, 0 rows affected (0.07 sec)
    
    #再次查看mode模式
    mysql> select @@sql_mode;
    +-------------------------+
    | @@sql_mode              |
    +-------------------------+
    | PAD_CHAR_TO_FULL_LENGTH |
    +-------------------------+
    
    mysql> select x,length(x),y,length(y) from t1;
    +-------------+-----------+------------+-----------+
    | x           | length(x) | y          | length(y) |
    +-------------+-----------+------------+-----------+
    | 你瞅呢      |        11 | 你瞅啥     |        10 |
    +-------------+-----------+------------+-----------+
    ```

- #### 四、枚举类型和集合类型

  - ENUM中文名称叫枚举类型，它的值范围需要在创建表时通过枚举方式显示。ENUM**只允许从值集合中选取单个值，而不能一次取多个值**。
  - SET和ENUM非常相似，也是一个字符串对象，里面可以包含0-64个成员。根据成员的不同，存储上也有所不同。set类型可以**允许值集合中任意选择1或多个元素进行组合**。对超出范围的内容将不允许注入，而对重复的值将进行自动去重。

  ![1556097957313](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556097957313.png)

  - 举例子

    ```mysql
    mysql> create table t10 (name char(20),gender enum('female','male'));
    Query OK, 0 rows affected (0.01 sec)
    
    # 选择enum('female','male')中的一项作为gender的值，可以正常插入
    mysql> insert into t10 values ('nezha','male');
    Query OK, 1 row affected (0.00 sec)
    
    # 不能同时插入'male,female'两个值，也不能插入不属于'male,female'的值
    mysql> insert into t10 values ('nezha','male,female');
    ERROR 1265 (01000): Data truncated for column 'gender' at row 1
    
    mysql> create table t11 (name char(20),hobby set('抽烟','喝酒','烫头','翻车'));
    Query OK, 0 rows affected (0.01 sec)
    
    # 可以任意选择set('抽烟','喝酒','烫头','翻车')中的项，并自带去重功能
    mysql> insert into t11 values ('yuan','烫头,喝酒,烫头');
    Query OK, 1 row affected (0.01 sec)
    
    mysql> select * from t11;
    +------+---------------+
    | name | hobby        |
    +------+---------------+
    | yuan | 喝酒,烫头     |
    +------+---------------+
    
    # 不能选择不属于set('抽烟','喝酒','烫头','翻车')中的项，
    mysql> insert into t11 values ('alex','烫头,翻车,看妹子');
    ERROR 1265 (01000): Data truncated for column 'hobby' at row 1
    
    #设置默认值
    mysql> create table t4 (id int primary key, name varchar(12), sex enum('male','female') default 'male',  hobby set('music','basketball','swimming','sleep','drinking')) #设置默认值
    ```

    

#### 四、完整性约束

​	为了防止不符合规范的数据进入数据库，在用户对数据进行插入、修改、删除等操作时，DBMS自动按照一定的约束条件对数据进行监测，使不符合规范的数据不能进入数据库（也就是在见表时的有约束），以确保数据库中存储的数据正确、有效、相容。 约束条件与数据类型的宽度一样，都是可选参数，主要分为以下几种

```mysql
PRIMARY KEY (PK)    #标识该字段为该表的主键，可以唯一的标识记录
FOREIGN KEY (FK)    #标识该字段为该表的外键
NOT NULL            #标识该字段不能为空
UNIQUE KEY (UK)     #标识该字段的值是唯一的
AUTO_INCREMENT      #标识该字段的值自动增长（整数类型，而且为主键）
DEFAULT             #为该字段设置默认值
 
UNSIGNED             #无符号
ZEROFILL             #使用0填充

#1. 是否允许为空，默认NULL，可设置NOT NULL，字段不允许为空，必须赋值
#2. 字段是否有默认值，缺省的默认值是NULL，如果插入记录时不给字段赋值，此字段使用默认值
	sex enum('male','female') not null default 'male'
 	
#必须为正值（无符号） 不允许为空 默认是20
	age int unsigned NOT NULL default 20
# 3. 是否是key
    主键 primary key
    外键 foreign key
    索引 (index,unique...)
```

- **not null 与 default**

  ```mysql
  是否可空，null表示空，非字符串
      not null - 不可空
      null - 可空
  
  默认值，创建列时可以指定默认值，当插入数据时如果未主动设置，则自动添加默认值
  语法： create table tb1(
          nid int not null default 2,
          num int not null
      );
      
  #实列
  #id字段默认可以为空，未设置默认值
      mysql> create table t11(id int);
      Query OK, 0 rows affected (0.31 sec)
  
      #查看表结构
      mysql> desc t11;
      +-------+---------+------+-----+---------+-------+
      | Field | Type    | Null | Key | Default | Extra |
      +-------+---------+------+-----+---------+-------+
      | id    | int(11) | YES  |     | NULL    |       |
      +-------+---------+------+-----+---------+-------+
  
      #给t11表插一个空的值
      mysql> insert into t11 values();
      Query OK, 1 row affected (0.10 sec)
  
      #查询结果如下
      mysql> select * from t11;
      +------+
      | id   |
      +------+
      | NULL |
      +------+
  
  #设置not null，插入值时不能为空
  #设置字段id不为空
      mysql> create table t12(id int not null);
      Query OK, 0 rows affected (0.28 sec)
  
      #查看表结构
      mysql> desc t12;
      +-------+---------+------+-----+---------+-------+
      | Field | Type    | Null | Key | Default | Extra |
      +-------+---------+------+-----+---------+-------+
      | id    | int(11) | NO   |     | NULL    |       |
      +-------+---------+------+-----+---------+-------+
  
      #不能插入空
      mysql> insert into t12 values();
      ERROR 1364 (HY000): Field 'id' doesn't have a default value
      
      注意：如果插入成功,请查看当前mode模式是否为PAD_CHAR_TO_FULL_LENGTH
      #修改为默认值
      mysql> SET sql_mode = 'NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES';
      Query OK, 0 rows affected, 1 warning (0.00 sec)
  
      #再次查看mode
      mysql> select @@sql_mode;
      +--------------------------------------------+
      | @@sql_mode                                 |
      +--------------------------------------------+
      | STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION |
      +--------------------------------------------+
      1 row in set (0.00 sec)
  
      #再次插入空值，就会报错！
      mysql> insert into t12 values();
      ERROR 1364 (HY000): Field 'id' doesn't have a default value
  
  
  #给id字段设置默认值
      mysql> create table t13(id int default 1);  #default可以设置值
      Query OK, 0 rows affected (0.27 sec)
  
      #查看表结构
      mysql> desc t13;
      +-------+---------+------+-----+---------+-------+
      | Field | Type    | Null | Key | Default | Extra |
      +-------+---------+------+-----+---------+-------+
      | id    | int(11) | YES  |     | 1       |       |
      +-------+---------+------+-----+---------+-------+
  
      #插入一个空值
      mysql> insert into t13 values();
      Query OK, 1 row affected (0.09 sec)
  
      #查看表记录
      mysql> select * from t13;
      +------+
      | id   |
      +------+
      |    1 |
      +------+
  ```

- **unique 唯一性**：在mysql中称为**单列唯一**,注意：`unique只是约束在char数据类型内不能重复,但是不能约束null`

  ```mysql
  #道理很简单一个公司不能有两个相同的部门，一个年级不能有相同的班级
  
  #方式一
      create table department(
          id int,
          name char(10) unique
      );
  
      #插入2条记录，name是一样的会报错
      mysql> insert into department values(1,'it'),(2,'it');
      ERROR 1062 (23000): Duplicate entry 'it' for key 'name'
      
  #方式二
      create table department(
          id int,
          name char(10) ,
          unique(id),
          unique(name)    #使用括号
      );
  
      #插入2条记录
      mysql> insert into department values(1,'it'),(2,'sale');
      Query OK, 2 rows affected (0.09 sec)
      Records: 2  Duplicates: 0  Warnings: 0
      
      
  #联合唯一：也就是说你在你们班里可能重名，但是你不可能身份证、或则学号也重，这就是身份证和名字连在一起必须一个唯一
  #创建services表
  create table services(
      id int,
      ip char(15),
      port int,
      unique(id),
      unique(ip,port)     #联合唯一
  );
  
  #查看表结构,UNI表示唯一，MUL表示联合
  mysql> desc services;
  +-------+----------+------+-----+---------+-------+
  | Field | Type     | Null | Key | Default | Extra |
  +-------+----------+------+-----+---------+-------+
  | id    | int(11)  | YES  | UNI | NULL    |       |
  | ip    | char(15) | YES  | MUL | NULL    |       |
  | port  | int(11)  | YES  |     | NULL    |       |
  +-------+----------+------+-----+---------+-------+
  3 rows in set (0.00 sec)
  
  #插入3条记录
  #联合唯一，只要两列记录，有一列不同，既符合联合唯一的约束
  insert into services values
      (1,'192,168,11,23',80),
      (2,'192,168,11,23',81),
      (3,'192,168,11,25',80);
  
  #查看表记录
  mysql> select * from services;
  +------+---------------+------+
  | id   | ip            | port |
  +------+---------------+------+
  |    1 | 192,168,11,23 |   80 |
  |    2 | 192,168,11,23 |   81 |
  |    3 | 192,168,11,25 |   80 |
  +------+---------------+------+
  3 rows in set (0.00 sec)
  
  #插入一条记录,ip和port是已经存在的。那么就导致插入失败!
  mysql> insert into services values (4,'192,168,11,23',80);
  ERROR 1062 (23000): Duplicate entry '192,168,11,23-80' for key 'ip'
  ```

- not null 和 unique 的结合 ==> primary key，如果有多个not null +unique 则会选择第一个为主键

  ```mysql
  mysql> create table t1(id int not null unique);
  Query OK, 0 rows affected (0.02 sec)
  
  mysql> desc t1;
  +-------+---------+------+-----+---------+-------+
  | Field | Type    | Null | Key | Default | Extra |
  +-------+---------+------+-----+---------+-------+
  | id    | int(11) | NO   | PRI | NULL    |       |
  +-------+---------+------+-----+---------+-------+
  ```

- **primary key** 主键

  - 主键为了保证表中的每一条数据的该字段都是表格中的唯一值。换言之，它是用来独一无二地确认一个表格中的每一行数据。 主键可以包含一个字段或多个字段。当主键包含多个栏位时，称为组合键 (Composite [Key](https://www.baidu.com/s?wd=Key&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)),也可以叫联合主键。主键可以在建置新表格时设定 (运用 CREATE TABLE 语句)，或是以改变现有的表格架构方式设定 (运用 ALTER TABLE)。主键必须唯一，主键值非空；可以是单一字段，也可以是多字段组合。

  ```mysql
  一个表中可以：
      单列做主键
      多列做主键（复合主键）
      约束：等价于 not null unique,字段的值不为空且唯一
      存储引擎默认是（innodb）:对于innodb存储引擎来说，一张表必须有一个主键。
      一张表中只能有一个主键 : 主键从约束的角度上来说 就是非空且唯一的
      只不过,非空+唯一可以设置多个字段,但是主键只能给一个表中的一个字段设置
      
     
  #单列主键（单字段主键）
  ============单列做主键===============
      #方法一：not null+unique
      create table department1(
      id int not null unique, #主键
      name varchar(20) not null unique,
      comment varchar(100)
      );
  
      mysql> desc department1;
      +---------+--------------+------+-----+---------+-------+
      | Field   | Type         | Null | Key | Default | Extra |
      +---------+--------------+------+-----+---------+-------+
      | id      | int(11)      | NO   | PRI | NULL    |       |
      | name    | varchar(20)  | NO   | UNI | NULL    |       |
      | comment | varchar(100) | YES  |     | NULL    |       |
      +---------+--------------+------+-----+---------+-------+
  
      #方法二：在某一个字段后用primary key
      create table department2(
      id int primary key, #主键
      name varchar(20),
      comment varchar(100)
      );
  
      mysql> desc department2;
      +---------+--------------+------+-----+---------+-------+
      | Field   | Type         | Null | Key | Default | Extra |
      +---------+--------------+------+-----+---------+-------+
      | id      | int(11)      | NO   | PRI | NULL    |       |
      | name    | varchar(20)  | YES  |     | NULL    |       |
      | comment | varchar(100) | YES  |     | NULL    |       |
      +---------+--------------+------+-----+---------+-------+
  
      #方法三：在所有字段后单独定义primary key
      create table department3(
      id int,
      name varchar(20),
      comment varchar(100),
      primary key(id); #创建主键并为其命名pk_name
  
      mysql> desc department3;
      +---------+--------------+------+-----+---------+-------+
      | Field   | Type         | Null | Key | Default | Extra |
      +---------+--------------+------+-----+---------+-------+
      | id      | int(11)      | NO   | PRI | NULL    |       |
      | name    | varchar(20)  | YES  |     | NULL    |       |
      | comment | varchar(100) | YES  |     | NULL    |       |
      +---------+--------------+------+-----+---------+-------+
  
      # 方法四：给已经建成的表添加主键约束
      mysql> create table department4(
          -> id int,
          -> name varchar(20),
          -> comment varchar(100));
      Query OK, 0 rows affected (0.01 sec)
  
      mysql> desc department4;
      +---------+--------------+------+-----+---------+-------+
      | Field   | Type         | Null | Key | Default | Extra |
      +---------+--------------+------+-----+---------+-------+
      | id      | int(11)      | YES  |     | NULL    |       |
      | name    | varchar(20)  | YES  |     | NULL    |       |
      | comment | varchar(100) | YES  |     | NULL    |       |
      +---------+--------------+------+-----+---------+-------+
  
  
      mysql> alter table department4 modify id int primary key;
      Query OK, 0 rows affected (0.02 sec)
      Records: 0  Duplicates: 0  Warnings: 0
  
      mysql> desc department4;
      +---------+--------------+------+-----+---------+-------+
      | Field   | Type         | Null | Key | Default | Extra |
      +---------+--------------+------+-----+---------+-------+
      | id      | int(11)      | NO   | PRI | NULL    |       |
      | name    | varchar(20)  | YES  |     | NULL    |       |
      | comment | varchar(100) | YES  |     | NULL    |       |
      +---------+--------------+------+-----+---------+-------+
  
  #多字段主键 （联合主键）
      ==================多列做主键================
      create table service(
      ip varchar(15),
      port char(5),
      service_name varchar(10) not null,
      primary key(ip,port)
      );
  
  
      mysql> desc service;
      +--------------+-------------+------+-----+---------+-------+
      | Field        | Type        | Null | Key | Default | Extra |
      +--------------+-------------+------+-----+---------+-------+
      | ip           | varchar(15) | NO   | PRI | NULL    |       |
      | port         | char(5)     | NO   | PRI | NULL    |       |
      | service_name | varchar(10) | NO   |     | NULL    |       |
      +--------------+-------------+------+-----+---------+-------+
  
      mysql> insert into service values
          -> ('172.16.45.10','3306','mysqld'),
          -> ('172.16.45.11','3306','mariadb')
          -> ;
      Query OK, 2 rows affected (0.00 sec)
      Records: 2  Duplicates: 0  Warnings: 0
  
      mysql> insert into service values ('172.16.45.10','3306','nginx');  #也就是不能重复
      ERROR 1062 (23000): Duplicate entry '172.16.45.10-3306' for key 'PRIMARY'
  ```

- **auto_increment:** 约束字段为自动增长，被约束的字段必须同时被key约束,

  ```mysql
  #auto_increment 自增的条件(这一列必须是数字,这一列必须是uniuqe)
  #不指定id，则自动增长
      create table student(
      id int primary key auto_increment,
      name varchar(20),
      sex enum('male','female') default 'male'
      );
  
      mysql> desc student;
      +-------+-----------------------+------+-----+---------+----------------+
      | Field | Type                  | Null | Key | Default | Extra          |
      +-------+-----------------------+------+-----+---------+----------------+
      | id    | int(11)               | NO   | PRI | NULL    | auto_increment |
      | name  | varchar(20)           | YES  |     | NULL    |                |
      | sex   | enum('male','female') | YES  |     | male    |                |
      +-------+-----------------------+------+-----+---------+----------------+
      mysql> insert into student(name) values
          -> ('egon'),
          -> ('alex')
          -> ;
  
      mysql> select * from student;
      +----+------+------+
      | id | name | sex  |
      +----+------+------+
      |  1 | egon | male |
      |  2 | alex | male |
      +----+------+------+
  
  
  #也可以指定id
      mysql> insert into student values(4,'asb','female');
      Query OK, 1 row affected (0.00 sec)
  
      mysql> insert into student values(7,'wsb','female');
      Query OK, 1 row affected (0.00 sec)
  
      mysql> select * from student;
      +----+------+--------+
      | id | name | sex    |
      +----+------+--------+
      |  1 | egon | male   |
      |  2 | alex | male   |
      |  4 | asb  | female |
      |  7 | wsb  | female |
      +----+------+--------+
  
  
  #对于自增的字段，在用delete删除后，再插入值，该字段仍按照删除前的位置继续增长
      mysql> delete from student;
      Query OK, 4 rows affected (0.00 sec)
  
      mysql> select * from student;
      Empty set (0.00 sec)
  
      mysql> insert into student(name) values('ysb');
      mysql> select * from student;
      +----+------+------+
      | id | name | sex  |
      +----+------+------+
      |  8 | ysb  | male |
      +----+------+------+
  
  #应该用truncate清空表，比起delete一条一条地删除记录，truncate是直接清空表，在删除大表时用它
      mysql> truncate student;
      Query OK, 0 rows affected (0.01 sec)
  
      mysql> insert into student(name) values('egon');
      Query OK, 1 row affected (0.01 sec)
  
      mysql> select * from student;
      +----+------+------+
      | id | name | sex  |
      +----+------+------+
      |  1 | egon | male |
      +----+------+------+
      
  #delete from t1; #如果有自增id，新增的数据，仍然是以删除前的最后一样作为起始。
  #truncate table t1;数据量大，删除速度比上一条快，且直接从零开始。
      
  #设置偏移量
      查看可用的 开头auto_inc的词
      mysql> show variables like 'auto_inc%';
      +--------------------------+-------+
      | Variable_name            | Value |
      +--------------------------+-------+
      | auto_increment_increment | 1     |
      | auto_increment_offset    | 1     |
      +--------------------------+-------+
      rows in set (0.02 sec)
      # 步长auto_increment_increment,默认为1
      # 起始的偏移量auto_increment_offset, 默认是1
  
       # 设置步长 为会话设置，只在本次连接中有效
       set session auto_increment_increment=5;
  
       #全局设置步长 都有效。
       set global auto_increment_increment=5;
  
       # 设置起始偏移量
       set global  auto_increment_offset=3;
  
      #强调：If the value of auto_increment_offset is greater than that of auto_increment_increment, the value of auto_increment_offset is ignored. 
      翻译：如果auto_increment_offset的值大于auto_increment_increment的值，则auto_increment_offset的值会被忽略 
  
      # 设置完起始偏移量和步长之后，再次执行show variables like'auto_inc%';
      发现跟之前一样，必须先exit,再登录才有效。
  
      mysql> show variables like'auto_inc%';
      +--------------------------+-------+
      | Variable_name            | Value |
      +--------------------------+-------+
      | auto_increment_increment | 5     |
      | auto_increment_offset    | 3     |
      +--------------------------+-------+
      rows in set (0.00 sec)
  
      #因为之前有一条记录id=1
      mysql> select * from student;
      +----+---------+------+
      | id | name    | sex  |
      +----+---------+------+
      |  1 | xiaobai | male |
      +----+---------+------+
      row in set (0.00 sec)
      # 下次插入的时候，从起始位置3开始，每次插入记录id+5
      mysql> insert into student(name) values('ma1'),('ma2'),('ma3');
      Query OK, 3 rows affected (0.00 sec)
      Records: 3  Duplicates: 0  Warnings: 0
  
      mysql> select * from student;
      +----+---------+------+
      | id | name    | sex  |
      +----+---------+------+
      |  1 | xiaobai | male |
      |  3 | ma1     | male |
      |  8 | ma2     | male |
      | 13 | ma3     | male |
      +----+---------+------+
      auto_increment_increment和 auto_increment_offset
  
  ```

- foreign keyn (重点)

  - 之前我们存储了这样一张表

  - 外键的使用条件

    ```
    1.两个表必须是InnoDB表，MyISAM表暂时不支持外键
    2.外键列必须建立了索引，MySQL 4.1.2以后的版本在建立外键时会自动创建索引，但如果在较早的版本则需要显式建立；
    3.外键关系的两个表的列必须是数据类型相似，也就是可以相互转换类型的列，比如int和tinyint可以，而int和char则不可以；
    4、当设置字段为unique唯一字段时，设置该字段为外键成功
    5、被关联的字段必须是唯一的 （重点，也就是说关联从表必须时唯一得）
    ```

    ![1556101363260](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556101363260.png)

    假如公司有上万人，那么我们要对相同的部分重复存储，特别浪费空间，解决办法：我们可以定义一张部门表，然后让员工信息表与之关联，即foreign key：

    ![1556101605431](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556101605431.png)

```mysql
#1.创建表时先创建被关联表，再创建关联表，有个先后顺序，所以关联表创建外键
# 先创建被关联表（dep表）
    create table dep(
        id int primary key,
        name varchar(20) not null,
        descripe varchar(20) not null
    );

#再创建关联表（emp表）
    create table emp(
        id int primary key,
        name varchar(20) not null,
        age int not null,
        dep_id int,
        constraint fk_dep foreign key(dep_id) references dep(id) 
    );

#2.插入记录时，先往被关联表中插入记录，再往关联表中插入记录
    insert into dep values
    (1,'IT','IT技术有限部门'),
    (2,'销售部','销售部门'),
    (3,'财务部','花钱太多部门');

    insert into emp values
    (1,'zhangsan',18,1),
    (2,'lisi',19,1),
    (3,'egon',20,2),
    (4,'yuanhao',40,3),
    (5,'alex',18,2);

3.删除表
#按道理来说，删除了部门表中的某个部门，员工表的有关联的记录相继删除。
mysql> delete from dep where id=3;
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`db5`.`emp`, CONSTRAINT `fk_dep` FOREIGN KEY (`dep_id`) REFERENCES `dep` (`id`))

#但是先删除员工表的记录之后，再删除当前部门就没有任何问题，意思先删除被关联表
mysql> delete from emp where dep_id='3';
Query OK, 1 row affected (0.36 sec)

#查看员工表记录
mysql> select * from emp;
+----+----------+-----+--------+
| id | name     | age | dep_id |
+----+----------+-----+--------+
|  1 | zhangsan |  18 |      1 |
|  2 | lisi     |  19 |      1 |
|  3 | egon     |  20 |      2 |
|  5 | alex     |  18 |      2 |
+----+----------+-----+--------+

#删掉部门表中id为3的记录，也就是财务部
mysql> delete from dep where id=3;
Query OK, 1 row affected (0.09 sec)

#查看部分表，发现财务部没有了
mysql> select * from dep;
+----+-----------+----------------------+
| id | name      | descripe             |
+----+-----------+----------------------+
|  1 | IT        | IT技术有限部门       |
|  2 | 销售部    | 销售部门             |
+----+-----------+----------------------+

#创建外键的条件：当设置字段为unique唯一字段时，设置该字段为外键成功，或则主键


#级联删除和更新（主表）
上面的删除表记录的操作比较繁琐，按道理讲，裁掉一个部门，该部门的员工也会被裁掉。其实呢，在建表的时候还有个很重要的内容，叫同步删除，同步更新，也叫级联更新和级联删除

接下来：
    重复上面的操作建表
    注意：在关联表中加入
    on delete cascade #同步删除 也叫级联删除
    on update cascade #同步更新 也叫级联更新
    
#删除已存在的表emp和dep
mysql> drop table emp;
Query OK, 0 rows affected (0.19 sec)

mysql> drop table dep;
Query OK, 0 rows affected (0.19 sec)

#创建表dep，结构还是保持不变
create table dep(
    id int primary key,
    name varchar(20) not null,
    descripe varchar(20) not null
);

#创建表emp，结构改变了
create table emp(
    id int primary key,
    name varchar(20) not null,
    age int not null,
    dep_id int,
    constraint fk_dep foreign key(dep_id) references dep(id)  #注意写法fk_dep是取的一个名字
    on delete cascade   #创建级联删除
    on update cascade    #创建级联更新，作用于被关联表，主表
);

#重新插入记录
insert into dep values
(1,'IT','IT技术有限部门'),
(2,'销售部','销售部门'),
(3,'财务部','花钱太多部门');

insert into emp values
(1,'zhangsan',18,1),
(2,'lisi',19,1),
(3,'egon',20,2),
(4,'yuanhao',40,3),
(5,'alex',18,2)

#查看效果
#再去删被关联表(dep)的记录，关联表(emp)中的记录也跟着删除
mysql> delete from dep where id=3;
Query OK, 1 row affected (0.09 sec)

#查看表记录，发现财务部删除了
mysql> select * from dep;
+----+-----------+----------------------+
| id | name      | descripe             |
+----+-----------+----------------------+
|  1 | IT        | IT技术有限部门       |
|  2 | 销售部    | 销售部门             |
+----+-----------+----------------------+

#查看员工表记录，dep_id为3的记录都没有了
mysql> select * from emp;
+----+----------+-----+--------+
| id | name     | age | dep_id |
+----+----------+-----+--------+
|  1 | zhangsan |  18 |      1 |
|  2 | lisi     |  19 |      1 |
|  3 | egon     |  20 |      2 |
|  5 | alex     |  18 |      2 |
+----+----------+-----+--------+

#注意：
constraint 英文表示约束，后面的名字可以随便。约定成俗，以fk_开头，后面是关联的字段
references 英文表示标记，后面是被关键表。dep(id)，表示dep表里面的id字段
     建立外键时必须两边都是唯一的
级联时：
      . cascade方式
    在父表上update/delete记录时，同步update/delete掉子表的匹配记录 
       . set null方式
    在父表上update/delete记录时，将子表上匹配记录的列设为null
    要注意子表的外键列不能为not null  
       . No action方式
    如果子表中有匹配的记录,则不允许对父表对应候选键进行update/delete操作  
       . Restrict方式
    同no action, 都是立即检查外键约束
       . Set default方式
    父表有变更时,子表将外键列设置成一个默认的值 但Innodb不能识别
```



#### 五、表的操作和介绍

​	表就相当于文件，表中的一条记录就相当于文件的一行内容，不同的是，表中的一条记录有对应的标题，称为表的字段，还记得我们之前写过的‘员工信息表作业’么？存储这员工信息的文件是这样的：

![1556093810338](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556093810338.png)

- 创建表语法：

  ```mysql
  create table 表名(
  字段名1 类型[(宽度) 约束条件],
  字段名2 类型[(宽度) 约束条件],
  字段名3 类型[(宽度) 约束条件]
  );
  
  #注意：
  1. 在同一张表中，字段名是不能相同
  2. 宽度和约束条件可选，也就是我们要学到的完整性约束等
  3. 字段名和类型是必须的
  
  #示例：
  mysql> create database staff;
  Query OK, 1 row affected (0.00 sec)
  
  mysql> use staff;
  Database changed
  mysql> create table staff_info (id int,name varchar(50),age int(3),sex enum('male','female'),phone bigint(11),job varchar(11));
  Query OK, 0 rows affected (0.02 sec)
  
  mysql> show tables;
  +-----------------+
  | Tables_in_staff |
  +-----------------+
  | staff_info      |
  +-----------------+
  
  mysql> desc staff_info;   #查看表的详细信息，也可以使用 show create table staff_info\G   不加；
                         #describe [tablename];这种方法和desc [tablename];效果相同；可以查看当前的表结构
  +-------+-----------------------+------+-----+---------+-------+
  | Field | Type                  | Null | Key | Default | Extra |
  +-------+-----------------------+------+-----+---------+-------+
  | id    | int(11)               | YES  |     | NULL    |       |
  | name  | varchar(50)           | YES  |     | NULL    |       |
  | age   | int(3)                | YES  |     | NULL    |       |
  | sex   | enum('male','female') | YES  |     | NULL    |       |
  | phone | bigint(11)            | YES  |     | NULL    |       |
  | job   | varchar(11)           | YES  |     | NULL    |       |
  +-------+-----------------------+------+-----+---------+-------+
  
  mysql> select id,name,sex from staff_info;
  Empty set (0.00 sec)
  
  mysql> select * from staff_info;
  Empty set (0.00 sec)
  ```

- 向表里插入数据

  ```mysql
  mysql> insert into staff_info (id,name,age,sex,phone,job) values   (1,'Alex',83,'female',13651054608,'IT');  # 当只插入一个数据的时候就用value；
  Query OK, 1 row affected (0.00 sec)
  
  mysql> insert into staff_info values (2,'Egon',26,'male',13304320533,'Teacher');
  Query OK, 1 row affected (0.00 sec)
  
  mysql> insert into staff_info values (3,'nezha',25,'male',13332353222,'IT'),(4,'boss_jin',40,'male',13332353333,'IT');
  Query OK, 2 rows affected (0.00 sec)
  Records: 2  Duplicates: 0  Warnings: 0     # Duplicates 重复的
  
  mysql> select * from staff_info;
  +------+----------+------+--------+-------------+---------+
  | id   | name     | age  | sex    | phone       | job     |
  +------+----------+------+--------+-------------+---------+
  |    1 | Alex     |   83 | female | 13651054608 | IT      |
  |    2 | Egon     |   26 | male   | 13304320533 | Teacher |
  |    3 | nezha    |   25 | male   | 13332353222 | IT      |
  |    4 | boss_jin |   40 | male   | 13332353333 | IT      |
  +------+----------+------+--------+-------------+---------+
  ```

- 复制表

  ```mysql
  mysql> create table b1 select * from db2.a1;   #从数据库db2中复制a1
  
  mysql> create table b3 like db2.a1;     #只复制db2.a1的表结构
  ```

- 删除表

  ```mysql
  mysql> drop table db3.b3;   #删除表
  ```

- #### 修改表结构

  ![1556094639922](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556094639922.png)

  -  alter操作非空和唯一

    ```mysql
    create table t(id int unique,name char(10) not null);
    
    # 去掉null约束
    	alter table t modify name char(10) null;
    # 添加null约束
    	alter table t modify name char(10) not null;
    
    # 去掉unique约束
    	alter table t drop index id;
    # 添加unique约束
    	alter table t modify id int unique;
    
    alter处理null和unique约束
    ```

  - alter 操作主键

    ```mysql
    1、首先创建一个数据表table_test：
        create table table_test(
            id varchar(100) NOT NULL,
            name varchar(100) NOT NULL,
            PRIMARY KEY (name)
        ); 
    2、如果发现主键设置错了,应该是id是主键,但如今表里已经有好多数据了,不能删除表再重建了，仅仅能在这基础上改动表结构。
    先删除主键
    	alter table table_test drop primary key;
    然后再增加主键
    	alter table table_test add primary key(id);
    注:在增加主键之前,必须先把反复的id删除掉。
    ```

  - **为表添加外键**

    ```mysql
    创建press表
        CREATE TABLE press (
          id int(11) NOT NULL,
          name char(10) DEFAULT NULL,
          PRIMARY KEY (`id`)
        ) ；
    
    创建book表
        CREATE TABLE book (
          id int(11) DEFAULT NULL,
          bk_name char(12) DEFAULT NULL,
          press_id int(11) NOT NULL,
          KEY `press_id` (`press_id`)
        ) ；
    
    为book表添加外键
    	alter table book add constraint fk_id foreign key(press_id) references press(id);
    
    删除外键
    	alter table book drop foreign key fk_id;
    	
    添加级联外键：
      	alter table orders add foreign key(user_id) references user(user_id) on delete cascade on update cascade;
        
    ```
  
  ------
  
  
  
  ####  表的具体相关操作：
  
  ```mysql
  
    # 表重命名
        mysql> alter table staff_info rename staff;
        Query OK, 0 rows affected (0.00 sec)
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+-------+
        | Field | Type                  | Null | Key | Default | Extra |
        +-------+-----------------------+------+-----+---------+-------+
        | id    | int(11)               | YES  |     | NULL    |       |
        | name  | varchar(50)           | YES  |     | NULL    |       |
        | age   | int(3)                | YES  |     | NULL    |       |
        | sex   | enum('male','female') | YES  |     | NULL    |       |
        | phone | bigint(11)            | YES  |     | NULL    |       |
        | job   | varchar(11)           | YES  |     | NULL    |       |
        +-------+-----------------------+------+-----+---------+-------+
    #删除一列数据
    
    delete from 表名 where 条件
    delete from user where id = 1;
    
    # 删除sex列
        mysql> alter table staff drop sex;
        Query OK, 0 rows affected (0.02 sec)
        Records: 0  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-------------+------+-----+---------+-------+
        | Field | Type        | Null | Key | Default | Extra |
        +-------+-------------+------+-----+---------+-------+
        | id    | int(11)     | YES  |     | NULL    |       |
        | name  | varchar(50) | YES  |     | NULL    |       |
        | age   | int(3)      | YES  |     | NULL    |       |
        | phone | bigint(11)  | YES  |     | NULL    |       |
        | job   | varchar(11) | YES  |     | NULL    |       |
        +-------+-------------+------+-----+---------+-------+
    
    # 添加列
        mysql> alter table staff add sex enum('male','female');
        Query OK, 0 rows affected (0.03 sec)
        Records: 0  Duplicates: 0  Warnings: 0
    
    # 修改id的宽度
        mysql> alter table staff modify id int(4); #modify 旧字段 新属性
        Query OK, 0 rows affected (0.02 sec)
        Records: 0  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+-------+
        | Field | Type                  | Null | Key | Default | Extra |
        +-------+-----------------------+------+-----+---------+-------+
        | id    | int(4)                | YES  |     | NULL    |       |
        | name  | varchar(50)           | YES  |     | NULL    |       |
        | age   | int(3)                | YES  |     | NULL    |       |
        | phone | bigint(11)            | YES  |     | NULL    |       |
        | job   | varchar(11)           | YES  |     | NULL    |       |
        | sex   | enum('male','female') | YES  |     | NULL    |       |
        +-------+-----------------------+------+-----+---------+-------+
    
    # 修改name列的字段名
        mysql> alter table staff change name sname varchar(20); #chang 旧字段 新
        Query OK, 4 rows affected (0.03 sec)
        Records: 4  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+-------+
        | Field | Type                  | Null | Key | Default | Extra |
        +-------+-----------------------+------+-----+---------+-------+
        | id    | int(4)                | YES  |     | NULL    |       |
        | sname | varchar(20)           | YES  |     | NULL    |       |
        | age   | int(3)                | YES  |     | NULL    |       |
        | phone | bigint(11)            | YES  |     | NULL    |       |
        | job   | varchar(11)           | YES  |     | NULL    |       |
        | sex   | enum('male','female') | YES  |     | NULL    |       |
        +-------+-----------------------+------+-----+---------+-------+
    
    # 修改sex列的位置
        mysql> alter table staff modify sex enum('male','female') after sname; #这个after是排序的
        Query OK, 0 rows affected (0.02 sec)
        Records: 0  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+-------+
        | Field | Type                  | Null | Key | Default | Extra |
        +-------+-----------------------+------+-----+---------+-------+
        | id    | int(4)                | YES  |     | NULL    |       |
        | sname | varchar(20)           | YES  |     | NULL    |       |
        | sex   | enum('male','female') | YES  |     | NULL    |       |
        | age   | int(3)                | YES  |     | NULL    |       |
        | phone | bigint(11)            | YES  |     | NULL    |       |
        | job   | varchar(11)           | YES  |     | NULL    |       |
        +-------+-----------------------+------+-----+---------+-------+
        6 rows in set (0.00 sec)
    
    # 创建自增id主键
        mysql> alter table staff modify id int(4) primary key auto_increment;
        Query OK, 4 rows affected (0.02 sec)
        Records: 4  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+----------------+
        | Field | Type                  | Null | Key | Default | Extra          |
        +-------+-----------------------+------+-----+---------+----------------+
        | id    | int(4)                | NO   | PRI | NULL    | auto_increment |
        | sname | varchar(20)           | YES  |     | NULL    |                |
        | sex   | enum('male','female') | YES  |     | NULL    |                |
        | age   | int(3)                | YES  |     | NULL    |                |
        | phone | bigint(11)            | YES  |     | NULL    |                |
        | job   | varchar(11)           | YES  |     | NULL    |                |
        +-------+-----------------------+------+-----+---------+----------------+
    
    # 删除主键，可以看到删除一个自增主键会报错
        mysql> alter table staff drop primary key;
        ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it       must be defined as a key
    
    # 需要先去掉主键的自增约束，然后再删除主键约束
        mysql> alter table staff modify id int(11);
        Query OK, 4 rows affected (0.02 sec)
        Records: 4  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+-------+
        | Field | Type                  | Null | Key | Default | Extra |
        +-------+-----------------------+------+-----+---------+-------+
        | id    | int(11)               | NO   | PRI | 0       |       |
        | sname | varchar(20)           | YES  |     | NULL    |       |
        | sex   | enum('male','female') | YES  |     | NULL    |       |
        | age   | int(3)                | YES  |     | NULL    |       |
        | phone | bigint(11)            | YES  |     | NULL    |       |
        | job   | varchar(11)           | YES  |     | NULL    |       |
        +-------+-----------------------+------+-----+---------+-------+
    
        mysql> alter table staff drop primary key;
        Query OK, 4 rows affected (0.06 sec)
        Records: 4  Duplicates: 0  Warnings: 0
    
    # 添加联合主键
        mysql> alter table staff add primary key (sname,age);
        Query OK, 0 rows affected (0.02 sec)
        Records: 0  Duplicates: 0  Warnings: 0
    
    # 删除主键
        mysql> alter table staff drop primary key;
        Query OK, 4 rows affected (0.02 sec)
        Records: 4  Duplicates: 0  Warnings: 0
    
    # 创建主键id
        mysql> alter table staff add primary key (id);
        Query OK, 0 rows affected (0.02 sec)
        Records: 0  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+-------+
        | Field | Type                  | Null | Key | Default | Extra |
        +-------+-----------------------+------+-----+---------+-------+
        | id    | int(11)               | NO   | PRI | 0       |       |
        | sname | varchar(20)           | NO   |     |         |       |
        | sex   | enum('male','female') | YES  |     | NULL    |       |
        | age   | int(3)                | NO   |     | 0       |       |
        | phone | bigint(11)            | YES  |     | NULL    |       |
        | job   | varchar(11)           | YES  |     | NULL    |       |
        +-------+-----------------------+------+-----+---------+-------+
    
    # 为主键添加自增属性
        mysql> alter table staff modify id int(4) auto_increment;
        Query OK, 4 rows affected (0.02 sec)
        Records: 4  Duplicates: 0  Warnings: 0
    
        mysql> desc staff;
        +-------+-----------------------+------+-----+---------+----------------+
        | Field | Type                  | Null | Key | Default | Extra          |
        +-------+-----------------------+------+-----+---------+----------------+
        | id    | int(4)                | NO   | PRI | NULL    | auto_increment |
        | sname | varchar(20)           | NO   |     |         |                |
        | sex   | enum('male','female') | YES  |     | NULL    |                |
        | age   | int(3)                | NO   |     | 0       |                |
      | phone | bigint(11)            | YES  |     | NULL    |                |
        | job   | varchar(11)           | YES  |     | NULL    |                |
      +-------+-----------------------+------+-----+---------+----------------+
    
   #删除表：`drop table 表名`
  ```
  
  

#### 六、多表结构的创建与分析

- 如何找到两张表之间的关系

  ```
  
  分析步骤：
  #1、先站在左表的角度去找
  是否左表的多条记录可以对应右表的一条记录，如果是，则证明左表的一个字段foreign key 右表一个字段（通常是id）
  
  #2、再站在右表的角度去找
  是否右表的多条记录可以对应左表的一条记录，如果是，则证明右表的一个字段foreign key 左表一个字段（通常是id）
  
  #3、总结：
  #多对一：
  如果只有步骤1成立，则是左表多对一右表
  如果只有步骤2成立，则是右表多对一左表
  
  #多对多
  如果步骤1和2同时成立，则证明这两张表时一个双向的多对一，即多对多,需要定义一个这两张表的关系表来专门存放二者的关系
  
  #一对一:
  如果1和2都不成立，而是左表的一条记录唯一对应右表的一条记录，反之亦然。这种情况很简单，就是在左表foreign key右表的基础上，将左表的外键字段设置成unique即可
  ```

  

例题：创建有一个学生、老师、班级的数据库，关系如下：

![1556103251958](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556103251958.png)

```mysql
#班级表，#这里的comment只能在show create table class 时能看见，提示
CREATE TABLE class (
  cid int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '班级id', 
  caption varchar(32) DEFAULT NULL COMMENT '班级', 
  PRIMARY KEY (`cid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='班级表';

#学生表
CREATE TABLE student (
  sid int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '学生id',  #要么unique要么下面主键设置
  sname varchar(32) DEFAULT NULL,
  gender enum('女','男') DEFAULT '男' COMMENT '性别',
  class_id int(11) unsigned NOT NULL COMMENT '班级id',
  PRIMARY KEY (`sid`),
  constraint fk_class foreign key(class_id) references class(cid)
  on delete cascade  #级联删除
  on update cascade
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='学生表';

#老师表
CREATE TABLE teacher (
  tid int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '老师id',
  tname varchar(32) NOT NULL COMMENT '老师名字',
  PRIMARY KEY (`tid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='老师表';

#课程表
CREATE TABLE course (
  cid int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '课程id',
  cname varchar(32) NOT NULL COMMENT '课程名',
  tearch_id int(11) unsigned NOT NULL COMMENT '老师id',
  PRIMARY KEY (`cid`),
  constraint fk_tearch foreign key(tearch_id) references teacher(tid)
  on delete cascade 
  on update cascade 
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='课程表';

#成绩表
CREATE TABLE score (
  sid int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '成绩id',
  student_id int(11) unsigned NOT NULL COMMENT '学生id',
  corse_id int(11) unsigned NOT NULL COMMENT '课程id',
  number int(11) NOT NULL DEFAULT '0' COMMENT '成绩',
  PRIMARY KEY (`sid`),
  constraint fk_score_student foreign key(student_id) references student(sid)
  on delete cascade 
  on update cascade,
  constraint fk_score_corse foreign key(corse_id) references course(cid)
  on delete cascade 
  on update cascade
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='成绩表';
```

每日回顾

```perl
# 存储引擎
    # innodb : 外键  行级锁(并发修改)  事务(客户管理系统)
    # myisam : 表级锁 不支持外键\事务\行级锁
    # memory : 只能在内存中存储数据 重启server数据丢失
# mysql中的基础数据类型
    # 数字
        # int         id/age/部门编号
        # float(8,2)  salary
    # 字符串
        # char    定长  越是长度固定char越节省空间    读写速度快
                    # 名字 部门名
        # varchar 变长  越是长度不固定varchar越节省空间  读写速度慢
                    # 部门描述
    # 时间
        # year
        # date       入职日期  离职 开学 毕业
        # time
        # datetime   出生日期 交易记录 打卡时间
        # timestamp
    # enum 和 set
        # enum 单选
            # enum('male','female')
        # set 多选(去重)
# 完整性约束
    # id int unsigned
    # id int default 0
    # id int not null
    # id int unique
    # auto_increment 相当于非空+自增且只能用于整数类型，本身要求唯一unique
        # id int unique auto_increment
        # id int primary key auto_increment
    # 非空 + 唯一
        # id int unique not null 如果没有主键,第一个设置非空唯一的就是主键
    # 联合唯一
        # id int,
        # name char(12),
        # unique(id,name)
    # primary key  主键 一张表只能有一个主键
        # id int primary key
    # foreign key  外键
        # id int,
        # name char(12),
        # tid int,
        # foreign key(tid) references 外表(字段名) on update cascade on delete cascade
# 表的操作
    # 创建表
        # create table 表名(
        #   字段名 类型(长度约束)  其他约束,
        #   字段名 类型(长度约束)  其他约束,
        #   字段名 类型(长度约束)  其他约束);
    # 删除表
        # drop table 表名
    # 修改表
        # alter table 表名 rename 新表名;
        #                  add    新字段  类型(长度约束)  其他约束 first;
        #                  drop   字段 ;
        #                  modify 原字段名  新类型(新长度) 新约束 after 某字段;
        #                  change 原字段名  新字段名 新类型(新长度) 新约束;
    # 查看表结构
        # desc 表名 == describe 表名
        # show create table 表名; 查看详细表结构,存储引擎 编码 更复杂的约束条件


```

