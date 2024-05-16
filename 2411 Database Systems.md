#  2411 Database Systems

**Database Systems note -- *Made by Ziyang CHEN, Rocky, 陈子阳***

**PolyU FinTech & AI student 21'-25' - Department of Computing**

----

[TOC]

We will have the final exam together with the students of Wednesday session. It will be a close-book/notes 2-hour exam and the format is very similar to that of the midterm exam. The exam will be comprehensive and all content in this course except those in the last two lectures will be tested. But this time, we will focus on **File Organization and Indexing, Query Optimization and Transactions and Concurrency Control**. The final exam will be eaiser than the midterm exam. 

我们将与周三班级的学生一起进行期末考试。 这将是一个 2 小时的闭卷/笔记考试，形式与期中考试非常相似。 考试将是全面的，除了最后两节课的内容外，本课程的所有内容都将被测试。 但这一次，我们将专注于**文件组织和索引、查询优化和事务以及并发控制**。 期末考试会比期中考试容易。



##  1. <u>ER , Relational Data Model</u>

---

<img src="2411%20Database%20Systems.assets/image-20221013205324579.png" alt="image-20221013205324579" style="zoom:150%;" />

### **超键(Superkey):** 

attribute set，比如: {ID} and {ID,name} are both superkeys of instructor.

**Example of the super key**

Consider the student table with columns [ID,NAME,PHONE]

- - [ID]: As ***no two students will have the same Id,*** it will help to uniquely access the student details, **hence it is a super key**Now we will identify all the Super Keys  present in the table that is the  set of attributes that will help to uniquely access a record
  - [NAME, ID]: Even if more than one student has the same name then ***the combination name will help to recognize the record, as student id will break the tie*** and this is combination is a super key
- [PHONE]: As ***no two students will have the same phone number,*** a phone number will help to uniquely access the student details and hence ***phone number is a super key***
- **Every Candidate key is a super key**
- If we just add one or more attributes to a candidate key then, it becomes a super key.

<img src="2411%20Database%20Systems.assets/Super-Key.webp" alt="Super-Key" style="zoom:33%;" />



### **候选码(Candidate key):**

**关系中的一个属性组，其值能唯一标识一个元组**，若从该属性组中去掉任何一个属性，它就不具有这一性质了，这样的属性组称作候选码。

**Example:**

学生(S#, Sname, Sage, Sclass, Saddress)
其中属性**S#**是候选码，属性组**(Sname, Saddress)**也是候选码(同名同地址的两个同学是不存在的）

<img src="2411%20Database%20Systems.assets/Candidate.webp" alt="Candidate" style="zoom:33%;" />



### **主键( primary key )：**

当有多个候选码时, 可以选定一个作为主码, 主键是唯一的

一个数据表中只能包含一个主键。你可以使用主键来查询数据

DBMS以主码为主要线索管理关系中的各个元组。

**Example:**

可选定属性 **S#** 作为"学生"表的主码，也可以选定屈性组 (Sname,Saddress) 作为“学生”表的 primary key

**Creating a primary key: ** 

```sql
CREATE TABLE EMP ( 
  ID INT,      
  NAME VARCHAR (20),      
  AGE INT,     
  COURSE VARCHAR (10),   
  PRIMARY KEY (ID) 
);
```

<img src="2411%20Database%20Systems.assets/Primary-Key.webp" alt="Primary-Key" style="zoom: 33%;" />



### **外键(foreign key):** 

外键用于关联两个表，一种关系中的value必须出现在另一种关系中

- 当前表中**用作外键**的列或列的列表**必须是另一个表中的主键** 
- 一张表的 **Primary key** 列在另一张表中用作 **foreign key** 的**结构和数据类型必须相同** 
- 包含外键的表称为**子表**，包含候选键的表称为**引用表或父表。**

<img src="2411%20Database%20Systems.assets/Foreign-Key.webp" alt="Foreign-Key" style="zoom:33%;" />

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2018.13.31.png" alt="截屏2022-10-17 18.13.31" style="zoom:50%;" />



### **三级模式结构**

---

<img src="2411%20Database%20Systems.assets/dataabstraction.webp" alt="dataabstraction" style="zoom: 33%;" />

- **视图/外模式/用户模式/子模式(View1, View2, View3 / External schema)：**给最终用户看到的数据的样子

  | 课程信息表（课程号，选课人数） |
  | :----------------------------: |

- **(概念/逻辑)模式(Conceptual/Logical Schema)：**定义逻辑结构, 概念模式中的这些表称为基表, 与物理模式的表一一对应

  **Example** : Create table emp(id num(5) primary key ,name varchar(10));

  |   学生   | （学号，名字，年龄，GPA） |
  | :------: | :-----------------------: |
  |   课程   |    （课程号，课程名）     |
  | 选课关系 |  （学号，课程号，成绩）   |

- **内/物理模式(Internal/Physical schema)：**描述了数据在磁盘上是怎么存的。数据库是建立在文件之上的。这些数据保存的特定格式和方式，就是物理模式。

|  用堆文件形式存储这三张表  |
| :------------------------: |
| 在学生的某项信息中建立索引 |



### **数据独立性** & Constraint

---

**在一个 level/schema 进行的所有事务或更改都不会影响到其他 level/schema **

- **逻辑数据独立性：保护数据的逻辑结构的改变, 在概念级别添加新实体不应影响外部级别**

​		一个在数据库的基础之上开发的应用程序不受数据逻辑结构的影响。

​		假如基表变动（也就是数据结构变动），那么通过改变一些逻辑，能够使得原来基表还未变动之前的视图不受改变。

​		即基表虽然改变, 但是视图为了保证不变，我修改基表和视图的映射。

- **物理数据独立性：保护数据的物理结构的改变**

​		一个表结构变化，比如我前面使用哈希文件存的，但是现在用堆文件存，只要表逻辑结构不变，物理结构变化是没事的。

 

- **Cardinality ratio:** 1:1, 1:N, N:1, or M:N。一对一，一对多，多对一，多对多
- **Participation constraint: **: single line (partial participation), double line (every entity participate)
- **The (min,max) notation:** 

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2020.55.31.png" alt="截屏2022-10-13 20.55.31" style="zoom: 33%;" />

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2020.57.46.png" alt="截屏2022-10-13 20.57.46" style="zoom:150%;" />



Informal Terms             Formal Terms

Table                    				Relation

Column                   			Attribute

Row                     				Tuple

Values in a column           Domain

Table Definition             	Schema

 

### ER to Relational schema:

---

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2020.59.27.png" alt="截屏2022-10-13 20.59.27" style="zoom:50%;" />

![截屏2022-10-13 20.59.09](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2020.59.09.png)

**Example:** 

![截屏2022-10-17 11.30.32](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2011.30.32.png)

**Answer:** note that a table for a 1:M relationship (diamond) can be saved/skipped with proper adjustment of the table on the M side...

- Airline(**<u>AirCode</u>**, Address, Telephone, ...)
- AirPlane(**<u>PlaneID</u>**, Model, No.ofSeats, ..., AirCode)
- Airport(**<u>APname</u>**, City, Capacity, ...)
- Flight(**<u>Flight#</u>**, Type, AirCode, PlaneID, APname, Date, Depart-time, Gate) 
- Stops-At(**<u>Flight#</u>, <u>APname</u>**, Sequence#, Date, Time)



## 2. <u>SQL</u>

----

### 2.1 <u>SQL语句分类</u>

##### **DQL：**

​		数据查询语言（凡是带有select关键字的都是查询语句）
​		select...		

##### **DML：**

​			数据操作语言（凡是对表当中的数据进行增删改的都是DML）
​			insert delete update
​			insert 增
​			delete 删
​			update 改

​		这个主要是操作表中的数据data。

##### **DDL：**

​		数据定义语言
​		凡是带有create、drop、alter的都是DDL。
​		DDL主要操作的是表的结构。不是表中的数据。
​		create：新建，等同于增
​		drop：删除
​		alter：修改
​		这个增删改和DML不同，这个主要是对表结构进行操作。

##### **TCL：**

​		事务提交：commit;
​		事务回滚：rollback;



### 2.2 <u>MYSQL中的数据类型</u>

---

**varchar(最长255)**
		**可变长度的字符串**，比较智能，节省空间。会根据实际的数据长度动态分配空间。

​		优点：节省空间
​		缺点：需要动态分配空间，速度慢。

**char(最长255)**
		**定长字符串**，不管实际的数据长度是多少，分配固定长度的空间去存储数据。
		使用不恰当的时候，可能会导致空间的浪费。

​		优点：不需要动态分配空间，**速度快。**
​		缺点：使用不当可能会导致空间的浪费。

​		**varchar和char我们应该怎么选择？**
​			性别字段你选什么？因为性别是固定长度的字符串，所以选择char。
​			姓名字段你选什么？每一个人的名字长度不同，所以选择varchar。

**int(最长11)**

​		数字中的整数型。等同于java的int。

**float/double**
		单/双精度浮点型数据

**date**
		短日期类型

### 2.3 <u>Table</u>

---

#### 创建一个Table:

```sql
CREATE TABLE STUDENT (
  STU_ID CHAR(4),
  STUDENT_NAME CHAR(10),
  ADDRESS ,
  BIRTHDATE DATE
  GENDER CHAR(6),
  EMAIL VARCHAR(255));
```

#### 删除一个Table:

```sql
drop table t_student; // 当这张表不存在的时候会报错！
// 如果这张表存在的话，删除
drop table if exists t_student;
```

#### INSERT INTO

```sql
INSERT INTO STUDENT VALUES('1000','ROCKY', 'SHENZHEN', '07-JAN-04', 'MALE');
INSERT INTO STUDENT VALUES('1111', 'GIGI', 'HONGKONG', '08-APR-03', 'FEMALE');
INSERT INTO STUDENT VALUES('2222','AJONES','NEWYORK','21-DEC-10','MALE');
INSERT INTO STUDENT VALUES('3333','JONESB','TOKYO','21-MAR-01','FEMALE');
INSERT INTO STUDENT VALUES('4444','JONES','TAIWAN','16-JUN-10','MALE');
INSERT INTO STUDENT VALUES('5555','DJONES','SAN DIEGO','25-APR-04','FEMALE');
INSERT INTO STUDENT VALUES('6666','MARTIN JONES','WASHINGTON','28-JAN-1999','MALE');
```

#### 修改数据update

- update 表名 set 字段名1=值1,字段名2=值2,字段名3=值3... where 条件;

- 没有条件限制(where)会导致所有数据全部更新。

```sql
update STUDENT set name = 'jack', birth = '2000-10-11' where id = 2; 
-- to set name = 'jack', birth = '2000-10-11' for those id = 2.

UPDATE Deposit SET balance = balance * 1.05;
-- to increase the payment by 5% to all accounts; it is applied to each tuple exactly once.

UPDATE Deposit SET balance = balance * 1.06 WHERE balance > 10000;
UPDATE Deposit SET balance = balance * 1.05 WHERE balance <= 10000
-- to increase the payment by 6% to all accounts with balance over $10000; all others receive 5% increase.
```

#### alter添加/删除/修改表中的列

```sql
-- Adding a single column 
ALTER TABLE student ADD Email varchar(255); -- adds an "Email" column to the "student" table

-- Adding multiple columns 
ALTER TABLE student ADD (
father name VARCHAR(60),
mother name VARCHAR(60),
DOB DATE);

-- Modifying a single column 
ALTER TABLE student MODIFY(
address varchar(75)); -- 将地址列的数据类型更改为 varchar 和 size 为 75，覆盖现有的 size 值和数据类型

-- Modifying multiple columns
ALTER TABLE student MODIFY(
address varchar(75),
dob date);

-- Rename 
ALTER TABLE student RENAME address to location;

-- Drop
ALTER TABLE student DROP COLUMN Email; -- deletes the "Email" column from the "student" table
```

<img src="2411%20Database%20Systems.assets/Alter.webp" alt="Alter" style="zoom:50%;" />

#### Deletion

```sql
delete STUDENT where age = 22;
-- those tuples in STUDENT table for which age = 22 are deleted from STUDENT table.

DELETE Deposit WHERE bname IN (SELECT bname FROM Branch WHERE b_city = “<__>”)
```



## 2.4<u>条件查询格式</u>

<img src="/Users/chenziyang/Desktop/2411%20Database%20Systems.assets/image-20221013201917726.png" alt="image-20221013201917726" style="zoom:150%;" />



## 2.5 <u>总结完整SQL的DQL语句</u>

<img src="/Users/chenziyang/Desktop/2411%20Database%20Systems.assets/image-20221013201945612.png" alt="image-20221013201945612" style="zoom: 150%;" />

![202204191253480](2411%20Database%20Systems.assets/202204191253480-5915329.png)

##### **group by：**

where的优先级高于group by，所以where不执行完，分组函数执行不了。按照某个字段或者某些分段进行分组。执行顺序：from-> group by -> select。分组函数一般都是和group by联合使用，这也是为什么称之为分组函数的原因，并且任何一个分组函数（count，max，avg，min，sum）都是在group by执行完成后才执行。

如果一个sql语句后面有group by则查询的字段字段只能有分组字段，和分组函数。

- count计数：select count（*/字段名）from 表名； //统计的是总记录条数/当前字段不为null的数据条数。

- sum求和：select sum（字段名） from 表名；
- avg平均值：select avg（字段名） from 表名；
- max最大值：select max（字段名） from 表名；
- min最小值：select min（字段名） from 表名；

```sql
select  job, sum(sal) from emp group by job;  
```

 ![202204191252604](2411%20Database%20Systems.assets/202204191252604-5915335.png)

```sql
select job, deptno, sum(sal) from emp group by job,deptno;  
```

![202204191253428](2411%20Database%20Systems.assets/202204191253428-5915341.png)



![截屏2022-10-13 23.55.24](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.55.24-5915348.png)

```sql
select deptno,count(*) 
from emp 
group by deptno;
```

![截屏2022-10-13 23.55.49](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.55.49-5915356.png)

```sql
select deptno,max(sal) 
from emp
group by deptno
```

![截屏2022-10-13 23.56.19](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.56.19-5915364.png)

```sql
select deptno,max(sal)
from emp
where deptno!=30
group by deptno;
```

![截屏2022-10-13 23.56.48](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.56.48-5915367.png)

```sql
select deptno,max(sal) 
from emp
group by deptno
order by deptno;
```

![截屏2022-10-13 23.57.18](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.57.18-5915372.png)

```sql
SELECT ename , SUM(sal) FROM emp
GROUP BY ename
HAVING SUM(sal)>2000;
```

![截屏2022-10-13 23.58.00](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.58.00-5915398.png)



##### **having：**

对分组之后的数据进行再次过滤。可以理解作为优先级低的where；

```sql
select job, avg(sal) from emp group by job having avg(sal) > 2000;  
```

![202204191254493](2411%20Database%20Systems.assets/202204191254493-5915402.png)



**distinct去除重复字段:** select distinct 字段1，字段2 from 表；使得字段1和字段2联合起来去重。distinct必须出现在所有字段的最前面。不可：select 字段1，distinct 字段2 from 表；**count（diatinct 字段），先为字段去重，再统计数据条数；**

```sql
select distinct job from emp; -- distinct只能出现在所有字段的最前方。
select distinct job, deptno from emp; -- distinct出现在job,deptno两个字段之前，表示两个字段联合起来去重。
```



#####  order by

```sql
语法格式:
select ename,sal from emp order by sal; -- 默认是升序 asc！！
指定降序： desc
指定升序： asc

两个字段排序:
查询员工名字和薪资，要求按照薪资升序，如果薪资一样的话，再按照名字升序排列。
select ename,sal from emp order by sal asc, ename asc; -- sal在前，起主导，只有sal相等的时候，才会考虑启用ename排序。
```



### 2.6 笛卡尔积现象:

在表的连接查询方面的一种现象：当两张表进行查询的的时候，没有任何条件限制，最终的查询结果条数是两张表的乘积。

```sql
SELECT e.ename, d.dname FROM emp e, dept d;   
```

### 2.7 加条件避免笛卡尔积现象:

```sql
SELECT e.ename, d.dname FROM emp e, dept d WHERE e.deptno = d.deptno ;
```



### 2.8 <u>内连接 Inner join:</u>

---

##### **等值连接: **join …on

```sql
SELECT e.ename,d.dname FROM emp e  JOIN  dept d  ON e.deptno = d.deptno WHERE 过滤条件; 
```

##### **非等值连接**: BETWEEN..AND..

```sql
SELECT e.ename,e.sal,s.grade FROM emp e JOIN salgrade s ON e.sal BETWEEN s.losal AND s.hisal
```

##### **自连接：**Join

**一张表看作两张表，自己连自己**

```sql
SELECT a.ename,b.mgr admin FROM emp a JOIN emp b WHERE a.mgr = b.empno ; 
```



### 2.9 <u>外连接 Outer join</u>

---

左外连接（左连接）：表示左边是主表。

![截屏2022-10-13 20.41.49](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2020.41.49-5915408.png)

右外连接（右连接）：表示右边是主表。 

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2020.41.33.png" alt="截屏2022-10-13 20.41.33" style="zoom: 67%;" />



### 2.10 <u>子查询</u>

---

select语句中嵌套select语句，被嵌套的select语句称为子查询。

```sql
子查询都可以出现在哪里呢？
select
  ..(select).
from
  ..(select).
where
  ..(select).
  
  
where子句中的子查询:

第一步：查询最低工资是多少
  select min(sal) from emp;
  +----------+
  | min(sal) |
  +----------+
  |   800.00 |
  +----------+
第二步：找出>800的
  select ename,sal from emp where sal > 800;

第三步：合并
  select ename,sal from emp where sal > (select min(sal) from emp);
  
from子句中的子查询:
	注意：from后面的子查询，可以将子查询的查询结果当做一张临时表。

select后面出现的子查询（这个内容不需要掌握，了解即可！！！）
	注意：对于select后面的子查询来说，这个子查询只能一次返回1条结果
```



### 2.11 <u>常用字段里的命令词</u>

---

#### view

是基于 SQL 语句的结果集的虚拟表, 视图也将**具有行和列，就像**数据库中的真实表一样, ***视图始终显示最新数据***

```sql
-- Syntax
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

**视图的作用: **

- 视图能够**简化用户的操作**

- **站在不同的角度去看待同一份数据**（同一张表的数据，通过不同的角度去看待。）
- 我们**可以面向视图对象进行增删改查**，对视图对象的增删改查，会导致原表被操作 (影响到原表数据) ！**(缺点)**
- 假设有一条非常复杂的SQL语句，而这条SQL语句需要在不同的位置上反复使用。
  每一次使用这个sql语句的时候都需要重新编写，很长，很麻烦，怎么办？
  		可以把这条复杂的SQL语句以视图对象的形式新建。
  		在需要编写这条SQL语句的位置直接使用视图对象，可以大大简化开发。
  		并且利于后期的维护，因为修改的时候也只需要修改一个位置就行，只需要
  		修改视图对象所映射的SQL语句。
- 我们以后面向视图开发的时候，使用视图的时候可以像使用table一样。

- 可以对视图进行增删改查等操作。**视图不是在内存当中而是存储在硬盘上的，不会消失。**

<img src="2411%20Database%20Systems.assets/202204181159977.png" alt="202204181159977" style="zoom: 67%;" />

```sql
表复制：
	create table dept2 as select * from dept;
dept2表中的数据：
	select * from dept2;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+

创建视图对象：
	create view dept2_view as select * from dept2;

删除视图对象：
	drop view dept2_view;

注意：只有DQL语句才能以view的形式创建。
	create view view_name as 这里的语句必须是DQL语句;
```

```sql
面向视图查询:
select * from dept2_view; 

面向视图插入:
insert into dept2_view(deptno,dname,loc) values(60,'SALES', 'BEIJING');

查询原表数据:
mysql> select * from dept2;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
|     60 | SALES      | BEIJING  |
+--------+------------+----------+

面向视图删除:
mysql> delete from dept2_view;

查询原表数据:
mysql> select * from dept2;
Empty set (0.00 sec)

创建视图对象:
create view emp_dept_view as select e.ename,e.sal,d.dname from emp e join dept d on e.deptno = d.deptno;

查询视图对象:
mysql> select * from emp_dept_view;
+--------+---------+------------+
| ename  | sal     | dname      |
+--------+---------+------------+
| CLARK  | 2450.00 | ACCOUNTING |
| KING   | 5000.00 | ACCOUNTING |
| MILLER | 1300.00 | ACCOUNTING |
| SMITH  |  800.00 | RESEARCH   |
| JONES  | 2975.00 | RESEARCH   |
| SCOTT  | 3000.00 | RESEARCH   |
| ADAMS  | 1100.00 | RESEARCH   |
| FORD   | 3000.00 | RESEARCH   |
| ALLEN  | 1600.00 | SALES      |
| WARD   | 1250.00 | SALES      |
| MARTIN | 1250.00 | SALES      |
| BLAKE  | 2850.00 | SALES      |
| TURNER | 1500.00 | SALES      |
| JAMES  |  950.00 | SALES      |
+--------+---------+------------+

面向视图更新:
update emp_dept_view set sal = 1000 where dname = 'ACCOUNTING';

原表数据被更新:
mysql> select * from emp;
+-------+--------+-----------+------+------------+---------+---------+
| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | 
+-------+--------+-----------+------+------------+---------+---------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800.00 |    NULL |     
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600.00 |  300.00 |     
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250.00 |  500.00 |     
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975.00 |    NULL |     
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850.00 |    NULL |     
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 1000.00 |    NULL |     
|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000.00 |    NULL |     
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 1000.00 |    NULL |     
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500.00 |    0.00 |     
|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100.00 |    NULL |     
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950.00 |    NULL |     
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000.00 |    NULL |     
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1000.00 |    NULL |     
+-------+--------+-----------+------+------------+---------+---------+
```



#### **instr**

If a substring is found in a string, instr() will return its position; Otherwise, it returns 0.

```sql
select instr('Oracle SQL Query', 'SQL') from dual;  -- return 8

-- 假设您希望从表中获取数据，其中特定列包含特定字符串: 
SELECT * FROM Branch WHERE instr(B_CITY,'New')>0;
```



#### all

子查询返回列表的所有值都必须满足

```sql
-- Find branches having greater assets than all branches in New Territory
-- 找到资产比新界所有分行都多的分行

-- 1.使用 NOT EXISTS
SELECT X.bname
FROM Branch X
WHERE NOT EXISTS (SELECT *
FROM Branch Y
WHERE Y.b_city = “New Territory” 
AND Y.assets >= X.assets);

-- 2. 使用 ALL
SELECT bname
FROM Branch
WHERE assets > ALL (SELECT assets
FROM Branch
WHERE b_city = “New Territory”);
```



#### some

```sql
-- Find names of all branches that have greater assets than some branch located in Kowloon
-- 找出所有资产超过位于九龙的某些分行的分行名称

-- 1.使用 WHERE
SELECT X.bname
FROM Branch X, Branch Y WHERE X. assets > Y.assets
AND Y.b_city= “Kowloon”;

-- 2.使用 SOME
SELECT bname
FROM Branch
WHERE assets > SOME (SELECT assets
FROM Branch
WHERE b_city = “Kowloon”);
```



#### Minus/intersect/union

<img src="/Users/chenziyang/Desktop/2411%20Database%20Systems.assets/image-20221013234638805.png" alt="image-20221013234638805" style="zoom: 200%;" />



##### minus(-)

A-B: 它包含在A中但不在B中的所有元组。把属于关系A不属于关系B的元组找出来

![Set-Operations-in-DBMS-3-150x150](2411%20Database%20Systems.assets/Set-Operations-in-DBMS-3-150x150-5915432.png)

![截屏2022-10-13 23.39.33](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.39.33-5915436.png)

```sql
SELECT Name FROM Table1
MINUS
SELECT Name FROM Table2;
```

![截屏2022-10-13 23.39.56](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.39.56-5915440.png)

##### **intersect**

- `INTERSECT` statement combines result-set of two or more select queries and returns only those values that are common in both the result set
- Simply INTERSECT returns the common rows are values common in both the data sets and will not consider duplicate values

<img src="/Users/chenziyang/Desktop/2411%20Database%20Systems.assets/Set-Operations-in-DBMS-2.png" alt="Set-Operations-in-DBMS-2" style="zoom: 33%;" />

![截屏2022-10-13 23.43.55](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.43.55-5915447.png)

```sql
SELECT name FROM First
INTERSECT
SELECT name FROM Second;
```

![截屏2022-10-13 23.44.05](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.44.05-5915451.png)



##### Union

<img src="/Users/chenziyang/Desktop/2411%20Database%20Systems.assets/Set-Operations-in-DBMS-1.png" alt="Set-Operations-in-DBMS-1" style="zoom: 33%;" />

```sql
案例：查询工作岗位是MANAGER和SALESMAN的员工？
select ename,job from emp where job = 'MANAGER' or job = 'SALESMAN';
select ename,job from emp where job in('MANAGER','SALESMAN');
+--------+----------+
| ename  | job      |
+--------+----------+
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| JONES  | MANAGER  |
| MARTIN | SALESMAN |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
| TURNER | SALESMAN |
+--------+----------+

select ename,job from emp where job = 'MANAGER'
union
select ename,job from emp where job = 'SALESMAN';

+--------+----------+
| ename  | job      |
+--------+----------+
| JONES  | MANAGER  |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| MARTIN | SALESMAN |
| TURNER | SALESMAN |
+--------+----------+
union的效率要高一些。对于表连接来说，每连接一次新表，
则匹配的次数满足笛卡尔积，成倍的翻。。。
但是union可以减少匹配的次数。在减少匹配次数的情况下，
还可以完成两个结果集的拼接。

a 连接 b 连接 c
a 10条记录
b 10条记录
c 10条记录
匹配次数是：1000

a 连接 b一个结果：10 * 10 --> 100次
a 连接 c一个结果：10 * 10 --> 100次
使用union的话是：100次 + 100次 = 200次。（union把乘法变成了加法运算）

union在使用的时候有注意事项吗？

//错误的：union在进行结果集合并的时候，要求两个结果集的列数相同。
select ename,job from emp where job = 'MANAGER'
union
select ename from emp where job = 'SALESMAN';

// MYSQL可以，oracle语法严格 ，不可以，报错。要求：结果集合并时列和列的数据类型也要一致。
select ename,job from emp where job = 'MANAGER'
union
select ename,sal from emp where job = 'SALESMAN';
```



#### exists



#### not exists



#### limit

作用：将查询结果集的一部分取出来。通常使用在分页查询当中（百度默认：一页显示10条记录）分页的作用是为了提高用户的体验，因为一次全部都查出来，用户体验差。可以一页一页翻页看

```sql
limit的使用:

缺省用法：limit 5; 这是取前5
select ename,sal from emp order by sal desc limit 5; -- 取前5	limit 0,5;
注意：mysql当中limit在order by之后执行！！！！！！

取出工资排名在[3-5]名的员工:
select ename,sal from emp order by sal desc limit 2, 3; -- 2表示起始位置从下标2开始，就是第三条记录。3表示长度。

分页(起始下标从0开始)
每页显示3条记录
第1页：limit 0,3		[0 1 2]
第2页：limit 3,3		[3 4 5]
第3页：limit 6,3		[6 7 8]
第4页：limit 9,3		[9 10 11]

每页显示pageSize条记录
第pageNo页：limit (pageNo - 1) * pageSize, pageSize

public static void main(String[] args){
  // 用户提交过来一个页码，以及每页显示的记录条数
  int pageNo = 5; //第5页
  int pageSize = 10; //每页显示10条

  int startIndex = (pageNo - 1) * pageSize;
  String sql = "select ...limit " + startIndex + ", " + pageSize;
}

记公式：
limit (pageNo-1)*pageSize , pageSize
```



## 3. <u>Relational Algebra</u>

![截屏2022-10-17 16.17.03](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.17.03.png)

![截屏2022-10-17 16.16.52](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.16.52.png)



### 3.1 <u>Relational Operations:</u>

#### **select operation选择操作(σ)：**

从一个表中把满足条件的元组选出来。实际上就是加限制条件来选元组，他是一种一元关系操作。

```sql
σ sal > 1500 and sal < 3000 (emp) 
-- 找出表中符合工资为1500到3000的元组(tuples)：
select * from emp where sal > 1500 and sal < 3000;

σ course = "Java" (Student)
-- Selects tuples from Student where course = ‘Java’

σ course = "Java" and age = "20"(Student)
-- Selects tuples from Student where the course is ‘Java’ and age is ’20’.

σ salary > 50000 (Employee)
-- Selects tuples from Employee where salary is greater than 50000
```



#### project operation投影操作(π)：

将需要的属性列出来。投影是一元关系操作，投影就是选取关系中的某些列，即只选取了某些属性来看，即选取了某些列，就是投影：**Duplicate tuples are eliminated by the π operation.**

```sql
π empno, ename (emp) -- 选取emp表中的empno, ename 属性
select empno, ename from emp;
```

![截屏2022-10-17 16.22.41](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.22.41.png)

![截屏2022-10-17 16.23.44](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.23.44.png)

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.24.59.png" alt="截屏2022-10-17 16.24.59" style="zoom: 50%;" />

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.25.29.png" alt="截屏2022-10-17 16.25.29" style="zoom:50%;" />



#### Union operation (υ)

操作A ∪ B包括表A或表B中的所有元组。即使表a和表B中都存在一个元组，结果也只包含该元组一次。

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.07.31.png" alt="截屏2022-10-13 23.07.31" style="zoom: 33%;" />

#### Intersection (∩)

```sql
A ∩ B
```

<img src="/Users/chenziyang/Desktop/2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.20.24.png" alt="截屏2022-10-13 23.20.24" style="zoom:67%;" />



#### Set different集合差操作(-)

A-B: 它包含在A中但不在B中的所有元组。把属于关系A不属于关系B的元组找出来

```sql
A-B
```

![截屏2022-10-13 23.10.18](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.10.18-5915491.png)



#### Cartesian product笛卡尔乘积×

​     两个关系的拼接

![20200325215350287](2411%20Database%20Systems.assets/20200325215350287-5915495.png)

```sql
σ Attribute 2 = '1' (A X B)
-- The above example shows all rows from relation A and B whose Attribute 2 has value 1
```

 ![截屏2022-10-13 23.11.47](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.11.47-5915500.png)

![截屏2022-10-17 17.43.06](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2017.43.06.png)



### 3.2 <u>Join operations</u>

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-14%2000.13.28.png" alt="截屏2022-10-14 00.13.28" style="zoom:67%;" />



#### Ɵ连接 (THETA JOIN)

类似于 **笛卡尔积 (CARTESIAN PRODUCT) 后跟 SELECT**。 条件 c（通常使用Ɵ）称为连接条件。

生成满足连接条件的R1和R2的所有tuple组合。

投彩与选择操作只是对单个关系(表)进行操作，而实际应用中往往涉及多个
表之间的操作，这就耑要Ɵ连接操作

![截屏2022-10-17 16.28.20](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.28.20.png)

![截屏2022-10-17 16.29.59](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.29.59.png)

![截屏2022-10-17 16.33.30](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.33.30.png)



#### 等值连接⋈= (EQUIJOIN)

![截屏2022-10-17 16.34.04](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.34.04.png)

特殊的Ɵ连接，产生来自R1和R2的所有tuple的组合，这些tuple满足只用等价比较的连接条件。

连接条件 c 包括一个或多个相等比较，涉及来自 R1 和 R2 的属性。 也就是说，c 的形式为：(Ai=Bj)

```sql
(Ai=Bj) AND ... AND (Ah=Bk); 1<i,h<m, 1<j,k<n

-- In the above EQUIJOIN operation:
Ai, ..., Ah are called the join attributes of R1
Bj, ..., Bk are called the join attributes of R2

Example of using EQUIJOIN:
-- Retrieve each DEPARTMENT's name and its manager's name:
T ← DEPARTMENT ⋈ MGRSSN=SSN(EMPLOYEE) -- It is actually a “short hand” of σ MGRSSN=SSN (DEPARTMENT X EMPLOYEE)

RESULT ← π DNAME,FNAME,LNAME(T)

```

![截屏2022-10-17 16.36.23](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.36.23.png)

![截屏2022-10-17 16.37.08](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.37.08.png)



#### 自然连接Natural Join (*)

- JOIN 操作还允许连接来自不同关系的各种相关元组。
- 只有在关系之间存在共同的属性（列）时，才能执行自然连接。
- 属性的名称和类型必须相同。![截屏2022-10-18 14.17.08](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2014.17.08.png)

![截屏2022-10-17 16.39.16](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.39.16.png)

![截屏2022-10-17 16.40.13](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.40.13.png)

![截屏2022-10-17 16.42.40](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.42.40.png)

![截屏2022-10-17 16.43.00](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.43.00.png)

![截屏2022-10-17 16.43.18](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.43.18.png)

![截屏2022-10-17 16.43.55](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.43.55.png)

![image-20221013231618306](2411%20Database%20Systems.assets/image-20221013231618306-5915555.png)

```sql
C * D
```

![截屏2022-10-13 23.16.53](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-13%2023.16.53-5915558.png)



#### Join operations 练习

![截屏2022-10-17 16.48.18](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.48.18.png)

![截屏2022-10-17 16.50.48](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.50.48.png)



## 4. <u>Integrity Constraints (ICs) & Normal Forms</u>

### 4.1 <u>integrity constraint</u>

![截屏2022-10-17 16.03.51](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2016.03.51.png)

- 在数据库管理系统中，**完整性约束( integrity constraint)**是应用于表字段（列）或关系的**pre-defined**规则集，**以确保数据库表中存在的数据的整体有效性、完整性和一致性得到维护**。
- **每次执行表插入、更新、删除或更改操作时(INSERT a tuple, DELETE a tuple, MODIFY a tuple)**，都会评估完整性约束中提到的所有条件或规则。只有当约束结果为True时，才能插入、更新、删除或更改数据。因此， integrity constraint可用于防止授权用户对数据库的任何意外损坏。

![types-of-integrity-constraints](2411%20Database%20Systems.assets/types-of-integrity-constraints.webp)

#### Key constraints

键是一组实体，用于唯一标识其实体集中的实体。一个实体集中可能有多个键，但在这些多个键中，只有一个键是主键。关系数据库表中的主键只能包含唯一值，不能包含空值。

**Example:**Consider a student's table

![截屏2022-10-17 15.52.15](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2015.52.15.png)

学生表的最后一行违反了键完整性约束，因为Roll No 102在主键列中重复了两次。主键必须唯一且不为空，因此上述学生表的 Roll No 列中不允许出现重复值。



#### Domain Constraint

**域完整性约束(Domain integrity constraint)** 包含一组规则或条件，以限制列可以在数据库表中保存的属性或值的种类。 域的数据类型可以是char, int, string, date, number...

```sql
-- Constraint Creation Example
create domain age_constraint int
constraint age_test
check(value > 18);

-- Table Example
create table student (
student_id PRIMARY KEY,
student_name varchar(30),
student_age age_constraint int
); 

-- Example:
Name: string of char(30) 
Balance: Number(6,2); -- 6代表可以有的位数digits, 2代表小数点后面几位	
-- 如果是 Number(6,2) 那就不能写char在里面
```



#### Entity integrity constraints 

实体完整性约束(Entity Integrity)用于确保主键不能为空。主键用于识别表中的单个记录，如果主键为空值，则我们无法识别这些记录。表中除主键列外的任何位置都可以有空值

![截屏2022-10-17 15.49.53](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2015.49.53.png)

我们可以看到 ID 列是主键，并且在最后一行包含一个空值，这违反了实体完整性约束。



#### Referential integrity constraints

Also called **“foreign key constraint”**

参照完整性约束(Referential integrity constraints)确保两个关系数据库表之间必须始终存在有效关系。两个表之间的这种有效关系确认表中存在 **foreign key**。它应该始终引用另一个表中的相应值或属性，或者为 null

Example: 考虑一个 Employee 和一个 Department 表，其中 Dept_ID 充当两个表之间的外键

![截屏2022-10-17 15.50.38](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2015.50.38.png)

![截屏2022-10-17 15.50.46](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2015.50.46.png)

Dept_ID 充当Employees 表中的外键和Department 表中的主键。 DeptID=4 的行违反了参照完整性约束，因为 DeptID 4 未定义为 Departments 表中的主键列。

```sql
CREATE TABLE Customer (
Cname Char(20) NOT NULL,
Street Char(30),
City Char(25), 
PRIMARY KEY (Cname));

CREATE TABLE Branch
(Bname Char(20) NOT NULL,
Asset Integer, BCity Char(20),
PRIMARY KEY (Bname));

CREATE TABLE Deposit (
B-name Char(20),
AcctNo Char(10) NOT NULL,
Cname Char(20) NOT NULL,
Balance Integer,
PRIMARY KEY (AcctNo, Cname),
FOREIGN KEY (Bname) REFERENCES Branch, 
FOREIGN KEY (Cname) REFERENCES Customer);
```

![截屏2022-10-17 17.00.45](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2017.00.45.png)

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2018.15.29.png" alt="截屏2022-10-17 18.15.29" style="zoom:150%;" />



### 4.2 <u>Functional dependency and Attribute Closure</u>

#### **4.2.1 <u>函数依赖性 (Functional Dependency):</u>**  

- 如果一个属性是由 DBMS 系统中的另一个属性决定的，那么它是一个`functional dependency`
- A 对 B的函数依赖**由 A →B 表示(A determines B)**
- **由A可以得出B**
- **Candidate key definitely have functional dependency relationships**
- 每当***两个元组的属性 A 和属性 B 具有相同的值***时，关系***A->B 就被称为函数依赖*** 
- `Functional dependency`是当一个属性唯一地确定另一个属性时存在的关系 

![截屏2022-10-17 17.01.30](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2017.01.30.png)

![截屏2022-10-17 18.34.00](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2018.34.00.png)



- 在下表中，如果我们知道 Stu_ID 的值，我们可以得到 Stu_Name、Marks、City 等
- 由此我们可以说**Stu_Name、Marks 和 City 在功能上依赖于 Stu_ID**

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2017.04.42.png" alt="截屏2022-10-17 17.04.42" style="zoom:50%;" />

#### Non trivial functional dependency

- In a relationship, **if attribute B is not a subset of attribute A, then it is considered as a non-trivial dependency.**
- A nontrivial dependency occurs when ***A->B holds true where B is not a subset of A***.

**Example:**

![截屏2022-10-17 17.40.33](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2017.40.33.png)

- {Stu_ID} -> {Stu_Name} (if we know the Stu_ID, we knows the Stu_Name)
- But ***Stu_Name is not a subset of Stu_ID***, and hence it’s non-trivial functional dependency.



#### Armstrong's inference rules:

- **Reflexive rule : ** **If B is subset of A, then A → B.**
- **Augmentation rule :** When **A -> B, and C is attribute set, then AC->BC** (AC 代表 A U C)
- **Transitivity rule :** **If A→B and B→C, then A→C**

#### Additional inference rules:

- **Decomposition:** If X → YZ, then X → Y and X → Z
- **Union:** If X → Y and X → Z, then X → YZ
- **Psuedotransitivity:** If X → Y and WY → Z, then WX → Z



#### 4.2.2 <u>Attribute Closure set</u> 

![截屏2022-10-17 23.18.34](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2023.18.34.png)

如果属性集在功能上依赖于“A”的属性，则集合“A+”被称为“A”的闭包 (The set “A+” is said to be the closure set of “A” **if the set of attributes are functionally dependent on the attributes of “A”**)

A+ 包含可以从原始 A 推断出的所有函数依赖。

- (ID)+ = {ID, Name, State, Country, Age} 
- (State)+ = {State, Country}



**Example:**

U = {A, B, C, D, E}    FDs = {AB → C, B → D, C → E, EC → B, AC → B} 求(AB)+

**闭包就是：由AB可以推导出来的全部元素的集合**

(AB)+ =  {A, B, C, D, E}  



U = {A, B, C, D}   	FDs = {A → C, C → A, B → AC, D → AC} 求(AD)+

(AD)+ = {A, D, C}



**通过属性闭包找到候选键和超级键: ** 

- **如果关系的所有属性都存在于属性集的属性闭包**中，那么这样的属性**集将具有该关系的超级键**

- 如果关系的所有属性或**由任何属性集都没有确定，那么这样的集合也将是候选键**

  (ID, Name)+ = {ID, Name, State, Country, Age}

  (ID)+ = {ID, Name,State,Country, Age}

- (ID, Name)**将是超级键，但不是候选键**，因为它的子集 (ID) + 等于关系中存在的所有属性。因此，**ID 将成为候选键**。



#### Equivalence of functional dependencies

Two sets of FDs F and G are equivalent if: 

Every FD in F can be inferred from G

Every FD in G can be inferred from F 

Hence, F and G are equivalent if F+ =G+



There is a method for checking equivalence of sets of FDs

Example:

F: {A → BC}
 G: {A → B, A → C} 

Are they equivalent?

F+ =G+ = {A → B, A → C, A → BC}

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2018.38.53.png" alt="截屏2022-10-17 18.38.53" style="zoom:50%;" />





#### 4.3 <u>Normalization (Relational Database Design)</u>

规范化是将表分解为更多表的过程，以便新形成的表不存在插入、删除和更新数据的所有问题

Normalization is the process of organizing the data and the attributes of a database. It is performed to reduce the data redundancy in a database and to ensure that data is stored logically. Data redundancy means having the same data but at multiple places. It is necessary to remove data redundancy because it causes anomalies in a database which makes it very hard for a database administrator to maintain it.

规范化是组织数据和数据库属性的过程。 执行它是为了减少数据库中的数据冗余并确保数据按逻辑存储。 数据冗余意味着在多个地方拥有相同的数据。 有必要消除数据冗余，因为它会导致数据库中出现异常，这使得数据库管理员很难维护它。

![截屏2022-10-18 10.44.58](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2010.44.58.png)

There are 4 different normalisation benchmarks used which are –

- **1 NF (normal form):** 要求任何一张表<u>**必须有主键**</u>，**<u>每一个字段原子性 (Atomic values)不可再分</u>**
- **2 NF:** **<u>必须是1 NF (normal form)</u>**，要求所有非主键字段**完全依赖主键**，<u>**不要**产生**部分依赖**</u> (No partial dependencies)就是说 --- **不能有复合主键**
- **3 NF:** **<u>必须是2 NF (normal form)</u>**，要求所有非主键字段**直接依赖主键**，<u>**不要**产生**传递依赖**</u>
- **BCNF (Boyce-Codd Normal Form):** <u>**必须是3 NF (normal form)**</u>, 如果每个非平凡的函数依赖 A -> B，A 是一个超级键，即 LHS 应该始终是一个超级键。



**作用：**

- 数据的完整性 
- 消除数据冗余
- 使数据检索和数据插入，更新变得平滑、更快、优化

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2020.58.22.png" alt="截屏2022-10-17 20.58.22" style="zoom:67%;" />

#### 4.3.1 第一范式1NF

要求任何一张表<u>**必须有主键**</u>，**<u>每一个字段原子性 (Atomic values)不可再分</u>**

```sql
  学生编号 学生姓名 联系方式
	------------------------------------------
	1001		张三		zs@gmail.com,1359999999
	1002		李四		ls@gmail.com,13699999999
	1001		王五		ww@163.net,13488888888
	
以上是学生表，满足第一范式吗？
	不满足，第一：没有主键。第二：联系方式可以分为邮箱地址和电话

学生编号(pk) 学生姓名	邮箱地址			 联系电话
----------------------------------------------------
1001				张三		zs@gmail.com	1359999999
1002				李四		ls@gmail.com	13699999999
1003				王五		ww@163.net		13488888888
```

**Problem Table:**

![截屏2022-10-17 21.07.51](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2021.07.51.png)

从上图中，我们可以看到一个**单元格包含两个值(两个电话号码)**，这违反了我们的规则

**解决方法:** 将表分成两部分，使一个表中的所有***多值属性在另一个表中的单值属性处*** ，并将原表的主键属性添加到每个新形成的表中

![截屏2022-10-17 21.10.43](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2021.10.43.png)



#### 4.3.2 第二范式2NF

**<u>必须是1 NF (normal form)</u>**，要求所有非主键字段**完全依赖主键**，<u>**不要**产生**部分依赖**</u> (No partial dependencies)就是说 --- **不能有复合主键**

```sql
	学生编号 		学生姓名 	教师编号 	教师姓名
	----------------------------------------------------
	1001			张三		  001			王老师
	1002			李四		  002			赵老师
	1003			王五		  001			王老师
	1001			张三		  002			赵老师
这张表描述了学生和老师的关系：（1个学生可能有多个老师，1个老师有多个学生）
这是非常典型的：多对多关系！

分析以上的表是否满足第一范式？
	不满足第一范式。

怎么满足第一范式呢？修改

学生编号+教师编号(pk)		学生姓名  教师姓名
----------------------------------------------------
1001			001				张三			王老师
1002			002				李四			赵老师
1003			001				王五			王老师
1001			002				张三			赵老师

学生编号 教师编号，两个字段联合做主键，复合主键（PK: 学生编号+教师编号）
经过修改之后，以上的表满足了第一范式。但是满足第二范式吗？
	不满足，“张三”依赖1001，“王老师”依赖001，显然产生了部分依赖。
	产生部分依赖有什么缺点？
		数据冗余了。空间浪费了。“张三”重复了，“王老师”重复了。

为了让以上的表满足第二范式，你需要这样设计：
	使用三张表来表示多对多的关系！！！！
	学生表
	学生编号(pk)		学生名字
	------------------------------------
	1001					张三
	1002					李四
	1003					王五
	
	教师表
	教师编号(pk)		教师姓名
	--------------------------------------
	001					王老师
	002					赵老师

	学生教师关系表
	id(pk)			学生编号(fk)			教师编号(fk)
	------------------------------------------------------
	1						1001						001
	2						1002						002
	3						1003						001
	4						1001						002
	
	-- 多对多怎么设计？
	-- 多对多，三张表，关系表两个外键！！！！！！！！
```

**Problem Table:**

![截屏2022-10-17 22.21.05](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2022.21.05.png)

- 上表是第一范式，因为它没有多值属性
- student_id 和 subject_id 列的组合将成为表的主键。
- 但是teacher 列完全依赖于subject_id 列并且独立于student_id。
- 因此，即使表处于第一范式，它也会因插入更新和删除而出现问题，因此我们需要使用第二范式。

**将表转换为 2-NF 的过程: **

- 识别部分和全部依赖关系并应用分解规则
- 在上表列中，教师依赖于 subject_id
- 简单的解决方案是为 subject_id 和 Teacher 创建一个单独的表，并将其从 Score 表中删除
- 2范式的结果表是

![截屏2022-10-17 22.22.00](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2022.22.00.png)



#### 4.3.3 第三范式3NF

建立在第二范式的基础之上，要求所有非主键字段直接依赖主键，不产生传递依赖 (No transitive dependency)

任何非主属性不依赖于其他非主属性

- **什么是传递依赖 (transitive dependency)？**

​		当非主属性依赖于另一个非主属性而不是依赖于主属性或主键时

```sql
	学生编号（PK） 学生姓名  班级编号  	班级名称
---------------------------------------------------------

​		1001				张三		01			一年一班
​		1002				李四		02			一年二班
​		1003				王五		03			一年三班
​		1004				赵六		03			一年三班

以上表的设计是描述：班级和学生的关系。很显然是1对多关系！
一个教室中有多个学生。

分析以上表是否满足第一范式？
	满足第一范式，有主键。

分析以上表是否满足第二范式？
	满足第二范式，因为主键不是复合主键，没有产生部分依赖。主键是单一主键。

分析以上表是否满足第三范式？
	第三范式要求：不要产生传递依赖！
	一年一班依赖01，01依赖1001，产生了传递依赖。
	不符合第三范式的要求。产生了数据的冗余。

那么应该怎么设计一对多呢？

	班级表：一
	班级编号(pk)				班级名称
	----------------------------------------
	01								一年一班
	02								一年二班
	03								一年三班

	学生表：多

	学生编号（PK） 学生姓名 	班级编号(fk)
	-------------------------------------------
	1001				张三				01			
	1002				李四				02			
	1003				王五				03			
	1004				赵六				03		
	
	-- 一对多，两张表，多的表加外键！！！！！！！！！！！！
```

**Problem Table:**

![截屏2022-10-17 22.34.04](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2022.34.04.png)

- 在 employee table 中，我们具有如下所示的 dependency
  Project id -> date completion
- Project id 不是取决于primary key of the table employee ID，它取决于a non-prime attribute date_completion

该表属于第一范式，因为它没有任何多值属性 (not have any multivalued attributes)
该表属于第二范式，因为没有复合且没有任何部分依赖的机会 (no composite and no chances of any partial dependency.)

**But still, the stables office from the insert, update and delete anomalies(异常)**

- 插入异常：在分配员工之前无法分配项目 ID
- 更新异常：更新日期完成的值需要多次更新，因为它是冗余存储的表
-  删除异常：删除员工会导致整个项目数据的删除，即使该项目中甚至有一个员工在表中受到影响
- 即使表处于第二范式，它也会因为存在传递依赖关系而遭受所有异常，如图所示

**Note**: Table with any kind anomaly will have a transitive dependency.



**如何消除传递依赖？**

-  传递依赖被转换成单独的表
- 每个传递依赖的行列式成为对应表的主键属性
- 将每个传递依赖产生的每个表的主键添加为原始表中的外键以及所有剩余属性

**we have a transitive dependency [ project id -> date completion ]**

- 将原始员工表分成两个表，其中一个表项目由传递依赖项组成，第二个表员工始终表示属性
- 在一个新形成的项目表中将包含项目 ID、完成日期和决定性项目 ID 将作为这个新形成的表的主键属性

![截屏2022-10-17 22.39.47](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2022.39.47.png)

- In the newly formed employees, table project id is derived as a foreign key attribute from the original employee table along with the remaining attributes.



**现在比较这个新形成的表格和之前的问题表格**

- 可以在没有员工信息的情况下添加 Project ID
- 删除员工数据不会导致删除项目表中的项目数据，更新project-completion的值不会导致多次更新，因为它没有redundancy



#### 4.3.4 BCNF 巴斯科德范式

A relation R is in BCNF (Boyce-Codd normal form) if and only if every determinant (left-hand side of an FD) is a candidate key.

BCNF 是 Boyce-Codd Normal Form 的缩写，以下是关系为 BCNF 形式的条件:

- <u>**必须是3 NF (normal form)**</u>, 任何非主属性不能对主键子集依赖
- 如果每个非平凡的函数依赖 A -> B，A 是一个超级键，即 left hand side 应该始终是一个超级键

**problem table:**

![截屏2022-10-18 00.21.57](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2000.21.57.png)

**改为BCNF表:**

![截屏2022-10-18 00.22.31](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2000.22.31.png)



![截屏2022-10-18 15.29.11](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2015.29.11.png)

**Example:**

R = {A, B, C, D, E, F}

F = {AE → F, A → B, BC → D, CD → A, CE → F}

- 求候选键

​			Left: C, E

​			Left and Right: A, B, D

​			Right: F

- 看Left，发现C, E不能推出所有的R，所以不行
- 看Left and Right，先从Left and Right的第一个A配合Left开始看，发现ACE可以推出所有的R，第二个B配合Left开始看，发现BCE也可以推出所有的R，DCE也可以推出所有的R

所以候选键就是ACE, BCE, DCE



- 找3 NF第一步：将F的各个关系写成相应的R集合：



 R(A,B,C,D,E,F,G)
  FD = {A → B, A → C, C → D, C → E, E → FG}



**Problem Table: **

![截屏2022-10-17 23.02.44](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2023.02.44.png)

- 当一个员工可以在多个部门工作时，考虑一个表员工，这些依赖关系会导致插入、更新和删除问题
- 它属于第一范式，因为它没有多个属性，它属于第二范式和第三范式，还因为它没有各种部分和传递依赖。



**将表转换为 BCNF 的过程: **

上表中的函数依赖如下：

- **EMP → EMP_COUNTRY**
  **EMP_DEPT → {DEPT_NO, EMP_DEPT_NO}**
- 候选键：{EID, EMP_DEPT}
- 从表中可以看出，我们必须需要一个候选键组合 {EID, EMP-DEPT}，因为单个员工在两个以上部门工作，因此他的 ID 重复，因此只有员工 ID 不能用作主键
- 从原始员工表创建三个表
- 创建一个名为 EMP_COUNTRY 的表，其中包含员工国家和员工 ID，并且可以从该表中唯一标识每条记录。

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2023.03.52.png" alt="截屏2022-10-17 23.03.52" style="zoom:80%;" />

- 创建另一个包含员工部门类型编号和员工部门编号的表。

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2023.04.23.png" alt="截屏2022-10-17 23.04.23" style="zoom:67%;" />



- 再创建一张 EMP_DEPT_MAPPING 表，其中包含员工 ID 和部门 EMP_DEPT_MAPPING 表：

  <img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-17%2023.04.39.png" alt="截屏2022-10-17 23.04.39" style="zoom:80%;" />

  **functional dependencies: **

  **EID**  **→**  **EMP_COUNTRY** 

  **EMP_DEPT**  **→**  **{DEPT_NO, EMP_DEPT_NO}** 

  结论：该表是 BCNF ，因为每个依赖项(dependency)的左侧是所有三个表中的超键



## 5. <u>File Organization and Indexing</u>

**文件组织(File Organization)**指的是数据组织成record、block和访问结构的方式，包括把record和block存储在磁盘上的方式，以及记录和块之间相互联系的方法

今天要学习的是关于物理层 (physical layer)，因为我们要讨论**数据是如何存储，并且要高效率的存储**

以及如何访问数据？从物理层来说, 有效率的组织数据来存储数据和获取数据，访问数据。

![截屏2022-10-18 14.34.00](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2014.34.00.png)

#### 5.1 <u>storage medium</u>

- 将不同性价比的存储器组织在一起，满足高速度、大容量、低价格 需求
- CPU与内存直接交换信息，按存储单元(存储字byte）进行访问
- Magnetic Disk 按存储块(block)进行访问，其信息需先装入内存，才能被CPU处理

![截屏2022-10-18 15.35.28](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2015.35.28.png)



##### 主储存 (Primary storage): 

主储存 (Primary storage)包括可由计算机的 **central processing unit (CPU)** 直接操作的存储介质 (storage media)，例如计算机的**主存储器 (main memory)** 和更小但更快的高速**缓存存储器 (cache memories)**。 主存储 (Primary storage) 通常提供对数据的快速访问 (fast access to data)，但存储容量有限 (limited storage capacity)

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2011.35.39.png" alt="截屏2022-10-18 11.35.39" style="zoom:67%;" />

##### 二级存储 (Secondary storage)

我们一般使用**固态驱动器 (SSD)**，二级存储设备，**存储容量大，成本低**

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2011.35.57.png" alt="截屏2022-10-18 11.35.57" style="zoom: 50%;" />

##### 三级存储 (Tertiary storage) 

**光盘(Optical disks)**（CD-ROM、DVD 和其他类似storage media）和**磁带(tapes)**是当今系统中用作归档数据库(archiving databases)的离线存储(offline storage)的可移动介质，因此属于称为三级存储的类别。

- 与主存储设备(primary storage devices)相比，这些设备通常具有更大的容量、更低的成本和更慢的数据访问速度( larger capacity, cost less, and provide slower access to data)

- 二级或三级存储中的数据不能直接由CPU处理； 首先必须将其复制到主存储(primary storage)中，然后由 CPU 处理

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2011.39.40.png" alt="截屏2022-10-18 11.39.40" style="zoom: 50%;" />



#### 5.2 <u>Disk Storage Devices</u>

磁盘结构

**disk pack (磁盘组)**

- 数据存储 (Data stored) 为磁盘表面的磁化区域 (magnetized areas)
- disk pack包含多个连接到旋转主轴的磁盘
- 磁盘 (Disks) 在每个磁盘表面上分为同心圆形轨道 (concentric circular tracks)。 磁道容量 (Track capacities) 通常在 4 到 50 KB 之间变化
- 磁道被分成块 (A track is divided into blocks) (在某些系统中，有一个称为扇区(sectors)的中间单元，磁盘块是一个虚拟概念。是操作系统自己＂杜撰＂的。大小由操作系统决定，操作系统可以配置一个块多大。一个块大小=一个扇区大小*2的n次方。N是可以修改的)
- 可以通过将一个block定义为多个sectors，操作系统可以在不增加block地址数量的情况下使用更大的硬盘驱动器
- 每个系统的The block size B是固定的。 典型的 block 大小范围从 B=512 bytes 到 B=4096 bytes.
- 整个 block 在磁盘和主内存 (disk and main memory)之间传输以进行处理

<u>eg. 每个盘面有2^16个磁道(Track)，每个磁道有2^8个扇区(sector)，每个扇区有B=512 bytes 到 B=4096 byte</u>

A physical disk block address consists of a surface number, track number (within surface), and block number (within track)

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2016.16.21.png" alt="截屏2022-10-18 16.16.21" style="zoom: 40%;" />

![pIYBAF463XyAIRfuAAMbaE2wWgI305](2411%20Database%20Systems.assets/pIYBAF463XyAIRfuAAMbaE2wWgI305.png)

![fig13_02](2411%20Database%20Systems.assets/clip_image001-5915561.png)

![image-20221018142650813](2411%20Database%20Systems.assets/image-20221018142650813.png)



#### 5.3 <u>Files of Records</u>

- A file is a sequence  of records, where each record is a collection of data values (or data items).
- **file descriptor  (file header):** 包括描述文件的信息，例如field names及其数据类型 (data types,)，以及磁盘上文件块的地址(the addresses of the file blocks on the disk)
- Records are stored on disk blocks.
- A file can have fixed-length  records or variable-length  records.

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2015.42.43.png" alt="截屏2022-10-18 15.42.43" style="zoom: 50%;" />



**File records can be** 

**unspanned (no record can span two blocks)** : 意味着没有记录可以跨越两个块, 必须放在同一个block

![截屏2022-10-18 18.51.02](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.51.02.png)

**spanned (a record can be stored in more than one block): **可以跨越 block 的边界

![截屏2022-10-18 18.51.11](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.51.11.png)

In a file of fixed-length records, all records have the same format. Usually, unspanned blocking is used with such files.
Files of variable-length records require additional information to be stored in each record. Usually spanned blocking is used with such files.
The physical disk blocks that are allocated to hold the records of a file can be **contiguous, linked, or indexed.**



#####  5.3.1 Operations on Files

- **OPEN**: Make the file ready for access, and associates a pointer that will refer to a *current* file record at each point in time.
- **FIND**: Searches for the first file record that satisfies a certain condition, and makes it the current file record.
- **FINDNEXT**: Searches for the next file record (from the current record) that satisfies a certain condition, and makes it the current file record.
- **READ**: Reads the current file record into a program variable.
- **INSERT**: Inserts a new record into the file, and makes it the current file record.
- **DELETE**: Removes the current file record from the file, usually by marking the record as *no longer valid*.
- **MODIFY**: Changes the values of some fields of the current file record.
- **CLOSE**: Terminates access to the file.
- **REORGANIZE**: Reorganizes the file records: e.g., the records marked as deleted are physically removed from the file; new organization of the file records is created.
- **READ_ORDERED**: Read the file blocks in order of a specific field of the file.



#####  5.3.2 Unordered Files

插入快，搜索慢

- 也称为 **pile** 文件。
- 按特定字段的顺序读取记录需要对文件记录进行排序。
- **Linear Search** in O(n), expensive; 要搜索记录，必须通过文件记录进行**线性搜索(linear search)**。 这需要平均读取和搜索一半的文件块，因此非常昂贵。
- **Insertion** in O(1), efficient; 记录**插入非常有效(Record insertion is quite efficient)**，新记录**插入文件末尾(inserted at the end of the file)。**

![截屏2022-10-18 16.46.54](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2016.46.54.png)

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.00.51.png" alt="截屏2022-10-18 18.00.51" style="zoom:50%;" />



##### 5.3.3 Ordered Files

插入慢，搜索快

- 也称为 **sequential file**
- 用于存储排序的属性通常称为**排序字段(Ordering field)**，通常，排序字段使用关系中的**主码**，所以又称**排序码(Ordering key)**
- **Insertion** is expensive: for maintain the order/position;
- **Searching** is easy with **Binary Search** in O(log⁡n);

![截屏2022-10-18 16.47.32](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2016.47.32.png)

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.04.36.png" alt="截屏2022-10-18 18.04.36" style="zoom:50%;" />

<img src="2411%20Database%20Systems.assets/image-20221018175500050.png" alt="image-20221018175500050" style="zoom:55%;" />

Binary Tree: 每个节点node对应于一个磁盘块(disk block)；如果文件有n个块，树的高度将是log2n

<img src="2411%20Database%20Systems.assets/image-20221018180532948.png" alt="image-20221018180532948" style="zoom:50%;" />

```java
class TreeNode {
private:	
	int info;
	TreeNode* left;
	TreeNode* right;
};

class Mytree{
private:
	TreeNode* root;
}
```



##### 5.3.4 Hash Files

- 搜索和更新效率都提高了

- 用于进行Hash函数计算的属性通常称为 Hash field，Hash field 通常也采用关系中的主码，所以又称hash key

- Hashing for disk files is called **External Hashing**

- Search is very efficient on the hash key;
  - First, based on the hash key K, using the hash function to find the bucket index i=h(K);
  - Bucket(i) is where the target record is stored in;
  - Then, use **linear search** in Bucket(i) to find the target record, which is much faster than linear search in the whole file;

**buckets: 相同容量大存储地，用M表示。可以理解为多个block，bucket = one (or a fixed number of) disk block**, numbered *bucket**0**, bucket**1**, ..., bucket**M-1**.*

**哈希函数(hashing function)：**可以将**hash key value K **映射到{0,1,2,3,4,... M-1}中的某一值

![截屏2022-10-18 18.47.07](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.47.07.png)

![截屏2022-10-18 18.14.32](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.14.32.png)

不同记录可能被hash成同一桶号，此时需在桶内顺序检索出某一记录

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.21.18.png" alt="截屏2022-10-18 18.21.18" style="zoom:50%;" />



###### Collisions

当一个新记录哈希到一个已经满的桶时发生**Collisions**



解决方法:  **Open addressing**

- **Linear Probing**

从由 hash address 指定的已占用位置开始，程序依次检查后续位置，直到找到未使用的(空)位置

If collide, try **Bucket_id+1, Bucket_id+2, Bucket_id+3 …**

- **Quadratic Probing**

和**Linear Probing**相比**Quadratic Probing**可以缓解bucket的近邻的痛苦，所以他试图平衡一点。好的，把问题展开，分发到更多的桶。

由 hash address 指定的已占用位置开始，程序根据 2^n 检查后续位置，直到找到未使用的(空)位置

If collide, try **Bucket_id+1, Bucket_id+4, Bucket_id+8…**

![截屏2022-10-18 18.37.20](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2018.37.20.png)

<img src="2411%20Database%20Systems.assets/image-20221018183833167.png" alt="image-20221018183833167" style="zoom:67%;" />

**Chaining**:

- Various **overflow** locations are **kept**, usually by **extending the array** with a number of overflow positions;
- A **pointer** field is added to each **record location**.
- [![img](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/18.png)](https://ultrafish.cn/2022/12/13/database-systems-course-note/18.png)

![image-20221018184108257](2411%20Database%20Systems.assets/image-20221018184108257.png)





###### Dynamic And Extendible Hashing

记录的数量可能是个问题，要存储在数据文件中的数据可以增长或缩小。那么我们就会遇到问题，因为这是分配给此类的固定房间数量可能还不够。因此会发生很多碰撞

所以要处理那种 static 的情况。提出了两种不同的技术：**Dynamic And Extendible Hashing** 来对哈希技术进行了调整，以允许文件记录数量的动态增长和收缩。

Both dynamic and extendible hashing use the **binary representation** of the hash value *h*(K) in order to access a **directory**.

![截屏2022-10-18 19.15.04](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2019.15.04.png)



**In dynamic hashing**

- the directory is a **binary tree**.

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/19.png" alt="19" style="zoom:50%;" />

**In extendible hashing** 

- 为桶引入一间接层，即用一个指向块的指针数组来表示桶，而不是用数据块本身组成的数组来表示桶
- 指针数组能增长，其长度总是2的幂。因而数组每增长一次，桶的数目就翻倍。不过，并非每个桶都有一个数据块；如果某些桶中的所有记录可以放在一个块中，则这些桶可能共享一个块。
- 散列函数h为每个键计算出一个K位二进制序列，该K足够大，比如32。但是桶的数目总是使用从序列第一位或最后一位算起的若干位，此位数小于K,比如说i位。也就是说，当i是使用的位数时，桶数组将有2^i个项。

- the directory is an array of size 2^d (where d is called the **global depth**)
- An **insertion** in a bucket that is **full** causes the bucket to **split** into two buckets and the records are **redistributed** among the two buckets. 插入已满的桶会导致桶分成两个桶，并且记录会在两个桶之间重新分配。

<img src="2411%20Database%20Systems.assets/image-20221018190903047.png" alt="image-20221018190903047" style="zoom: 50%;" />

- e.g., `01X` is full, then a new record adds in, `01X` will split into `010X` and `011X`, and the records will be redistributed into `010X` and `011X`;

为了说明桶拆分，假设新插入的记录导致溢出，哈希值以 01 开头的桶——图 13.11 中的第三个桶。

记录将分布在两个桶之间：第一个包含所有记录，哈希值以 010 开头，第二个哈希值以 011 开头。

现在 010 和 011 的两个目录位置指向两个新的不同存储桶。在分裂之前，他们指向同一个桶。

两个新桶的局部深度d′为3，比旧桶的局部深度大1。

如果图 13.11 中哈希值以 111 开头的记录的存储桶溢出，则两个新存储桶需要一个全局深度 d = 4 的目录，因为这两个存储桶现在分别标记为 1110 和 1111，因此它们的局部深度都是 4。

目录大小因此加倍，并且目录中的每个其他原始位置也被分成两个位置，这两个位置都具有与原始位置相同的指针值。



##### 5.3.5 Indexes as Access Paths

- **a file of entries <field value, pointer to record>**, **Ordered** by field value;

- Index is called an **Access Path** on the field;
- **Binary Search** on the index yields a pointer to the file record

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2020.14.51.png" alt="截屏2022-10-18 20.14.51" style="zoom:40%;" />

<img src="2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2020.48.40.png" alt="截屏2022-10-18 20.48.40" style="zoom:42%;" />

i = index

![截屏2022-10-18 20.50.11](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2020.50.11.png)

![截屏2022-12-30 22.29.00](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-30%2022.29.00.png)



#### 5.4 <u>Types of Single-level Indexes</u>

##### 5.4.1 Index file

索引文件是一种辅助存储结构，其存在与否不改变存储表的物理存储结构；然而其存在，可以明显提高存储表的访问速度。

![截屏2022-11-07 14.18.15](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2014.18.15.png)

在student表中创建一个基于Sname的素引:

```sql
create index indexSname on student(Sname);
```

在student表中创建一个基于Sname和Sclass的素引

```sql
create index indexSnSc on student(Sname, Sclass);
```

如某索引不再需要，则可通过撤消命令，撤消用户创建的索引

```sql
drop index indexSname;
```



###### Dense index & sparse index

- 对于主文件中每一个记录(形成的每一个索引字段值)，都有一个索引项和它对应，指明该记录所在位置。这样的索引/称稠密素引(dense index)
- 对于主文件中部分记录(形成的索引字段值），有索引项和它对应，这样的索引称非稠密素引(undense index)或稀疏素引(sparse index)

![截屏2022-11-07 14.23.07](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2014.23.07.png)

![截屏2022-11-07 14.27.04](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2014.27.04.png)



##### 5.4.2 主索引 Primary Indexes

- 是一种 **稀疏素引(sparse index)**

- Ordered on a **key** field;

- The key field value point to the **first record in each block**, which is called the block *anchor*;

- 每个索引块有个索引项

![21](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/21.png)

![截屏2022-11-07 14.29.50](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-07%2014.29.50.png)



##### 5.4.3 辅助索引 Secondary Indexes

Secondary Indexes 是定义在主文件的任一或多个非排序字段上的辅助存储结构。

> Provides a **secondary means (layers)** of accessing a file for which some primary access already exists

- Secondary index may be on a **candidate** key field or a **non-key field**;

The **Ordered Index File** has two fields:

- **Non-ordering field** (ie.,the indexing field) of the data file;
- **Block pointer** or a **Record pointer**;
- Dense index: Include **one entry for each record** in the data file;

Secondary Index on **candidate** key:![24](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/24.png)

Secondary Index on **non-key** field:

![25](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/25.png)

![截屏2022-11-07 14.35.43](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2014.35.43.png)



##### 5.4.4 聚焦索引 Clustering Indexes

是指索引中邻近的记录在主文件中也是临近存储的；

- Defined on an **ordered** data file;

- Ordered on a **non-key** field;

- The key field value point to the **first data** block of each **distinct** value of the field;

  ![截屏2022-10-18 20.30.09](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2020.30.09.png)

![22](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/22.png)





### 6. <u>B-Trees and B+-Trees as Dynamic Multi-level Indexes</u>

#### 6.1 <u>Multi-level Indexes</u>

- **Original index file** is called the **first-level index**;
- **The index to the index** is called the **second-level index**;

[![img](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/26.png)](https://ultrafish.cn/2022/12/13/database-systems-course-note/26.png)

- A multi-level index can be created for any type of first-level index (primary, secondary, clustering) as long as the **first-level index consists of more than one disk block**;
- Such a multi-level index is a form of ***Search Tree\***;

[![img](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/27.png)](https://ultrafish.cn/2022/12/13/database-systems-course-note/27.png)

[![img](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/28.png)](https://ultrafish.cn/2022/12/13/database-systems-course-note/28.png)

- p = n means n pointers from a node to its children;



#### 6.2 B Trees and B+ Trees

**B Tree**: Balanced Tree;

- a special type of self-balancing search tree in which each node can contain more than one key and can have more than two children. It is a generalized form of the binary search tree.

------

- Multi-level indexes use B tree or B + tree data structures, which **leave space in each tree node (disk block) to allow for new index entries**;
- Node is kept between **half-full** and **completely full**;

------

Operation:

- Insertion:

  - Not full: quite efficient;
  - Full: Insertion causes a node ***split\*** into two nodes;
  
- Deletion:

  - Not less than half full: quite efficient;
- Less than half full: **merged** with neighboring nodes;

------

##### B Tree

> Pointers to **data records** exist at **all levels** of the tree.

<P1,<K1,Pr1>,P2,<K2,Pr2>,…,<Kq–1,Prq–1>,Pq>

- P is the tree pointer;

- Pr is the data pointer;

- K is the key field;

- Each node has at most p tree pointers;

  - Each **node**, except the root and leaf nodes, has at least **⎡⎤⎡(p/2)⎤ tree pointers**;
  - The **root** node has **at least two tree pointers** unless it is the only node in the tree;
  
- A node with **q tree pointers**, q≤p, has **q−1 search key field** values;

**[Example]**:

[![img](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/29.png)](https://ultrafish.cn/2022/12/13/database-systems-course-note/29.png)

------

##### B+ Tree

> All pointers to **data records** exists **only** at the **leaf-level** nodes
> dDta pointers are stored only at the leaf nodes of the tree;
> **Order**-sensitive;

For each **internal** node:

- No data pointer inside:

  <P1,K1,P2,K2,…,Pq−1,Kq−1,Pq>

- Pi is a **tree pointer**;

- **Internal** node has **at most p tree pointers**;

- Each internal node has at most p tree pointers;

  - Each **node**, except the root and leaf nodes, has at least **⎡⎤⎡(p/2)⎤ tree pointers**;
  - The **root** node has **at least two tree pointers** unless it is the only node in the tree;

- A node with **q tree pointers**, q≤p, has **q−1 search key field** values;

For each **leaf** node:

<<K1,Pr1>,<K2,Pr2>,…,<Kq−1,Prq−1>,Pnext>

- Pri is a data pointer;
- Pnext points to the **next leaf node** of the B+ -tree;
- Each **leaf** node has **at least ⎡⎤⎡(p/2)⎤ values**;
- All **leaf** nodes are at the **same level**.

------

Example **Insertion** in a B+ Tree

- Every **key value** must exist at the **leaf level**, because all data pointers are at the leaf level;
- Every value appearing in an **internal node** also appears as the **rightmost value in the leaf level** of the subtree;
- Root Node只有Tree node pointers，在leaf Node只有data pointers 
- split的时候，第二个Node是第一个Node的half(ceiling)

$$
P = 3; \bf 在\ Internal\ Node\ 里我们只允许3个Tree\ node\ pointers\\\ Pleaf=2; \bf 在\ leaf\ Node\ 我们只允许2个data\ pointers,leaf\ node只能有两个elements
$$

[![img](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/31.png)](https://ultrafish.cn/2022/12/13/database-systems-course-note/31.png)

------

Example **Deletion** in a B+ Tree

- Merge:

  - **redistribute** the entries among the node and its **sibling** so that both are at least half full;

- Try to redistribute entries with the left sibling;

  - if this is not possible, an attempt to **redistribute** with the **right** sibling is made.
- If this is also not possible, the three nodes are **merged into two leaf nodes**.

![img](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/32.png)



------

##### Example

**An example of insertion to a B+ Tree, with p = 3, and pleaf = 2**![截屏2022-10-18 21.43.05](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2021.43.05.png)

![截屏2022-10-18 21.43.39](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2021.43.39.png)



### 7. <u>Query optimization 查询优化</u>

为处理查询选择合适的执行策略的过程(The process of choosing a suitable execution strategy for processing a query)

让我们先看看当用户提交一个 SQL Query 时系统内部发生的情况:

![Document (5)-1](2411%20Database%20Systems.assets/Document%20(5)-1.jpg)

1. 首先你的 SQL 查询被提交到系统。它将通过验证(validation) 确保您的 SQL 语法(syntactic) 是否正确。没有语法错误语法错误。数据库管理系统进行扫描、解析和验证。

2. 然后会将 Query 转化为中间形式/表示(Immediate form / intermediate representation of query)，例如关系代数(relational algebra)。传递给一个称为查询优化器(Query optimizer)的模块。这是数据库管理系统中 DBMS 中的一个模块。

3. 然后优化器会根据它生成执行计划(optimizer will generate execution plan)。例如Access pass for example, what index available? What are the kind of file structure? 然后将其传递给代码生成器(Query code generator)。
4. 生成查询的执行代码(code to execute the query)

5. 数据库处理器处理查询并从数据库中获取数据。最后将结果返回给用户。

所以我们今天的重点就是聊聊**如何将有效的 SQL 转换为中间形式并使用什么样的策略进行优化**。



#### Query Block

查询块(Query Block) 可以转化为代数算子并进行优化的基本单元(The basic unit that can be translated into the algebraic operators and optimized)

```sql
A query block 包含一个 SELECT-FROM-WHERE 表达式，以及 GROUP BY和 HAVING (如果它们是块的一部分)
```

**Nested queries: ** 在一个 select from where 子句中还有一个 select from where 子句

Query 中的嵌套查询(Nested queries)也可以被标识为独立的查询块

**Aggregate operators( group by )** in SQL 我们需要扩展代数(extended algebra)以适应 SQL 中的聚合函数



Immediate form Example：将这两个块翻译成代数（F表示Aggregate operators）

![截屏2022-11-07 19.50.25](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2019.50.25.png)





#### Search Methods for Selection

![截屏2022-11-07 14.29.50](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2014.29.50.png)

![截屏2022-11-07 14.32.21](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2014.32.21.png)

![截屏2022-10-18 20.30.09](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-10-18%2020.30.09.png)



| 对于有序文件：data file is ordered according to this search key |
| :----------------------------------------------------------- |
| **S1. Linear search** (brute force) ：依次检索文件中的**每个记录**;<br /> Test whether its attribute values satisfy the selection condition. |
| **S2. Binary search**：（有序）文件通过二分查找将搜索空间减少一半<br /> An example is <u>**σ SSN='123456789' (EMPLOYEE)**</u> if SSN is the ordering attribute for the employee file. |
| **S3**. **Using a primary index or hash key** to retrieve a single record：主索引(或散列键)的键属性的相等比较<br /> <u>**σ SSN='123456789' (EMPLOYEE)**</u>, we can use the primary index (or the hash key) to retrieve the record. |
| **S4**. **Using a primary index** to retrieve multiple records：当比较条件不只是相等，而是 >, ≥, <, or ≤ on a key field with a primary index <br />例如 <u>**σ DNUMBER>5 (DEPARTMENT)**</u> 中dnumber > 5，我们使用索引找到满足对应相等条件（dnumber = 5）的记录； 然后检索（有序）文件中的所有后续记录。 对于条件 dnumber < 5，检索所有前面的记录 |



| 其他文件：                                                   |
| :----------------------------------------------------------- |
| **S5**. **Using a clustering index** to retrieve multiple records：ordered according to this non key attribute，对具有 clustering index 的 non key attribute 上进行相等比较<br />例如 **<u>σ DNO=5 (EMPLOYEE)</u>** 中的dno = 5，我们使用 clustering index 来检索**所有满足选择条件的记录** |
| **S6.Using a secondary index or B+ tree**：根据此候选键或主键或其他键排序。 secondary index 和 B+ tree既可以用于关键字段，也可以用于非关键字段。<br />如果 indexing field 具有唯一值。这意味着这个索引字段是一个key field。<br />如果 the indexing field 是 non key field，则可以检索多条记录。此外，我们还可以在涉及>、>=、<或<=(即**RANGE QUERIES**)的条件下检索记录<br />**RANGE QUERY EXAMPLE:** 如果我想找到所有在第五部门工作的女员工，而且薪水大于30K。对于员工来说，salary 和 dno 是一个non key field，如果我有可用的二级索引或 B + 3在工资。<br />例如： **<u>σ DNO=5 AND SALARY>30000 AND SEX=F (EMPLOYEE)</u>** |
| **S7**. **Conjunctive selection**：说明有多个条件<br/>连接条件中任何单一**简单条件**所涉及的属性具有允许使用S2到S6方法之一的 **access path(index or hashing function)**。<br />使用该条件检索记录，然后检查每个检索到的记录是否满足连接选择中剩余的简单条件<br/>例如： **<u>σ DNO=5 AND SALARY>30000 AND SEX=F (EMPLOYEE)</u>** |
| **S8**. **Conjunctive selection using a composite index**：Composite Index is a index involved two or more attributes。连接条件(conjunctive condition) 中相等条件包含两个或两个以上的属性，组合字段上存在一个**复合索引 combined fields**<br/>如果您的搜索条件涉及相等条件中的多个属性检查它们是否是多个属性，它们是由综合索引在组合中共同定义的一部分，请使用它。<br/>例如，如果在 **<u>σ ESSN=123456789 AND PNO=10 (WORKS_ON)</u>** 的**works_on**文件的复合键(essn, pno)上创建了索引，我们可以直接使用索引。 |
| **S9**. **Conjunctive selection by intersection of record pointers**：它不适用于块指针。它假设每条记录都有一个指针直接指向记录，secondary indexes 可用于连接条件中相等比较条件中涉及的所有(或部分) fields，索引包括 record pointers (rather than block pointers) <br />每个索引可用于检索满足单个条件的**记录指针(record pointers)**。 <br />这些记录指针集合的**交集(intersection)**给出了满足 conjunctive condition 记录，因此我们可以使用这些指针直接检索这些记录。 <br />如果只有部分条件有 secondary indexes，则对检索到的每条记录进行进一步测试，判断是否满足其余条件 |

- 每当单个条件指定选择（例如 **<u>σ SSN='123456789' (EMPLOYEE)</u> / <u>σ DNUMBER>5 (DEPARTMENT)</u> /  <u>σ DNO=5 (EMPLOYEE)</u>**），我们只检查该条件所涉及的属性上是否存在 access path 
- 如果存在 access path ，则使用该访问路径对应的方法；否则，使用方法 S1 的 “brute force” 线性搜索方法
- Query optimization 主要用于 conjunctive select 条件，只要条件中涉及的多个属性具有 access path 
- 优化器应该选择以最有效的方式检索最少记录(fewest records)的 access path



关系数据库的执行效率问题：

![截屏2022-11-07 18.01.18](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2018.01.18.png)

![截屏2022-11-07 18.04.21](2411%20Database%20Systems.assets/%E6%88%AA%E5%B1%8F2022-11-07%2018.04.21.png)

如何使数据库查询的执行时间最短？

三个层面进行优化：

- **语义优化**：利用模型的语义及完整性规则，优化查询。
- **语法优化 --- 逻辑层优化**：利用语法结构，优化操作执行顺序：
- **执行优化 --- 物理层优化**：存取路经和执行算法的选择与执行次序优化：



尽可能早做选择运算
尽可能早做投影运算
改写成等价的效果更好的语句



#### General Transformation Rules

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.36.44.png" alt="截屏2022-11-28 13.36.44" style="zoom:80%;" />

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.38.20.png" alt="截屏2022-11-28 13.38.20" style="zoom:80%;" />

![截屏2022-11-28 13.41.05](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.41.05.png)

![截屏2022-11-28 13.42.55](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.42.55.png)

![截屏2022-11-28 13.45.06](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.45.06.png)

![截屏2022-11-28 13.47.55](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.47.55.png)

![截屏2022-11-28 13.49.23](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.49.23.png)

![截屏2022-11-28 13.50.09](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.50.09.png)

![截屏2022-11-28 13.51.07](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.51.07.png)



1. **Cascade of σ：**将任何具有联合条件的选择操作分解为一系列选择操作。这允许在将选择操作向下移动到树的不同分支时有更大的自由度。

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.07.04.png" alt="截屏2022-11-28 13.07.04" style="zoom:50%;" />

2. **Commutativity of σ :** The σ operation is commutative, i.e.,

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.07.53.png" alt="截屏2022-11-28 13.07.53" style="zoom:50%;" />

3. **Cascade of** **π** : In a cascade (sequence) of **π** operations, 除了最后一个之外的所有操作都可以忽略(为了优化，我们经常使用下述等式的相反方向)：

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.08.31.png" alt="截屏2022-11-28 13.08.31" style="zoom:50%;" />

4. **Commuting σ with π :** If the selection condition c involves only the attributes A1, ..., An in the projection list, the two operations can be commuted:

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.10.07.png" alt="截屏2022-11-28 13.10.07" style="zoom:50%;" />

![截屏2022-11-28 13.11.34](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.11.34.png)

![截屏2022-11-28 13.13.29](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.13.29.png)

![截屏2022-11-28 13.19.43](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.19.43.png)

![截屏2022-11-28 13.15.48](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.15.48.png)

![截屏2022-11-28 13.17.00](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.17.00.png)

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2013.17.41.png" alt="截屏2022-11-28 13.17.41" style="zoom: 45%;" />



#### Query Trees

![34](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/34.png)



### 8. <u>Transactions and Concurrency Control</u>

#### 8.1 Transactions

-  A set of operations that are used to perform some logical set of work (one or more access operations 一个/多个存取操作)
-  事务(Transactions) 是一个完整的业务逻辑不可再分：一批操作(一组 DML)

- **begin_transaction**：这标志着事务执行的开始。
- **end_transaction**：表示读写事务操作已经结束，标志着事务执行的结束限度。此时可能需要检查事务引入的更改是否可以永久应用于数据库，或者事务是否因为违反并发控制或其他原因而必须中止。
- **read** or **write(insert, update, delete)**: 这些指定对作为事务的一部分执行的数据库项的读取或写入操作。
- **commit_transaction**：清空事务性活动的日志文件，将数据全部彻底持久化到数据库表中。提交事务标志着，**事务的结束。并且是一种全部成功的结束。**
- **rollback** (or **abort**): 将之前所有的DML操作全部撤销，并且清空事务性活动的日志文件。回滚事务标志着，**事务的结束。并且是一种全部失败的结束。**
- SET AUTOCOMMIT:禁用或启用事务的自动提交模式

两个示例事务(银行帐户操作):

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221128140531217.png" alt="image-20221128140531217" style="zoom:50%;" />


说到底，说到本质上，**一个事务其实就是多条DML语句同时成功，或者同时失败**

开启事务： `START TRANSACTION 或 BEGIN TRANSACTION;` 提交事务： `COMMIT;` 回滚事务： `ROLLBACK;`

操作实例：

```sql
start transaction;

select * from account where name = '张三';
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';

commit;
```



##### 四大特性ACID

- **原子性(Atomicity)：**整个事务中的所有操作，必须作为一个单元全部完成(或全部取消)。它要么全部执行，要么根本不执行。
- **一致性(Consistency)：**数据库在事务前后保持一致。它指的是数据库的正确性。
- **隔离性(Isolation)：**此属性意味着多个事务可以并发发生，而不会导致数据库状态不一致。这些交易独立发生，不受任何外部干扰。在提交该事务中的特定更改之前，任何其他事务都无法看到/访问特定事务中发生的更改。
- **持久性(Durability)：**事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。在事务完成以后，该事务对数据库所作的更改将持久地保存在数据库之中，并不会被回滚。一旦事务更改了数据库并提交了更改，这些更改绝不能因为后续失败而丢失。



#### 8.2 Schedule:

简单来说就是：collection of transactions

它是多个事务的时间顺序执行顺序。It is chronological execution sequence of multiple transaction.

A schedule (or history) S of n transactions T1, T2, …, Tn：是是事务操作的排序，受制于约束。对于参与 S 的每个事务 Ti，Ti 在 S 中的操作必须出现在 与 Ti 中的顺序相同（但来自其他事务 Tj 的操作可以与 Ti 在 S 中的操作交错）。

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-13%2013.59.47.png" alt="截屏2022-12-13 13.59.47" style="zoom:50%;" />

- **Serial Schedule 串行调度**

  如果一个事务开始，那么其他事务将在正在进行的事务结束后开始

  缺点：系列时间表总是一致的，另一笔交易的等待时间很高

  优点：Serial Schedule 始终是可恢复的、级联的、严格的和一致的 (recoverable, cascades, strict and consistent)。串行调度总是给出正确的结果。

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2001.02.03.png" alt="截屏2022-12-21 01.02.03" style="zoom:67%;" />

- **Parallel Schedule 并行调度**

  一次执行多个事务，比如如果有三个事务 t1、t2 和 t3，那么在一段时间内 t1 将执行一组操作，在一段时间内 t2 将执行一组操作，依此类推。

  缺点：可能一致也可能不一致，并不总是可恢复的、级联的、严格的和一致的。

  优点：另一笔交易的等待时间很低

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2001.02.21.png" alt="截屏2022-12-21 01.02.21" style="zoom:67%;" />



**Interleaved/Serial processing(交错处理)**:

Concurrent execution of processes is interleaved in a single CPU.(进程的并发执行在单个 CPU 中交错进行)

**Parallel processing(并行处理)**:

Processes are concurrently executed in multiple CPUs. (进程在多个 CPU 中并发执行)

![image-20221213140853622](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221213140853622.png)



#### 8.3 数据库故障恢复

事务是一个原子工作单元，它要么全部完成，要么根本没有完成。出于恢复目的，系统需要跟踪事务何时开始、终止以及提交或中止。

- **A computer failure (system crash):** 事务执行期间计算机系统出现硬件或软件错误。如果硬件崩溃，计算机内存中的内容可能会丢失。

- **A transaction or system error:** 事务中的某些操作可能会导致它失败，例如整数溢出或被零除。事务失败也可能由于错误的参数值或逻辑编程错误而发生。此外，用户可以在交易执行期间中断交易。

- **Local errors or exception conditions detected by the transaction:** 某些情况需要取消事务。例如，可能找不到事务数据。诸如银行数据库中账户余额不足之类的情况可能会导致事务（例如从该账户中提取资金）被取消。事务中的程序中止导致交易失败。
- **Concurrency control enforcement:** 并发控制方法可能会决定中止事务，稍后重新启动，因为它违反了可串行性，或者因为多个事务处于死锁状态。
- **Disk failure:** 某些磁盘块可能会因为读取或写入故障或磁盘读写头崩溃而丢失数据。这可能发生在读取或写入操作期间
- **Physical problems:** 电源或空调故障、火灾、盗窃、错误地覆盖磁盘或磁带以及操作员安装错误的磁带

**State Transition Diagram: **

![image-20221128154729020](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221128154729020.png)





**Recovery techniques use the following operators:**

恢复是什么：把DB由当前不正确状态恢复到已知为正确的某一状态。

事务故障可通过**重做事务(Redo)**和**撤消事务(Undo)**来恢复。Redo 可保证已提交事务的持久性，而 undo 则消除未提交事务的影响

数据库通常借助日志来实现事务，常见的有**undo log、redo log，undo/redo log**都能保证事务特性，这里主要是原子性和持久性，即事务相关的操作，要么全做，要么不做，并且修改的数据能得到持久化。

- **undo log**是把所有没有COMMIT的事务回滚到事务开始前的状态，系统崩溃时，可能有些事务还没有COMMIT，在系统恢复时，这些没有COMMIT的事务就需要借助undo log来进行回滚。也就是说：**针对所有未COMMIT的日志，根据undo log来进行回滚。** 
- **redo log**是指在回放日志的时候把已经COMMIT的事务重做一遍，对于没有commit的事务按照abort处理，不进行任何操作。也就是说：**针对所有已COMMIT的日志，根据redo log来进行重做。** 
- **undo/redo log** 更灵活



运行日志(**System Log**) 跟踪所有影响数据库项目值的事务操作。

- 可能需要此信息以允许从事务失败中恢复。
- 日志保存在磁盘上，因此它不受除磁盘或灾难性故障之外的任何类型故障的影响。
- 此外，日志会定期备份到存档存储（磁带）以防止此类灾难性故障。



##### 利用Undo型日志进行故障恢复

- 故障时未提交的事务，撤销(Undo)

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2016.09.42.png" alt="截屏2022-11-28 16.09.42" style="zoom:70%;" />

![截屏2022-11-28 16.09.20](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2016.09.20.png)

如果我们想要恢复的话，就可以利用这个日志来恢复，首先从日志的首部尾部开始往前撤销未完成事物的所有修改。

![Screenshot 2023-01-19 at 16.40.21](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-19%20at%2016.40.21.png)

![Screenshot 2023-01-19 at 16.41.08](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-19%20at%2016.41.08.png)

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.39.47.png" alt="Screenshot 2023-01-18 at 22.39.47" style="zoom:38%;" />

![Screenshot 2023-01-18 at 22.37.57](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.37.57.png)

![Screenshot 2023-01-19 at 18.15.27](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-19%20at%2018.15.27.png)



##### 利用Redo型日志进行故障恢复

- 故障时已提交的事务，重做(Redo)

![Screenshot 2023-01-19 at 17.11.04](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-19%20at%2017.11.04.png)

![截屏2022-11-28 21.01.19](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-11-28%2021.01.19.png)

![Screenshot 2023-01-18 at 22.40.58](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.40.58.png)

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.43.27.png" alt="Screenshot 2023-01-18 at 22.43.27" style="zoom:40%;" />

![Screenshot 2023-01-18 at 22.41.22](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.41.22.png)



##### Undo/Redo结合型日志及其故障恢复

发生系统故障时，我们可以使用日志进行恢复：

- 故障时已提交的事务，重做(Redo)
- 故障时未提交的事务，撤销(Undo)

![Screenshot 2023-01-19 at 17.12.23](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-19%20at%2017.12.23.png)

![Screenshot 2023-01-19 at 17.14.24](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-19%20at%2017.14.24.png)

![Screenshot 2023-01-18 at 22.47.48](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.47.48.png)

![Screenshot 2023-01-18 at 22.48.35](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.48.35.png)

![Screenshot 2023-01-18 at 22.49.16](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.49.16.png)

![Screenshot 2023-01-18 at 22.49.43](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-18%20at%2022.49.43.png)

![Screenshot 2023-01-19 at 17.19.25](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Screenshot%202023-01-19%20at%2017.19.25.png)

例：

当系统从崩溃中恢复时，必须按照什么顺序 undone 和 redone 事务?

- 首先，undo 所有在崩溃之前开始但尚未提交的事务(there is a **start** record but no **commit** record in the log)。这些事务 undo, 以相反的时间顺序 **reverse temporal order**(从最新到最旧);
- 接下来，redo 所有在崩溃之前提交的事务(there is a **commit** record in the log)。这些事务 redo, 按照时间顺序 **in temporal order** (从最老的到最新的)。



#### 8.4 Schedules Classified on Recoverability

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2023.36.01.png" alt="截屏2022-12-21 23.36.01" style="zoom:67%;" />

在transactions中，当我们讨论 schedules 的时候我们会将其分为 serializability 和 recoverability

如果他不是可串行化的，那我们就要考虑一下，他是否是可恢复的

##### Irrecoverable/nonrecoverable Schedule

- 如果 transaction 在它从中读取数据的事务提交之前提交，则称调度是不可恢复的

$$
(T 
1
​
 →T 
2
​
  \ Reads)⟹(T 
2
​
  \ Commits→T 
1
​
  \ Commits)
$$

Example: 

![Irrecoverable-Schedule-Example](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Irrecoverable-Schedule-Example.png)

- T2 执行脏读操作。
- T2 在 T1 之前提交。
- T1 稍后失败并回滚。
- T2 读取的值现在不正确。
- T2 无法恢复，因为它已经提交。



##### Recoverable schedule:

- No committed transaction needs to be rolled back. 不需要回滚的已提交的事务。


- A schedule S 是 **recoverable**，如果调度(schedule)中存在的每个事务(transaction)仅在它从中读取值的所有事务都完全执行/提交后才提交，则调度是可恢复的调度
- 如果 T1 和 T2 是两个transactions，T2 从 T1 读取数据，然后 T2 在 T1 commit 之后 commit，就是**“Recoverable” schedule**:

$$
(T1→T2\ Reads)⟹(T1\ Commits→T2\ Commits)
$$

Example: 

![Recoverable-Schedule-Example](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Recoverable-Schedule-Example.png)

- T2 执行脏读操作。
- T2 的提交操作延迟到 T1 提交或回滚。
- T1 稍后提交。
- 现在允许 T2 提交。
- 万一 T1 失败，T2 有机会通过回滚来恢复。

![截屏2022-12-19 22.14.33](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-19%2022.14.33.png)

可恢复的时间表可能是以下任何一种：

![Types-of-Recoverable-Schedules-1](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Types-of-Recoverable-Schedules-1.png)



###### Cascaded schedule

- 如果一个 transaction 发生错误(abort)，那么他就会自动使其他 transactions rollback (Cascaded rollback)。A single failure leads to a series of rollback
- All uncommitted transactions that read an item from a failed transaction must be rolled back. 所有从失败事务中读取项的未提交事务都必须回滚。
- **disadvantage: CPU utilization will be degrade/waste**

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2000.41.50.png" alt="截屏2022-12-21 00.41.50" style="zoom:67%;" />

在这里，事务 T2 依赖于事务 T1。事务 T3 依赖于事务 T2。事务 T4 依赖于事务 T3。

在这个时间表中，

- 事务 T1 的失败导致事务 T2 回滚。
- 事务 T2 的回滚导致事务 T3 回滚。
- 事务 T3 的回滚导致事务 T4 回滚。

这种回滚称为 **Cascaded rollback**

如果有四个学生，假设4人都在进行一些考试，T1 很聪明，现在 T1 write something，T2 read it, T3 also read it, T4 also read it. 他们只是在盲目地抄袭。但是 T1经过一段时间的思考，发现计算出了错误的答案，于是擦掉了答案。这个时候T1 和 T2、T3、T4 说擦掉重新写，T2、T3 和 T4说没问题，于是其他人都擦掉了答案。但是这就意味着他们做的所有努力都失败了，浪费了性能。

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-19%2022.21.09.png" alt="截屏2022-12-19 22.21.09" style="zoom:50%;" />



###### Cascadeless schedule

- Cascadeless schedule **只有前一个事务commit后**，后面的事务才能 **Read** 前一个事务的数据，否则就只能进行 **Write**
- Cascadeless schedules 比 cascading schedules 更受欢迎，因为当需要 rollback 时，CPU 周期的损失最小化。

![截屏2022-12-22 00.36.53](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-22%2000.36.53.png)

这里，X的更新值仅在事务T1提交后才由事务T2读取。因此， Schedule 是Cascadeless schedule。

- However, it **allows uncommitted write operations.**

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2001.44.45.png" alt="截屏2022-12-21 01.44.45" style="zoom:100%;" />

###### Strict Schedules

- 如果在一个时间表中，*直到最后一个写入它的事务被提交或中止*，一个*事务既不允许读取也不允许写入*数据项，那么这样的时间表称为严格时间表。假设我们有两个交易Ta和Tb。事务Ta的写操作先于事务Tb的读或写操作，因此事务 Ta 的提交或中止操作也应该先于 Tb 的读或写操作。严格的计划只允许提交的读写操作。该附表实施的限制比级联调度。

- 在此调度中，事务既不能读也不能写项目X，直到写入X的最后一个事务已提交。

- Strict schedule 只允许 committed read and write operations.
- 显然，Strict schedule 比 cascadeless schedule 实施更多限制。

![截屏2022-12-22 00.39.13](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-22%2000.39.13.png)



#### Summery & Example

- Strict schedules are more strict than cascadeless schedules.
- All strict schedules are cascadeless schedules.
- All cascadeless schedules are not strict schedules.

![Cascadeless-and-Strict-Schedule](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Cascadeless-and-Strict-Schedule.png)

##### Example: 

**Schedule**: w1(X,5); w2(X,8); a1 (T1 aborts);

- Initially, X=9
- T1 writes a value 5 for X (keeping 9 as “before image”)
- T2 writes a value 8 for X (keeping 5 as “before image”)
- Then T1 aborts

**If it is Cascadeless**: 如果系统按照T1保存的“之前的图像”恢复，那么X现在的值就是9，也就是说，T2的效果就没有了

**If it is Strict**: w2 can not happen until T1 commits 



#### 8.5 Serializability in DBMS

- Serial Schedule 具有较少的资源利用率和低吞吐量。为了改进它，两个或多个事务同时运行。但是事务并发可能会导致数据库不一致。为避免这种情况，我们需要检查这些 Parallel schedule 是否可序列化 (serializable)。
- 如果一个 Parallel schedule 可以转换为 Serial Schedule，那么他就是 serializable schedule。
- Serial Schedule 不能 serializable，因为他已经是 serial 的了。所以我们总是让 Parallel schedule 去 serializable。

但是 Being serializable is not the same as being serial

Being serializable implies that the schedule is a **correct schedule.**

它将使数据库保持一致状态。

这种交错是合适的，它将导致一种状态，就好像事务是串行执行的，但由于并发执行，它将获得效率。

![image-20221221154912456](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221221154912456.png)

在此时间表中，Write(A)/Read(A) 和 Write(B)/Read(B) 称为冲突操作。这是因为上述所有条件都适用于他们。因此，通过交换（非冲突）第二对“A”数据项的读/写操作和第一对“ B”数据项的读/写操作，这个非串行调度可以转换为可串行化日程。因此，*它是冲突可序列化*的。

<u>我们要做的就是交换 non-conflict</u>

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2001.24.08.png" alt="截屏2022-12-21 01.24.08" style="zoom:67%;" />

可以主要使用两种类型来验证任何 non-serial/parallel schedule 的可串行性：

![Equivalence-of-Schedules](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Equivalence-of-Schedules.png)

##### Result Equivalence

- 如果任意两个调度执行后产生相同的结果，则称它们为结果等价调度。
- 这种等价关系被认为是最不重要的。
- 这是因为某些计划可能对某些值集产生相同的结果，而对其他一些值集产生不同的结果。

下面哪些 schedules 是 result equivalent?

![Equivalence-of-Schedules-Problem-01](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Equivalence-of-Schedules-Problem-01.png)

要检查给定的时间表是否等价于结果，

- 我们将考虑 X 和 Y 的一些任意值。
- 然后，我们将比较每个计划产生的结果。
- 那些产生相同结果的计划将是结果等价的。

比如让 X = 2 和 Y = 5。在替换这些值时，每个时间表产生的结果是：

**S1 的结果：** X = 21 和 Y = 10

**S2：** X = 21 和 Y = 10

**S3：** X = 11 和 Y = 10

- 显然，时间表 S1 和 S2 产生的结果是相同的。因此，我们得出结论，S1 and S2 are result equivalent schedules.



##### Conflict Equivalence

- Two schedules are said to be conflict equivalent if the order of any two conflicting operations (RW,WW) is the same in both schedules. 如果**<u>两个调度中的任何两个冲突操作 (RW,WW) 的顺序相同</u>**，则称这两个调度是冲突等价的。

**冲突操作：**如果所有条件都满足，则称两个操作是冲突的： 

- 它们属于 **不同的事务**
- 他们对 **同一个数据项进行操作**
- 其中 **至少有一个是写操作**

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2002.13.53.png" alt="截屏2022-12-21 02.13.53" style="zoom:60%;" />

以下两个 schedules 是否是 conflict equivalent?

![Equivalence-of-Schedules-Problem-02](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/Equivalence-of-Schedules-Problem-02.png)

要检查给定的时间表是否冲突等效，

- 我们将写下它们的 **order of pairs of conflicting operations.**
- 然后，我们将比较 the order of both the schedules.
- 如果发现两个调度具有相同的顺序，那么它们将是 conflict equivalent.

**For schedule S1-** The required order is-

- R1(A) , W2(A)
- W1(A) , R2(A)
- W1(A) , W2(A)

**For schedule S2-**The required order is-

- R1(A) , W2(A)
- W1(A) , R2(A)
- W1(A) , W2(A) 

Clearly, both the given schedules have the same order. Thus, we conclude that S1 and S2 are conflict equivalent schedules.



##### Conflict serializable:

A schedule **S** is said to be conflict serializable if it is conflict equivalent to some serial schedule **S’**.

- Looks at only read_Item (X) and write_Item (X) operations
- Constructs a ***precedence graph* (also called *serialization graph*)** -- a graph with directed edges 
- An edge is created from Ti to Tj if one of the operations in Ti appears before a conflicting operation in Tj
- The schedule is serializable **if and only if** the precedence graph has no cycles. 

![35](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/35.png)

(a) Precedence graph for serial schedule A.
(b) Precedence graph for serial schedule B.
(c) Precedence graph for schedule C ( **not serializable** ).
(d) Precedence graph for schedule D ( **serializable, equivalent to schedule A** ).

------

##### precedence graphs

每个 transactions 就是一个 Node 每个 conflict pair 就是一条边

![截屏2022-12-21 18.01.07](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2018.01.07.png)

如果画完 precedence graphs 发现 No Loop/Cycle 那么他就是 conflict serializability.

![image-20221221155204489](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221221155204489.png)

Constructing the precedence graphs for schedules A and D from Figure 17.5 to test for conflict serializability.

(a) Precedence graph for serial schedule A.

(b) Precedence graph for serial schedule B.

(c) Precedence graph for schedule C (not serializable). 

(d) Precedence graph for schedule D (serializable, equivalent to schedule A).





**Serializable schedule (No Loop/Cycle):**

由于图中没有循环，它是一个冲突的可序列化调度，也是一个串行调度。由于它是一个串行调度，我们也可以检测事务的顺序。

**事务的顺序：** t1 将首先执行，因为 T1 上没有传入边。t3 将执行第二个，因为它仅取决于 T1。t2 将最后执行，因为它同时依赖于 T1 和 T3。

因此，其等效串行调度的顺序是：t1 --> t3 --> t2

![截屏2022-12-21 21.39.33](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2021.39.33.png)

**Non-serializable schedule (Loop/Cycle): **

**不可序列化的时间表**。T1 的 R(x) 与 T3 的 W(x) 冲突，因此存在从 T1 到 T3 的有向边。T1 的 R(y) 与 T2 的 W(y) 冲突，因此存在从 T1 到 T2 的有向边。T3 的 W(y\x) 与 T1 的 W(x) 冲突，因此存在从 T3 到 T 的有向边。类似地，我们将为每个冲突对创建边。现在，随着循环的形成，事务无法序列化。

![截屏2022-12-21 21.37.29](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2021.37.29.png)

NOTE: If a schedule is conflicting serializable, then it is surely a consistent schedule. On the other hand, a non-conflicting serializable schedule may or may not be serial. To further check its serial behavior, we use the concept of View Serializability.



**Another example of serializability Testing** 

![image-20221221155303158](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221221155303158.png)

![image-20221221155307354](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221221155307354.png)

![image-20221221155324694](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221221155324694.png)

![image-20221221155330620](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221221155330620.png)

##### Exercises

![40](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/40.png)

![截屏2022-12-21 22.06.27](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-21%2022.06.27.png)



#### 8.6 Concurrency Control

Purposes of **Concurrency Control**

- To enforce **isolation** (through mutual exclusion) among conflicting transactions. 
- 保持数据库的一致性 (consistency)
- 解决 read-write (RW) 和 write-write (WW) conflicts.

**Example**: 在并发执行环境中，如果 T1 和 T2 在一个数据项 A 上发生冲突，那么现有的并发控制将决定 T1 或 T2 是否应该获取 A 以及另一个事务是回滚还是等待。

**Isolation level** \<isolation> 

 where \<isolation> can be:

1. READ UNCOMMITTED 

2. READ COMMITTED

3. REPEATABLE READ 

4. SERIALIZABLE 

-  The default is SERIALIZABLE. 
- SERIALIZABLE: the interleaved execution of transactions will stick to our notion of serializability. 
- However, if any transaction executes at a lower level, then serializability may be violated. 



| 问题                        | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| **Nonrepeatable** **Read**: | Allowing another transaction to write a new value between multiple reads of one transaction. <br />假设 T1 读取了 Smith 的员工 salary。 接下来，T2 更新 Smith 的 salary。 如果 T1 再次读取 Smith 的 salary，那么它将看到 Smith 的 salary 不同值。 |
| **Dirty Read**              | Reading a value that was written by a transaction which failed.<br />事务 A 读取数据 DT 的值为 1000 并将其修改为 1500 并存储在临时缓冲区中。事务 B 读取数据 DT 为 1500 并提交它，并且 DT 的值在数据库 DB 中永久更改为 1500。然后事务A出现服务器错误，想要回滚到初始值1000，就出现了脏读问题。 |
| **Phantoms(幻像):**         | New rows being read using the same read with a condition. <br />事务B最初读取DT的值为1000，事务A从数据库DB中删除数据DT，然后事务B再次读取该值，发现错误说数据库DB中不存在数据DT。 |

并发事务隔离级别：

| Isolation Level     | Dirty Read (脏读) | Nonrepeatable Read (不可重复读) | Phantoms (幻读) |
| ------------------- | ----------------- | ------------------------------- | --------------- |
| Read uncommitted    | √                 | √                               | √               |
| Read committed      | ×                 | √                               | √               |
| Repeatable Read     | ×                 | ×                               | √               |
| Serializable (默认) | ×                 | ×                               | ×               |

- √表示在当前隔离级别下该问题会出现
- Serializable 性能最低；Read uncommitted 性能最高，数据安全性最差

查看事务隔离级别： `SELECT @@TRANSACTION_ISOLATION;` 设置事务隔离级别： `SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE };` SESSION 是会话级别，表示只针对当前会话有效，GLOBAL 表示对所有会话有效



#### 8.7 Two-Phase Locking Techniques

- Schedule 是 concurrent，我们可以使用 Lock/Unlock 让他变得 recoverable 和 serializable，以保证 Schedule 的正确性

- **两段锁协议 **指 <u>所有事务必须分两个阶段对数据项加锁和解锁</u>
- 在对任何数据进行读、写操作之前，事务首先要获得对该数据的封锁
- 在释放一个封锁之后，事务不再申请和获得任何其他封锁



- **Locking** is an operation which secures permission 是一种**保护权限的操作**，**禁止 **事务执行 **Read / Write** 操作。

Example: Lock (X). Data item X is **locked** on behalf of the requesting transaction.

- **Unlocking** is an operation which removes these permissions from the data item 从数据项中删除这些权限(lock)的操作。

Example: Unlock (X): Data item X is made **available** to all other transactions.

- **Lock and Unlock are <u>Atomic</u> operations**



##### Shared mode: <u>Read lock (X)</u>

- **<u>Read Only</u>**，其它事务 **<u>只能再对A加S锁</u>**，而不能加X锁，直到T释放A上的S锁

##### Exclusive mode: <u>Write lock (X)</u>

- <u>**Read and Write**</u>，其它任何事务都 **<u>不能再对A加任何类型的锁</u>**，直到T释放A上的锁

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-23%2000.06.49.png" alt="截屏2022-12-23 00.06.49" style="zoom:40%;" />

**Lock Manager:** Managing locks on data items.

**Lock table:** Lock manager uses it to store the identify of transaction locking a data item, the data item, lock mode and pointer to the next data item locked. One simple way to implement a lock table is through linked list.

![image-20221222001827626](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/image-20221222001827626.png)



A transaction is **well-formed** if:

- 它必须在读取或写入之前锁定数据项。
- 它不得锁定已锁定的数据项，也不得尝试解锁空闲数据项。



##### Lock conversion

**Lock upgrade: read lock -> write lock**

if Ti has a read-lock (X) and Tj has no read-lock (X) (i ¹ j) then convert read-lock (X) to write-lock (X)

else force Ti to wait until Tj unlocks X

**Lock downgrade: write lock -> read lock**

if Ti has a write-lock (X)  *(\*no transaction can have any lock on X\*)* convert write-lock (X) to read-lock (X)



##### Locking (Growing) Phase: 扩展阶段

事务可以申请获得任何数据项上的任何类型的锁，但是不能释放任何锁

##### Unlocking (Shrinking) Phase: 收缩阶段

事务可以释放任何数据项上的任何类型的锁，但是不能再申请任何锁

![截屏2022-12-23 00.44.55](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-23%2000.44.55.png)

![截屏2022-12-23 00.45.39](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-23%2000.45.39.png)



Let’s see a transaction implementing 2-PL. 

![截屏2022-12-25 23.43.37](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-25%2023.43.37.png)

This is just a skeleton transaction that shows how unlocking and locking work with 2-PL. Note for: 

**Transaction T1**:

- The growing Phase is from steps 1-3.
- The shrinking Phase is from steps 5-7.
- Lock Point at 3 **(Lock Point is 增长阶段结束的点)**

**Transaction T2**:

- The growing Phase is from steps 2-6.
- The shrinking Phase is from steps 8-9.
- Lock Point at 6



##### Three Locking Algorithm to Generate Good Schedule

2-PL 确保了可串行化，但是 2-PL 仍然存在一些缺点，它仍然会遇到问题(eg. Cascading Aborts, and Deadlocks...)！如果我们没有很好地安排它们，于是我们引入了 Three Locking Algorithm：

- **Basic**: Transaction locks data items incrementally. This may cause deadlock… 你可以一个个添加 lock, 但这可能会导致死锁…

- **Conservative**: Prevents deadlock by locking all desired data items before the transaction begins execution. 通过在事务开始执行之前锁定所有所需的数据项来防止死锁。

eg. 如果你有一个事务需要从数据库访问数据项ABC。然后，你需要首先获得所有ABC上的所有的锁，然后才能执行其余操作。
$$
XLock(A)\ XLock(B)\ XLock(C)\ ...\ Unlock(A)\ Unlock(B)\ Unlock(C)
$$

|        | **T1**          | **T2**          |
| :----- | :-------------- | :-------------- |
| **1**  | **Lock-X(A)**   |                 |
| **2**  | **Lock-X(B)**   |                 |
| **3**  | Read(A)         |                 |
| **4**  | *operation on A |                 |
| **5**  | Write(A)        |                 |
| **6**  | **Unlock(A)**   |                 |
| **7**  |                 | **Lock-X(A)**   |
| **8**  |                 | Read(A)         |
| **9**  |                 | *operation on A |
| **10** |                 | Write(A)        |
| **11** |                 | **Unlock(A)**   |
| **12** | Read(B)         |                 |
| **13** | *operation on B |                 |
| **14** | Write(B)        |                 |
| **15** | **Unlock(B)**   |                 |
| **16** | **Commit**      |                 |
| **17** |                 | **Commit**      |

- **Strict**: Basic算法的更严格版本，**<u>unlocking is performed after a transaction terminates (commits or aborts and rolled-back)。</u>**这是最常用的 two-phase locking algorithm.![截屏2022-12-26 00.00.58](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-26%2000.00.58.png)



##### Deadlock

- 当两个事务互相之间需要等待对方释放资源时，但没有一个事务愿意放弃分配给它们的 CPU 和内存资源，如果系统不进行干预则会一直等待下去，也就是进入了死锁(deadlock）状态。死锁使整个系统停止，因为没有任务完成并且永远处于等待状态。
- **两个或多个事务无限期地等待彼此放弃锁**

例：T1申请封锁R1 (可封锁)，T2封锁R2 (可封锁)，T1申请封锁R2 (等待T2释放R2锁，T1进入等待)，T2申请封锁R1 (等待T1释放R1锁，T2进入等待)。在这里 T1 和 T2 都 follow two-phase policy but they are deadlock!

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-23%2017.28.49.png" alt="截屏2022-12-23 17.28.49" style="zoom:67%;" />

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-23%2016.53.28.png" alt="截屏2022-12-23 16.53.28" style="zoom:50%;" />



##### Deadlock Prevention

事务在开始执行之前锁定它引用的所有数据项。这种锁定方式可防止死锁，因为事务从不等待数据项。Conservative 2PL 使用这种方法，因此它没有死锁。

**Transaction timestamps时间戳，用TS(T)表示**：

- If T1 starts before T2, then TS(T1) < TS(T2)，We say T1 is “older” than T2
- 每个事务都有关联的读写时间戳，分别由最近的读写操作执行的时间组成。

下图中，TS(T1) < TS(T2) < TS(T3)

![截屏2022-12-23 23.24.08](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-23%2023.24.08.png)

- **Wait-Die Scheme 爱幼:** 

  If TS(Ti) < TS(Tj), then Ti is allowed to wait; 

  Otherwise **abort Ti (ie, Ti dies) and restart Ti later with the same timestamp.**

  当一个事务请求一个已经被其他事务锁定的资源时，DBMS检查两个事务的时间戳，并**让旧的事务等待，直到该资源可用。**

  **一个旧的事务被允许等待一个年轻的事务，**

  请求较老事务持有的项的较年轻事务将中止并重新启动

- **Wound-wait Scheme 尊老:** 

  If TS(Ti) < TS(Tj), then abort Tj (ie, Ti wounds Tj) and restart it later with the same timestamp;

  Otherwise Ti is allowed to wait.

  当一个较老的事务需要一个已经被较年轻的事务(稍后启动的事务)锁定的资源时，较年轻的事务被迫终止/停止其处理，并释放锁定的资源供较老的事务自己执行。较年轻的事务现在以一分钟的延迟重新启动，但时间戳保持不变。如果一个较年轻的事务请求由较老的事务持有的资源，则较年轻的事务将等待较老的事务释放资源。

  **一个年轻的事务被允许等待一个老的事务，**

  一个较老的事务请求一个较年轻的事务持有的项，通过 **中止(aborting)** 它 **抢占(preempts)** 较年轻的事务。

<u>但是！这两种方案都可能导致一些事务Tj被中止并**不必要地重新启动，即使Tj可能永远不会真正导致死锁**</u>



**A more aggressive approach…**

允许死锁发生。The Scheduler maintains 一个 **检测周期(detecting cycle)** 的 **wait-for-graph (等待图) **。如果存在一个循环，then one transaction involved in the cycle is selected (victim) and rolled-back (则选择一个受害者，并回滚)

The **wait-for-graph** is created using the **lock table**. As soon as a transaction is blocked, it is added to the graph. 

**wait-for-graph (等待图) **是检测死锁情况的方法之一。适用于较小的数据库。在此方法中，基于事务及其对资源的锁定绘制图形。

- When a chain like: T1 waits for T2, T2 waits for T1, occurs, a cycle is formed!
- One of the transactions will be chosen to abort.
- 如果创建的图形具有闭环或循环，则存在死锁。 针对上述场景，Wait-For图绘制如下 

<img src="../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-26%2000.12.50.png" alt="截屏2022-12-26 00.12.50" style="zoom:63%;" />

![截屏2022-12-23 23.39.19](../previous%20and%20others%27%20file%20/Typora%E5%9B%BE%E7%89%87/%E6%88%AA%E5%B1%8F2022-12-23%2023.39.19.png)

**Following table list the differences between Wait – Die and Wound -Wait scheme prevention schemes :**

| Wait–Die                                                     | Wound-Wait                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| It is based on a non-preemptive technique.                   | It is based on a preemptive technique.                       |
| In this, older transactions must wait for the younger one to release its data items. | In this, older transactions never wait for younger transactions. |
| The number of aborts and rollback is higher in these techniques. | In this, the number of aborts and rollback is lesser.        |

当检测算法确定发生死锁时，系统必须从死锁中恢复，最常见的解决方案是**回滚一个或多个事务以打破死锁**



Three issues need to be addressed:

- **Selecting a victim 选择受害者**，We should roll back those transactions which will incur the **"minimum cost"**

  事务已经计算了多长时间，在完成指定的任务之前，事务还要计算多长时间

  事务使用了多少数据项

  要完成事务还需要多少数据项

  回滚将涉及多少事务



- **Rollback 回滚**，一旦我们决定某个特定的事务必须回滚，我们就必须确定这个事务应该回滚到什么程度(how far this transaction should be rolled back)

  最**简单**的解决方案是**完全回滚**: **abort the transaction and then restart it**

  但是，更有效的方法是**将事务回滚到 打破死锁(break the deadlock) 所必需的程度**

  这种方法要求系统保留所有正在运行的事务状态的**附加信息**……



- **Starvation 饥饿**，饥饿发生在特定事务 **持续(consistently) waits 或 restarts**，并且永远没有机会进一步进行时。

  在死锁解决中，同一事务可能始终被选为受害者并回滚。这种限制在所有基于优先级的调度机制中都是固有的。

eg. 假设数据库中有 3 个事务，即 T1、T2 和 T3，它们试图获取数据项 "I"上的锁。现在，假设调度程序将锁授予 T1（可能由于某些优先级），而其他两个事务正在等待锁。T1的执行一结束，另一个事务T4也过来了，请求对 数据项 I 加锁。现在，这一次调度器给T4加了锁，T2、T3又要等待了。这样，如果新的事务一直在请求锁，T2 和 T3 可能要无限期地等待，从而导致**Starvation**。 

**饥饿的解决方案:**

1. **Increasing Priority** **增加优先级** 
   饥饿发生在事务必须无限期等待时，在这种情况下，我们可以增加该特定事务的优先级。但此解决方案的缺点是，可能会发生其他事务可能不得不等待更长时间，直到最高优先级事务到来并继续进行。
2. **Modification in Victim Selection algorithm 受害者选择算法的修改**
   如果交易是重复选择的受害者，则可以通过降低其相对于其他交易的优先级来修改算法。
3. **First Come First Serve approach (先到先得)** 
   一种公平的调度方法，即可以采用FCFS，其中事务可以按顺序获取一个项目的锁，其中请求了锁。
4. **Wait-die 和 wound wait 方案** 
   这些方案使用事务的时间戳排序机制。 





