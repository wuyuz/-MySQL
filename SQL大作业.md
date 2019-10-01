## SQL大作业

```sql
###1、查询男生、女生的人数；
mysql> select gender,count(sid) as gender_count from student group by gender;
+--------+--------------+
| gender | gender_count |
+--------+--------------+
| 女     |            6 |
| 男     |           10 |
+--------+--------------+
2 rows in set (0.00 sec)

###2、查询姓“张”的学生名单；
mysql> select sname from student where sname like '张%';
+--------+
| sname  |
+--------+
| 张三   |
| 张一   |
| 张二   |
| 张四   |
+--------+
4 rows in set (0.00 sec)

###3、课程平均分从高到低显示 ###########
select t1.cname,t2.avg_num from course as t1
inner join
(select course_id,avg(num) as avg_num from score group by course_id ) as t2
on 
t1.cid=t2.course_id
order by t2.avg_num desc;

+--------+---------+
| cname  | avg_num |
+--------+---------+
| 美术   | 85.2500 |
| 物理   | 65.0909 |
| 体育   | 64.4167 |
| 生物   | 53.4167 |
+--------+---------+

###4、查询有课程成绩小于60分的同学的学号、姓名；
## 先 select student_id from score where num<60;
select sid,sname from student where sid in (select student_id from score where num<60);


###5、查询至少有一门课与学号为1的同学所学课程相同的同学的学号和姓名；
##学号为1的同学所学课程的id 
	## select course_id from score where student_id=1;
	(##拿到课程名字:select cname from course where cid in (select course_id from score where student_id=1);)
##跟这个同学课程id一样的学生id
	## select student_id from score where course_id in ( select course_id from score where student_id=1;)
	
select sid,sname from student where sid in (  
select student_id from score where course_id in ( select course_id from score where student_id=1)
);


###6、查询出只选修了一门课程的全部学生的学号和姓名；
## 只有一个成绩——score表中按照student_id分组count(student_id)为1的
	## select student_id from score group by student_id having count(student_id)=1;
select sid,sname from student where sid in 
(select student_id from score group by student_id having count(student_id)=1);


###7、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分；
	## 以课程id进行分组~用聚合函数max、min
	select course_id as '课程id',max(num) as '最高分',min(num)as '最低分' from score group by course_id;
	
+----------+-----------+-----------+
| 课程id   | 最高分    | 最低分    |
+----------+-----------+-----------+
|        1 |        91 |         8 |
|        2 |       100 |         9 |
|        3 |        87 |        43 |
|        4 |       100 |        22 |
+----------+-----------+-----------+


*****注意：连玩表以后把结果查出来！select t1.student_id from （连表后的结果）
###8、查询课程编号“2”的成绩比课程编号“1”课程低的所有同学的学号、姓名；
	## 肯定同时选修了1与2课程
	select student_id,num from score where course_id=1;
	select student_id,num from score where course_id=2;
	## 连表得到同时选修了1与2课程的学生id
	select t1.student_id from  
	(select student_id,num from score where course_id=1) as t1
	inner join
	(select student_id,num from score where course_id=2) as t2
	on t1.student_id=t2.student_id and t1.num>t2.num;
	## 最后得到学生id及姓名
	select sid,sname from student where sid in 
	(select t1.student_id from 
	(select student_id,num from score where course_id=1)as t1
	inner join 
	(select student_id,num from score where course_id=2) as t2
	on t1.student_id=t2.student_id and t1.num>t2.num);
	

*****注意：连玩表以后把结果查出来！select t1.student_id from （连表后的结果）
###9、查询“生物”课程比“物理”课程成绩高的所有学生的学号及姓名；
	## 生物课程学生的学号及成绩
		## select cid from course where cname='生物';
		select student_id,num from score where course_id=(select cid from course where cname='生物');
	## 物理课程学生id及学号
		select student_id,num from score where course_id=(select cid from course where cname='物理');
	## 连表，找出学生id，再在student表中找
	select sid,sname from student where sid in
	(select t1.student_id from 
	(select student_id,num from score where course_id=(select cid from course where cname='生物')) as t1
	inner join
	(select student_id,num from score where course_id=(select cid from course where cname='物理')) as t2
	on 
	t1.student_id=t2.student_id and t1.num>t2.num );
	
	
###10、查询平均成绩大于60分的同学的学号和平均成绩;
	##score表中以student_id分组 得到的表与Student表inner join
	##select student_id,avg(num) as avg_num from score group by student_id 
select t1.sid,t1.sname,t2.avg_num from student as t1
inner join 
(select student_id,avg(num) as avg_num from score group by student_id)as t2
on t1.sid=t2.student_id
where t2.avg_num>60;

+-----+--------+---------+
| sid | sname  | avg_num |
+-----+--------+---------+
|   3 | 张三   | 82.2500 |
|   4 | 张一   | 64.2500 |
|   5 | 张二   | 64.2500 |
|   6 | 张四   | 69.0000 |
|   7 | 铁锤   | 66.0000 |
|   8 | 李三   | 66.0000 |
|   9 | 李一   | 67.0000 |
|  10 | 李二   | 74.2500 |
|  11 | 李四   | 74.2500 |
|  12 | 如花   | 74.2500 |
|  13 | 刘三   | 87.0000 |
+-----+--------+---------+


###11、查询所有同学的学号、姓名、选课数、总成绩；
	##score表中：以student_id分组，得出选课数与总成绩 再与student连表
	##select student_id,count(course_id)as course_count,sum(num)as sum_score from score group by student_id

select t1.sid,t1.sname,t2.course_count,t2.sum_score from student as t1
inner join	
(select student_id,count(course_id)as course_count,sum(num)as sum_score from score group by student_id)as t2
on t1.sid=t2.student_id;


###12、查询姓“李”的老师的个数；
select count(1) from teacher where tname like '李%';


###13、查询没学过“张磊老师”课的同学的学号、姓名；
	##teacher--course--score--student
	##通过老师找到course_id：
		## select cid from course where teacher_id=(select tid from teacher where tname='张磊老师');
	##score表用上面的course_id找“有这门课成绩的学生的student_id”
		## select student_id from score where course_id in (select cid from course where teacher_id=(select tid from teacher where tname='张磊老师'));
	##没选过的，就是not in 
select sid,sname from student where sid not in (
select student_id from score where course_id in 
(select cid from course where teacher_id=(select tid from teacher where tname='张磊老师'))
);
	
	
###14、查询学过“1”并且也学过编号“2”课程的同学的学号、姓名；
	##学过课程1的学生id
		##select student_id from score where course_id=1;
	##学过课程2的学生id
		##select student_id from score where course_id=2;
	**两张表inner join一下：条件是student_id相等
	'''
		select t1.student_id from  (select student_id from score where course_id=1)as t1
		inner join 
		(select student_id from score where course_id=2) as t2
		on 
		t1.student_id=t2.student_id;
	'''
		
select sid,sname from student where sid in (
	select t1.student_id from  (select student_id from score where course_id=1)as t1
		inner join 
		(select student_id from score where course_id=2) as t2
		on 
		t1.student_id=t2.student_id
);
			

###15、查询学过“李平老师”所教的所有课的同学的学号、姓名；
	## teacher--course--score--student
	## 李平老师的tid：select tid from teacher where tname='李平老师';
	## 在course表找到他教的课程cid：select cid from course where teacher_id=(select tid from teacher where tname='李平老师' );
	## score表通过course_id找student_id：(可以去重)
		##select distinct student_id from score where course_id in (select cid from course where teacher_id=(select tid from teacher where tname='李平老师' ));

select sid,sname from student where sid in (
select distinct student_id from score where course_id in 
(select cid from course where teacher_id=(select tid from teacher where tname='李平老师' ))
);



```

