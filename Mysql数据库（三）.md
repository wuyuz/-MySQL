## Mysql数据库（外键、数据更新以及单表查询）

#### 一、外键的三种情况

​	前一篇我们已经说了一些和多表之间的关系，这一篇将学习两张表之间或则多个表之间的关系：

- 多对一

- 多对多

- 一对一

  根据上一章的规则：

  ```
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

  -  **一对多（或多对一）**：一个出版社可以出版多本书。

  ![1556108223090](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556108223090.png)

  一本书，对应一个出版社，是正常的。           （一对一）

  一本书，对应多个出版社，那就是盗版的。        （一对多）

  多本书，可以对应一个出版社，比如工业出版社。   （多对一）

  总结：

  *<u>`左表的多条记录可以对应右边的一条记录，则左表的一个字段foreign key 右表一个字段（通常是id）`</u>*

  ```mysql
  #代码实现 多本书对一个出版社
  #关联方式：foreign key
  
  create table press(    #创建出版社
      id int primary key auto_increment,
      name varchar(20)
  );
  
  create table book(    # 创建书
      id int primary key auto_increment,
      name varchar(20),
      press_id int not null,
      constraint fk_book_press foreign key(press_id) references press(id)
      on delete cascade  #级联删除
      on update cascade  #级联更新 
  );
  
  # 先往被关联表中插入记录
  insert into press(name) values
  ('北京工业地雷出版社'),
  ('人民音乐不好听出版社'),
  ('知识产权没有用出版社')
  ;
  
  # 再往关联表中插入记录
  insert into book(name,press_id) values
  ('九阳神功',1),
  ('九阴真经',2),
  ('九阴白骨爪',2),
  ('独孤九剑',3),
  ('降龙十巴掌',2),
  ('葵花宝典',3)
  ;
  
  查询结果：
  mysql> select * from book;
  +----+-----------------+----------+
  | id | name            | press_id |
  +----+-----------------+----------+
  |  1 | 九阳神功        |        1 |
  |  2 | 九阴真经        |        2 |
  |  3 | 九阴白骨爪      |        2 |
  |  4 | 独孤九剑        |        3 |
  |  5 | 降龙十巴掌      |        2 |
  |  6 | 葵花宝典        |        3 |
  +----+-----------------+----------+
  
  mysql> select * from press;
  +----+--------------------------------+
  | id | name                           |
  +----+--------------------------------+
  |  1 | 北京工业地雷出版社             |
  |  2 | 人民音乐不好听出版社           |
  |  3 | 知识产权没有用出版社           |
  +----+--------------------------------+
  ```

  - 作者和数据的关系 

    **多对多**：一个作者可以写多本书，一本书也可以有多个作者，**双向的一对多，即多对多**，显然此时两张表无法实现这个模型，因为关联表和从表必须是两两独立的，不能你是我的关联表，我也是你的关联表。

    ![1556108801287](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556108801287.png)

    ```mysql
    #代码实现
    #关联方式：foreign key+一张新的表
    
    # 创建被关联表author表，之前的book表在讲多对一的关系已创建
    create table author(
        id int primary key auto_increment,
        name varchar(20)
    );
    #这张表就存放了author表和book表的关系，即查询二者的关系查这表就可以了，book已创建
    create table author2book(
        id int not null unique auto_increment,
        author_id int not null,
        book_id int not null,
        constraint fk_author foreign key(author_id) references author(id)
        on delete cascade
        on update cascade,
        constraint fk_book foreign key(book_id) references book(id)
        on delete cascade
        on update cascade,
        primary key(author_id,book_id)
    );
    #插入四个作者，id依次排开
    insert into author(name) values('egon'),('alex'),('wusir'),('yuanhao');
    
    # 每个作者的代表作
    egon: 九阳神功、九阴真经、九阴白骨爪、独孤九剑、降龙十巴掌、葵花宝典
    alex: 九阳神功、葵花宝典
    wusir:独孤九剑、降龙十巴掌、葵花宝典
    yuanhao:九阳神功
    
    # 在author2book表中插入相应的数据
    insert into author2book(author_id,book_id) values
    (1,1),
    (1,2),
    (1,3),
    (1,4),
    (1,5),
    (1,6),
    (2,1),
    (2,6),
    (3,4),
    (3,5),
    (3,6),
    (4,1)
    ;
    # 现在就可以查author2book对应的作者和书的关系了
    mysql> select * from author2book;
    +----+-----------+---------+
    | id | author_id | book_id |
    +----+-----------+---------+
    |  1 |         1 |       1 |
    |  2 |         1 |       2 |
    |  3 |         1 |       3 |
    |  4 |         1 |       4 |
    |  5 |         1 |       5 |
    |  6 |         1 |       6 |
    |  7 |         2 |       1 |
    |  8 |         2 |       6 |
    |  9 |         3 |       4 |
    | 10 |         3 |       5 |
    | 11 |         3 |       6 |
    | 12 |         4 |       1 |
    +----+-----------+---------+
    ```

  - 一对一：`一个用户只能注册一个博客，即一对一的关系。看图说话`

![1556109256064](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556109256064.png)

```mysql
#代码实现
#关联方式：foreign key+unique
#例如： 一个用户只能注册一个博客

#两张表： 用户表 （user）和 博客表(blog)
# 创建用户表
create table user(
    id int primary key auto_increment,
    name varchar(20)
);
# 创建博客表,user_id 字段必须唯一
create table blog(
    id int primary key auto_increment,
    url varchar(100),
    user_id int unique,
    constraint fk_user foreign key(user_id) references user(id)
    on delete cascade
    on update cascade
);
#插入用户表中的记录
insert into user(name) values
('alex'),
('wusir'),
('egon'),
('xiaoma')
;
# 插入博客表的记录
insert into blog(url,user_id) values
('http://www.cnblog/alex',1),
('http://www.cnblog/wusir',2),
('http://www.cnblog/egon',3),
('http://www.cnblog/xiaoma',4)
;
# 查询wusir的博客地址
select url from blog where user_id=2;
```

- 总结：

  **在公司，一般不让用外键，影响性能，它会消耗硬盘。**
  下面的内容，将会介绍多表查询，来解决这个问题。

  

#### 二、数据的增删改查

- 在MySQL管理软件中，通过SQL语句中的DML语言来实现数据的操作

  - 1.使用INSERT实现数据的插入
    2.UPDATE实现数据的更新
    3.使用DELETE实现数据的删除
    4.使用SELECT查询数据以及。

- 插入数据insert

  - ![1556109580100](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556109580100.png)

  - 补充

    ```mysql
    insert into 表名 value (值1，值2……)     #当插入的值只有一个时
    
    insert into 表名 set 字段1=值1,字段2=值2,……  #第二种指定字段插入数据
    
    insert into 表名(字段1，字段2，字段3……) select （字段1，字段2，字段3……） from 表2 where …… # 复制表
    
    insert into 表名 select * from 另一张表;
    insert into 表名 select 字段1,字段2 from 另一张表;
    insert into 表名(字段1,字段2) select 字段1,字段2 from 另一张表;
    ```

- **更新数据update**

  ```mysql
UPDATE 表名 SET
      字段1=值1,
      字段2=值2,
      WHERE CONDITION;
   
   #示例
   UPDATE mysql.user SET password='123'
      where user='root' and host='localhost';
  ```
  
- **删除数据delete**

  ```mysql
  DELETE FROM 表名
      WHERE CONITION;
      
  #示例
  DELETE FROM mysql.user
      WHERE password='';
  #delete一般和where配合使用，如果没有where，表示清空表数据。
  ```

  

- 清空数据 truncate

  ```mysql
  # 语法
  TRUNCATE TABLE 表名;
  
  注意：truncate table 在清理大表时，速度非常快!
  使用truncate table 清理上千万数据时，存储空间不会立即释放。
  这是因为删除操作后在数据文件中留下碎片所致，需要使用命令
  
  OPTIMIZE TABLE 表名;    #整理数据文件的碎片，在执行过程中，会锁表！请谨慎操作！
  ```

  

- delete * from和truncate table都能清空表数据，那么二者之间的区别在于：

  ```
  truncate table 不仅是删除表里面的数据，而且还会清空表里面主键的标识。也就是说使用过truncate table 的表在重新写入数据的时候，标识符会从0或1重新开始（看你设置的种子号）。
  
  delete * from就是仅仅能删除数据，不能清空标识。不过delete * from可以后面加Where truncate table却不能加Where
  ```



#### 三、单表查询

- 单表查询的语句：

  ```mysql
  SELECT DISTINCT 字段1,字段2... FROM 表名
                                WHERE 条件
                                GROUP BY field   #分组其实分下来的数据就是些小组了，只是不可见唯一
                                HAVING 筛选      #作用于分组
                                ORDER BY field
                                LIMIT 限制条数    # 限制条数
  ```

- **关键字的执行优先级（重点）**

  ```mysql
  从上到下的顺序依次是：（最上最优先）
  from        # 第一件事找到文件
  where       #拿着约束条件去取文件
  group by    #将取出的一条条记录进行分组group by 如果没有视为一个整组，默认只返回每组第一个，但实际已分好
  having      #将分组的结构进行过滤， 总是和group by 连用,where中不能出现聚合函数,所以和聚合函数有关的条件筛选也只能用having
  select      #执行select 选择输出的字段
  distinct    #去除重复的
  order by    #将结果按条件排序：默认：asc，desc降序
  limit       #显示限制结果的显示条数 limit 3 显示从上至下三条， limit 2，3显示第3条到第6条
  ```

  ![1556110813355](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556110813355.png)

- 建表准备

  ```mysql
  company.employee
      员工id      id                  int             
      姓名        emp_name            varchar
      性别        sex                 enum
      年龄        age                 int
      入职日期     hire_date           date
      岗位        post                varchar
      职位描述     post_comment        varchar
      薪水        salary              double
      办公室       office              int
      部门编号     depart_id           int
  
  
  
  #创建表
  create table employee(
  id int not null unique auto_increment,
  emp_name varchar(20) not null,
  sex enum('male','female') not null default 'male', #大部分是男的
  age int(3) unsigned not null default 28,
  hire_date date not null,
  post varchar(50),
  post_comment varchar(100),
  salary double(15,2),
  office int, #一个部门一个屋子
  depart_id int
  );
  
  #插入记录
  #三个部门：教学，销售，运营
  insert into employee(emp_name,sex,age,hire_date,post,salary,office,depart_id) values
  ('egon','male',18,'20170301','老男孩驻沙河办事处外交大使',7300.33,401,1), #以下是教学部
  ('alex','male',78,'20150302','teacher',1000000.31,401,1),
  ('wupeiqi','male',81,'20130305','teacher',8300,401,1),
  ('yuanhao','male',73,'20140701','teacher',3500,401,1),
  ('liwenzhou','male',28,'20121101','teacher',2100,401,1),
  ('jingliyang','female',18,'20110211','teacher',9000,401,1),
  ('jinxin','male',18,'19000301','teacher',30000,401,1),
  ('成龙','male',48,'20101111','teacher',10000,401,1),
  
  ('歪歪','female',48,'20150311','sale',3000.13,402,2),#以下是销售部门
  ('丫丫','female',38,'20101101','sale',2000.35,402,2),
  ('丁丁','female',18,'20110312','sale',1000.37,402,2),
  ('星星','female',18,'20160513','sale',3000.29,402,2),
  ('格格','female',28,'20170127','sale',4000.33,402,2),
  
  ('张野','male',28,'20160311','operation',10000.13,403,3), #以下是运营部门
  ('程咬金','male',18,'19970312','operation',20000,403,3),
  ('程咬银','female',18,'20130311','operation',19000,403,3),
  ('程咬铜','male',18,'20150411','operation',18000,403,3),
  ('程咬铁','female',18,'20140512','operation',17000,403,3)
  ;
  ```

- **简单查询**

  ```mysql
  #简单查询
      SELECT id,emp_name,sex,age,hire_date,post,post_comment,salary,office,depart_id 
      FROM employee;
  
      SELECT * FROM employee;
  
      SELECT emp_name,salary FROM employee;
  
  #避免重复DISTINCT
      SELECT DISTINCT post FROM employee;    
  
  #通过四则运算查询
      SELECT emp_name, salary*12 FROM employee;
      SELECT emp_name, salary*12 AS Annual_salary FROM employee;
      SELECT emp_name, salary*12 Annual_salary FROM employee;   #空格重名
      SELECT emp_name, (salary*12) as Annual_salary FROM employee;  #as 重命名
  
  #定义显示格式
     CONCAT() 函数用于连接字符串
     SELECT CONCAT('姓名: ',emp_name,'  年薪: ', salary*12)  AS Annual_salary 
     FROM employee;
     
     CONCAT_WS() 第一个参数为分隔符
     SELECT CONCAT_WS(':',emp_name,salary*12)  AS Annual_salary 
     FROM employee;
  
     结合CASE语句：
     SELECT
         (
             CASE
             WHEN emp_name = 'jingliyang' THEN
                 emp_name
             WHEN emp_name = 'alex' THEN
                 CONCAT(emp_name,'_BIGSB')
             ELSE
                 concat(emp_name, 'SB')
             END
         ) as new_name
     FROM
         employee;
   
   #小练习
  1 查出所有员工的名字，薪资,格式为
      <名字:egon>    <薪资:3000>
  2 查出所有的岗位（去掉重复）
  3 查出所有员工名字，以及他们的年薪,年薪的字段名为annual_year
  #解答：
      select concat('<名字:',emp_name,'>    ','<薪资:',salary,'>') from employee;
      select distinct depart_id from employee;
      select emp_name,salary*12 annual_salary from employee;
  ```

- **where约束**

  ```mysql
  where字句中可以使用：
      1. 比较运算符：> < >= <= <> !=
      2. between 80 and 100 值在80到100之间
      3. in(80,90,100) 值是80或90或100
      4. like 'e%'
          通配符可以是%或_，
          %表示任意多字符
          _表示一个字符 
      5. 逻辑运算符：在多个条件直接可以使用逻辑运算符 and or not
  
  #1:单条件查询
      SELECT emp_name FROM employee
          WHERE post='sale';
          
  #2:多条件查询
      SELECT emp_name,salary FROM employee
          WHERE post='teacher' AND salary>10000;
  
  #3:关键字BETWEEN AND
      SELECT emp_name,salary FROM employee 
          WHERE salary BETWEEN 10000 AND 20000;
  
      SELECT emp_name,salary FROM employee 
          WHERE salary NOT BETWEEN 10000 AND 20000;
      
  #4:关键字IS NULL(判断某个字段是否为NULL不能用等号，需要用IS)
      SELECT emp_name,post_comment FROM employee 
          WHERE post_comment IS NULL;
  
      SELECT emp_name,post_comment FROM employee 
          WHERE post_comment IS NOT NULL;
          
      SELECT emp_name,post_comment FROM employee 
          WHERE post_comment=''; 注意''是空字符串，不是null
      ps：
          执行
          update employee set post_comment='' where id=2;
          再用上条查看，就会有结果了
  
  #5:关键字IN集合查询
      SELECT emp_name,salary FROM employee 
          WHERE salary=3000 OR salary=3500 OR salary=4000 OR salary=9000 ;
      
      SELECT emp_name,salary FROM employee 
          WHERE salary IN (3000,3500,4000,9000) ;
  
      SELECT emp_name,salary FROM employee 
          WHERE salary NOT IN (3000,3500,4000,9000) ;
  
  #6:关键字LIKE模糊查询
      通配符’%’
      SELECT * FROM employee 
              WHERE emp_name LIKE 'eg%';
  
      通配符’_’
      SELECT * FROM employee 
              WHERE emp_name LIKE 'al__';    
              
  #小练习
  1. 查看岗位是teacher的员工姓名、年龄
  2. 查看岗位是teacher且年龄大于30岁的员工姓名、年龄
  3. 查看岗位是teacher且薪资在9000-1000范围内的员工姓名、年龄、薪资
  4. 查看岗位描述不为NULL的员工信息
  5. 查看岗位是teacher且薪资是10000或9000或30000的员工姓名、年龄、薪资
  6. 查看岗位是teacher且薪资不是10000或9000或30000的员工姓名、年龄、薪资
  7. 查看岗位是teacher且名字是jin开头的员工姓名、年薪
  
  select emp_name,age from employee where post = 'teacher';
  select emp_name,age from employee where post='teacher' and age > 30; 
  select emp_name,age,salary from employee where post='teacher' and salary between 9000 and 10000;
  select * from employee where post_comment is not null;
  select emp_name,age,salary from employee where post='teacher' and salary in (10000,9000,30000);
  select emp_name,age,salary from employee where post='teacher' and salary not in (10000,9000,30000);
  select emp_name,salary*12 from employee where post='teacher' and emp_name like 'jin%';
  ```

- **group by 分组 **

  ```mysql
  单独使用GROUP BY关键字分组
      SELECT post FROM employee GROUP BY post;
      注意：我们按照post字段分组，那么select查询的字段只能是post，想要获取组内的其他相关信息，需要借助函数
  
  GROUP BY关键字和GROUP_CONCAT()函数一起使用
      SELECT post,GROUP_CONCAT(emp_name) FROM employee GROUP BY post;#按照岗位分组，并查看组内成员名
      SELECT post,GROUP_CONCAT(emp_name) as emp_members FROM employee GROUP BY post;
  
  GROUP BY与聚合函数一起使用
      select post,count(id) as count from employee group by post;#按照岗位分组，并查看每个组有多少人
      
  #如果我们用unique的字段作为分组的依据，则每一条记录自成一组，这种分组没有意义
  #多条记录之间的某个字段值相同，该字段通常用来作为分组的依据
  ```

- **聚合函数**

  ```mysql
  #强调：聚合函数聚合的是组的内容，若是没有分组，则默认一组
  
  示例：
      SELECT COUNT(*) FROM employee;
      SELECT COUNT(*) FROM employee WHERE depart_id=1;
      SELECT MAX(salary) FROM employee;
      SELECT MIN(salary) FROM employee;
      SELECT AVG(salary) FROM employee;
      SELECT SUM(salary) FROM employee;
      SELECT SUM(salary) FROM employee WHERE depart_id=3;
      
   #小练习
  1. 查询岗位名以及岗位包含的所有员工名字
  2. 查询岗位名以及各岗位内包含的员工个数
  3. 查询公司内男员工和女员工的个数
  4. 查询岗位名以及各岗位的平均薪资
  5. 查询岗位名以及各岗位的最高薪资
  6. 查询岗位名以及各岗位的最低薪资
  7. 查询男员工与男员工的平均薪资，女员工与女员工的平均薪资
  
  #题1：分组
  mysql> select post,group_concat(emp_name) from employee group by post;
  #题目2：
  mysql> select post,count(id) from employee group by post;
  #题目3：
  mysql> select sex,count(id) from employee group by sex;
  #题目4：
  mysql> select post,avg(salary) from employee group by post;
  #题目5
  mysql> select post,max(salary) from employee group by post;
  #题目6
  mysql> select post,min(salary) from employee group by post;
  #题目七
  mysql> select sex,avg(salary) from employee group by sex;
  ```

- **having 过滤**

  ```mysql
  #！！！执行优先级从高到低：where > group by > having 
  #1. Where 发生在分组group by之前，因而Where中可以有任意字段，但是绝对不能使用聚合函数。
  #2. Having发生在分组group by之后，因而Having中可以使用分组的字段，无法直接取到其他字段,可以使用聚合函数
  
  mysql> select * from emp where salary > 100000;  #查询工资大于100000的
  mysql> select post,group_concat(emp_name) from employee group by post having salary > 10000;#错误，分组后无法直接取到salary字段，也就是说having 的判断对象必须在select部分能取到
  
  1. 查询各岗位内包含的员工个数小于2的岗位名、岗位内包含员工名字、个数
  3. 查询各岗位平均薪资大于10000的岗位名、平均工资
  4. 查询各岗位平均薪资大于10000且小于20000的岗位名、平均工资
  #题1：
  mysql> select post,group_concat(emp_name),count(id) from employee group by post having count(id) < 2;
  #题目2：
  mysql> select post,avg(salary) from employee group by post having avg(salary) > 10000;
  #题目3：
  mysql> select post,avg(salary) from employee group by post having avg(salary) > 10000 and avg(salary) <20000;
  ```

- **order by查询排序**

  ```mysql
  按单列排序
      SELECT * FROM employee ORDER BY salary;
      SELECT * FROM employee ORDER BY salary ASC;  #升序
      SELECT * FROM employee ORDER BY salary DESC;  #降序
  
  按多列排序:先按照age排序，如果年纪相同，则按照薪资排序
      SELECT * from employee
          ORDER BY age,  #先按年龄升序
          salary DESC;    #再在年龄相同的中降序
          
  #小练习
  1. 查询所有员工信息，先按照age升序排序，如果age相同则按照hire_date降序排序
  2. 查询各岗位平均薪资大于10000的岗位名、平均工资,结果按平均薪资升序排列
  3. 查询各岗位平均薪资大于10000的岗位名、平均工资,结果按平均薪资降序排列
  #题目1
  mysql> select * from employee ORDER BY age asc,hire_date desc;
  #题目2
  mysql> select post,avg(salary) from employee group by post having avg(salary) > 10000 order by avg(salary) asc;
  #题目3
  mysql> select post,avg(salary) from employee group by post having avg(salary) > 10000 order by avg(salary) desc;
  ```

- **limit 限制查询记录数**

  ```mysql
  示例：
      SELECT * FROM employee ORDER BY salary DESC 
          LIMIT 3;                    #默认初始位置为0 
      
      SELECT * FROM employee ORDER BY salary DESC
          LIMIT 0,5; #从第0开始，即先查询出第一条，然后包含这一条在内往后查5条
  
      SELECT * FROM employee ORDER BY salary DESC
          LIMIT 5,5; #从第5开始，即先查询出第6条，然后包含这一条在内往后查5条
          
  #小练习
  1. 分页显示，每页5条
  mysql> select * from  employee limit 0,5;
  mysql> select * from  employee limit 5,5;
  ```

- **使用正则表达式查询**

  ```mysql
  SELECT * FROM employee WHERE emp_name REGEXP '^ale';
  SELECT * FROM employee WHERE emp_name REGEXP 'on$';
  SELECT * FROM employee WHERE emp_name REGEXP 'm{2}';
  
  小结：对字符串匹配的方式
  WHERE emp_name = 'egon';
  WHERE emp_name LIKE 'yua%';
  WHERE emp_name REGEXP 'on$';
  
  #小练习
  1、查看所有员工中名字是jin开头，n或者g结果的员工信息
  select * from employee where emp_name regexp '^jin.*[gn]$';
  ```

------

**例题**1：

```mysql
    书名 作者 出版社 价格 出版日期(publish_date)
    倚天屠龙记 egon 北京工业地雷出版社 70 2019-7-1
    九阳神功 alex 人民音乐不好听出版社 5 2018-7-4
    九阴真经 yuan 北京工业地雷出版社 62 2017-7-12
    九阴白骨爪 jin 人民音乐不好听出版社 40 2019–8-7
    独孤九剑 alex 北京工业地雷出版社 12 2017-9-1
    降龙十巴掌 egon 知识产权没有用出版社 20 2019-7-5
    葵花宝典 yuan 知识产权没有用出版社 33 2019–8-2

0.建表book，并向表中插入数据
1.查询egon写的所有书和价格
 	mysql>select autor,name,price from book where autor='egon';
2.找出最贵的图书的价格
	mysql> select name,(max(price)) as max_price from book; 
3.求所有图书的均价
	mysql> select (avg(price)) as avg_price from book; 
4.将所有图书按照出版日期排序
	mysql> select name,data_time from book order by data_time;
5.查询alex写的所有书的平均价格
	mysql> select group_concat(name) name,autor,avg(price) avgname from book group by autor having autor='alex';
6.查询人民音乐不好听出版社出版的所有图书
	mysql> select group_concat(name) allname from book group by public having public='人民音乐不好听出版社'; 
7.查询人民音乐出版社出版的alex写的所有图书和价格
	mysql> select name ,autor from book group by public having public='人民音乐不好听出版社' and autor='alex';  
8.找出出版图书均价最高的作者
	mysql> select autor,max(price) from book;
9.找出最新出版的图书的作者和出版社
	mysql> select autor,max(data_time) from book;  
10.显示各出版社出版的所有图书	
	mysql> select group_concat(name),public from book group by public;
11.查找价格最高的图书，并将它的价格修改为50元。（当对原来的表进行修改时需要对搜索的表进行重命名）
update book a, 
(select book_id,max(price) from book) b
set a.price=50 
where a.book_id=b.book_id;
12.删除价格最低的那本书对应的数据
13.将所有alex写的书作业修改成alexsb
delete from book where price in (select * from (select min(price) from book) a);
14.select year(publish_date) from book
```

**例题2**、有下图的关系，请完成相应的操作（注意，主体结构是下图，具体数据多于下图）

![1556189100880](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556189100880.png)

```mysql
1、查询男生、女生的人数；
	select gender,(count(sid)) as 个数 from student group by gender;
2、查询姓“张”的学生名单；
	select * from student where sname like '张%';
3、课程平均分从高到低显示
	 select course_id,avg(num) 平均分 from score group by course_id order by 平均分 desc
4、查询有课程成绩小于60分的同学的学号、姓名；
	 select sid 学号,sname 姓名 from student where sid in (select student_id from score where num<60);
5、查询至少有一门课与学号为1的同学所学课程相同的同学的学号和姓名；
	select sname 姓名,sid from student where class_id = (select class_id from student where sid=1) and sid != 1;
6、查询出只选修了一门课程的全部学生的学号和姓名；
select sname 姓名,sid from student where sid in (select sid from score where course_id in (select course_id from score where student_id =1));
7、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分；
mysql> select course_id 课程ID,max(num) 最高分,min(num) 最低分 from score
    -> group by course_id;
8、查询课程编号

9、查询“生物”课程比“物理”课程成绩高的所有学生的学号；

10、查询平均成绩大于60分的同学的学号和平均成绩;
11、查询所有同学的学号、姓名、选课数、总成绩；

12、查询姓“李”的老师的个数；

13、查询没学过“张磊老师”课的同学的学号、姓名；

14、查询学过“1”并且也学过编号“2”课程的同学的学号、姓名；

15、查询学过“李平老师”所教的所有课的同学的学号、姓名；
```

```mysql
select sname from
(select * from student A left join score B on B.student_id = A.sid where course_id=2) as C 
left join 
( select student_id,num,course_id from student D left join score E on E.student_id = D.sid where course_id=1) as F on C.student_id = F.student_id where F.num< C.num;


select sname from
(select * from student A left join score B on B.student_id = A.sid where course_id=2) as C 
left join 
( select * from score where course_id=1) as F on C.student_id =F.student_id where F.num<C.num;
```

