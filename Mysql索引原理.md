## Mysql索引原理

#### 一、索引的介绍

- 索引：数据库中专门用于帮助用户快速查找数据的一种数据结构。类似于字典中的目录，查找字典内容时可以根据目录查找到数据的存放位置吗，然后直接获取。

- 索引的作用：约束和加速查找

- 常见的几种索引：

  ```
  单列：普通索引，唯一索引，主键索引
  多列：联合索引（多列），比如：联合主键索引、联合唯一索引、联合普通索引（联合索引，也称之为组合索引）
  ```

- 为什么要有索引？（读需求高于写）

  ```
  一般的应用系统，读写比例在10:1左右，而且插入操作和一般的更新操作很少出现性能问题，在生产环境中，我们遇到最多的，也是最容易出问题的，还是一些复杂的查询操作，因此对查询语句的优化显然是重中之重。说起加速查询，就不得不提到索引了。
  ```

- 什么是索引?

  ```
  索引在MySQL中也叫是一种“键”，是存储引擎用于快速找到记录的一种数据结构。索引对于良好的性能
  非常关键，尤其是当表中的数据量越来越大时，索引对于性能的影响愈发重要。
  索引优化应该是对查询性能优化最有效的手段了。索引能够轻易将查询性能提高好几个数量级。
  索引相当于字典的音序表，如果要查某个字，如果不使用音序表，则需要从几百页中逐页去查。
  ```

  看看索引的威力：

  ![1556346584870](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556346584870.png)

- 索引的利弊

  ```
  索引是应用程序设计和开发的一个重要方面。若索引太多，应用程序的性能可能会受到影响。而索引太少，对查询性能又会产生影响，要找到一个平衡点，这对应用程序的性能至关重要。
  ```

- 无索引和有索引的区别以及建立的目的

  ```
  无索引：从前往后一条一条查询，默认还是会生成一个查询结构
  有索引：创建索引的本质，就是创建额外的文件（某种格式存储，查询的时候，先去格外的文件找，定好位置，然后再去原		 始表中直接查询。但是创建索引越多，会对硬盘也是有损耗。
  
  目的：
      a.额外的文件保存特殊的数据结构
      b.查询快，但是插入更新删除依然慢
      c.创建索引之后，必须命中索引才能有效 （重点）
  ```

- 索引的种类：

  ```
  hash索引和BTree索引：
      （1）hash类型的索引：查询单条快，范围查询慢
      （2）btree类型的索引：b+树，层数越多，数据量指数级增长（我们就用它，因为innodb默认支持它）
      
  #我们可以在创建上述索引的时候，为其指定索引类型，分两类
      hash类型的索引：查询单条快，范围查询慢
      btree类型的索引：b+树，层数越多，数据量指数级增长（我们就用它，因为innodb默认支持它）
  
  #不同的存储引擎支持的索引类型也不一样
      InnoDB 支持事务，支持行级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引；
      MyISAM 不支持事务，支持表级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引；
      Memory 不支持事务，支持表级别锁定，支持 B-tree、Hash 等索引，不支持 Full-text 索引；
      NDB 支持事务，支持行级别锁定，支持 Hash 索引，不支持 B-tree、Full-text 等索引；
      Archive 不支持事务，支持表级别锁定，不支持 B-tree、Hash、Full-text 等索引；	
  ```

  



#### 二、索引的原理

​	简单的举个例子：引是应用程序设计和开发的一个重要方面。若索引太多，应用程序的性能可能会受到影响。而索引太少，对查询性能又会产生影响，要找到一个平衡点，这对应用程序的性能至关重要（相当于标签）。总之：索引的目的在于提高查询效率。

​	**索引本质都是：通过不断地缩小想要获取数据的范围来筛选出最终想要的结果，同时把随机的事件变成顺序的事件，也就是说，有了这种索引机制，我们可以总是用同一种查找方式来锁定数据。**

​	数据库也是一样，但显然要复杂的多，因为不仅面临着等值查询，还有范围查询(>、<、between、in)、模糊查询(like)、并集查询(or)等等。数据库应该选择怎么样的方式来应对所有的问题呢？我们回想字典的例子，能不能把数据分成段，然后分段查询呢？最简单的如果1000条数据，1到100分成第一段，101到200分成第二段，201到300分成第三段......这样查第250条数据，只要找第三段就可以了，一下子去除了90%的无效数据。但如果是1千万的记录呢，分成几段比较好？稍有算法基础的同学会想到搜索树，其平均复杂度是lgN，具有不错的查询性能。但这里我们忽略了一个关键的问题，复杂度模型是基于每次相同的操作成本来考虑的。而数据库实现比较复杂，一方面数据是保存在磁盘上的，另外一方面为了提高性能，每次又可以把部分数据读入内存来计算，因为我们知道访问磁盘的成本大概是访问内存的十万倍左右，所以简单的搜索树难以满足复杂的应用场景

- **磁盘IO与预读**

  - 前面提到了访问磁盘，那么这里先简单介绍一下磁盘IO和预读，磁盘读取数据靠的是机械运动，每次读取数据花费的时间可以分为寻道时间、旋转延迟、传输时间三个部分，寻道时间指的是磁臂移动到指定磁道所需要的时间，主流磁盘一般在5ms以下；旋转延迟就是我们经常听说的磁盘转速，比如一个磁盘7200转，表示每分钟能转7200次，也就是说1秒钟能转120次，旋转延迟就是1/120/2 = 4.17ms；传输时间指的是从磁盘读出或将数据写入磁盘的时间，一般在零点几毫秒，相对于前两个时间可以忽略不计。那么访问一次磁盘的时间，即一次磁盘IO的时间约等于5+4.17 = 9ms左右，听起来还挺不错的，但要知道一台500 -MIPS（Million Instructions Per Second）的机器每秒可以执行5亿条指令，因为指令依靠的是电的性质，换句话说执行一次IO的时间可以执行约450万条指令，数据库动辄十万百万乃至千万级数据，每次9毫秒的时间，显然是个灾难。下图是计算机硬件延迟的对比图，供大家参考：

    ![1556347549306](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556347549306.png)

    考虑到磁盘IO是非常高昂的操作，计算机操作系统做了一些优化，**当一次IO时，不光把当前磁盘地址的数据，而是把相邻的数据也都读取到内存缓冲区内**，因为局部预读性原理告诉我们，当计算机访问一个地址的数据的时候，与其相邻的数据也会很快被访问到。每一次IO读取的数据我们称之为一页(page)。具体一页有多大数据跟操作系统有关，一般为4k或8k，也就是我们读取一页内的数据时候，实际上才发生了一次IO，这个理论对于索引的数据结构设计非常有帮助

#### 三、索引的数据结构

- 树

  > ```
  > 	树状图是一种数据结构，它是由n（n>=1）个有限结点组成一个具有层次关系的集合。把它叫做“树”是因为它看起来像一棵倒挂的树，也就是说它是根朝上，而叶朝下的。
  > 	它具有以下的特点：每个结点有零个或多个子结点；没有父结点的结点称为根结点；每一个非根结点有且只有一个父结点；除了根结点外，每个子结点可以分为多个不相交的子树
  > ```

  ![1556348301357](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556348301357.png)

- B+树

  > ```
  > 	前面讲了索引的基本原理，数据库的复杂性，又讲了操作系统的相关知识，目的就是让大家了解，任何一种数据结构都不是凭空产生的，一定会有它的背景和使用场景，我们现在总结一下，我们需要这种数据结构能够做些什么，其实很简单，那就是：每次查找数据时把磁盘IO次数控制在一个很小的数量级，最好是常数数量级。那么我们就想到如果一个高度可控的多路搜索树是否能满足需求呢？就这样，b+树应运而生（B+树是通过二叉查找树，再由平衡二叉树，B树演化而来）
  > ```

  ![1556349095779](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556349095779.png)

  - B+树性质

    > ```
    > 	1.索引字段要尽量的小：通过上面的分析，我们知道IO次数取决于b+数的高度h，假设当前数据表的数据为N，每个磁盘块的数据项的数量是m，则有h=㏒(m+1)N，当数据量N一定的情况下，m越大，h越小；而m = 磁盘块的大小 / 数据项的大小，磁盘块的大小也就是一个数据页的大小，是固定的，如果数据项占的空间越小，数据项的数量越多，树的高度越低。这就是为什么每个数据项，即索引字段要尽量的小，比如int占4字节，要比bigint8字节少一半。这也是为什么b+树要求把真实的数据放到叶子节点而不是内层节点，一旦放到内层节点，磁盘块的数据项会大幅度下降，导致树增高。当数据项等于1时将会退化成线性表。
    > 	2.索引的最左匹配特性：当b+树的数据项是复合的数据结构，比如(name,age,sex)的时候，b+数是按照从左到右的顺序来建立搜索树的，比如当(张三,20,F)这样的数据来检索的时候，b+树会优先比较name来确定下一步的所搜方向，如果name相同再依次比较age和sex，最后得到检索的数据；但当(20,F)这样的没有name的数据来的时候，b+树就不知道下一步该查哪个节点，因为建立搜索树的时候name就是第一个比较因子，必须要先根据name来搜索才能知道下一步去哪里查询。比如当(张三,F)这样的数据来检索时，b+树可以用name来指定搜索方向，但下一个字段age的缺失，所以只能把名字等于张三的数据都找到，然后再匹配性别是F的数据了， 这个是非常重要的性质，即索引的最左匹配特性。
    > ```

#### 四、聚集索引与辅助索引（B+树的）

​	**在数据库中，B+树的高度一般都在2~4层，这也就是说查找某一个键值的行记录时最多只需要2到4次IO，这倒不错。因为当前一般的机械硬盘每秒至少可以做100次IO，2~4次的IO意味着查询时间只需要0.02~0.04秒。**

**数据库中的B+树索引可以分为聚集索引（clustered index）和辅助索引（secondary index）**

**聚集索引与辅助索引相同的是：**

​	**不管是聚集索引还是辅助索引，其内部都是B+树的形式，即高度是平衡的，叶子结点存放着所有的数据。**

**聚集索引与辅助索引不同的是： 叶子结点存放的是否是一整行的信息**

- 聚集索引

  > ```
  > #InnoDB存储引擎表是索引组织表，即表中数据按照主键顺序存放。
  > 而聚集索引（clustered index）就是按照每张表的主键构造一棵B+树，同时叶子结点存放的即为整张表的行记录数据，也将聚集索引的叶子结点称为数据页。
  > 聚集索引的这个特性决定了索引组织表中数据也是索引的一部分。同B+树数据结构一样，每个数据页都通过一个双向链表来进行链接。
  >     
  > #如果未定义主键，MySQL取第一个唯一索引（unique）而且只含非空列（NOT NULL）作为主键，InnoDB使用它作为聚簇索引。
  >     
  > #如果没有这样的列，InnoDB就自己产生一个这样的ID值，它有六个字节，而且是隐藏的，使其作为聚簇索引。（也就是没有unique这属性
  > 
  > #由于实际的数据页只能按照一棵B+树进行排序，因此每张表只能拥有一个聚集索引。
  > 在多数情况下，查询优化器倾向于采用聚集索引。因为聚集索引能够在B+树索引的叶子节点上直接找到数据。
  > 此外由于定义了数据的逻辑顺序，聚集索引能够特别快地访问针对范围值得查询。
  > 
  > ```

  ![1556350690184](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556350690184.png)

  - 聚集索引的好处：

    ```mysql
    	1、它对主键的排序查找和范围查找速度非常快，叶子节点的数据就是用户所要查询的数据。如用户需要查找一张表，查询最后的10位用户信息，由于B+树索引是双向链表，所以用户可以快速找到最后一个数据页，并取出10条记录。
    	2、范围查询（range query），即如果要查找主键某一范围内的数据，通过叶子节点的上层中间节点就可以得到页的范围，之后直接读取数据页即可
    	
    mysql> desc s1;
    +--------+-------------+------+-----+---------+-------+
    | Field  | Type        | Null | Key | Default | Extra |
    +--------+-------------+------+-----+---------+-------+
    | id     | int(11)     | NO   |     | NULL    |       |
    | name   | varchar(20) | YES  |     | NULL    |       |
    | gender | char(6)     | YES  |     | NULL    |       |
    | email  | varchar(50) | YES  |     | NULL    |       |
    +--------+-------------+------+-----+---------+-------+
    rows in set (0.12 sec)
    
    mysql> explain select * from s1 where id > 1 and id < 1000000; #没有聚集索引，预估需要检索的rows数如下，也就是说没有检索到索引，所以没有时间差
    +----+-------------+-------+------------+------+---------------+------+---------+------+---------+----------+-------------+
    | id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows    | filtered | Extra       |
    +----+-------------+-------+------------+------+---------------+------+---------+------+---------+----------+-------------+
    |  1 | SIMPLE      | s1    | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 2690100 |    11.11 | Using where |
    +----+-------------+-------+------------+------+---------------+------+---------+------+---------+----------+-------------+
    row in set, 1 warning (0.00 sec)
    
    mysql> alter table s1 add primary key(id);
    Query OK, 0 rows affected (16.25 sec)
    Records: 0  Duplicates: 0  Warnings: 0
    
    mysql> explain select * from s1 where id > 1 and id < 1000000; #有聚集索引，预估需要检索的rows数如下，整理索引
    +----+-------------+-------+------------+-------+---------------+---------+---------+------+---------+----------+-------------+
    | id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref  | rows    | filtered | Extra       |
    +----+-------------+-------+------------+-------+---------------+---------+---------+------+---------+----------+-------------+
    |  1 | SIMPLE      | s1    | NULL       | range | PRIMARY       | PRIMARY | 4       | NULL | 1343355 |   100.00 | Using where |
    +----+-------------+-------+------------+-------+---------------+---------+---------+------+---------+----------+-------------+
    row in set, 1 warning (0.09 sec)
    
    #最开始没有添加主键时 type=ALL，说明全部查找了，最后经过 添加主键使得type=range，而且possible_key表明命中了主键
    ```

    ![1556352303370](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556352303370.png)

    

- 辅助索引

  - 表中除了聚集索引外其他索引都是辅助索引（Secondary Index，也称为非聚集索引），与聚集索引的区别是：辅助索引的叶子节点不包含行记录的全部数据。

    ​	叶子节点除了包含键值以外，每个叶子节点中的索引行中还包含一个书签（bookmark）。该书签用来告诉InnoDB存储引擎去哪里可以找到与索引相对应的行数据。由于InnoDB存储引擎是索引组织表，因此InnoDB存储引擎的辅助索引的书签就是相应行数据的聚集索引键。如下图：

![1556352482102](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556352482102.png)

> ```
> 辅助索引的存在并不影响数据在聚集索引中的组织，因此每张表上可以有多个辅助索引，但只能有一个聚集索引。当通过辅助索引来寻找数据时，InnoDB存储引擎会遍历辅助索引并通过叶子级别的指针获得只想主键索引的主键，然后再通过主键索引来找到一个完整的行记录。
> 
> 举例来说，如果在一棵高度为3的辅助索引树种查找数据，那需要对这个辅助索引树遍历3次找到指定主键，如果聚集索引树的高度同样为3，那么还需要对聚集索引树进行3次查找，最终找到一个完整的行数据所在的页，因此一共需要6次逻辑IO访问才能得到最终的一个数据页。
> ```

- 总结：聚集索引和辅助索引的区别

  ```
  聚集索引
      1.纪录的索引顺序与无力顺序相同
         因此更适合between and和order by操作
      2.叶子结点直接对应数据
         从中间级的索引页的索引行直接对应数据页
      3.每张表只能创建一个聚集索引
  
  非聚集索引
      1.索引顺序和物理顺序无关
      2.叶子结点不直接指向数据页
      3.每张表可以有多个非聚集索引，需要更多磁盘和内容
         多个索引会影响insert和update的速度
  
  ```

  

#### 五、Mysql索引管理

- 功能：

  > ```
  > 1. 索引的功能就是加速查找
  > 2. mysql中的primary key，unique，联合唯一也都是索引，这些索引除了加速查找以外，还有约束的功能
  > ```

- Mysql常用的索引

  ```
  普通索引INDEX：加速查找
  
  唯一索引：
      -主键索引PRIMARY KEY：加速查找+约束（不为空、不能重复）
      -唯一索引UNIQUE:加速查找+约束（不能重复）
  
  联合索引：
      -PRIMARY KEY(id,name):联合主键索引
      -UNIQUE(id,name):联合唯一索引
      -INDEX(id,name):联合普通索引
  ```

- 创建/删除索引的语法

![1556352870752](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556352870752.png)

#### 六、测试索引

- 准备：创建3000000条数据

  ```mysql
  #1. 准备表
  create table s1(
  id int,
  name varchar(20),
  gender char(6),
  email varchar(50)
  );
  
  #2. 创建存储过程，实现批量插入记录
  delimiter $$ #声明存储过程的结束符号为$$
  create procedure auto_insert1()
  BEGIN
      declare i int default 1;
      while(i<3000000)do
          insert into s1 values(i,'eva','female',concat('eva',i,'@oldboy'));
          set i=i+1;
      end while;
  END$$ #$$结束
  delimiter ; #重新声明分号为结束符号
  
  #3. 查看存储过程
  show create procedure auto_insert1\G 
  
  #4. 调用存储过程
  call auto_insert1();
  ```

- 在没有索引的前提下测试查询速度

  ```mysql
  #无索引：mysql根本就不知道到底是否存在id等于333333333的记录，只能把数据表从头到尾扫描一遍，此时有多少个磁盘块就需要进行多少IO操作，所以查询速度很慢
  mysql> select * from s1 where id=333333333;
  Empty set (0.33 sec)
  ```

- 在表中已经存在大量数据的前提下，为某个字段建立索引，建立速度会很慢

  ![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913163337125-480382090.png)

- 在索引建立完毕后，以该字段为查询条件时，查询速度提升明显

  ![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913171928047-457783306.png)

  > ```
  > #1. 一定是为搜索条件的字段创建索引，比如select * from s1 where id = 333;就需要为id加上索引
  > 
  > #2. 在表中已经有大量数据的情况下，建索引会很慢，且占用硬盘空间，建完后查询速度加快
  > 比如create index idx on s1(id);会扫描表中所有的数据，然后以id为数据项，创建索引结构，存放于硬盘的表中。
  > 建完以后，再查询就会很快了。
  > 
  > #3. 需要注意的是：innodb表的索引会存放于s1.ibd文件中，而myisam表的索引则会有单独的索引文件table1.MYI
  > MySAM索引文件和数据文件是分离的，索引文件仅保存数据记录的地址。而在innodb中，表数据文件本身就是按照B+Tree（BTree即Balance True）组织的一个索引结构，这棵树的叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键，因此innodb表数据文件本身就是主索引。
  > 因为inndob的数据文件要按照主键聚集，所以innodb要求表必须要有主键（Myisam可以没有），如果没有显式定义，则mysql系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则mysql会自动为innodb表生成一个隐含字段作为主键，这字段的长度为6个字节，类型为长整型.
  > ```

  

#### 七、正确使用索引

- 索引未命中：**并不是说我们创建了索引就一定会加快查询速度，**若想利用索引达到预想的提高查询速度的效果，我们在添加索引时，必须遵循以下问题

  - **1 范围问题，或者说条件不明确，条件中出现这些符号或关键字：**>、>=、<、<=、!= 、between...and...、like， 大于号、小于号。

    ![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913173142047-877845727.png)

    ![img](C:\Users\RootUser\Desktop\知识点复习\1036857-20170913173317000-2056548532.png)

    ![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913173514032-289009705.png)

    ![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913174115844-1107691992.png)

  - **2 尽量选择区分度高的列作为索引,区分度的公式是count(distinct col)/count(\*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录**

![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913180335203-1305806986.png)

​		分析上图原因：

> ```
> 	我们编写存储过程为表s1批量添加记录，name字段的值均为egon，也就是说name这个字段的区分度很低（gender字段也是一样的，我们稍后再搭理它），回忆b+树的结构，查询的速度与树的高度成反比，要想将树的高低控制的很低，需要保证：在某一层内数据项均是按照从左到右，从小到大的顺序依次排开，即左1<左2<左3<...
> 	而对于区分度低的字段，无法找到大小关系，因为值都是相等的，毫无疑问，还想要用b+树存放这些等值的数据，只能增加树的高度，字段的区分度越低，则树的高度越高。极端的情况，索引字段的值都一样，那么b+树几乎成了一根棍。本例中就是这种极端的情况，name字段所有的值均为'egon'
> #现在我们得出一个结论：
> 	为区分度低的字段建立索引，索引树的高度会很高，然而这具体会带来什么影响呢？？？
> 
> #1：如果条件是name='xxxx',那么肯定是可以第一时间判断出'xxxx'是不在索引树中的（因为树中所有的值均为'egon’），所以查询速度很快
> 
> #2：如果条件正好是name='egon',查询时，我们永远无法从树的某个位置得到一个明确的范围，只能往下找，往下找，往下找。。。这与全表扫描的IO次数没有多大区别，所以速度很慢		
> ```

-  

  -  **3 索引列不能在条件中参与计算，保持列“干净”，比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’)**      

    ![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913181917438-461459737.png)

  - and/or

    ```
    #1、and与or的逻辑
        条件1 and 条件2:所有条件都成立才算成立，但凡要有一个条件不成立则最终结果不成立
        条件1 or 条件2:只要有一个条件成立则最终结果就成立
    
    #2、and的工作原理
        条件：
            a = 10 and b = 'xxx' and c > 3 and d =4
        索引：
            制作联合索引(d,a,b,c)
        工作原理:
            对于连续多个and：mysql会按照联合索引，从左到右的顺序找一个区分度高的索引字段(这样便可以快速锁定很小的范围)，加速查询，即按照d—>a->b->c的顺序
    
    #3、or的工作原理
        条件：
            a = 10 or b = 'xxx' or c > 3 or d =4
        索引：
            制作联合索引(d,a,b,c)
            
        工作原理:
            对于连续多个or：mysql会按照条件的顺序，从左到右依次判断，即a->b->c->d
    ```

    ![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913184346985-350786634.png)

    在左边条件成立但是索引字段的区分度低的情况下（name与gender均属于这种情况），会依次往右找到一个区分度高的索引字段，加速查询

    ![1556354410902](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556354410902.png)

    ![1556354427787](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556354427787.png)

    经过分析，在条件为name='egon' and gender='male' and id>333 and email='xxx'的情况下，我们完全没必要为前三个条件的字段加索引，因为只能用上email字段的索引，前三个字段的索引反而会降低我们的查询效率。

  - **5 最左前缀匹配原则（详见第八小节），非常重要的原则，对于组合索引mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配(指的是范围大了，有索引速度也慢)，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整**

![img](https://images2017.cnblogs.com/blog/1036857/201709/1036857-20170913190827078-212193584.png)

- 其他注意事项

  > ```
  > - 避免使用select *
  > - 使用count(*)
  > - 创建表时尽量使用 char 代替 varchar
  > - 表的字段顺序固定长度的字段优先
  > - 组合索引代替多个单列索引（由于mysql中每次只能使用一个索引，所以经常使用多个条件查询时更适合使用组合索引）
  > - 尽量使用短索引
  > - 使用连接（JOIN）来代替子查询(Sub-Queries)
  > - 连表时注意条件类型需一致
  > - 索引散列值（重复少）不适合建索引，例：性别不适合
  > 
> 
  > #总结关于索引无法命中的情况：
  > 1、使用or关键字会导致无法命中索引
  > 2、左前导查询回到著无法命中索引，如 like '%a'或者 like '%a%'
  > 3、单列索引的索引列为null时全值匹配会使索引失效,组合索引全为null时索引失效
  > 4、组合索引不符合左前缀原则的列无法命中索引，如我们有4个列a、b、c、d，我们创建一个组合索引INDEX(`a`,`b`,`c`,`d`),那么能命中	引的查询为a,ab,abc,abcd,除此之外都无法命中索引
  > 5、强制类型转换会导致索引失效
  > 6、如果mysql估计全表查询会比使用索引快，那么则不会使用索引
  > 7、负向查询条件会导致无法使用索引，比如NOT IN,NOT LIKE,!=等
  > 
  > ```
  
  

#### 八、联合索引与覆盖索引

- 联合索引

  - 联合索引是指对表上的多个列合起来做一个索引。联合索引的创建方法与单个索引的创建方法一样，不同之处仅在于有多个索引列，如下

    ```mysql
    mysql> create table t(
        -> a int,
        -> b int,
        -> primary key(a),
        -> key idx_a_b(a,b)
        -> );
    Query OK, 0 rows affected (0.11 sec)
    ```

    那么何时需要使用联合索引呢？在讨论这个问题之前，先来看一下联合索引内部的结果。从本质上来说，联合索引就是一棵B+树，不同的是联合索引的键值得数量不是1，而是>=2。接着来讨论两个整型列组成的联合索引，假定两个键值得名称分别为a、b如图：

    ![1556354911121](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556354911121.png)

    可以看到这与我们之前看到的单个键的B+树并没有什么不同，键值都是排序的，通过叶子结点可以逻辑上顺序地读出所有数据，就上面的例子来说，即（1,1），（1,2），（2,1），（2,4），（3,1），（3,2），数据按（a,b）的顺序进行了存放。

    ​	因此，对于查询select * from table where a=xxx and b=xxx, 显然是可以使用(a,b) 这个联合索引的，对于单个列a的查询select * from table where a=xxx,也是可以使用（a,b）这个索引的。但对于b列的查询select * from table where b=xxx,则不可以使用（a,b） 索引，其实你不难发现原因，叶子节点上b的值为1、2、1、4、1、2显然不是排序的，因此对于b列的查询使用不到(a,b) 索引。

    ​	**联合索引的第二个好处是在第一个键相同的情况下，已经对第二个键进行了排序处理**，例如在很多情况下应用程序都需要查询某个用户的购物情况，并按照时间进行排序，最后取出最近三次的购买记录，这时使用联合索引可以帮我们避免多一次的排序操作，因为索引本身在叶子节点已经排序了，如下

    ```mysql
    
    #===========准备表==============
    create table buy_log(
        userid int unsigned not null,
        buy_date date
    );
    
    insert into buy_log values
    (1,'2009-01-01'),
    (2,'2009-01-01'),
    (3,'2009-01-01'),
    (1,'2009-02-01'),
    (3,'2009-02-01'),
    (1,'2009-03-01'),
    (1,'2009-04-01');
    
    alter table buy_log add key(userid);
    alter table buy_log add key(userid,buy_date);
    
    #===========验证==============
    mysql> show create table buy_log;
    | buy_log | CREATE TABLE `buy_log` (
      `userid` int(10) unsigned NOT NULL,
      `buy_date` date DEFAULT NULL,
      KEY `userid` (`userid`),
      KEY `userid_2` (`userid`,`buy_date`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
    
    #可以看到possible_keys在这里有两个索引可以用，分别是单个索引userid与联合索引userid_2,但是优化器最终选择了使用的key是userid因为该索引的叶子节点包含单个键值，所以理论上一个页能存放的记录应该更多
    mysql> explain select * from buy_log where userid=2;
    +----+-------------+---------+------+-----------------+--------+---------+-------+------+-------+
    | id | select_type | table   | type | possible_keys   | key    | key_len | ref   | rows | Extra |
    +----+-------------+---------+------+-----------------+--------+---------+-------+------+-------+
    |  1 | SIMPLE      | buy_log | ref  | userid,userid_2 | userid | 4       | const |    1 |       |
    +----+-------------+---------+------+-----------------+--------+---------+-------+------+-------+
    1 row in set (0.00 sec)
    
    #接着假定要取出userid为1的最近3次的购买记录，用的就是联合索引userid_2了，因为在这个索引中，在userid=1的情况下，buy_date都已经排序好了
    mysql> explain select * from buy_log where userid=1 order by buy_date desc limit 3;
    +--+-----------+-------+----+---------------+--------+-------+-----+----+------------------------+
    |id|select_type|table  |type|possible_keys  | key    |key_len|ref  |rows| Extra                  |
    +--+-----------+-------+----+---------------+--------+-------+-----+----+------------------------+
    | 1|SIMPLE     |buy_log|ref |userid,userid_2|userid_2| 4     |const|  4 |Using where; Using index|
    +--+-----------+-------+----+---------------+--------+-------+-----+----+------------------------+
    1 row in set (0.00 sec)
    
    #ps：如果extra的排序显示是Using filesort，则意味着在查出数据后需要二次排序(如下查询语句，没有先用where userid=3先定位范围，于是即便命中索引也没用，需要二次排序)
    mysql> explain select * from buy_log order by buy_date desc limit 3;
    +--+-----------+-------+-----+-------------+--------+-------+----+----+---------------------------+
    |id|select_type| table |type |possible_keys|key     |key_len|ref |rows|Extra                      |
    +--+-----------+-------+-----+-------------+--------+-------+----+----+---------------------------+
    | 1|SIMPLE     |buy_log|index| NULL        |userid_2| 8     |NULL|  7 |Using index; Using filesort|
    +--+-----------+-------+-----+-------------+--------+-------+----+----+---------------------------+
    
    #对于联合索引（a,b）,下述语句可以直接使用该索引，无需二次排序
    select ... from table where a=xxx order by b;
    
    #然后对于联合索引(a,b,c)来首，下列语句同样可以直接通过索引得到结果
    select ... from table where a=xxx order by b;
    select ... from table where a=xxx and b=xxx order by c;
    
    #但是对于联合索引(a,b,c)，下列语句不能通过索引直接得到结果，还需要自己执行一次filesort操作，因为索引（a，c)并未排序
    select ... from table where a=xxx order by c;
    ```

- 覆盖索引

  - **InnoDB存储引擎支持覆盖索引（covering index，或称索引覆盖），即从辅助索引中就可以得到查询记录，而不需要查询聚集索引中的记录。**

    使用覆盖索引的一个好处是：辅助索引不包含整行记录的所有信息，故其大小要远小于聚集索引，因此可以减少大量的IO操作

    ------

     	注意：覆盖索引技术最早是在InnoDB Plugin中完成并实现，这意味着对于InnoDB版本小于1.0的，或者MySQL数据库版本为5.0以下的，InnoDB存储引擎不支持覆盖索引特性

    ------

    对于InnoDB存储引擎的辅助索引而言，由于其包含了主键信息，因此其叶子节点存放的数据为（primary key1，priamey key2，...,key1，key2，...）。例如

    ```mysql
    
    select age from s1 where id=123 and name = 'egon'; #id字段有索引，但是name字段没有索引,该sql命中了索引，但未覆盖，需要去聚集索引中再查找详细信息。
    最牛逼的情况是，索引字段覆盖了所有，那全程通过索引来加速查询以及获取结果就ok了
    mysql> desc s1;
    +--------+-------------+------+-----+---------+-------+
    | Field | Type | Null | Key | Default | Extra |
    +--------+-------------+------+-----+---------+-------+
    | id | int(11) | NO | | NULL | |
    | name | varchar(20) | YES | | NULL | |
    | gender | char(6) | YES | | NULL | |
    | email | varchar(50) | YES | | NULL | |
    +--------+-------------+------+-----+---------+-------+
    4 rows in set (0.21 sec)
    
    mysql> explain select name from s1 where id=1000; #没有任何索引
    +--+-----------+-----+----------+----+-------------+----+-------+----+-------+--------+-----------+
    |id|select_type|table|partitions|type|possible_keys|key |key_len|ref | rows  |filtered| Extra     |
    +--+-----------+-----+----------+----+-------------+----+-------+----+-------+--------+-----------+
    | 1| SIMPLE    | s1  | NULL     |ALL | NULL        |NULL| NULL  |NULL|2688336| 10.00  |Using where|
    +--+-----------+-----+----------+----+-------------+----+-------+----+-------+--------+-----------+
    1 row in set, 1 warning (0.00 sec)
    
    mysql> create index idx_id on s1(id); #创建索引
    Query OK, 0 rows affected (4.16 sec)
    Records: 0 Duplicates: 0 Warnings: 0
    
    mysql> explain select name from s1 where id=1000; #命中辅助索引，但是未覆盖索引，还需要从聚集索引中查找name
    +--+-----------+-----+----------+----+-------------+------+-------+-----+----+--------+-----+
    |id|select_type|table|partitions|type|possible_keys|   key|key_len| ref |rows|filtered|Extra|
    +--+-----------+-----+----------+----+-------------+------+-------+-----+----+--------+-----+
    | 1| SIMPLE    | s1  | NULL     | ref| idx_id      |idx_id| 4     |const| 1  | 100.00 | NULL|
    +--+-----------+-----+----------+----+-------------+------+-------+-----+----+--------+-----+
    1 row in set, 1 warning (0.08 sec)
    
    mysql> explain select id from s1 where id=1000; #在辅助索引中就找到了全部信息，Using index代表覆盖索引
    +--+-----------+-----+----------+----+-------------+------+-------+-------+------+----------+-----+
    |id|select_type|table|partitions|type|possible_keys| key  |key_len| ref |rows|filtered| Extra     |
    +--+-----------+-----+----------+----+--------------------+-------+-------+------+----------+-----+
    | 1| SIMPLE    | s1  | NULL     | ref| idx_id      |idx_id|  4    |const| 1  | 100.00 |Using index|
    +--+-----------+-----+----------+----+-------------+------+-------+-----+----+--------+-----------+
    1 row in set, 1 warning (0.03 sec)
    ```

    

#### 九、查询优化神器-explain

​		**关于explain命令相信大家并不陌生，具体用法和字段含义可以参考官网explain-output，这里需要强调rows是核心指标，绝大部分rows小的语句执行一定很快（有例外，下面会讲到）。所以优化语句基本上都是在优化rows。**

```mysql

执行计划：让mysql预估执行操作(一般正确)
    all < index < range < index_merge < ref_or_null < ref < eq_ref < system/const
    id,email
    
    慢：
        select * from userinfo3 where name='alex'
        
        explain select * from userinfo3 where name='alex'
        type: ALL(全表扫描)
            select * from userinfo3 limit 1;
    快：
        select * from userinfo3 where email='alex'
        type: const(走索引)
```

[具体学习网址](https://www.cnblogs.com/Eva-J/articles/10145093.html)

执行计划详解：explain + 查询SQL - 用于显示SQL执行信息参数，根据参考信息可以进行SQL优化

```mysql
mysql> explain select * from userinfo;
+----+-------------+----------+------+---------------+------+---------+------+---------+-------+
| id | select_type | table    | type | possible_keys | key  | key_len | ref  | rows    | Extra |
+----+-------------+----------+------+---------------+------+---------+------+---------+-------+
|  1 | SIMPLE      | userinfo | ALL  | NULL          | NULL | NULL    | NULL | 2973016 | NULL  |
+----+-------------+----------+------+---------------+------+---------+------+---------+-------+

mysql> explain select * from (select id,name from userinfo where id <20) as A;
+----+-------------+------------+-------+---------------+---------+---------+------+------+-------------+
| id | select_type | table      | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
+----+-------------+------------+-------+---------------+---------+---------+------+------+-------------+
|  1 | PRIMARY     | <derived2> | ALL   | NULL          | NULL    | NULL    | NULL |   19 | NULL        |
|  2 | DERIVED     | userinfo   | range | PRIMARY       | PRIMARY | 4       | NULL |   19 | Using where |
+----+-------------+------------+-------+---------------+---------+---------+------+------+-------------+

#参数说明：
select_type：
查询类型
    SIMPLE          简单查询
    PRIMARY         最外层查询
    SUBQUERY        映射为子查询
    DERIVED         子查询
    UNION           联合
    UNION RESULT    使用联合的结果
    
table：
    正在访问的表名
type：
    查询时的访问方式，性能：all < index < range < index_merge < ref_or_null < ref < eq_ref < system/const
    ALL 全表扫描，对于数据表从头到尾找一遍
    select * from userinfo;
    特别的：如果有limit限制，则找到之后就不在继续向下扫描
       select * from userinfo where email = 'alex112@oldboy'
       select * from userinfo where email = 'alex112@oldboy' limit 1;
       虽然上述两个语句都会进行全表扫描，第二句使用了limit，则找到一个后就不再继续扫描。

INDEX ： 全索引扫描，对索引从头到尾找一遍
    select nid from userinfo;

RANGE： 对索引列进行范围查找
    select *  from userinfo where name < 'alex';
    PS:
        between and
        in
        >   >=  <   <=  操作
        注意：!= 和 > 符号


INDEX_MERGE： 合并索引，使用多个单列索引搜索
    select *  from userinfo where name = 'alex' or nid in (11,22,33);

REF： 根据索引查找一个或多个值
    select *  from userinfo where name = 'alex112';

EQ_REF： 连接时使用primary key 或 unique类型
    select userinfo2.id,userinfo.name from userinfo2 left join tuserinfo on userinfo2.id = userinfo.id;

CONST：常量
    表最多有一个匹配行,因为仅有一行,在这行的列值可被优化器剩余部分认为是常数,const表很快,因为它们只读取一次。
    select id from userinfo where id = 2 ;

SYSTEM：系统
    表仅有一行(=系统表)。这是const联接类型的一个特例。
    select * from (select id from userinfo where id = 1) as A;

possible_keys：可能使用的索引

key：真实使用的

key_len： MySQL中使用索引字节长度

rows： mysql估计为了找到所需的行而要读取的行数 ------ 只是预估值

extra：
    该列包含MySQL解决查询的详细信息
    "Using index"
        此值表示mysql将使用覆盖索引，以避免访问表。不要把覆盖索引和index访问类型弄混了。
    "Using where"
        这意味着mysql服务器将在存储引擎检索行后再进行过滤，许多where条件里涉及索引中的列，当（并且如果）它读取索引时，就能被存储引擎检验，因此不是所有带where子句的查询都会显示"Using where"。有时"Using where"的出现就是一个暗示：查询可受益于不同的索引。
    "Using temporary"
        这意味着mysql在对查询结果排序时会使用一个临时表。
    "Using filesort"
        这意味着mysql会对结果使用一个外部索引排序，而不是按索引次序从表里读取行。mysql有两种文件排序算法，这两种排序方式都可以在内存或者磁盘上完成，explain不会告诉你mysql将使用哪一种文件排序，也不会告诉你排序会在内存里还是磁盘上完成。
    "Range checked for each record(index map: N)"
        这个意味着没有好用的索引，新的索引将在联接的每一行上重新估算，N是显示在possible_keys列中索引的位图，并且是冗余的
```



#### 十、慢日志管理

```mysql

MySQL日志管理
========================================================
错误日志: 记录 MySQL 服务器启动、关闭及运行错误等信息
二进制日志: 又称binlog日志，以二进制文件的方式记录数据库中除 SELECT 以外的操作
查询日志: 记录查询的信息
慢查询日志: 记录执行时间超过指定时间的操作
中继日志： 备库将主库的二进制日志复制到自己的中继日志中，从而在本地进行重放
通用日志： 审计哪个账号、在哪个时段、做了哪些事件
事务日志或称redo日志： 记录Innodb事务相关的如事务执行时间、检查点等
========================================================
一、bin-log
1. 启用
# vim /etc/my.cnf
[mysqld]
log-bin[=dir\[filename]]
# service mysqld restart
2. 暂停
//仅当前会话
SET SQL_LOG_BIN=0;
SET SQL_LOG_BIN=1;
3. 查看
查看全部：
# mysqlbinlog mysql.000002
按时间：
# mysqlbinlog mysql.000002 --start-datetime="2012-12-05 10:02:56"
# mysqlbinlog mysql.000002 --stop-datetime="2012-12-05 11:02:54"
# mysqlbinlog mysql.000002 --start-datetime="2012-12-05 10:02:56" --stop-datetime="2012-12-05 11:02:54" 

按字节数：
# mysqlbinlog mysql.000002 --start-position=260
# mysqlbinlog mysql.000002 --stop-position=260
# mysqlbinlog mysql.000002 --start-position=260 --stop-position=930
4. 截断bin-log（产生新的bin-log文件）
a. 重启mysql服务器
b. # mysql -uroot -p123 -e 'flush logs'
5. 删除bin-log文件
# mysql -uroot -p123 -e 'reset master' 


二、查询日志
启用通用查询日志
# vim /etc/my.cnf
[mysqld]
log[=dir\[filename]]
# service mysqld restart

三、慢查询日志
启用慢查询日志
# vim /etc/my.cnf
[mysqld]
log-slow-queries[=dir\[filename]]
long_query_time=n
# service mysqld restart
MySQL 5.6:
slow-query-log=1
slow-query-log-file=slow.log
long_query_time=3  单位为秒
查看慢查询日志
测试:BENCHMARK(count,expr)
SELECT BENCHMARK(50000000,2*3);
```

#### 十一、分页性能调优

```mysql
第1页：
select * from userinfo limit 0,10;
第2页：
select * from userinfo limit 10,10;
第3页：
select * from userinfo limit 20,10;
第4页：
select * from userinfo limit 30,10;
......
第2000010页
select * from userinfo limit 2000000,10;

PS:会发现，越往后查询，需要的时间约长，是因为越往后查，全文扫描查询，会去数据表中扫描查询。
```

最优的解决方案

- 只有上一页和下一页

  ```mysql
  下一页：
  select * from userinfo where id>max_id limit 10;
  
  上一页：
  select * from userinfo where id<min_id order by id desc limit 10;
  
  下一页：
  mysql> select * from userinfo where id > 20010 limit 10;
  +-------+-----------+--------+------------------+
  | id    | name      | gender | email            |
  +-------+-----------+--------+------------------+
  | 20011 | alex20011 | male   | egon20011@oldboy |
  | 20012 | alex20012 | male   | egon20012@oldboy |
  | 20013 | alex20013 | male   | egon20013@oldboy |
  | 20014 | alex20014 | male   | egon20014@oldboy |
  | 20015 | alex20015 | male   | egon20015@oldboy |
  | 20016 | alex20016 | male   | egon20016@oldboy |
  | 20017 | alex20017 | male   | egon20017@oldboy |
  | 20018 | alex20018 | male   | egon20018@oldboy |
  | 20019 | alex20019 | male   | egon20019@oldboy |
  | 20020 | alex20020 | male   | egon20020@oldboy |
  +-------+-----------+--------+------------------+
  10 rows in set (0.00 sec)
  
  上一页：
  mysql> select * from userinfo where id<20011 order by id desc limit 10;
  +-------+-----------+--------+------------------+
  | id    | name      | gender | email            |
  +-------+-----------+--------+------------------+
  | 20010 | alex20010 | male   | egon20010@oldboy |
  | 20009 | alex20009 | male   | egon20009@oldboy |
  | 20008 | alex20008 | male   | egon20008@oldboy |
  | 20007 | alex20007 | male   | egon20007@oldboy |
  | 20006 | alex20006 | male   | egon20006@oldboy |
  | 20005 | alex20005 | male   | egon20005@oldboy |
  | 20004 | alex20004 | male   | egon20004@oldboy |
  | 20003 | alex20003 | male   | egon20003@oldboy |
  | 20002 | alex20002 | male   | egon20002@oldboy |
  | 20001 | alex20001 | male   | egon20001@oldboy |
  +-------+-----------+--------+------------------+
  10 rows in set (0.33 sec)
  ```

- 中间有页码的情况

  ```mysql
  #语法：
  select * from (select * from userinfo where id > pre_max_id limit (cur_max_id-pre_max_id))*10) as A order by id desc limit 10;
  
  #比如现在是2001页
  mysql> select * from userinfo where id > 20010 limit 10;
  +-------+-----------+--------+------------------+
  | id    | name      | gender | email            |
  +-------+-----------+--------+------------------+
  | 20011 | alex20011 | male   | egon20011@oldboy |
  | 20012 | alex20012 | male   | egon20012@oldboy |
  | 20013 | alex20013 | male   | egon20013@oldboy |
  | 20014 | alex20014 | male   | egon20014@oldboy |
  | 20015 | alex20015 | male   | egon20015@oldboy |
  | 20016 | alex20016 | male   | egon20016@oldboy |
  | 20017 | alex20017 | male   | egon20017@oldboy |
  | 20018 | alex20018 | male   | egon20018@oldboy |
  | 20019 | alex20019 | male   | egon20019@oldboy |
  | 20020 | alex20020 | male   | egon20020@oldboy |
  +-------+-----------+--------+------------------+
  #现在需要跳转到2003页
  mysql> select * from userinfo where id > 20030 limit 10;
  +-------+-----------+--------+------------------+
  | id    | name      | gender | email            |
  +-------+-----------+--------+------------------+
  | 20031 | alex20031 | male   | egon20031@oldboy |
  | 20032 | alex20032 | male   | egon20032@oldboy |
  | 20033 | alex20033 | male   | egon20033@oldboy |
  | 20034 | alex20034 | male   | egon20034@oldboy |
  | 20035 | alex20035 | male   | egon20035@oldboy |
  | 20036 | alex20036 | male   | egon20036@oldboy |
  | 20037 | alex20037 | male   | egon20037@oldboy |
  | 20038 | alex20038 | male   | egon20038@oldboy |
  | 20039 | alex20039 | male   | egon20039@oldboy |
  | 20040 | alex20040 | male   | egon20040@oldboy |
  +-------+-----------+--------+------------------+
  10 rows in set (0.00 sec)
  ```

  

#### 十二、面试题

```mysql
1、例举3种不走索引的情景（索引不会命中的情景）
    - like '%xx'
        select * from userinfo where name like '%al';
    - 使用函数
        select * from userinfo where reverse(name) = 'alex333';
    - or
        select * from userinfo where id = 1 or email = 'alex122@oldbody';
        特别的：当or条件中有未建立索引的列才失效，以下会走索引
                select * from userinfo where id = 1 or name = 'alex1222';
                select * from userinfo where id = 1 or email = 'alex122@oldbody' and name = 'alex112';
    - 类型不一致
        如果列是字符串类型，传入条件是必须用引号引起来，不然...
        select * from userinfo where name = 999;
    - !=
        select count(*) from userinfo where name != 'alex';
        特别的：如果是主键，则还是会走索引
            select count(*) from userinfo where id != 123;
    - >
        select * from userinfo where name > 'alex'
        特别的：如果是主键或索引是整数类型，则还是会走索引
            select * from userinfo where id > 123;
    - order by
        select email from userinfo order by name desc;
        当根据索引排序时候，选择的映射如果不是索引，则不走索引
        特别的：如果对主键排序，则还是走索引：
            select * from userinfo order by id desc;

    - 组合索引最左前缀
        如果组合索引为：(name,email)
        name and email       -- 使用索引
        name                 -- 使用索引
        email                -- 不使用索引
    -尽量使用组合索引
    
 2、什么是最左前缀
 	对于组合索引mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配(指的是范围大了，有索引速度也慢)，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。
```

#### 十三、总结

```
# 索引
    # 认识mysql中的key
        # index key    普通索引,能够加速查询,辅助索引
        # unique key   唯一 + 索引,辅助索引
        # primary key  唯一 + 非空 + 聚集索引
            # 主键作为条件的查询如果能够让索引生效那么效率总是更高
        # foreign key  本身没有索引的,但是它关联的外表中的字段是unique索引
        # primary key 和unique 标识的字段不需要再添加索引
            # 直接就可以利用索引加速查询
        # 能用unique的时候尽量不用index
            # unique除了是索引之外还能做唯一约束,如果做了唯一约束
            # b+树就更健康
    # 正确的使用索引
        # 创建索引 creeat index 索引名 on 表名(字段名)
        # 删除索引 drop index 索引名 on 表名

        # 1.条件一定是建立了索引的字段,如果条件使用的字段根本就没有创建索引,那么索引不生效
        # 2.如果条件是一个范围,随着范围的值逐渐增大,那么索引能发挥的作用也越小
        # 3.如果使用like进行模糊查询,那么使用a%的形式能命中索引,%a形式不能命中索引
        # 4.尽量选择区分度高的字段作为索引列
        # 5.索引列不能在条件中参与计算,也不能使用函数
        # 6.在多个条件以and相连的时候,会优点选择区分度高的索引列来进行查询
        #   在多个条件以or相连的时候,就是从左到右依次判断
        # 7.制作联合索引
            # 1.最左前缀原则 a,b,c,d 条件是a的能命中索引,条件是a,b能命中索引,a,b,c能命中,a,c.... 只要没有a就不能命中索引
                # 如果在联合查询中,总是涉及到同一个字段,那么就在建立联合索引的时候将这个字段放在最左侧
            # 2.联合索引 如果按照定义顺序,从左到右遇到的第一个在条件中以范围为条件的字段,索引失效
                # 尽量将带着范围查询的字段,定义在联合索引的最后面
            # drop index
            # 如果我们查询的条件总是多个列合在一起查,那么就建立联合索引
                # create index ind_mix on s1(id,email)

                # select * from s1 where id = 1000000    命中索引
                # select * from s1 where email = 'eva1000000@oldboy'  未命中索引
                # 但凡是创建了联合索引,那么在查询的时候,再创建顺序中从左到右的第一列必须出现在条件中
                # select count(*) from s1 where id = 1000000 and email = 'eva10%';  命中索引

                # select count(*) from s1 where id = 1000000 and email like 'eva10%'; 可以命中索引
                # 范围 :
                # select * from s1 where id >3000 and email = 'eva300000@oldboy';  不能命中索引
        # 8.条件中涉及的字段的值必须和定义表中字段的数据类型一致,否则不能命中索引

    # 关于索引的两个名词
        # 覆盖索引 查一个数据不需要回表
            # select name from 表 where age = 20   不是覆盖索引
            # select age from 表 where age =20  是覆盖索引
            # select count(age) from 表 where age =20  是覆盖索引
        # 合并索引
            # 当我们为单独的一列创建索引的时候
                # 如果条件是这一列,且使用正确就可以命中索引
            # 当我们为两列分别创建单独的索引的时候
                # 如果这两列都是条件,那么可能只能命中期中一个条件
                # 如果这两列都是条件,那么可能会命中两个索引  - 合并索引
            # 我们为多列直接创建联合所以
                # 条件命中联合索引
```

