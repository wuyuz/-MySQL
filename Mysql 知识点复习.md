## Mysql 知识点复习



#### 登陆数据库服务器

- mysql -uroot -p123；

#### 查询数据库服务器中的所有数据库

- show databases；

#### 选中数据库并查询一张表的所有数据

- use 数据库名;
- select  *  from admin;

#### 在数据库服务器中创建我们的数据库

- create database test;

#### 在某个数据库中查看数据表

- show tables；

#### 创建一个数据表

- create table pet (

  name  varchar(20),

  owner  varchar(20),

  species  varchar(20),

  sex  char(1),

  birth date); 

#### 查看一张表的详情

- des pet；
- show create table pet;

#### 向表中插入一条数据

- insert into pet values ('full','diane','hamster','f','1993-02-01');

#### Mysql的常用数据类型

Mysql支持多种类型，大致可以分为三类：

- 数值

  ```
  类型            大小              范围（有符号）             用途
  
  tinyint        1字符            （-128，127）               小整数
  smallint       2字符             （-32768， 32767）         大整数
  mediumint      3字符 
  int            4字符
  bigint         8字符
  float          4字符
  double         8字符
  ```

  

- 日期/时间

  ```
  类型            大小               格式                  用途
  
  date           3字符              yyy-mm-dd             日期值
  time           3字符              hh：mm：ss            时间值或持续时间
  year           1字符              yyy                   年份值
  datetime       8字符              yyy-mm-dd hh：mm：ss   混合日期时间
  timestamp      4字符              
  ```

  

- 字符串

  ```
  类型            大小                           用途
  
  cahr           0-255字符                     定长字符串，占空间，但速度快
  varchar        0-65535字符                   变长字符串
  text           0-65535字符                    长文本数据
  longtext       超大文本数据
  ```



#### 数据表的增删改查(insert、 delete、update、select)

- 准备数据

  ```
  insert into pet values('fluffy','harold','cat','f','1995-01-03');
  insert into pet values('Claws','Gwen','cat','f','1991-04-03');
  insert into pet values('Buffy','harold','dog','f','1995-06-05');
  insert into pet values('Fang','Benny','doy','m','1999-01-03');
  insert into pet values('Bowser','diane','dog','m','1990-08-03');
  insert into pet values('slim','benny','snake','m','1997-01-03');
  insert into pet values('puffball','harold','cat','f','1995-01-03');
  ```

- 删除数据

  ```
  delete from pet where name='fluffy';
  ```

- 修改数据

  ```
  update pet set name='旺财' where owner='Benny';
  ```



#### mysql如何修改表结构

- 如何修改主键约束

  ```
  如果忘记添加一张表的主键，我们可以自动修改：
  create table user(
  	id int,
  	name varchar(20));
  
  alter table user add primary key(id);   # 给表添加主键约束
  alter table user drop primary key;        #删除表结构的主键约束
  alter table user modify id int primary key;   #修改字段的主键约束
  ```

- 如何修改字段唯一约束

  ```
  create table user5(
  	id int,
  	name varchar(20) unique);  
  	
  alter table user5 drop index name;  # 删除唯一约束
  alter table user5 modify name varchar(20) unique  # 使用修改的方式添加唯一约束
  alter table user5 add unique(name)   # 通过添加的方式修改字段结构
  ```



#### mysql建表约束

- 主键约束: 字段不重复且不为空

  ```
  create table user(
  	id int primary key,
  	name varchar(20)
  	);
  	
  联合主键：
  create table user2(
  	id int,
  	name varchar(20),
  	password varchar(20),
  	primary key(id,name)   # 联合主键，只要主键值不完全一样即可
  	);
  ```

- 自增约束:  与主键配合使用，自动生成序号

  ```
  create table user3(
  	id int primary key auto_increment,  
  	name varchar(20)
  	);
  ```

- 唯一约束：约束字符的值不可以重复

  ```
  create table user5(
  	id int,
  	name varchar(20));  
  
  #方式一
  alter table user5 add unique(name);
  
  #方式二：
  create table user5(
  	id int,
  	name varchar(20),
  	unique(name));  
  
  # 方式三：
  create table user5(
  	id int,
  	name varchar(20) unique);  
  ```

- 非空约束：修饰的字段不能为空

  ```
  create table user6(
  	id int,
  	name varchar(20) not null)
  ```

- 默认约束: 插入值时没有数据，就是用默认值

  ```
  create table user8(
  	id int,
  	name varchar(20),
  	age int default 10)
  ```

- 外键约束：涉及多张表的关联

  ```
  班级表：
  create table classes(
  	id int primary key,
  	name varchar(20));
  
  学生表：
  create table students(
  	id int primary key,
  	name varchar(20),
  	class_id int,
  	foreign key(class_id) references classes(id));  # 本张表的class_id字段关联classes表的id字段
  	
  插入班级数据：
  insert into classes values(1,'一班');
  insert into classes values(2,'二班');
  insert into classes values(3,'三班');
  
  插入学生数据：
  insert into students values(1001，’张三',1);
  insert into students values(1002，’李三',2);
  insert into students values(1003，’陈三',1);
  ```

  

#### 数据表查询练习

- 数据准备

  ```
  学生表：
  create table student(
  	sno varchar(20) primary key,
  	sname varchar(20) not null,
  	ssex varchar(10) default '女',
  	sbirthday datetime,
  	class varchar(20));
  
  教师表：
  create table teacher(
  	tno varchar(20) primary key,
  	tname varchar(20) not null,
  	tsex varchar(10) not null,
  	tbirthday datetime,
  	prof varchar(20) not null,
  	deport varchar(20) not null
  	)
  
  课程表：
  create table course(
  	cno varchar(20) primary key,
  	cname varchar(20) not null,
  	tno varchar(20) not null,
  	foreign key(tno) references teacher(tno));
  	
  成绩表：
  create table score(
  	sno varchar(20) primary key,
  	cno varchar(20) not null,
  	degree decimal,
  	foreign key(sno) references student(sno),
      foreign key(cno) references course(cno))
      
  往数据表中添加数据：
  insert into student values('108','曾华','男','1997-05-05','9482');
  insert into student values('109','曾小华','女','1993-05-01','9483');
  insert into student values('110','王华','男','1990-05-05','9484');
  insert into student values('111','张强','男','1992-05-05','9485');
  insert into student values('112','曾得','女','1997-05-05','9482');
  insert into student values('113','李欣','女','1997-05-05','9482');
  insert into student values('115','牛牛','女','1993-05-01','9483');
  insert into student values('116','王尼玛','男','1990-05-05','9484');
  insert into student values('117','张全蛋','男','1992-05-05','9485');
  insert into student values('118','赵铁柱','女','1997-05-05','9482');
  
  #教师表数据
  insert into teacher values('216','尼玛','男','1990-05-05','副教授','计算机系');
  insert into teacher values('217','全蛋','男','1992-05-05','讲师','数学');
  insert into teacher values('218','柱','女','1997-05-05','助教','计算机系');
  insert into teacher values('219','鸣人','男','1997-05-05','助教','手里剑');
  
  # 课程表
  insert into course values('3-105','计算机技术','216');
  insert into course values('3-106','离散数学','216');
  insert into course values('4-107','操作系统','218');
  insert into course values('3-108','数字电路','219');
  insert into course values('3-109','代数','217');
  
  # 成绩表：
  insert into score values('108','3-105','89');
  insert into score values('109','3-105','84');
  insert into score values('110','3-105','60');
  insert into score values('111','3-106','20');
  insert into score values('112','3-106','99');
  insert into score values('113','3-107','91');
  insert into score values('114','3-107','83');
  insert into score values('115','3-107','83');
  insert into score values('116','3-108','89');
  insert into score values('118','3-105','9');
  insert into score values('117','3-105','59');
  ```

- 查询student表中得所有记录

  ```
  mysql> select * from student;
  
  mysql> select count(*) 总数 from student;   # 查询总数
  
  mysql> select sname 姓名, ssex 性别 from student;    #查询指定字段
  ```

- 查询教师所有单位即不充分得deport字段

  ```
  mysql> select distinct deport from teacher;
  ```

- 查询score表中成绩在60-80之间得所有记录

  ```
  mysql> select * from score where degree between 60 and 80;
  
  mysql> select * from score where degree > 60 and degree < 80;
  ```

- 查询score表中成绩为83，86或91得记录

  ```
  mysql> select * from score where degree in (83, 86, 91);
  ```

- 查询student表中'9482'班或性别为'女'的同学的记录

  ```
  mysql> select * from student where class = '9482' or ssex='女';
  ```

- 以class降序查询student表的所有记录

  ```
  mysql> select * from student order by class desc;
  ```

- 以cno升序、degree降序查询score表的所有记录

  ```
  mysql> select * from score order by cno asc,degree desc;
  ```

- 查询‘9482’ 班的所有学生人数

  ```
  mysql>select count(*) from student where class = '9482';
  ```

- 查询score表中的最高分的学生学号和课程号（子查询和排序）

  ```
  mysql>select sno, cno from score where degree=(select max(degree) from score);
  
  mysql> select sno,cno from score order by degree desc limit 0,1;
  ```

- 查询每门课的平均成绩

  ```
  mysql>select cno,avg(degree) from score group by cno; #分组
  ```

- 查询score表中至少有两名学生选修的并以3开头的课程的平均成绩

  ```
  select cno,avg(degree) from score group by cno having count(*) >= 2 and cno like '3%';
  ```

- 查询分数大于70，小于90的sno列

  ```
  select sno,degree from score where between 70 and 90;
  ```

- 查询所有学生的sname,cno和degree列

  ```
  mysql> select sname, cno, degree from student left join score on student.sno = score.sno;
  
  mysql>select sname, cno, degree from student,score where student.sno = score.sno;
  ```

- 查询所有学生的sno,cname和degree列

  ```
  mysql>select sno,cname,degree from course,score where course.cno = score.cno;
  ```

- 查询所有学生的sname,cname,degree列

  ```
  mysql>select sname,cname,degree from student,course,score where student.sno = score.sno and course.cno = score.cno;
  
  mysql>select sname,cname,degree from (select cno, sname,degree from student left join score on student.sno = score.sno) as A left join course as B on B.cno =  A.cno;
  ```

- 查询 '9484' 班学生每门课的平均分

  ```
  mysql>select cno, avg(degree) from score where sno in (select sno from student where class ='9484') group by cno;
  ```

- 查询选修’3-105'课程的成绩高于‘109’号同学‘3-105’成绩的所有同学记录

  ```
  mysql>select * from score where cno='3-105' and degree > (select degree from score where sno='109' and cno='3-105');
  ```

- 查询成绩高于学号为‘109’，课程号为‘3-105’的成绩的所有记录

  ```
  mysql>select * from score where degree > (select degree from score where sno='109' and cno='3-105' );
  ```

- 查询和学号为108，111的同学同年出生的所有学生的sno 、sname和sbirthday列

  ```
  mysql>select * from student where year(sbirthday) in (select year(sbirthday) from student where sno in (108,111));
  ```

- 查询 '尼玛' 老师任课的学生成绩

  ```
  mysql>select * from score where cno in (select cno from course where tno =(select tno from teacher where tname ='尼玛'));
  ```

- 查询选修某课程的同学人数多于3人的教师姓名

  ```
  mysql>select tname from teacher where tno=(select tno from course where cno =(select cno from score group by cno having count(*)>3))
  ```

- 查询'9484'班和 '9482'班的全体学生记录

  ```
  mysql>select * from student where class in ('9484','9482');
  ```

- 查询出’计算机系‘教师所教课程的成绩表

  ```
  mysql>select * from score where cno in (select cno from course where tno in (select tno from teacher where deport='计算机系'));
  ```

- 查询‘计算机系’ 和‘手里剑’ 不同职称的教师的tname和prof     （使用union来求笛卡尔乘积，并集）

  ```
  mysql>select * from teacher where deport='计算机专业' and prof not in (select prof from teacher where deport='手里剑') union select * from teacher where deport='手里剑' and prof not in (select prof from teacher where deport='计算机专业');
  ```

- 查询选修编号’3-106‘课程且成绩至少高于选修编号为’3-107‘的同学的cno、sno和degree，并按Degree从高到低排序  ( 使用any函数来比较每个子查询元素，只要有一个满足就可以， **any表示至少**)

  ```
  select * from score where cno = '3-106';
  select * from score where cno = '3-107';
  
  mysql>select * from score where cno='3-106' and degree > any(select degree from score where cno='3-107') order by degree desc;
  ```

- 查询选修编号’3-106‘课程且成绩高于选修编号为’3-107‘的同学的cno、sno和degree（使用all，表示比每一个都要高，**all表示全部**）

  ```
  mysql>select * from score where cno='3-106' and degree > all(select degree from score where cno='3-107');
  ```

- 查询所有'女'教师和'女'同学的name，sex和birthday

  ```
  mysql>select tname as name,tsex as sex,tbirthday as birthday from teacher where tsex='女' union select sname,ssex,sbirthday from student where ssex='女';
  ```

- 查询成绩比该课程平均成绩低的同学的成绩表

  ```
  mysql>select * from score as A where degree < (select avg(degree) from score as B where A.cno=B.cno);
  ```

- 查询所有任课教师的tname和deport

  ```
  mysql>select * from teacher where tno in (select tno from course);
  ```

- 查询至少有2名男生的班号

  ```
  mysql>select * from student where ssex = '男' group by class having count(*) >1;
  ```

- 查询student表中不姓'曾'的同学记录

  ```
  mysql>select * from student where sname not like '曾%';
  ```

- 查询student表中的每个学生的姓名和年纪

  ```
  mysql>select sname, year(now()) - year(sbirthday) as '年纪' from student;
  ```

- 查询student表中的最大和最小的sbirthday日期值

  ```
  mysql>select max(sbirthday) as '最大值', min(sbirthday) as '最小值' from student;
  ```

- 查询和'王华'同性别的所有同学的sname

  ```
  mysql>select sname from student where ssex=(select ssex from student where sname='王华');
  ```

- 查询和'王华'同性别并同班的同学的sname

  ```
  mysql>select sname from student where ssex=(select ssex from student where sname='王华') and class=(select class from student where sname='王华');
  ```

- 查询所有选修"计算机技术"课程的"男" 同学的成绩表

  ```
  select * from score where cno =(select cno from course where cname='计算机技术') and sno in (select sno from student where ssex ='男');
  ```



#### 创建两个表

```
person表：
create table person(
	id int,
	name varchar(20),
	cardId int);
	
card表：
create table card(
	id int,
	name varchar(20));
	
insert into card values(1,'饭卡'),(2,'建行卡'),(3,'农行卡'),(4,'工商卡'),(5,'邮政卡');

insert into person values(1,'张三',1),(2,'李四',1),(3,'王五',2),(4,'二麻子',3),(5,'李四',4),(6,'冯五',7);
```

-  inner join 查询

  ```
  select * from person inner join card on person.cardId = card.id;
  
  +------+--------+--------+------+--------+
  | id   | name   | cardId | id   | name   |
  +------+--------+--------+------+--------+
  |    1 | 张三   |      1 |    1 | 饭卡    |
  |    2 | 李四   |      1 |    1 | 饭卡    |
  |    3 | 王五   |      2 |    2 | 建行卡  |
  |    4 | 二麻子 |      3 |    3 | 农行卡   |
  |    5 | 李四   |      4 |    4 | 工商卡   |
  ```

- 内联查询，其实就是两张表中的数据，通过某个字段相对，查询出相关记录数据

  ```
  select * from person join card on person.cardId = card.id;
  
  +------+--------+--------+------+--------+
  | id   | name   | cardId | id   | name   |
  +------+--------+--------+------+--------+
  |    1 | 张三   |      1 |    1 | 饭卡   |
  |    2 | 李四   |      1 |    1 | 饭卡   |
  |    3 | 王五   |      2 |    2 | 建行卡 |
  |    4 | 二麻子 |      3 |    3 | 农行卡 |
  |    5 | 李四   |      4 |    4 | 工商卡 |
  +------+--------+--------+------+--------+
  ```

- left join 左联接, 会把左边所有的数据取出来，而右边数据，如果有相等的就显示，没有就null

  ```
  select * from person left join card on person.cardId = card.id;
  
  +------+--------+--------+------+--------+
  | id   | name   | cardId | id   | name   |
  +------+--------+--------+------+--------+
  |    1 | 张三   |      1 |    1 | 饭卡   |
  |    2 | 李四   |      1 |    1 | 饭卡   |
  |    3 | 王五   |      2 |    2 | 建行卡 |
  |    4 | 二麻子 |      3 |    3 | 农行卡 |
  |    5 | 李四   |      4 |    4 | 工商卡 |
  |    6 | 冯五   |      7 | NULL | NULL   |
  ```

  