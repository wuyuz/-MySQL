## Mysql 测试题

![1556189100880](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1556189100880.png)



- **创建一个生成整个表结构的sql文件**

  ```sql
  /*
   Navicat Premium Data Transfer
  
   Source Server         : localhost
   Source Server Type    : MySQL
   Source Server Version : 50624
   Source Host           : localhost
   Source Database       : sqlexam
  
   Target Server Type    : MySQL
   Target Server Version : 50624
   File Encoding         : utf-8
  
   Date: 10/21/2016 06:46:46 AM
  */
  
  SET NAMES utf8;
  SET FOREIGN_KEY_CHECKS = 0;
  
  -- ----------------------------
  --  Table structure for `class`
  -- ----------------------------
  DROP TABLE IF EXISTS `class`;
  CREATE TABLE `class` (
    `cid` int(11) NOT NULL AUTO_INCREMENT,
    `caption` varchar(32) NOT NULL,
    PRIMARY KEY (`cid`)
  ) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;
  
  -- ----------------------------
  --  Records of `class`
  -- ----------------------------
  BEGIN;
  INSERT INTO `class` VALUES ('1', '三年二班'), ('2', '三年三班'), ('3', '一年二班'), ('4', '二年九班');
  COMMIT;
  
  -- ----------------------------
  --  Table structure for `course`
  -- ----------------------------
  DROP TABLE IF EXISTS `course`;
  CREATE TABLE `course` (
    `cid` int(11) NOT NULL AUTO_INCREMENT,
    `cname` varchar(32) NOT NULL,
    `teacher_id` int(11) NOT NULL,
    PRIMARY KEY (`cid`),
    KEY `fk_course_teacher` (`teacher_id`),
    CONSTRAINT `fk_course_teacher` FOREIGN KEY (`teacher_id`) REFERENCES `teacher` (`tid`)
  ) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;
  
  -- ----------------------------
  --  Records of `course`
  -- ----------------------------
  BEGIN;
  INSERT INTO `course` VALUES ('1', '生物', '1'), ('2', '物理', '2'), ('3', '体育', '3'), ('4', '美术', '2');
  COMMIT;
  
  -- ----------------------------
  --  Table structure for `score`
  -- ----------------------------
  DROP TABLE IF EXISTS `score`;
  CREATE TABLE `score` (
    `sid` int(11) NOT NULL AUTO_INCREMENT,
    `student_id` int(11) NOT NULL,
    `course_id` int(11) NOT NULL,
    `num` int(11) NOT NULL,
    PRIMARY KEY (`sid`),
    KEY `fk_score_student` (`student_id`),
    KEY `fk_score_course` (`course_id`),
    CONSTRAINT `fk_score_course` FOREIGN KEY (`course_id`) REFERENCES `course` (`cid`),
    CONSTRAINT `fk_score_student` FOREIGN KEY (`student_id`) REFERENCES `student` (`sid`)
  ) ENGINE=InnoDB AUTO_INCREMENT=53 DEFAULT CHARSET=utf8;
  
  -- ----------------------------
  --  Records of `score`
  -- ----------------------------
  BEGIN;
  INSERT INTO `score` VALUES ('1', '1', '1', '10'), ('2', '1', '2', '9'), ('5', '1', '4', '66'), ('6', '2', '1', '8'), ('8', '2', '3', '68'), ('9', '2', '4', '99'), ('10', '3', '1', '77'), ('11', '3', '2', '66'), ('12', '3', '3', '87'), ('13', '3', '4', '99'), ('14', '4', '1', '79'), ('15', '4', '2', '11'), ('16', '4', '3', '67'), ('17', '4', '4', '100'), ('18', '5', '1', '79'), ('19', '5', '2', '11'), ('20', '5', '3', '67'), ('21', '5', '4', '100'), ('22', '6', '1', '9'), ('23', '6', '2', '100'), ('24', '6', '3', '67'), ('25', '6', '4', '100'), ('26', '7', '1', '9'), ('27', '7', '2', '100'), ('28', '7', '3', '67'), ('29', '7', '4', '88'), ('30', '8', '1', '9'), ('31', '8', '2', '100'), ('32', '8', '3', '67'), ('33', '8', '4', '88'), ('34', '9', '1', '91'), ('35', '9', '2', '88'), ('36', '9', '3', '67'), ('37', '9', '4', '22'), ('38', '10', '1', '90'), ('39', '10', '2', '77'), ('40', '10', '3', '43'), ('41', '10', '4', '87'), ('42', '11', '1', '90'), ('43', '11', '2', '77'), ('44', '11', '3', '43'), ('45', '11', '4', '87'), ('46', '12', '1', '90'), ('47', '12', '2', '77'), ('48', '12', '3', '43'), ('49', '12', '4', '87'), ('52', '13', '3', '87');
  COMMIT;
  
  -- ----------------------------
  --  Table structure for `student`
  -- ----------------------------
  DROP TABLE IF EXISTS `student`;
  CREATE TABLE `student` (
    `sid` int(11) NOT NULL AUTO_INCREMENT,
    `gender` char(1) NOT NULL,
    `class_id` int(11) NOT NULL,
    `sname` varchar(32) NOT NULL,
    PRIMARY KEY (`sid`),
    KEY `fk_class` (`class_id`),
    CONSTRAINT `fk_class` FOREIGN KEY (`class_id`) REFERENCES `class` (`cid`)
  ) ENGINE=InnoDB AUTO_INCREMENT=17 DEFAULT CHARSET=utf8;
  
  -- ----------------------------
  --  Records of `student`
  -- ----------------------------
  BEGIN;
  INSERT INTO `student` VALUES ('1', '男', '1', '理解'), ('2', '女', '1', '钢蛋'), ('3', '男', '1', '张三'), ('4', '男', '1', '张一'), ('5', '女', '1', '张二'), ('6', '男', '1', '张四'), ('7', '女', '2', '铁锤'), ('8', '男', '2', '李三'), ('9', '男', '2', '李一'), ('10', '女', '2', '李二'), ('11', '男', '2', '李四'), ('12', '女', '3', '如花'), ('13', '男', '3', '刘三'), ('14', '男', '3', '刘一'), ('15', '女', '3', '刘二'), ('16', '男', '3', '刘四');
  COMMIT;
  
  -- ----------------------------
  --  Table structure for `teacher`
  -- ----------------------------
  DROP TABLE IF EXISTS `teacher`;
  CREATE TABLE `teacher` (
    `tid` int(11) NOT NULL AUTO_INCREMENT,
    `tname` varchar(32) NOT NULL,
    PRIMARY KEY (`tid`)
  ) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
  
  -- ----------------------------
  --  Records of `teacher`
  -- ----------------------------
  BEGIN;
  INSERT INTO `teacher` VALUES ('1', '张磊老师'), ('2', '李平老师'), ('3', '刘海燕老师'), ('4', '朱云海老师'), ('5', '李杰老师');
  COMMIT;
  
  SET FOREIGN_KEY_CHECKS = 1;
  
  ```

- **将sql文件导入数据库的方法**

  ```mysql
  法一：进入 MySQL 控制台（如：MySQL 5.5 Command Line Client），使用 source 命令执行：
  Mysql>source 【sql脚本文件的路径全名】 或 Mysql>\. 【sql脚本文件的路径全名】，示例：source D:\test.sql 或者 \. D:\test.sql
  
  注意：1、如果在导入时出现部分汉字无法识别，一般情况是编码错误，使用非记事本编辑器打开，重新编辑保存即可。
  	 2、需要注意的是，使用这种方法导入sql文件会发生乱码，是因为没有在mysql登陆的时候设置默认编码：
  	 mysql -u root -p --default-character-set=utf8    //语句执行后密码登陆
  	 use dbname    
  	 source /root/newsdata.sql
  ```



**具体的题目：**

```mysql
1、自行创建测试数据
3、（收集的题）查找所有年龄为18~24的女孩
select * from girls where age between 15 and 18 and boyfriend = "no";

2、查询“生物”课程比“物理”课程成绩高的所有学生的学号；（先找到所有学生的生物课成绩表，再找到所有学生的物理表，再连接两表）
    select A.student_id,sw,ty from
    (select student_id,num as sw from score left join course on score.course_id = course.cid where course.cname = '生物') as A
    left join
    (select student_id,num  as ty from score left join course on score.course_id = course.cid where course.cname = '体育') as B
    on A.student_id = B.student_id where sw > if(isnull(ty),0,ty);
    
#法二
select * from( 
 (select * from score where course_id in (select cid from course where cname = '生物')) t1  
left join 
 (select * from score where course_id in (select cid from course where cname = '物理')) t2  
on  t1.student_id = t2.student_id) 
where t1.num > t2.num;

#法三 附加姓名
select C.student_id, sname, sw, ty from 
  (select A.student_id, sw, ty from
    (select student_id,  num as sw, cname from score left join course on score.course_id = course.cid where course.cname='生物') as A
left join
    (select student_id,  num as ty, cname from score left join course on score.course_id = course.cid where course.cname='物理') as B
on A.student_id = B.student_id where sw > ty) as C left join student on C.student_id = student.sid;

3、查询平均成绩大于60分的同学的学号和平均成绩；
select student_id,avg(num) from score group by student_id having avg(num) > 60
	
#法2：添加姓名
select sname, A.student_id, A.av from (select student_id,avg(num) as av from score group by student_id having av > 60)
as A left join student on A.student_id = student.sid;

#详细讲解：
	# 先查看每个同学的平均分数
select student_id,avg(num) from score group by student_id;

	# 在筛选成绩大于60分的同学的学号和平均成绩；
 select student_id,avg(num) from score group by student_id having avg(num) > 60;
	
4、查询所有同学的学号、姓名、选课数、总成绩；
    select score.student_id,sum(score.num),count(score.student_id),student.sname
    from
    score left join student on score.student_id = student.sid  
    group by score.student_id
    
#法二
    select sid,sname,sum_num ,count_stu 
    from student  
    left join 
    (select sum(num) sum_num,count(sid) count_stu,student_id from score group by 		student_id) t2  
    on  sid = student_id;

#法三：附加姓名
select sid, sname, course ,sumer from student right join (
  select  A.student_id, A.course, B.su sumer from(
    (select student_id,count(course_id) course from score group by student_id ) as A
    left join
    (select student_id, sum(num) su from score group by student_id)  as B on A.student_id = B.student_id
  )
) as C on C.student_id = student.sid
	
#详细讲解
# 先查看每个同学的总成绩
select student_id,sum(num) from score group by student_id;

# 学生和课程的关系只有成绩表中存在，因此要获取每个学生选择的课程，需要通过score表
select count(sid),student_id from score group by student_id;

# 将上面两步合并
select sum(num),count(sid),student_id from score group by student_id;

# 将学生的信息和成绩选课情况拼在一起
select sid,sname,sum_num ,count_stu 
from student  
left join 
 (select sum(num) sum_num,count(sid) count_stu,student_id from score group by student_id) t2  
on  sid = student_id;
    
5、查询姓“李”的老师的个数；
    select count(tid) from teacher where tname like '李%'、
    select count(1) from (select tid from teacher where tname like '李%') as B
    
6、查询没学过“叶平”老师课的同学的学号、姓名；（想查出学过叶平老师课的student_id,再取反）
    select * from student where sid not in (
    select DISTINCT student_id from score where score.course_id in (
    select cid from course left join teacher on course.teacher_id = teacher.tid where tname = '李平老师')
    )
    
    #法二
    select  A.student_id, cname, A.num, A.course_id  from course right join (select student_id, num, course_id from score left join student on student.sid = score.student_id
) as A on course.cid = A.course_id where A.num < 60;
   
   #详细讲解：
# 找到张磊老师的id 
select tid from teacher where tname == '张磊老师';

# 找到张磊老师所教课程
select cid from course where teacher_id = (select tid from teacher where tname = '张磊老师');

# 找到所有学习这门课的学生id
select student_id from score where course_id = (select cid from course where teacher_id = (select tid from teacher where tname = '张磊老师'));

# 找到没有学过这门课的学生对应的学生学号、姓名
select sid,sname from student where sid not in 
 (select student_id from score where course_id = (select cid from course where teacher_id = (select tid from teacher where tname = '张磊老师'))
);

    
7、查询学过“001”并且也学过编号“002”课程的同学的学号、姓名；（先找出学过1的学生，再在里面找学过2的学生）
    select student_id,sname from
    (select student_id,course_id from score where course_id = 1 or course_id = 2) as B
    left join student on B.student_id = student.sid group by student_id HAVING count(student_id) > 1
    
    #法二
    select student_id from (select student_id from score left join course on score.course_id = course.cid where cid = 1) as A where student_id in 
(select student_id from score left join course on score.course_id = course.cid where cid = 2);

#详解讲解
# 先查询学习课程id为1的所有学生
select * from score where course_id = 1;

# 先查询学习课程id为2的所有学生
select * from score where course_id = 2;

# 把这两张表按照学生的id 内连接起来 去掉只学习某一门课程的学生
select t1.student_id from
(select student_id from score where course_id = 1)  t1
inner join
(select student_id from score where course_id = 2) t2
on t1.student_id = t2.student_id

# 根据学号在学生表中找到对应的姓名
select sid,sname from student where sid in (select t1.student_id from (select student_id from score where course_id = 1)  t1 inner join (select student_id from score where course_id = 2) t2 on t1.student_id = t2.student_id);


8、查询学过“叶平”老师所教的所有课的同学的学号、姓名；
select distinct student.sid,sname from student where sid  in (select distinct student_id from (select * from teacher right join course on teacher.tid = course.teacher_id) as A
left join score on A.cid = score.course_id where tname = '李平老师');

#法二
select sid, sname from student right join (
    select student_id,course_id from score right join 
    (select cid from course inner join teacher on teacher.tid = course.teacher_id where tname = '李平老师') as t1
    on cid = course_id group by student_id having count(sid) = (
        select count(cid) from course inner join teacher on teacher.tid = course.teacher_id where tname = '李平老师'
    ) 
) as tmp on tmp.student_id = student.sid；

#详细讲解
#找到李平老师的tid
select tid from teacher where tname ='李平老师';

# 找到李平老师教的所有课程cid
 select cid from course where teacher_id in (select tid from teacher where tname ='李平老师');

# 找到李平老师教的所有课程数
 select count(cid) from course where teacher_id in (select tid from teacher where tname ='李平老师');

# 找到所有学习李平老师课程的学生
select * from score where course_id in ( select cid from course where teacher_id in (select tid from teacher where tname ='李平老师'));

# 查看所有学习李平老师课程的学生选课数
select student_id,count(course_id) from score where course_id in ( select cid from course where teacher_id in (select tid from teacher where tname ='李平老师')) group by student_id;

# 找到所有选择了李平老师所有课程的学生id
select  student_id from (
select student_id,count(course_id) course_count from score where course_id in ( select cid from course where teacher_id in (select tid from teacher where tname ='李平老师')) group by student_id) t1
where t1.course_count =
(select count(cid) from course where teacher_id in (select tid from teacher where tname ='李平老师'));

# 找到学生的其他信息
select sid,sname from student where sid in (
select  student_id from (
select student_id,count(course_id) course_count from score where course_id in ( select cid from course where teacher_id in (select tid from teacher where tname ='李平老师')) group by student_id) t1
where t1.course_count =
(select count(cid) from course where teacher_id in (select tid from teacher where tname ='李平老师'))
);


9、查询课程编号“002”的成绩比课程编号“001”课程低的所有同学的学号、姓名；
select sid, sname from student right join (select A.student_id,A.num kc1,B.num kc2 from (select student_id, num from score left join course on score.course_id = course.cid where cid = 1) as A
right join (select student_id, num from score left join course on score.course_id = course.cid where cid = 2) as B on A.student_id = B.student_id where A.num > B.num) as C on C.student_id = student.sid;

#法二：
select sid , sname from student right join (select std1 from (select student_id std1, num num1 from score where course_id =1) as t1
inner join 
(select student_id std2, num num2 from score where course_id = 2) as t2
on std2 = std1 where num1 > num2) as tmp on std1 = sid;

#详细讲解：
# 先找到每个学生的课程编号“1”的和课程编号“2”的成绩组成一张表
select t1.student_id from (select num num2,student_id from score where course_id = 2) t2 inner join (select student_id,num num1 from score where course_id = 1) t1 on t1.student_id = t2.student_id

# 再找到课程编号“2”的成绩比课程编号“1”课程低的所有学生的学号
select t1.student_id from (select num num2,student_id from score where course_id = 2) t2 inner join (select student_id,num num1 from score where course_id = 1) t1 on t1.student_id = t2.student_id where num2 < num1

# 再找到所有学生的学号、姓名
select sid,sname from student where sid in(select t1.student_id from (select num num2,student_id from score where course_id = 2) t2 inner join (select student_id,num num1 from score where course_id = 1) t1 on t1.student_id = t2.student_id where num2 < num1);


10、查询有课程成绩小于60分的同学的学号、姓名；
    select sid,sname from student where sid in (select distinct student_id from score where num < 60)

11、查询没有学全所有课的同学的学号、姓名；
   select student_id ,sname from score left join student on score.student_id = student.sid group by student_id having 
count(course_id) = (select count(1) from course); 

#详细讲解：
# 先统计一共有多少门课程
select count(cid) from course;

# 查看每个学生选择的课程书
select count(course_id) from score group by student_id;

# 查询所学课程数小于总课程数的学生学号
select student_id
from (select count(course_id) c_course_id,student_id from score group by student_id) t1 
where t1.c_course_id <  (select count(cid) from course) ;

# 查询没有学全所有课的同学的学号、姓名；
select sid,sname from student where sid in (
 select student_id from (select count(course_id) c_course_id,student_id from score group by student_id
 ) t1 where t1.c_course_id <  (select count(cid) from course)
) ;

 
    
12、查询至少有一门课与学号为“001”的同学所学相同的同学的学号和姓名；
    select student_id,sname, count(course_id)
    from score left join student on score.student_id = student.sid
    where student_id != 1 and course_id in (select course_id from score where student_id = 1) group by student_id；
    
 #详细讲解
 # 先看看学号为1的同学都学了哪些课程
select course_id from score where student_id = 1

# 找到学习 学号为1的同学所学课程 的学号
select distinct student_id from score where course_id in (select course_id from score where student_id = 1);

#  找到学习 学号为1的同学所学课程 的学号\姓名
select sid,sname from student where sid in (select distinct student_id from score where course_id in (select course_id from score where student_id = 1));

    
13、查询至少学过学号为“001”同学所选课程中任意一门课的其他同学学号和姓名；
    select student_id,sname, count(course_id)
    from score left join student on score.student_id = student.sid
    where student_id != 1 and course_id in (select course_id from score where student_id = 1) group by 		student_id having count(course_id) ＝ (select count(course_id) from score where student_id = 1)

14、查询和“002”号的同学学习的课程完全相同的其他同学学号和姓名；
    select student_id,sname from score left join student on score.student_id = student.sid where student_id in (
    select student_id from score  where student_id != 1 group by student_id HAVING count(course_id) = (select count(1) from score where student_id = 1)
    ) and course_id in (select course_id from score where student_id = 1) group by student_id HAVING count(course_id) = (select count(1) from score where student_id = 1)
    
#详细讲解
# 先查询2号同学学了哪些课程
select * from score where student_id =2;

# 找到学习了2号同学没学习课程的所有同学（找到所有和2号同学学习的课程不一样的同学）
select student_id from score where course_id not in (select course_id from score where student_id=2)

# 找到score表中所有的学生并且把 2号同学 以及（和2号同学学习的课程不一样的同学）排除出去
select student_id from score where student_id not in (select student_id from score where course_id not in (select course_id from score where student_id=2)) and student_id !=2

# 对剩余的和2号同学所选课程没有不同的同学所选课程数进行统计，如果和2号同学的课程数相同，就是选择了相同的课程
select student_id from score where student_id not in (
 select student_id from score where course_id not in (select course_id from score where student_id=2)
 ) and student_id !=2 group by student_id 
having count(course_id)= (select count(course_id) from score where student_id=2);

15、删除学习“叶平”老师课的SC表记录；
    delete from score where course_id in (
    select cid from course left join teacher on course.teacher_id = teacher.tid where teacher.name = '叶平' )
    
#详细讲解：
# 先查出李平老师的id
select tid from teacher where tname = '李平老师';

# 查看李平老师所教授的课程
select cid from course where teacher_id = (select tid from teacher where tname = '李平老师');

# 查看李平老师所教课程的成绩数据
select * from score where course_id in (select cid from course where teacher_id = (select tid from teacher where tname = '李平老师'));

# 执行删除命令
delete from score where course_id in (select cid from course where teacher_id = (select tid from teacher where tname = '李平老师'));


16、向SC表中插入一些记录，这些记录要求符合以下条件：①没有上过编号“002”课程的同学学号；②插入“002”号课程的平均成绩；
insert into score(student_id, course_id, num) select sid,2,(select avg(num) from score where course_id = 2)
from student where sid not in (
select student_id from score where course_id = 2
)

#详细讲解：
#  先找寻上过2号课程的同学
select student_id from score where course_id = 2;

# 再找到没上过2号课程的所有同学
select * from student where sid not in (select student_id from score where course_id = 2);

#  计算出学习2号课程的同学的平均成绩
select avg(num) from score where course_id = 2 group by course_id;

# 用笛卡尔积将上述两个表拼起来
select * from (select sid from student where sid not in (select student_id from score where course_id = 2)) t1,(select avg(num) from score where course_id = 2 group by course_id) t2;

#  向SC表中插入记录
insert into score (course_id,student_id,num)   select 2,t1.sid,t2.avg_num from (select sid from student where sid not in (select student_id from score where course_id = 2)) t1,(select avg(num) avg_num from score where course_id = 2 group by course_id) t2;


17、按平均成绩从低到高显示所有学生的“语文”、“数学”、“英语”三门的课程成绩，按如下形式显示： 学生ID,语文,数学,英语,有效课程数,有效平均分；
select sc.student_id,
(select num from score left join course on score.course_id = course.cid where course.cname = "生物" and score.student_id=sc.student_id) as sy,
(select num from score left join course on score.course_id = course.cid where course.cname = "物理" and score.student_id=sc.student_id) as wl,
(select num from score left join course on score.course_id = course.cid where course.cname = "体育" and score.student_id=sc.student_id) as ty,
count(sc.course_id),
avg(sc.num)
from score as sc
group by student_id desc  

#详细讲解:
# 查看每个学生的数学成绩
select student_id,num from score where course_id = (select cid from course where cname = '数学');

#  查看每个学生的语文成绩
select student_id,num from score where course_id = (select cid from course where cname = '语文');

#  查看每个学生的英语成绩
select student_id,num from score where course_id = (select cid from course where cname = '英语');

# 查看每个学生的平均成绩
select student_id,avg(num),count(num) from score group by student_id;

# 将上面的几张表拼接起来,为了生成所有学生的信息，用student表作为左连接的第一张表
select sid 学生ID,t2.num 语文,t1.num 数学, t3.num 英语,t4.count_course 有效课程数,t4.avg_num 有效平均分 from student 
 left join (select student_id,num from score where course_id = (select cid from course where cname = '数学')) t1
 on student.sid = t1.student_id
 left join (select student_id,num from score where course_id = (select cid from course where cname = '语文')) t2
 on student.sid = t2.student_id
 left join (select student_id,num from score where course_id = (select cid from course where cname = '英语')) t3
 on student.sid = t3.student_id
 left join (select student_id,avg(num) avg_num,count(num) count_course from score group by student_id)  t4
 on student.sid = t4.student_id
 

18、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分；
select course_id, max(num) as max_num, min(num) as min_num from score group by course_id;

#详细讲解：
# 查询成绩的最高分
select course_id c1,max(num) from score group by course_id

# 查询成绩的最低分
select course_id c1,min(num) from score group by course_id

# 查询成绩的最高分和最低分拼接
select * from ( (select course_id c1,max(num) from score group by course_id) t1 inner join (select course_id c2,min(num) from score group by course_id) t2 on t1.c1 = t2.c2 );

# 格式整理
select t1.c1,t1.max_num,t2.min_num from ( (select course_id c1,max(num) max_num from score group by course_id) t1 inner join (select course_id c2,min(num) min_num from score group by course_id) t2 on t1.c1 = t2.c2 );


19、按各科平均成绩从低到高和及格率的百分数从高到低顺序；
select course_id, avg(num) as avgnum,sum(case when score.num > 60 then 1 else 0 END)/count(1)*100 as percent from score group by course_id order by avgnum asc,percent desc;

#详细讲解：
# 先求平均成绩
select course_id,avg(num) from score group by course_id;

# 解决计算各科及格率的问题所有及格的人/所有人数
select t1.course_id,t1.count1/t2.count2 from 
(select course_id,count(course_id) count1 from score where num>60 group by course_id) t1 
left join
(select course_id,count(course_id) count2 from score group by course_id) t2
on t1.course_id = t2.course_id;

# 根据上述内容进行表的拼接
select  t_out1.course_id,t_out1.avgnum, t_out2.pass_per from 
(select course_id,avg(num) avgnum from score group by course_id ) t_out1
left join 
(select t1.course_id,t1.count1/t2.count2 pass_per from 
(select course_id,count(course_id) count1 from score where num>60 group by course_id) t1 
left join
(select course_id,count(course_id) count2 from score group by course_id) t2
on t1.course_id = t2.course_id) t_out2
on  t_out1.course_id = t_out2.course_id

# 加上排序
select  t_out1.course_id,t_out1.avgnum, t_out2.pass_per from  (select course_id,avg(num) avgnum from score group by course_id ) t_out1 left join  (select t1.course_id,t1.count1/t2.count2 pass_per from  (select course_id,count(course_id) count1 from score where num>60 group by course_id) t1  left join (select course_id,count(course_id) count2 from score group by course_id) t2 on t1.course_id = t2.course_id) t_out2 on  t_out1.course_id = t_out2.course_id order by avgnum ,pass_per desc;

 

20、课程平均分从高到低显示（现实任课老师）；
select avg(if(isnull(score.num),0,score.num)),teacher.tname from course
left join score on course.cid = score.course_id
left join teacher on course.teacher_id = teacher.tid
group by score.course_id

#法二:
select course_id,avg(num) avg_num from score group by course_id order by avg_num desc;


21、查询各科成绩前三名的记录:(不考虑成绩并列情况)
select score.sid,score.course_id,score.num,T.first_num,T.second_num from score left join
(
select sid,
(select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 0,1) as first_num,
(select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 3,1) as second_num from score as s1
) as T
on score.sid =T.sid
where score.num <= T.first_num and score.num >= T.second_num

22、查询每门课程被选修的学生数；
select course_id, count(1) from score group by course_id;

23、查询出只选修了一门课程的全部学生的学号和姓名；
select student.sid, student.sname, count(1) from score
left join student on score.student_id  = student.sid
group by course_id having count(1) = 1

24、查询男生、女生的人数；
select * from
(select count(1) as man from student where gender='男') as A ,
(select count(1) as feman from student where gender='女') as B

# 法二：
select gender,count(sid) from student group by gender;

25、查询姓“张”的学生名单；
select sname from student where sname like '张%';

26、查询同名同姓学生名单，并统计同名人数；	`.
select sname,count(1) as count from student group by sname;

27、查询每门课程的平均成绩，结果按平均成绩升序排列，平均成绩相同时，按课程号降序排列；
select course_id,avg(if(isnull(num), 0 ,num)) as avg from score group by course_id order by avg     asc,course_id desc;

28、查询平均成绩大于85的所有学生的学号、姓名和平均成绩；
select student_id,sname, avg(if(isnull(num), 0 ,num)) from score left join student on score.student_id = student.sid group by student_id;

29、查询课程名称为“数学”，且分数低于60的学生姓名和分数；
select student.sname,score.num from score
left join course on score.course_id = course.cid
left join student on score.student_id = student.sid
where score.num < 60 and course.cname = '数学'


30、查询课程编号为003且课程成绩在80分以上的学生的学号和姓名；
select * from score where score.student_id = 3 and score.num > 80

31、求选了课程的学生人数
select count(distinct student_id) from score
select count(c) from (
select count(student_id) as c from score group by student_id) as A

32、查询选修“杨艳”老师所授课程的学生中，成绩最高的学生姓名及其成绩；
select sname,num from score
left join student on score.student_id = student.sid
where score.course_id in (select course.cid from course left join teacher on course.teacher_id = teacher.tid where tname='张磊老师') order by num desc limit 1;

#详细讲解：
# 先找到“杨艳”老师的教师id
select tid from teacher where tname = '杨艳';

# 再找到杨艳老师教的所有课程
select cid from course where teacher_id in (select tid from teacher where tname = '杨艳');

# 再找到杨艳老师教的所有课程的最高分
select max(num) from score where course_id in (select cid from course where teacher_id in (select tid from teacher where tname = '李平老师'));

# 再找到杨艳老师教的所有课程的最高分对应的学生
select distinct student_id,num from score 
where num = (select max(num) from score where course_id in (select cid from course where teacher_id in (select tid from teacher where tname = '李平老师'))) 
and course_id in   (select cid from course where teacher_id in (select tid from teacher where tname = '李平老师'));

# 找到学生的姓名
select student.sname,t1.num from(
select distinct student_id,num from score 
where num = (select max(num) from score where course_id in (select cid from course where teacher_id in (select tid from teacher where tname = '李平老师'))) 
and course_id in   (select cid from course where teacher_id in (select tid from teacher where tname = '李平老师'))
) t1
left join
student
on 
t1.student_id = student.sid;

33、查询各个课程及相应的选修人数；
select course.cname,count(1) from score
left join course on score.course_id = course.cid
group by course_id;


34、查询不同课程但成绩相同的学生的学号、课程号、学生成绩；
select DISTINCT s1.course_id,s2.course_id,s1.num,s2.num from score as s1, score as s2 where s1.num = s2.num and s1.course_id != s2.course_id;


35、查询每门课程成绩最好的前两名；
select score.sid,score.course_id,score.num,T.first_num,T.second_num from score left join
(
select sid,
(select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 0,1) as first_num,
(select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 1,1) as second_num from score as s1
) as T
on score.sid =T.sid
where score.num <= T.first_num and score.num >= T.second_num

36、检索至少选修两门课程的学生学号；
select student_id from score group by student_id having count(student_id) > 1

37、查询全部学生都选修的课程的课程号和课程名；
select course_id,count(1) from score group by course_id having count(1) = (select count(1) from student);

38、查询没学过“叶平”老师讲授的任一门课程的学生姓名；
select student_id,student.sname from score
left join student on score.student_id = student.sid
where score.course_id not in (
select cid from course left join teacher on course.teacher_id = teacher.tid where tname = '张磊老师'
)
group by student_id

39、查询两门以上不及格课程的同学的学号及其平均成绩；
select student_id,count(1) from score where num < 60 group by student_id having count(1) > 2

40、检索“004”课程分数小于60，按分数降序排列的同学学号；
select student_id from score where num< 60 and course_id = 4 order by num desc;

41、删除“002”同学的“001”课程的成绩；
delete from score where course_id = 1 and student_id = 2

42、查询即选择 物理 又选择 体育 的学生；
SELECT student.sname,GROUP_CONCAT(course.cname) FROM course INNER JOIN score INNER JOIN student ON course.cid=score.course_id AND student.sid=score.student_id WHERE course.cname IN ('体育', '物理') GROUP BY student.sname HAVING COUNT(course.cname)=2;

# 42题、9题、1题解析 
法二
select * from (select A.student_id from (select * from score inner join course on score.course_id=course.cid where cname='物理') as A inner join (select * from score inner join course on score.course_id=course.cid where cname='体育') as B on A.student_id = B.student_id) as C inner join student on student.sid = C.student_id;   # 注意 当出现duplice 重复时sid等时，原因是表内字段重复，需要selcet选择你才需要的。如A.student_id 不能* 不然取数据时就出字段错误

select A.sid from (select * from score inner join course on score.course_id=course.cid where cname='物理') as A inner join (select * from score inner join course on score.course_id=course.cid where cname='体育') as B on A.student_id = B.student_id

#法三
select sname from
student,
(select student_id from score where course_id=(select cid from course where cname='体育')) t1,
(select student_id from score where course_id=(select cid from course where cname='物理')) t2
where  t1.student_id = t2.student_id and t2.student_id = student.sid;
```



