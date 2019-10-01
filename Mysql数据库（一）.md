## Mysql数据库（一）

在之前按写的选课系统或则是ftp项目中，我们都引用了db这个文件夹，那么数据量太大，或则需要快速对文件进行操作时，单一的文件已经无法进行处理，这是就引出了我们的数据库，数据库是属于C/S架构

- 数据库：是一个可以在一台机器上独立工作的，并且可以给我们提供高效、便捷的方式对数据进行增删改查的一种工具。


#### 一、数据库概述：

- ##### 什么是数据（Date）：

  ```
  描述事物的符号记录称为数据，描述事物的符号既可以是数字，也可以是文字、图片，图像、声音、语言等，数据由多种表现形式，它们都可以经过数字化后存入计算机。
   
  在计算机中描述一个事物，就需要抽取这一事物的典型特征，组成一条记录，就相当于文件里的一行内容，如：
  xiaomage,male,18,1999,山东,计算机系,2017,oldboy
  ```

  - 单纯的一条记录并没有任何意义，如果我们按逗号作为分隔，依次定义各个字段的意思，相当于定义表的标题

    ![img](https://images2018.cnblogs.com/blog/1341090/201806/1341090-20180611143240376-406940200.png)

    这样我们就能很清晰的看清楚一条命令所描述的信息了

- 什么是数据库（DateBase，简称**DB**）：

  - 数据库即存放数据的仓库，只不过这个仓库是在计算机存储设备上，而且数据是按一定的格式存放的，过去人们将数据存放在文件柜里，现在数据量庞大，已经不再适用；数据库是长期存放在计算机内、有组织、可共享的数据即可。；数据库中的数据按一定的数据模型组织、描述和储存，具有较小的冗余度、较高的数据独立性和易扩展性，并可为各种 用户共享。

- 什么是数据库管理系统（DateBase Management System 简称**DBMS**）：

  - 在了解了Data与DB的概念后，如何科学地组织和存储数据，如何高效获取和维护数据成了关键，这就用到了一个系统软件---数据库管理系统，其实是一个软件

    如MySQL、Oracle、SQLite、Access、MS SQL Server

    mysql：主要用于大型门户，例如搜狗、新浪等，它主要的优势就是开放源代码，因为开放源代码这个数据库是		免费的，他现在是甲骨文公司的产品。
    oracle：  主要用于银行、铁路、飞机场等。该数据库功能强大，软件费用高。也是甲骨文公司的产品。
    sql server：是微软公司的产品，主要应用于大中型企业，如联想、方正等。

    ```
    数据库管理软件分两大类：
    　　关系型：如sqllite，db2，oracle，access，sql server，MySQL，注意：sql语句通用
    　　非关系型：mongodb，redis，memcache
     
    可以简单的理解为：
        关系型数据库需要有表结构
        非关系型数据库是key-value存储的，没有表结构
    ```

- 数据库服务器：

  - 服务器：本质就是一台电脑；当一台计算机上安装了某个软件能够对外提供服务的时候,那么这台机器就成为服务器
  - 数据库服务器：这台机器上安装的服务是一个数据库的server端的时候,我们就得到了一台数据库服务器

- 什么是数据库管理员（DateBase Administrator,简称：DBA）

  - 也就是管理数据库的工作人员，往往涉及到数据库的结构安全和规划。

- 数据库的优势

  ```
  1.程序稳定性 ：这样任意一台服务所在的机器崩溃了都不会影响数据和另外的服务。
  
  2.数据一致性 ：所有的数据都存储在一起，所有的程序操作的数据都是统一的，就不会出现数据不一致的现象
  
  3.并发 ：数据库可以良好的支持并发，所有的程序操作数据库都是通过网络，而数据库本身支持并发的网络操作，不需要我们自己写socket
  
  4.效率 ：使用数据库对数据进行增删改查的效率要高出我们自己处理文件很多
  ```

- 数据库的特点

  ```
  数据库系统的特点：
      1 数据结构化（如上图odboy_stu）
      2 数据共享，冗余度低，易扩充
      3 数据独立性高
      4 数据由DBMS统一管理和控制
        a：数据的安全性保护
        b：数据的完整性检查
        c：并发控制
        d：数据库恢复
  ```

  

#### 二、Mysql数据库的安装和配置

- [linux版本](https://www.cnblogs.com/Eva-J/articles/9664401.html)
- [mac版本](https://www.cnblogs.com/Eva-J/articles/9664401.html)
- [windows版本](https://www.cnblogs.com/Eva-J/articles/9664401.html)

需要注意的是，安装的时候如果遇到下列情况：

原因是MRVCR.dll无法找到，是系统缺少插件，[可以下载插件安装即可](https://www.cnblogs.com/Eva-J/articles/9664401.html)

如果实在找不到原因，或则各种无法解决的问题，那么可以在网上下载VC运行库，这个库为我们虚拟了一个Mysql管理系统，我们可以直接用。

```mysql
# mysqld install
    # mysqld.exe install  要安装mysql的server端
    # net start mysql 启动server端
    # mysql -uroot -p 启动client端

# 重启server
    # net stop mysql
    # net start mysql
```



#### 三、Mysql配置和登陆

刚安装的mysql后:

- 1、打开终端，使用root登陆，输入：mysql -uroot -p;回车，最开始是没有密码的直接回车就行。

  - 查看当前用户

    ```mysql
    select user();  #查看当前用户
    ```

  - 退出

    ```mysql
    exit     # 也可以用\q quit退出
    ```

- 管理员为root(拥有最高权限,管理员账号）,密码为空，**以无密码的方式登录了管理员账号，是非常危险的一件事情，所以要为管理员账号设置密码**

  - 设置root用户密码

    ```mysql
    mysql>  set password = password('root'); # 给当前数据库设置密码,这是在mysql内部设置，最初登陆，用此命令可以给root创建密码
    
    windonws> mysqladmin -uroot -p password "123"  #设置初始密码 由于原密码为空，因此-p可以不用
    ```

  - 修改root用户密码

    ```mysql
    windows> mysqladmin -uroot -p"123" password "456"  #讲旧密码修改为456
    
    忘记mysql密码：
        1、以管理员身份打开cmd；
        2、net stop mysql 关掉mysql
        3、执行下列命令跳过授权表：
            C:\WINDOWS\system32> mysqld --skip-grant-tables　#执行完后，窗口会挂起，先别关闭
        4、新开一个窗口登陆：mysql -uroot  # 此时会跳过密码直接登陆
        5、使用select user()   # 查看当前用户
        6、现在可以任意的更改密码，执行下面的代码，讲密码设置为空：
            update mysql.user set authentication_string = password('') where user = 'root';
        7、刷新权限，执行下面的命令:
            flush privileges;
        8、关闭所有窗口，（退出mysql，关闭挂起的窗口）；新开个窗口让用户加载权限，以管理员身份进入cmd，查看        当前mysql进程
            tasklist |findstr mysql  #查看当前mysql的进程
        9、杀死当前的进程，（当然也可以在任务管理器中关闭）
            taskkill /F /PID 6052  # 杀死当前的进程pid
        10、重启mysql，使用新密码登陆
        
     修改密码：
     	给root用户设置新密码：mysql> update user set password=password("123456") where user="root";提示：Query OK, 1 rows affected (0.04 sec)Rows matched: 1 Changed: 1 Warnings: 0
    	刷新数据库mysql> flush privileges;
    
    ```

- 统一字符编码：有时mysql的配置默认字符编码不是utf8，此时就要我们手动切换

  - 进入mysql界面：执行\s,可以查看相应的配置信息

    ![1555931230628](C:\Users\wanglixing\AppData\Roaming\Typora\typora-user-images\1555931230628.png)

    - 我们在my.ini文件中配置信息，直接保存，重启即可（在MySQL文件中默认有个ini文件，但是里面是一些提示信息，就是指导我们自己写）

      ```mysql
      [mysql]
      # 设置mysql客户端默认字符集
      default-character-set=utf8
      [mysqld]
      #设置3306端口
      port = 3306
      # 设置mysql的安装目录
      basedir=D:\mysql\mysql-5.6.43-winx64
      # 设置mysql数据库的数据的存放目录
      datadir=D:\mysql\mysql-5.6.43-winx64\data
      # 允许最大连接数
      max_connections=200
      # 服务端使用的字符集默认为8比特编码的latin1字符集
      character-set-server=utf8
      # 创建新表时将使用的默认存储引擎
      default-storage-engine=INNODB
      #下面设置了直接mysql时登陆的用户和密码
    user=root
      password=456
      ```
  
  

#### 四、基本的使用方法

```python
#进入mysql客户端
$mysql
mysql> select user();  #查看当前用户
mysql> exit     # 也可以用\q quit退出

# 默认用户登陆之后并没有实际操作的权限
# 需要使用管理员root用户登陆
$ mysql -uroot -p   # mysql5.6默认是没有密码的
#遇到password直接按回车键
mysql> set password = password('root'); # 给当前数据库设置密码

# 创建账号
mysql> create user 'eva'@'192.168.10.%'   IDENTIFIED BY '123';  # 指示网段 相应网段的人可以访问，且给用户配置了密码
mysql> create user 'eva'@'192.168.10.5'   # 指示某机器可以连接
mysql> create user 'eva'@'%'                    #指示所有机器都可以连接  
mysql> show grants for 'eva'@'192.168.10.5';查看某个用户的权限 
# 远程登陆
$ mysql -uroot -p123 -h 192.168.10.3 -p

# 给账号授权
mysql> grant all on *.* to 'eva'@'%';
mysql> flush privileges;    # 刷新使授权立即生效

# 创建账号并授权
mysql> grant all on *.* to 'eva'@'%' identified by '123' 

# grant 权利 on 数据库名.表名 to '用户名'@'ip地址' identified by '123'; 创建某个密码的用户有对某文件的某权力
           #(权利 : SELECT INSERT UPDATE DELETE ALL)

# select * from performance_schema.users;   #可以查看管理员用户
```

   设想一下，当我们想要从文件中存取数据的时候，是一个非常繁琐的过程，主要是因为文件中所有的内容对我们来说是连续的，没有规则的。如果我们将数据按照规则存在一个文件中，在设计一种规则可以拼凑组合成我们需要的操作，并通过这些指示在文件中存取数据，那么操作数据是不是能够变得更加简单快速呢？这串规则就被我们成为SQL。

　　SQL : 结构化查询语言(Structured Query Language)简称SQL(发音：/ˈes kjuː ˈel/ "S-Q-L")，是一种特殊目的的编程语言，是一种数据库查询和[程序设计语言](https://baike.baidu.com/item/程序设计语言/2317999)，用于存取数据以及查询、更新和管理[关系数据库系统](https://baike.baidu.com/item/关系数据库系统)

　　SQL语言主要用于存取数据、查询数据、更新数据和管理关系数据库系统,SQL语言由IBM开发。SQL语言分为3种类型：

　　1、`DDL语句` 数据库定义语言： 数据库、表、视图、索引、存储过程，例如CREATE DROP ALTER

　　2、`DML语句` 数据库操纵语言： 插入数据INSERT、删除数据DELETE、更新数据UPDATE、查询数据SELECT

　　3、`DCL语句` 数据库控制语言： 例如控制用户的访问权限GRANT、REVOKE

- #### 操作文件夹（库）

  - 增 ： create database 文件名； （create database db1 charset utf8; #这里可以指定编码）

    ![img](https://images2018.cnblogs.com/blog/1341090/201806/1341090-20180611194134585-772186164.png) 

  - 查 ： 

    -  查看当前创建的数据库

      ```mysql
      show create database db1;  #也就是查看对应库里的详细信息
      ```

    - 查看所有的数据库mysql

      ```mysql
      show databases;
      ```

      ![1555932270793](C:\Users\wanglixing\AppData\Roaming\Typora\typora-user-images\1555932270793.png)

  - 改：`alter database db1 charset gbk;`   #这里修改的是文件的属性，如编码等

    ![img](https://images2018.cnblogs.com/blog/1341090/201806/1341090-20180611194228557-993132538.png)

  - 删： `drop database db1;`    #删除某个文件（库）

  

- #### 操作文件（表）：

  - 切换文件夹：

    ```mysql
    use db1;               #切换文件夹
    
    select database();     #查看当前所在文件夹
    ```

  - ###### 增：create table t1(id int,name char);  # 创建一个文件，里面具有id ；name 属性且带有相应的字节类型，还可以指定长度如：char(6)……

    ![img](https://images2018.cnblogs.com/blog/1341090/201806/1341090-20180611194545156-1238645850.png)

  - **查**：这里是查找文件夹里面文件，当我们进入文件后，需要查看里免得内容时，使用show tables，查看详细信息是desc 表名。

    ```mysql
    #查看当前的这张t1表
    show create table t1;
     
    # 查看所有的表
    show tables;
     
    # 查看表的详细信息 ，有什么属性，什么字段
    desc t1;
    ```

    ![1555933655154](C:\Users\wanglixing\AppData\Roaming\Typora\typora-user-images\1555933655154.png)

  - **改**： 修改文件内某个表内属性（字段）

    ```mysql
    # modify修改的意思
    	alter table t1 modify name char(6);
    # 改变name为大写的NAME
    	alter table t1 change name NAME char(7);
    ```

  - **删除表**：

    ```mysql
    drop table t1;
    ```

- #### 操作具体内容（表内）

  - 增：对表内每个属性添加值

    ```mysql
    # 插入3条数据，规定id，name数据，一次可以插入多个，插入一条使用value
    	insert t1(id,name) values(1,"mjj01"),(2,"mjj02"),(3,"mjj03");  
    
    #使用set
    	insert into t1 set id=4,name='wang';
    ```

    ![img](https://images2018.cnblogs.com/blog/1341090/201806/1341090-20180611195103585-1813884867.png)

  - 查：查找表内的某些字段，返回主目录 >use mysql

    ```mysql
    #只显示id字段：
    	select id from db1.t1;
    #执行显示2个字段
    	select id,name from db1.t1;<br>
    #查询所有字段
    	select * from db1.t1;
    ```
    
  - 改：通过条件修改数据

    ```
    #修改表中的所有行，name等于zhang。非常危险，小心使用
    	update db1.t1 set name='zhang';
    #修改id为2的行，name等于alex。推荐使用！
    	update db1.t1 set name='alex' where id=2;
    ```

  - 删：删除某个数据

    ```
    #清空表<br>delete from t1;<br>#删除表
    	delete from t1 where id=2;
    ```

    




#### 安装Mysql遇到的坑

- 环境缺失：

  - MRVCR.dll 缺失，上面有一种资源，也可能是VC++ 15未安装

  - MRVCR100.dll，重装了系统之后可能缺失文件库 ，可以下载Directx Repair 软件自动补全，可以取laomo中下载

  - 如果在mysqld --install 时发现无法启用服务名，问题在于不是管理员身份运行

  - 成功安装后，启动时报错1067：

    ```
    1、在网上用了很多方式，都不管用，最后删除了data文件下的两个文件：ib_logfile0 和 ib_logfile1，然后就可以了
    
    2、配置文件修改：my-default.ini文件
    [mysql]
    # 设置mysql客户端默认字符集
    default-character-set=utf8
    [mysqld]
    #设置3306端口
    port = 3306
    # 设置mysql的安装目录
    basedir=D:\mysql\mysql-5.6.43-winx64
    # 设置mysql数据库的数据的存放目录
    datadir=D:\mysql\mysql-5.6.43-winx64\data
    # 允许最大连接数
    max_connections=200
    # 服务端使用的字符集默认为8比特编码的latin1字符集
    character-set-server=utf8
    # 创建新表时将使用的默认存储引擎
    default-storage-engine=MYISAM
    #下面设置了直接mysql时登陆的用户和密码
    user=root
    password=456
    ```

  - 重装[mysql](https://www.jb51.net/article/129809.htm)

- 注意：

  ```python
  λ mysql -uroot -p
  Enter password: ***
  ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
   #我们第一次不用输入密码，直接回车就是了
  ```

  