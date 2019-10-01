## Mysql多表查询

- **多表查询**，就是为了解决外键问题，也就是说多表查询，就是将两张及以上的表格通过某种方式拼接成一张表，然后在这表里取我们想要的数据。（多表查询的方法：内连接、外连接之左连接、外连接之有连接、子查询）

  ```mysql
  #先做一些数据准备
  #创建员工表
  create table employee(
  id int primary key auto_increment,
  name varchar(20),
  sex enum('male','female') not null default 'male',
  age int,
  dep_id int
  );
  
  #插入数据
  insert into department values
  (200,'技术'),
  (201,'人力资源'),
  (202,'销售'),
  (203,'运营');
  
  insert into employee(name,sex,age,dep_id) values
  ('egon','male',18,200),
  ('alex','female',48,201),
  ('wupeiqi','male',38,201),
  ('yuanhao','female',28,202),
  ('nvshen','male',18,200),
  ('xiaomage','female',18,204)
  ;
  
  #查看部门表结构
  mysql> desc department;
  +-------+-------------+------+-----+---------+-------+
  | Field | Type        | Null | Key | Default | Extra |
  +-------+-------------+------+-----+---------+-------+
  | id    | int(11)     | YES  |     | NULL    |       |
  | name  | varchar(20) | YES  |     | NULL    |       |
  +-------+-------------+------+-----+---------+-------+
  
  #查看员工表结构
  mysql> desc employee;
  +--------+-----------------------+------+-----+---------+----------------+
  | Field  | Type                  | Null | Key | Default | Extra          |
  +--------+-----------------------+------+-----+---------+----------------+
  | id     | int(11)               | NO   | PRI | NULL    | auto_increment |
  | name   | varchar(20)           | YES  |     | NULL    |                |
  | sex    | enum('male','female') | NO   |     | male    |                |
  | age    | int(11)               | YES  |     | NULL    |                |
  | dep_id | int(11)               | YES  |     | NULL    |                |
  +--------+-----------------------+------+-----+---------+----------------+
  
  #查看部门表记录
  mysql> select * from department;
  +------+--------------+
  | id   | name         |
  +------+--------------+
  |  200 | 技术         |
  |  201 | 人力资源     |
  |  202 | 销售         |
  |  203 | 运营         |
  +------+--------------+
  
  #查看员工表记录
  mysql> select * from employee;
  +----+----------+--------+------+--------+
  | id | name     | sex    | age  | dep_id |
  +----+----------+--------+------+--------+
  |  1 | egon     | male   |   18 |    200 |
  |  2 | alex     | female |   48 |    201 |
  |  3 | wupeiqi  | male   |   38 |    201 |
  |  4 | yuanhao  | female |   28 |    202 |
  |  5 | nvshen   | male   |   18 |    200 |
  |  6 | xiaomage | female |   18 |    204 |
  +----+----------+--------+------+--------+
  #观察两张表，发现department表中id=203部门在employee中没有对应的员工，发现employee中id=6的员工在department表中没有对应关系。
  ```

- 以上已经创建好两张表，比如现在我要查询的员工信息以及该员工所在的部门。从该题中，我们看出既要查员工又要查该员工的部门，肯定要将两张表进行连接查询，多表连接查询。

  ```mysql
  # 多表查询语法
  SELECT 字段列表
      FROM 表1 INNER|LEFT|RIGHT JOIN 表2
      ON  表1.字段 = 表2.字段;
  ```

  

#### 一、多表查询之内外连接

- 1、交叉连接：不适用任何匹配条件，只是用于生成笛卡尔积（其实可以用把我们要连接的每一张表看作一个基，就好比坐标系的下x,y的维度，这样可以确保两张表的每个元素都落在这个区域内）。

  ```mysql
  mysql> select * from employee,department;
  +----+------------+--------+------+--------+------+--------------+
  | id | name       | sex    | age  | dep_id | id   | name         |
  +----+------------+--------+------+--------+------+--------------+
  |  1 | egon       | male   |   18 |    200 |  200 | 技术         |
  |  1 | egon       | male   |   18 |    200 |  201 | 人力资源     |
  |  1 | egon       | male   |   18 |    200 |  202 | 销售         |
  |  1 | egon       | male   |   18 |    200 |  203 | 运营         |
  |  2 | alex       | female |   48 |    201 |  200 | 技术         |
  |  2 | alex       | female |   48 |    201 |  201 | 人力资源     |
  |  2 | alex       | female |   48 |    201 |  202 | 销售         |
  |  2 | alex       | female |   48 |    201 |  203 | 运营         |
  |  3 | wupeiqi    | male   |   38 |    201 |  200 | 技术         |
  |  3 | wupeiqi    | male   |   38 |    201 |  201 | 人力资源     |
  |  3 | wupeiqi    | male   |   38 |    201 |  202 | 销售         |
  |  3 | wupeiqi    | male   |   38 |    201 |  203 | 运营         |
  |  4 | yuanhao    | female |   28 |    202 |  200 | 技术         |
  |  4 | yuanhao    | female |   28 |    202 |  201 | 人力资源     |
  |  4 | yuanhao    | female |   28 |    202 |  202 | 销售         |
  |  4 | yuanhao    | female |   28 |    202 |  203 | 运营         |
  |  5 | liwenzhou  | male   |   18 |    200 |  200 | 技术         |
  |  5 | liwenzhou  | male   |   18 |    200 |  201 | 人力资源     |
  |  5 | liwenzhou  | male   |   18 |    200 |  202 | 销售         |
  |  5 | liwenzhou  | male   |   18 |    200 |  203 | 运营         |
  |  6 | jingliyang | female |   18 |    204 |  200 | 技术         |
  |  6 | jingliyang | female |   18 |    204 |  201 | 人力资源     |
  |  6 | jingliyang | female |   18 |    204 |  202 | 销售         |
  |  6 | jingliyang | female |   18 |    204 |  203 | 运营         |
  +----+------------+--------+------+--------+------+--------------+
  #以上数据表明每一个人都与部门想念关联，我们要做的只需要在这张表中选出我们要的，其实我们下面要讲的所有连接都是基于交叉连接的。
  #两个集合X和Y的笛卡尓积（Cartesian product），又称直积，表示为X × Y，第一个对象是X的成员而第二个对象是Y的所有可能有序对的其中一个成员 
  ```

- 2、内链接：在交叉连接的基础上找出两张表共有的部分 （inner 表A join 表B on 连接条件 where 条件……）

  ```mysql
  #找两张表共有的部分，相当于利用条件从笛卡尔积结果中筛选出了正确的结果
  #department没有204这个部门，因而employee表中关于204这条员工信息没有匹配出来
  mysql> select employee.id,employee.name,employee.age,employee.sex,department.name from employee inner join department on employee.dep_id=department.id; 
  +----+-----------+------+--------+--------------+
  | id | name      | age  | sex    | name         |
  +----+-----------+------+--------+--------------+
  |  1 | egon      |   18 | male   | 技术         |
  |  2 | alex      |   48 | female | 人力资源     |
  |  3 | wupeiqi   |   38 | male   | 人力资源     |
  |  4 | yuanhao   |   28 | female | 销售         |
  |  5 | liwenzhou |   18 | male   | 技术         |
  +----+-----------+------+--------+--------------+
  
  #上述sql等同于
  mysql> select employee.id,employee.name,employee.age,employee.sex,department.name from employee,department where employee.dep_id=department.id;
  ```

- 3、外连接之左连接：优先显示左表全部记录（倾向于左表）

  ```mysql
  #以左表为准，即找出所有员工信息，当然包括没有部门的员工
  #本质就是：在内连接的基础上增加左边有右边没有的结果
  mysql> select employee.id,employee.name,department.name as depart_name from employee left join department on employee.dep_id=department.id;
  +----+------------+--------------+
  | id | name       | depart_name  |
  +----+------------+--------------+
  |  1 | egon       | 技术         |
  |  5 | liwenzhou  | 技术         |
  |  2 | alex       | 人力资源     |
  |  3 | wupeiqi    | 人力资源     |
  |  4 | yuanhao    | 销售         |
  |  6 | jingliyang | NULL         |
  +----+------------+-------------
  ```

- 4、外连接之右连接：优先显示右表的记录

  ```mysql
  #以右表为准，即找出所有部门信息，包括没有员工的部门
  #本质就是：在内连接的基础上增加右边有，左边没有的结果
  mysql> select employee.id,employee.name,department.name as depart_name from employee right join department on employee.dep_id=department.id;
  +------+---------+--------------+
  | id   | name    | depart_name  |
  +------+---------+--------------+
  |    1 | egon    | 技术         |
  |    2 | alex    | 人力资源     |
  |    3 | wupeiqi | 人力资源     |
  |    4 | yuanhao | 销售         |
  |    5 | nvshen  | 技术         |
  | NULL | NULL    | 运营         |
  +------+---------+--------------+
  ```

- 5、全外连接：显示左右两个表全部记录

  ```mysql
  #外连接：在内连接的基础上增加左边有右边没有的和右边有左边没有的结果
  #注意：mysql不支持全外连接 full JOIN
  #强调：mysql可以使用此种方式间接实现全外连接
  语法：select * from employee left join department on employee.dep_id = department.id 
         union all
        select * from employee right join department on employee.dep_id = department.id;
  
   mysql> select * from employee left join department on employee.dep_id = department.id
            union
          select * from employee right join department on employee.dep_id = department.id;
  +------+----------+--------+------+--------+------+--------------+
  | id   | name     | sex    | age  | dep_id | id   | name         |
  +------+----------+--------+------+--------+------+--------------+
  |    1 | egon     | male   |   18 |    200 |  200 | 技术         |
  |    5 | nvshen   | male   |   18 |    200 |  200 | 技术         |
  |    2 | alex     | female |   48 |    201 |  201 | 人力资源     |
  |    3 | wupeiqi  | male   |   38 |    201 |  201 | 人力资源     |
  |    4 | yuanhao  | female |   28 |    202 |  202 | 销售         |
  |    6 | xiaomage | female |   18 |    204 | NULL | NULL         |
  | NULL | NULL     | NULL   | NULL |   NULL |  203 | 运营         |
  +------+----------+--------+------+--------+------+--------------+
  
  #注意 union与union all的区别：union会去掉相同的纪录
  ```

- 符合条件连接查询（也就是在原有的连接表中添加条件）

  ```mysql
  #示例1：以内连接的方式查询employee和department表，并且employee表中的age字段值必须大于25,即找出年龄大于25岁的员工以及员工所在的部门
  select employee.name,department.name from employee inner join department
      on employee.dep_id = department.id
      where age > 25;
  
  #示例2：以内连接的方式查询employee和department表，并且以age字段的升序方式显示
  select employee.id,employee.name,employee.age,department.name from employee,department
      where employee.dep_id = department.id
      and age > 25
      order by age asc;
  ```

  

#### 二、多表查询之子查询 

​	无论是内外连接还是子查询都要注意select 后面选取的字段尽量提取需要的，当三个表连接时，如果都是使用*，那么在第二次取时，可能会有重复字段，不利于条件筛选。

```
1：子查询是将一个查询语句嵌套在另一个查询语句中。
2：内层查询语句的查询结果，可以为外层查询语句提供查询条件。
3：子查询中可以包含：IN、NOT IN、ANY、ALL、EXISTS 和 NOT EXISTS等关键字
4：还可以包含比较运算符：= 、 !=、> 、<等
```

- 带 in 关键字的子查询

  ```mysql
  #题1. 查询平均年龄在25岁以上的部门名
       #分析：先找出平均年龄大于25岁的的员工部门id
  mysql> select dep_id from employee group by dep_id having avg(age) > 25;
  +--------+
  | dep_id |
  +--------+
  |    201 |
  |    202 |
  +--------+
  
  #题2，根据部门id，到部门表department中查询。就可以找到部门名，需要用到上面的查询结果。使用where id in (...) 可以得到多条结果。
  select id,name from department
      where id in 
          (select dep_id from employee group by dep_id having avg(age) > 25);
  #返回结果：
  +------+--------------+
  | id   | name         |
  +------+--------------+
  |  201 | 人力资源     |
  |  202 | 销售         |
  +------+--------------+
  
  #题3、查看技术部员工姓名
  select name from employee
      where dep_id in 
          (select id from department where name='技术');
  
  #题4、查看不足1人的部门名(子查询得到的是有人的部门id)
  select name from department where id not in (select distinct dep_id from employee);
  ```

- 带比较运算符的子查询

  ```mysql
  #比较运算符：=、!=、>、>=、<、<=、<>
  #查询大于所有人平均年龄的员工名与年龄
  mysql> select name,age from employee where age > (select avg(age) from employee);
  +---------+------+
  | name    | age  |
  +---------+------+
  | alex    |   48 |
  | wupeiqi |   38 |
  +---------+------+
  
  #查询大于部门内平均年龄的员工名、年龄
  思路：
        （1）先对员工表(employee)中的人员分组(group by)，查询出dep_id以及平均年龄。
         (2)将查出的结果作为临时表,再对根据临时表的dep_id和employee的dep_id作为筛选条件将employee表和临时表进行内连接。
         (3)最后再将employee员工的年龄是大于平均年龄的员工名字和年龄筛选。
  
  mysql> select t1.name,t1.age from employee as t1
               inner join
              (select dep_id,avg(age) as avg_age from employee group by dep_id) as t2
              on t1.dep_id = t2.dep_id
              where t1.age > t2.avg_age;
  
  #返回结果
  +------+------+
  | name | age  |
  +------+------+
  | alex |   48 |
  +------+------+
  ```

- 带exists关键字的子查询

  ```mysql
  #EXISTS关字键字表示存在。在使用EXISTS关键字时，内层查询语句不返回查询的记录。而是返回一个真假值。True或False
  #当返回True时，外层查询语句将进行查询；当返回值为False时，外层查询语句不进行查询
  #department表中存在dept_id=203，Ture
  mysql> select * from employee  where exists (select id from department where id=200);
  +----+----------+--------+------+--------+
  | id | name     | sex    | age  | dep_id |
  +----+----------+--------+------+--------+
  |  1 | egon     | male   |   18 |    200 |
  |  2 | alex     | female |   48 |    201 |
  |  3 | wupeiqi  | male   |   38 |    201 |
  |  4 | yuanhao  | female |   28 |    202 |
  |  5 | nvshen   | male   |   18 |    200 |
  |  6 | xiaomage | female |   18 |    204 |
  +----+----------+--------+------+--------+
  #department表中存在dept_id=205，False
  mysql> select * from employee  where exists (select id from department where id=204);
  Empty set (0.00 sec)  #也就是说条件不成立，不返回结果
  ```

- 综合练习

  ```mysql
  #创建表
  create table employee(
  id int not null unique auto_increment,
  name varchar(20) not null,
  sex enum('male','female') not null default 'male', #大部分是男的
  age int(3) unsigned not null default 28,
  hire_date date not null,
  post varchar(50),
  post_comment varchar(100),
  salary double(15,2),
  office int, #一个部门一个房间
  depart_id int
  );
  
  #查看表结构
  mysql> desc employee;
  +--------------+-----------------------+------+-----+---------+----------------+
  | Field        | Type                  | Null | Key | Default | Extra          |
  +--------------+-----------------------+------+-----+---------+----------------+
  | id           | int(11)               | NO   | PRI | NULL    | auto_increment |
  | name         | varchar(20)           | NO   |     | NULL    |                |
  | sex          | enum('male','female') | NO   |     | male    |                |
  | age          | int(3) unsigned       | NO   |     | 28      |                |
  | hire_date    | date                  | NO   |     | NULL    |                |
  | post         | varchar(50)           | YES  |     | NULL    |                |
  | post_comment | varchar(100)          | YES  |     | NULL    |                |
  | salary       | double(15,2)          | YES  |     | NULL    |                |
  | office       | int(11)               | YES  |     | NULL    |                |
  | depart_id    | int(11)               | YES  |     | NULL    |                |
  +--------------+-----------------------+------+-----+---------+----------------+
  
  #插入记录
  #三个部门：教学，销售，运营
  insert into employee(name,sex,age,hire_date,post,salary,office,depart_id) values
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
  
  问题：查询每个部门最新入职的那位员工
  #内外连接做法：
  SELECT
      *
  FROM
      emp AS t1
  INNER JOIN (
      SELECT
          post,
          max(hire_date) max_date
      FROM
          emp
      GROUP BY
          post
  ) AS t2 ON t1.post = t2.post
  WHERE
      t1.hire_date = t2.max_date;
      
  #子查询做法
  
  ```

  