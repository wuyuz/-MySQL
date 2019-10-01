## Pymysql模块的使用

#### 一、下载与说明

- 目前我们已经学过了使用命令行连接远端mysql客服端，现在我们要是有python模块实现远端连接mysql，也就是我们今天要学的pymysql，最底层也是基于socket实现的。

- 安装

  ```shell
  pip3 install pymysql
  ```

#### 二、数据库连接

```python
import pymysql

db = pymysql.connect("数据库ip","用户","密码","数据库" ) # 打开数据库连接
cursor = conn.cursor()  
cursor.execute("SELECT VERSION()")                    # 使用 execute() 方法执行 SQL 查询
data = cursor.fetchone()                              # 使用 fetchone() 方法获取单条数据
print ("Database version : %s " % data)
db.close()                                            # 关闭数据库连接

#详细讲解
import pymysql

user = input('请输入用户名：')
pwd = input('请输入密码：')

# 1.连接
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', password='', db='db2', charset='utf8')

cursor = conn.cursor()  # 2.创建游标对象，用以表示执行的位置，或则取数据的位置

sql = "select * from userinfo where username='%s' and password='%s'" % (user, pwd)   # 注意%s需要加引号
print('sql语句:',sql)

cursor.execute(sql)    # 3.执行sql语句，
conn.commit()         # 提交到数据库执行,有部分数据存储在内存中，需要提交到数据库

result = cursor.execute(sql)  # 执行sql语句，返回sql查询成功的记录数目，即result返回影响的记录次数
print('返回记录数:',result)

cursor.close()  # 关闭连接，游标和连接都要关闭
conn.close()

if result:
    print('登陆成功')
else:
    print('登录失败')
    
#有几个参数是必须的host、port、user、password、db
#charset最好也要加上，否则无法识别中文！
```

- 创建表操作

  ```python
  import pymysql
  # 打开数据库连接
  db = pymysql.connect("localhost","testuser","test123","TESTDB" )
   
  # 使用 cursor() 方法创建一个游标对象 cursor
  cursor = db.cursor()
  # 使用 execute() 方法执行 SQL，如果表存在则删除
  cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
  
  # 使用预处理语句创建表
  sql = """CREATE TABLE EMPLOYEE (
           FIRST_NAME  CHAR(20) NOT NULL,
           LAST_NAME  CHAR(20),
           AGE INT,  
           SEX CHAR(1),
           INCOME FLOAT )"""
   
  cursor.execute(sql)
   
  # 关闭数据库连接
  db.close()
  

  
  # 例2）使⽤python，读⽂件，并通过pymysql模块将⽂本内容写⼊数据库（5分）
  import pymysql
  db = pymysql.connect('127.0.0.1','root','456','shop')
  cursor = db.cursor() #创建游标
  #goods表
  with open('goods','r',encoding='utf-8') as f:
      for line in f:
          line = line.strip().split(',')
          print(line)
          sql = "insert into goods(goods_id,gname,price,g_num) values(%s,%s,%s,%s)"\
                %(int(line[0]),repr(line[1]),float(line[2]),int(line[3]))
          print(sql)
          try:
              cursor.execute(sql)
              db.commit()
          except:
              db.rollback()
  db.close()
  ```
  
  



 

#### 三、exectue()之sql注入

- 什么时SQL注入：SQL 注入是非常常见的一种网络攻击方式，主要是通过参数来让 mysql 执行 sql 语句时进行预期之外的操作。（也就是传说的混淆视听）：即：因为传入的参数改变SQL的语义，变成了其他命令，从而操作了数据库。

  产生原因：`SQL语句使用了动态拼接的方式。`，例如下面的代码

  ```python
  import pymysql
  
  sql = 'SELECT count(*) as count FROM user WHERE id = ' + str(input['id']) + ' AND password = "' + input['password'] + '"'    # 通过字符串拼接让sql写入东西
  cursor = cursor(pymysql.cursors.DictCursor)  
  cursor.execute(sql)
  count = cursor.fetchone()
  if count is not None and count['count'] > 0:
      print('登陆成功')
      
  # 看上面的代码并没有什么问题，但是我们输入如下内容时，就会出现问题了：
  	input['id'] = '2 or 1=1'
   #你会发现，用户能够直接登录到系统中，因为原本 sql 语句的判断条件被 or 短路成为了永远正确的语句。   
  ```

  以上只是一种sql注入的方式，总之只要是通过用户输入数据来拼接 sql 语句，就必须在第一时间考虑如何避免 SQL 注入问题。

- 预防sql注入 - pymaql参数化语句

  ```PYTHON
  #pymysql 的 execute 支持参数化 sql，通过占位符 %s 配合参数就可以实现 sql 注入问题的避免。
  import pymysql
  
  sql = 'SELECT count(*) as count FROM user WHERE id = %s AND password = %s'
  valus = [input['id'], input['password']]
  cursor = dbclient.cursor(pymysql.cursors.DictCursor)
  cursor.execute(sql, values)  #可以写入一个可迭代对象
  count = cursor.fetchone()
  if count is not None and count['count'] > 0:
      print('登陆成功')
  #这样参数化的方式，让 mysql 通过预处理的方式避免了 sql 注入的存在。
  #需要注意的是，不要因为参数是其他类型而换掉 %s，pymysql 的占位符并不是 python 的通用占位符。同时，也不要因为参数是 string 就在 %s 两边加引号，mysql 会自动去处理。
  ```

  

#### 三、使用pymysql进行增删改

- 增加数据

```python

#commit()方法：在数据库里增、删、改的时候，必须要进行提交，否则插入的数据不生效。
import pymysql

user = input('请输入用户名：')
pwd = input('请输入密码：')
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', password='', db='db2', charset='utf8')

cursor = conn.cursor()
sql = "insert into userinfo(username,password) values (%s,%s)"
print('sql语句:',sql)

# 3.执行sql语句,返回sql查询成功的记录数

result = cursor.execute(sql,(user,pwd))   #插入一条数据
    #result = cursor.executemany(sql,[(user+'1',pwd),(user+'2',pwd)])   #同时插入多条数据
print('返回记录数:',result)
conn.commit()

# 关闭连接，游标和连接都要关闭
cursor.close()
conn.close()

#判断结果
if result:
    print('ok')
else:
    print('error')
```

- 改数据

  ```mysql
  # 大体一致，和上面一致，就是sql语句不同而已
  import pymysql
  # 打开数据库连接
  db = pymysql.connect("localhost","testuser","test123","TESTDB" )
   
  cursor = db.cursor()   # 使用cursor()方法获取操作游标 
   
  # SQL 更新语句
  sql = "UPDATE EMPLOYEE SET AGE = AGE + 1 WHERE SEX = '%c'" % ('M')
  try:
     cursor.execute(sql)  # 执行SQL语句
     db.commit()          # 提交到数据库执行
  except
     db.rollback()        # 发生错误时回滚,没有成功就会重新执行
   
  cursor.close()   # 关闭数据库连接
  db.close()
  ```

- 删除操作

  ```python
  import pymysql
   
  # 打开数据库连接
  db = pymysql.connect("localhost","testuser","test123","TESTDB" )
   
  # 使用cursor()方法获取操作游标 
  cursor = db.cursor()
   
  # SQL 删除语句
  sql = "DELETE FROM EMPLOYEE WHERE AGE > %s" % (20)
  try
     cursor.execute(sql)  # 执行SQL语句
     db.commit()          # 提交修改
  except
     db.rollback()        # 发生错误时回滚# 关闭连接
  db.close()
  ```

- 查找

  ```python
  #fetchone():获取下一行数据，第一次为首行；
  #fetchall():获取所有行数据源
  #fetchmany(4):获取4行数据
  
  import pymysql
   
  # 打开数据库连接
  db = pymysql.connect("localhost","testuser","test123","TESTDB" )
   
  # 使用cursor()方法获取操作游标 
  cursor = db.cursor()
   
  # SQL 查询语句
  sql = "SELECT * FROM EMPLOYEE \
         WHERE INCOME > %s" % (1000)
  try:
     
     cursor.execute(sql)# 执行SQL语句
     results = cursor.fetchall()# 获取所有记录列表
     for row in results:
        fname = row[0]
        lname = row[1]
        age = row[2]
        sex = row[3]
        income = row[4]
         # 打印结果
        print ("fname=%s,lname=%s,age=%s,sex=%s,income=%s" % \
               (fname, lname, age, sex, income ))
  except:
     print ("Error: unable to fetch data")
   
  # 关闭数据库连接
  db.close()
  
  
  cursor.scroll(1,mode='relative')  # 相对当前位置移动
  cursor.scroll(2,mode='absolute') # 相对绝对位置移动
  #在fetchone示例中，在获取行数据的时候，可以理解开始的时候，有一个行指针指着第一行的上方，获取一行，它就向下移动一行，所以当行指针到最后一行的时候，就不能再获取到行的内容，所以我们可以使用如下方法来移动行指针：
  ```



#### 四、数据备份

```mysql
#备份语法：
# mysqldump -h 服务器 -u用户名 -p密码 数据库名 > 备份文件.sql

#示例：
    #单库备份
    mysqldump -uroot -p123 db1 > db1.sql    # 在cmd中讲db1库写成一个备份文件
    mysqldump -uroot -p123 db1 table1 table2 > db1-table1-table2.sql   #将表写成备份文件

    #多库备份
    mysqldump -uroot -p123 --databases db1 db2 mysql db3 > db1_db2_mysql_db3.sql

    #备份所有库
    mysqldump -uroot -p123 --all-databases > all.sql 

#数据恢复
    #方法一：
    [root@egon backup]# mysql -uroot -p123 < /backup/all.sql

    #方法二：
    mysql> use db1;
    mysql> SET SQL_LOG_BIN=0;   #关闭二进制日志，只对当前session生效
    mysql> source /root/db1.sql
```

#### 五、事务和锁

```mysql
begin;  # 开启事务
select * from emp where id = 1 for update;  # 查询id值，for update添加行锁；
update emp set salary=10000 where id = 1; # 完成更新
commit; # 提交事务
```

#### 六、补充知识点

- DictCursor的这个功能是继承于CursorDictRowsMixIn，这个MixIn提供了3个额外的方法: fetchoneDict、fetchmanyDict、fetchallDict。

  ```
  在默认情况下cursor方法返回的是BaseCursor类型对象，BaseCursor类型对象在执行查询后每条记录的结果以列表(list)表示。如果要返回字典(dict)表示的记录，就要设置cursorclass参数为MySQLdb.cursors.DictCursor类。
  cur = conn.cursor(cursorclass=MySQLdb.cursors.DictCursor)
  ```

- 使用方法

  ```sql
  这个参数也可在调用connect方法建立连接时设置,如下：
  >>> conn = MySQLdb.connect(host='192.168.1.103', port=3306, 							user='testacc', pass
  						wd='test1234', db='1dcq', 
                          cursorclass=MySQLdb.cursors.DictCursor)
  >>>conn.close()
  
  #具体使用
  >>> cursor = conn.cursor(MySQLdb.cursors.DictCursor)
  >>> cursor.execute('SELECT * FROM pagesobject LIMIT 0, 1')
  1L
  >>> cursor.fetchone()   # 等价fetchoneDict()
  {'PageDesc': None, 'P_Id': 0L, 'isParent': 0, 'Html_open': 'true', 'PageName': '???', 'Id': 1L}
  >>> cursor.close()
  >>>conn.close()
  ```

- cursor是可以重复使用的，但涉及到事务如插入、删除时尽量即使提交，查询时可以多次

  ```sql
  import pymysql.cursors
  # 连接到数据库后实际上TCP的连接状态是ESTABLISHED
  connection = pymysql.connect(host='localhost',
                               user='user',
                               password='passwd',
                               db='db',
                               charset='utf8mb4',                           cursorclass=pymysql.cursors.DictCursor)
  
  try:
      with connection.cursor() as cursor:
          sql = "INSERT INTO `users` (`email`, `password`) VALUES (%s, %s)"
          cursor.execute(sql, ('webmaster@python.org', 'very-secret'))
  
      #默认不自动提交事务，所以需要手动提交
      connection.commit()
  
      with connection.cursor() as cursor:
          sql = "SELECT `id`, `password` FROM `users` WHERE `email`=%s"
          cursor.execute(sql, ('webmaster@python.org',))
          result = cursor.fetchone()
          print(result)
  finally:
      connection.close()
  ```

  

