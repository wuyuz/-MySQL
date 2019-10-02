Mysql常见函数、知识补充



#### 概念：

将一组逻辑语句封装在方法体中，对外暴露方法名

#### 分类：

- 单行函数

  如 concat、length、ifnull等

- 分组函数

  功能：做统计使用，又称为统计函数、聚合函数、组函数



#### 常见函数

##### 一、字符函数：

- length 长度

  ```
  mysql> select length('john');
  +----------------+
  | length('john') |
  +----------------+
  |              4 |
  +----------------+
  
  mysql> select length('张三haha');
  +--------------------+
  | length('张三haha') |
  +--------------------+
  |                  8 |
  +--------------------+
  ```

- concat 拼接字符串

  ```
  select concat(last_name,'_',first_name) 姓名 from employees;
  
  mysql> select concat(id,'_',name) 姓名 from person;
  +----------+
  | 姓名     |
  +----------+
  | 1_张三   |
  | 2_李四   |
  | 3_王五   |
  | 4_二麻子 |
  | 5_李四   |
  | 6_冯五   |
  +----------+
  ```

- upper 、lower 大小写

  ```
  mysql> select upper('join');
  +---------------+
  | upper('join') |
  +---------------+
  | JOIN          |
  +---------------+
  
  select concat(upper(last_name),lower(first_name)) 姓名 from employees;
  ```

- substr、substring 截取字符串

  ```
  mysql> select substr('李莫愁爱上了陆湛远',7);
  +--------------------------------+
  | substr('李莫愁爱上了陆湛远',7) |
  +--------------------------------+
  | 陆湛远                         |
  +--------------------------------+
  
  mysql> select substr('李莫愁爱上了陆湛远',1,3);
  +----------------------------------+
  | substr('李莫愁爱上了陆湛远',1,3) |
  +----------------------------------+
  | 李莫愁                           |
  +----------------------------------+
  ```

- trim 去后前后空格

  ```
  mysql> select trim('   张翠山 ');
  +--------------------+
  | trim('   张翠山 ') |
  +--------------------+
  | 张翠山             |
  
  mysql> select trim('a' from 'aaaa张翠山 aaaa');
  +----------------------------------+
  | trim('a' from 'aaaa张翠山 aaaa') |
  +----------------------------------+
  | 张翠山                           |
  ```

- lpad 左填充，最终给长度一致；rpad 右填充

  ```
  mysql> select lpad('殷素素',10,'+') out_put;
  +---------------+
  | out_put       |
  +---------------+
  | +++++++殷素素 |
  +---------------+
  ```

- replace 替换

  ```
  mysql> select replace('张无忌爱上周芷若' ,'周芷若','赵明') out_put;
  +----------------+
  | out_put        |
  +----------------+
  | 张无忌爱上赵明 |
  +----------------+
  ```



##### 二、数学函数

- round 四舍五入

  ```
  mysql> select round(1.45);
  +-------------+
  | round(1.45) |
  +-------------+
  |           1 |
  +-------------+
  
  mysql> select round(-1.45);
  +--------------+
  | round(-1.45) |
  +--------------+
  |           -1 |
  +--------------+
  
  mysql> select round(-1.45,2);
  +----------------+
  | round(-1.45,2) |
  +----------------+
  |          -1.45 |
  +----------------+
  ```

- ceil 向上取整，返回大于等于该参数的最小整数

  ```
  mysql> select ceil(1.02);
  +------------+
  | ceil(1.02) |
  +------------+
  |          2 |
  +------------+
  1 row in set (0.00 sec)
  
  mysql> select ceil(-1.02);
  +-------------+
  | ceil(-1.02) |
  +-------------+
  |          -1 |
  +-------------+
  ```

- floor 向下取整，返回小于等于该参数的最大整数

  ```
  mysql> select floor(9.99);
  +-------------+
  | floor(9.99) |
  +-------------+
  |           9 |
  +-------------+
  1 row in set (0.00 sec)
  
  mysql> select floor(-9.99);
  +--------------+
  | floor(-9.99) |
  +--------------+
  |          -10 |
  +--------------+
  ```

- truncate 截断

  ```
  select truncate(1.699,1);
  ```

- mod 取余

  ```
  select mode(10,3)
  ```



##### 三、日期函数

- now 返回当前系统日期+时间

  ```
  selcet now();
  ```

- curdate 返回当前系统日期，不包含时间

  ```
  mysql> select curdate();
  +------------+
  | curdate()  |
  +------------+
  | 2019-10-02 |
  +------------+
  ```

- curtime 返回当前时间，不包含日期

  ```
  mysql> select curtime();
  +-----------+
  | curtime() |
  +-----------+
  | 09:46:16  |
  +-----------+
  ```

- 可以获取指定部分，年、月、日、小时、分、秒

  ```
  mysql> select year(now()) 年;
  +------+
  | 年   |
  +------+
  | 2019 |
  +------+
  
  mysql> select month(now()) 月;
  +------+
  | 月   |
  +------+
  |   10 |
  +------+
  ```

  

##### 四、流程控制函数

- if函数： if else 的效果，常用于通过一段字段来判断是否合乎要求

  ```
  mysql> select if(10>5,'大','小') 判断;
  +------+
  | 判断 |
  +------+
  | 大   |
  
  select last_name, commission_pct,if(commission_pct is null,'没奖金','有奖金');
  ```

- case 函数：

  ```
  用法一: 添加分支，类似于switch
  
  案例： 查询员工的工资，要求
  		部门号为30,显示的工资为1.1倍；
  		部门号为40,显示的工资为1.2倍；
  		部门号为50,显示的工资为1.3倍；
  		其他部门工资不变。
  select salary 元素工资, department_id,
  	case department_id
  		when 30 then salary*1.1
  		when 40 then salary*1.2
  		when 50 then salary*1.3
  		else salary
  		end as 新工资
  from employees;
  
  用法二：多重if
  案例; 查询员工的工资的情况，要求
  		如果工资>2000，显示A级别
  		如果工资>1500，显示B级别
  		如果工资>1000，显示C级别
  		否则显示D级别
  select salary, 
  case
  	when salary > 2000 then 'A'
  	when salary > 1500 then 'B'
  	when salary > 1000 then 'C'
  	else 'D'
  	end as 工资级别
  from employees;
  ```

  

##### 五、分组函数

功能：用作统计使用，又称聚合函数或统计函数

``` 
select sum(salary) from employees;
select avg(salary) from employees;
select max(salary) from employees;
select min(salary) from employees;
select count(salary) from employees;

select sum(distinct salary) from employees;
select count(distinct salary)  from employees;

案例:
1、按员工姓名的长度分组， 查询每一组员工个数，筛选员工个数>5的有那些？
select count(*),length(last_name) len_name from employees group by length(last_name) having count(*)>5;

2、查询每个部门每个工种的员工的平均工资 (多个条件进行分组)
select avg(salary), department_id,job_id from employees group by department_id,job_id;
```



#### 连接查询

```
按功能分类：
	内连接：
		等值连接
		非等值连接
		自连接
	外连接：
		左外连接
		右外连接
		全外连接
	交叉连接
```

又称多表查询，当查询的字段来自多个表时，就会用到连接查询

- 笛卡尔乘积现象：表1有m行，表2有n行，结果=m*n行

  发生原因： 没有有效的连接条件

  如何避免：添加有效的连接条件

  ```
  select name, boyname from boys, beauty;
  
  笛卡尔乘积添加条件：
  select name,boyname from boys,beauty where beauty.boyfriend_id = boy.id;
  ```

- 等值连接

  ```
  案例：
  1、查询女神名和对应的男神名
  select name, boyname from boys,beauty where beauty.boyfriend_id = boys.id
  
  2、查询员工名对应的部门名
  select last_name, department_name from employees,departments where employees.department_id = departments.department_id;
  
  3、查询有奖金的员工名，部门名   (筛选)
  select last_name,department_name,commission_pct from employee e,departments d where e.department_id = d.department_id and e.commission_pct is not null;
  
  4、查询每个城市的部门个数
  select count(*) 个数,city from departments d, locations l group by city;
  
  5、查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
  select department_name,d.manager_id, min(salary)
  	from deparments d, employees e where d.department_id = e.department_id and e.commission_pct is not null group by department_name, d.manager_id;
  
  ---------------------------------------------
  sql99语法：要使用join关键字 
  6、查询员工名、部门名
  select last_name,department_name from employees e inner join department d on e.department_id = d.department_id
  
  7、查询名字中包含e的员工名和工种名（添加筛选）
  select last_name,job_title from employee e inner join jobs j on e.job_id = j.job_id where e.last_name like '%e%';
  
  8、查询部门个数>3的城市名和部门个数 (添加分组+筛选)
  select city, count(*) 部门个数 from departments d inner join locations l on d.location_id = l.location_id group by city having count(*) > 3;
  
  9、查询员工名、部门名、工种名，并按部门名降序
  select last_name,department_name, job_title from employees e
  	inner join departments d on e.department_id = d.department_id 
  	inner join jobs j on e.job_id = j.job_id
  order by department_name desc;
  ```

- 非等值连接

  ```
  案例：
  1、查询员工的工资和工资级别
  select salary, grade_level from employees e, job_grades g where salary between g.lowest_sal and g.highest_sal;
  
  ---------------------------------------------
  sql99语法：要使用join关键字 
  2、查询员工的工资和工资级别
  select salary,grade_level from employees e join job_grades g on e.salary between g.lowest_sal and g.highest_sal;
  ```

- 自连接 （针对于一些特殊的表，自己表查自己表）

  ```
  案例：
  1、查询员工名和上级的名称
  select e.employee_id, e.last_name, m.employee_id,m.last_name from employees e, employees m where e.manager_id = m.employee_id;
  
  ---------------------------------------------
  sql99语法：要使用join关键字 
  2、查询员工名和上级的名称
  select e.last_name, m.last_name from employees e 
  	join employees m on e.manager_id = m.employee_id;
  ```

- 外连接：

  应用场景： 用于查询一个表中有，另一表中没有的记录

  左外连接:   left join  左边的是主表

  右外连接：right join 右边的是主表

  ```
  案例
  1、查询男朋友、不再男神表中的女神名
  select b.name,bo.* from beauty b left beauty b left outer join boys bo on b.boyfriend_id = bo.id where bo.id is null;
  ```

  

#### 子查询

```
按结果集的行列数不同分类：
	标量子查询（结果集只有一行一列）
	列子查询（结果集只有一列多行）
	行子查询（结果集有一行多列）
	表子查询（结果集一般为多行多列）
	
特点：
	1、子查询放在小括号中
	2、子查询一般放在条件右侧
	3、标量子查询，一般搭配着单行操作符使用：> < >= <= <>
	
	列子查询，一般搭配着多行操作符使用： in 、any/some 、all
		in/not in : 等于列表中的任意一个
		any/some： 和子查询返回的某一值比较
		all: 和子查询返回额所有值比较
```

- 标量子查询

  ```
  1、查询abel的工资
  select salary from employees where last_name ='abel';
  
  2、查询员工的信息，满足selary>alel的工资
  select * from employees where salary >(select salary from employees where last_name ='abel');
  
  3、查询员工的姓名、job_id 和工资，要求job_id=abel的id且salary大于员工id为143的
  select last_name, job_id,salary from employees where job_id =(
  	select job_id from employees where employee_id =141
  	) and salary > (
  	select salary from employees where employee_id = 143);
  ```

- 列子查询 （多行子查询）

  ```
  1、 返回location_id 是1400或1700的部门中的所有员工的姓名
  	分析：
  	1、查询location_id 是1400或1700的部门标号
  		select distinct department_id from departments where location_id in (1400,1700);
  	2、最终版本：
  		select last_name from employees where department_id in (select distinct department_id from departments where location_id in (1400,1700));
  		
  2、 返回其他部门中比job_id 为’it_rpog'部门任一工资低的员工的员工号、姓名、job_id以及salary
  	分析：
  	1、 select distinct salary from employees where job_id = 'it_rpog';
  	2、最终版本:
  		select last_name,employee_id, job_id, salary from employees where salary < any(select distinct salary from employees where job_id = 'it_rpog');	
  ```

- 行子查询

  ```
  1、查询员工编号最小并且工资最高的员工信息
  select * from employees where (employee_id, salary) = (select min(employee_id),max(salary) from emmployees);
  
  2、查询员工号=102的部门名
  select (
  	select department_name from departments d inner join employee e on d.department_id = e.department_id where e.employee_id=102) 部门名;
  ```

  

#### 联合查询

union联合 ： 合并： 将多条查询语句的结果合并成一个结果

```
1、查询部门编号>90 或邮箱包含a的员工信息
select * from employees where email like '%a%'
union
select * from employees where department_id >90;
```



DML语言

#### 数据操作语言

```
插入：insert
	insert into 表名(列名，...) values(值1，....)
	insert into 表名 set 列名=值，...

修改：update
	update 表名 set 列名=新值，...where 筛选条件
	update 表1,表2 set 列=值，... where 连接条件 and 筛选条件;
	
删除： delete 
	 delete from 表名 where 筛选条件
	 delete 表1的别名|表2的别名 from 表1 别名 inner|left|rigit join 表2 别名 on 连接条件
	 where 筛选条件
	
1、修改张无忌的女朋友的手机号为114
	update boys bo inner join beauty b on bo.id=b.boyfriend_id set b.phone='114' where bo.boyname='张无忌';
	
2、删除手机号以9结尾的女神的信息
	delete from  beauty where phone like '%9';

3、删除张无忌的女朋友的信息
	delete b from beauty b inner join boys bo on b.boyfriend_id = bo.id where bo.boyname='张无忌';
	
```



DDL语言

#### 库管理

```
创建库：
 create database if not exists books;
 
库的修改：
 alter database books character set gbk;
 
库的删除：
 drop database if exists books;
```

#### 表管理

```
表的创建
 create table 表名 (
 	列名 列的类型[长度、约束],
 	列名 列的类型[长度、约束],
 	...
 	)
 	
 create table book(
 	id int,
 	bName varchar(20),
 	publishDate datetime
 	...)
 	
表的修改
 修改列名
 	alter table book change column publishDate pubDate DateTime;
 修改列的类型或约束
 	alter table book modify column pubDate timestamp;
 新增列
 	alter table book add column count int;
 删除列
 	alter table book drop column count;
 修改表名：
 	alter table book rename to new_book;
 
表的删除
 drop table new_book;	
 truncate table 表名;    #清空表
 
表的复制
 1、仅仅复制表的结构
 	create table copy like author;
 2、复制表结构+数据
 	create table copy2 select * from author;
```



#### 常见约束

```
六大约束：
	1、not null: 非空，用于保证该字段的值不能为空
	2、default ：默认，用于保证该字段有默认值
	3、primary key ： 主键，用于保证该字段的值具有唯一性，且非空
	4、unique： 唯一，用于保证该字段的值具有唯一性，可以为空
	5、foreign key：外键，用于限制两个表的关系
	
create table stuinfo(
	id int primary key ,
	stuname varchar(20) not null,
	gender char(1),
	seat int unique，
	age int default 19,
	majorId int foreign key references major(id))
```





#### 存储过程

一组预先编译好的SQL语句的集合，理解成批处理语句

优点：

```
1、提高代码的重用性
2、简化操作
3、减少编译次数并且减少和数据库服务器的连接次数，提高效率
```

- 创建语法

  ```
  create procedure 存储过程名(参数列表)
  BEGIN
  	存储过程体（一组合法SQL语句）
  END
  
  注意：
  	1、参数列表包含三部分	
  	参数模式     参数名    参数类型
  	举例：
  	IN        stuname     varchar(20)
  	
  	2、参数模式
  	IN: 该参数可以作为输入，也就是该参数需要调用方传入值
  	OUT： 该参数可以作为输出， 也就是说该参数可以作为返回值
  	INOUT： 该参数既可以输入，也可以输出
  	
  	3、如果存储过程体仅仅一句话，BEGIN END可以省略，存储过程体中的每条SQL语句的结尾要求必须加分号，存储过程结尾可以受用DELIMITER 重新设置结尾符
  	
  	4、调用语法  call 存储过程名(实参列表)
  ```

- 实例

  ```
  1、空参数列表
  例：插入到admin表中的五条记录
  	select * from admin;
  	
  	DELIMITER $
  	create procedure mypl()
  	BEGIN 
  		insert into admin(username,password) values('john','0000'),('johe','0000'),('johx','0000');
  	END $
  	
  	call mypl()$  # 执行存储过程
  	
  2、创建带in模式参数的存储过程
  例：创建存储过程实现，根据女神名，查询对应的男神信息
  	create procedure mypl2(in beautyName varchar(20))
  	BEGIN 
  		select bo.* from boys bo
  		right join beauty b on bo.id=b.boyfriend_id where b.name=beautyName;
  	END $
  	
  	call mysql('刘燕')$
  	
  例：创建存储过程实现用户是否登陆成功
  	create procedure mypl4(IN username varchar(20),IN password varchar(20))
  	BEGIN 
  		DECLARE result int default 0; # 声明并初始化一个变量
  		
  		select count(*) into result  # 赋值给一个变量
  		from admin where admin.username = username and admin.password = password;
  		
  		select if (result>0,'成功,‘失败');
  	END $
  	
  	call mysql4('张飞','2222')$
  	
  3、创建out模式的存储过程
  例: 根据女神名，返回对应的男神名
  	create procedure mypl5(IN beautyName varchar(20),OUT boyName varchar(20))
  	BEGIN
  		select bo.boyName into boyName  # 相当于不用我们创建这个变量了，直接赋值就可
  		from boys bo
  		inner join beauty b on bo.id = b.boyfriend_id
  		where b.name = beautyName;
  	END $
  	
  	SET @bName
  	call mypl('小昭',@bName)$
  	select @bName$
  	
  4、存储过程的删除
  	drop procedure 存储过程名
  	
  5、查看存储过程
  	show create procedure 存储过程名
  ```



#### 函数

和存储过程类似，区别在于函数有且只有一个返回值，存储过程可以0个也可以多个

- 创建函数

  ```
  create function 函数名(参数列表) returns 返回类型
  BEDGIN
  	函数体
  END
  
  ```

- 案例演示

  ```
  1、无参有返回
  例: 返回公司的员工个数
  	create function myf1() returns int
  	BEGIN
  		declare c int default 0;
  		select count(*) from employees;
  	END$
  	
  	select myf1()$
  	
  2、有参有返回
  例： 根据员工名，返回他的工资
  	create function myf2(empName varchar(20)) returns double
  	BEGIN
  		set @sal =0;
  		select salary into @sal
  		from employees
  		where lastname = empName
  		return @sal
  	END$
  	
  	select myf2('k_ing')
  	
  ```

  

#### 循环结构

- 分类

  ```
  while、loop、repeat
  
  1、while 语法
  【标签:】while 循环条件 do
  	循环体
  end while 【标签】;
  
  2、loop语法，用于模拟简单的死循环
  【标签:】loop 
  	循环体；
  end while 【标签】;
  
  3、repeat
  【标签:】repeat
  	循环体；
  until 结束循环的条件;
  ```

- 循环控制

  ```
  iterate 类似于continue，继续，结束本次循环，继续下一次
  leave   类似于break ，跳出结束当前所在的循环
  ```

- 案例

  ```
  1、批量插入，根据次数插入到admin表中多条记录
  	DELIMITER $
  	create procedure pro_while(IN insertCount INT)
  	BEGIN
  		declare i int default 1;
  		while i <= insertCount do
  			insert into admin(username,`password`) values(concat('rose',i),'666');
  			set i=i+1;
  		end while;
  	
  	END$
  	
  	call pro_while(100)$
  	
  	
  2、批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
  	truncate table admin$
  	create procedure test_while(IN insertCount INT)
  	BEGIN
  		declare i int default 1;
  		a:while i<insertCount do
  			insert into admin(username,`password`) values(concat('xiaohua',i),'000');
  			if i >= 20 then leave a;
  			end if;
  			set i=i+1;
  		end while a;
  	END$
  	
  	call test_while(10)$
  ```

  

