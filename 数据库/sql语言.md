# 数据库

### 数据类型：

（1）数值型 （2）字符型（3）日期型（4）位串型

### 常量：

（1）数值型常量（2）字符型常量（3）日期型常量

### 运算符与表达式：

（1）比较运算符：=、<、<=、>、>=、<>（或者!=）

（2）逻辑运算符：AND、OR和NOT

（3）算术运算符：+、-、*和/

### 谓词：

(1)between....and......(2)not between......and....

(3)in                             (4)not in

### 字符匹配：

like和not like:

rname like '李%':   %代表任意长度（可以为0）的字符串

rname like '李_':     _代表任意单个字符串

### 用于判断是否为空：

is null和is not null: rspecialty is null

### 运算符的优先级：

| 级别 | 运算符                             |
| ---- | ---------------------------------- |
| 1    | *（乘）、/（除）                   |
| 2    | +（正）、-（负）、+（加）、-（减） |
| 3    | =、>、<、>=、<=、<>                |
| 4    | NOT                                |
| 5    | AND                                |
| 6    | OR、BETWEEN、IN、LIKE、ALL、ANY    |
| 7    | =（赋值）                          |



## 模式定义：

创建模式：

```sql
CREATE SCHEMA  模式名 AUTHORIZATION 用户名
```

为用户li创建一个read_boorow模式：

```sql
CREATE SCHEMA AUTHORIZATION LI;
```

或者

```sql
CREATE SCHEMA AUTHORIZATION LI;
```

没有指定模式名，则其用户名即为隐含的模式名

**在模式中，创建数据库对象：**

为用户LI创建一个图书借阅模式reader_borrow，并且在该模式下定义读者数据表reader

```sql
CREATE SCHEMA reader_borrow AUTHORIZATION LI

CREATE TABLE reader(

 rno CHAR(5) PRIMARY KEY,

 rname CHAR(20),rgender CHAR(2),

 rage INT,

 rspecialty CHAR(20)

);
```

**在数据库对象前限定模式名**

在图书借阅模式reader_borrow下定义读者数据表reader

```sql
CREATE TABLE reader_borrow.reader(

 rno CHAR(5) PRIMARY KEY,

 rname CHAR(20),

 rgender CHAR(2),

 rage INT,

 rspecialty CHAR(20)

); 
```

**使用SET SCHEMA语句指定当前模式**

```sql
SET SCHEMA 模式名
```

其中，模式名即为当前模式。

例如，

```sql
 SET SCHEMA reader_borrow;

指定当前模式后，就可以直接使用

 CREATE TABLE reader(

 ……

);
```

**在数据库对象前限定模式名**

```sql
DROP SCHEMA 模式名 CASCADE|RESTRICT
```

CASCADE（级联）表示在删除模式的同时把该模式中所有的数据库对象一并删除；

RESTRICT（限制）表示在删除模式时，如果该模式中已经定义了数据库对象，在拒绝删除该模式。如果在该模式中没有任何数据库对象，则可以删除该模式。

eg:删除图书借阅模式reader_borrow

```sql
DROP SCHEMA reader_borrow CASCADE; 
```



### 基本表定义：

```sql
CRTEATE TABLE 基本表名 {

​		属性列名  数据类型 [列级完整性约束]

}
```



### 实现完整性约束的方法：

#### （1）列级完整性约束

针对基本表中的单个属性列设置限定条件，只能应用在一个属性列上

```sql
CREATE TABLE reader(

 rno CHAR(5) PRIMARY KEY, 

 rname CHAR(20), 

 rgender CHAR(2), 

 rage INT, 

 rspecialty CHAR(20) 

); 
```



#### （2）表级完整性约束：

针对表中多个列进行约束

```sql
CREATE TABLE borrow(

 rno CHAR(5), 

 bisbn CHAR(11), 

 startdate DATE,  

 enddate DATE, 

 fine NUMERIC(7,2) ,

 PRIMARY KEY(rno,bisbn,startdate)

);
```

**完整性约束命名子句**

使用CONSTRAINT定义约束，并命名。

   

```sql
CONSTRAINT 完整性约束条件名 [PRIMARY KEY短语| FOREIGN KEY短语| CHECK短语]
```

```sql
CREATE TABLE book(

 bisbn CHAR(11), 

 bname CHAR(50), 

 bauthor CHAR(40), 

 bpublisher CHAR(30), 

 bprice NUMERIC(7,2), 

 bcategory CHAR(2), 

 CONSTRAINT bookkey PRIMARY KEY(bisbn)

);


```



#### 实现参照完整性约束：

是指一个基本表的外键要么取值为空，要么取值为被参照表的某一个主键值

```sql
CREATE TABLE borrow(

 rno CHAR(5),bisbn CHAR(11), 

 startdate DATE, enddate DATE, 

 fine NUMERIC(7,2) ,PRIMARY KEY(rno,bisbn) ,

 FOREIGN KEY(rno) REFERENCES reader(rno),

 FOREIGN KEY(bisbn) REFERENCES book(bisbn)

);
```



#### 用户定义的完整性约束：

用户针对特定要求而规定的一些特殊条件

**(1)NOT NULL与DEFAULT约束**

NOT NULL和NULL——非空和空值约束。

DEFAULT——默认值约束。

```sql
CREATE TABLE book(

 bisbn CHAR(11), 

 bname CHAR(50) NOT NULL, 

 bauthor CHAR(40) NOT NULL, 

 bpublisher CHAR(30) NOT NULL, 

 bprice NUMERIC(7,2), 

 bcategory CHAR(2), 

 CONSTRAINT bookkey PRIMARY KEY(bisbn)

);


```

```sql
CREATE TABLE borrow

(

 rno CHAR(5), 

 bisbn CHAR(11), 

 startdate DATE,  

 enddate DATE, 

 fine NUMERIC(7,2) DEFAULT 0,

 PRIMARY KEY(rno,bisbn,startdate)

);


```

(2)UNIQUE约束

也称为唯一性约束，它确保在一个非主码列上值的唯一性。

创建专业表specialty，且定义属性列sname取值必须唯一

```sql
CREATE TABLE specialty

(

   sno INT PRIMARY KEY, /*专业编号*/

   sname CHAR(20) NOT NULL UNIQUE/*专业名*/

);

(3)C
```

CHECK约束

也称为检查约束，它对属性列取值进行约束。格式为：

CHECK(条件表达式)

在条件表达式中，可以出现属性列名、常量、运算符、函数等。

```sql
CREATE TABLE reader(

 rno CHAR(5) PRIMARY KEY, 

 rname CHAR(20) NOT NULL, 

 rgender CHAR(2) CHECK(rgender IN('男','女')),

 rage INT CHECK(rage BETWEEN 0 AND 100),

 rspecialty CHAR(20) 

);
```



### 更新基本表

（1）增加列属性：

```sql
ALTER TABLE 基本表名 ADD 新属性列名 数据类型[完整性约束]
```

(2)增加完整性约束：

```sql
 ALTER TABLE 基本表名 ADD完整性约束
```

```sql
ALTER TABLE reader ADD cno INT;

ALTER TABLE reader ADD FOREIGN KEY(cno)

  REFERENCES colledge(cno);
```

(3)删除属性列：

```sql
ALTER TABLE 基本表名 DROP COLUMN 属性列名

ALTER TABLE reader DROP COLUMN rphone;
```

（4）删除完整性约束

```sql
ALTER TABLE 基本表名 DROP CONSTRAINT 完整性约束名
```

```sql
ALTER TABLE borrow DROP CONSTRAINT borrowReader;

ALTER TABLE borrow DROP CONSTRAINT borrowBook;
```

（5）更改属性列

```sql
ALTER TABLE 基本表名 ALTER COLUMN 属性列名 数据类型
```

```sql
ALTER TABLE reader ALTER COLUMN rphone CHAR(20);
```



### 删除基本表

```sql
DROP TABLE 基本表名 [RESTRICT | CASCADE]
```

```sql
DROP TABLE borrow CASCADE;
```



### 数据查询

```sql
SELECT [ALL | DISTINCT] 目标列表达式 [, …]

 FROM 基本表名或视图名 [, …]

 [WHERE 条件表达式]

 [GROUP BY 列名1 [HAVING 条件表达式]]

 [ORDER BY 列名2 [ASC | DESC]];
```

**为列指定别名**

通过为列指定别名，可以改变查询结果的列标题，使其显示一个有意义的名字。

格式为：目标列表达式 别名

```sql
SELECT rname 姓名, 2014-rage 出生年份 

 FROM reader;
```

**去掉重复值**

如果在查询时不希望出现重复行，可以使用DISTINCT

```sql
SELECT DISTINCT rno 

 FROM borrow
```

**用关系表达式表示的条件**

在关系表达式中，可以出现常量、属性列、关系运算符和函数。

```sql
SELECT rname 

 FROM reader

 WHERE rspecialty='CP';
```



#### 聚焦函数

| 函数                          | 功能               |
| ----------------------------- | ------------------ |
| COUNT([DISTINCT \| ALL] *)    | 统计元组个数       |
| COUNT([DISTINCT \| ALL] 列名) | 计算一列中值的个数 |
| SUM([DISTINCT \| ALL] 列名)   | 计算一列值的总和   |
| AVG([DISTINCT \| ALL] 列名)   | 计算一列值的平均值 |
| MAX([DISTINCT \| ALL] 列名)   | 求一列值中的最大值 |
| MIN([DISTINCT \| ALL] 列名)   | 求一列值中的最小值 |

**对查询结果排序**

ORDER BY子句的格式：

```sql
 ORDER BY 列名 [ASC | DESC]
```

查询“清华大学出版社”出版的图书名称和价格，查询结果按价格的降序排列

 

```sql
SELECT bname, bprice 

 FROM book

 WHERE bpublisher ='清华大学出版社'

 ORDER BY bprice DESC;
```

**分组查询**

GROUP BY子句的格式：

```sql
 GROUP BY 列名 [HAVING 条件表达式]
```

查询图书馆藏书中各出版社的图书数量

```sql
 SELECT bpublisher, COUNT(bpublisher) quantity 

 FROM book

 GROUP BY bpublisher;
```



查询出版两本及以上图书的出版社名称及图书数量

```sql
 SELECT bpublisher, COUNT(bpublisher) quantity  FROM book

  GROUP BY bpublisher HAVING COUNT(*)>=2;
```



##### **连接查询**

##### 使用WHERE子句表示连接

```sql
  WHERE [表名1.]列名1 比较运算符 [表名2.]列名2
```

要求连接条件中的连接字段的类型必须是可比的，但名字不必相同；比较运算符主要包括：=、>、<、>=、<=和<>。



查询每个读者及其所借图书的情况

```sql
 SELECT reader.*, borrow.*  

 FROM reader, borrow

 WHERE reader.rno=borrow.rno; 使用FROM子句表示连接
```



#### 使用FROM子句表示连接

```sql
FROM 表名1 JOIN 表名2 ON 表名1.列名1 比较运算符 表名2.列名2
```

查询每个读者及其所借图书的情况

```sql
SELECT reader.rno,rname,rgender,rage

,rspecialty,bisbn,startdate,enddate,fine

 FROM reader JOIN borrow ON reader.rno=borrow.rno;
```

**复合条件连接**

查询王建立所借图书的图书ISBN号

```sql
SELECT reader.rname, borrow.bisbn 

 FROM reader, borrow

  WHERE reader.rno=borrow.rno AND reader.rname='王建立'; 
```

**多表连接**

查询每个读者的读者编号、姓名及其所借图书的名称

```sql
 SELECT r.rno, r.rname, bk.bname 

 FROM reader r, borrow b, book bk

 WHERE r.rno=b.rno AND b.bisbn=bk.bisbn ; 
```

**自身连接**

查询借阅ISBN号为“302-02368-5”和“302-03314-5”两本书的读者

```sql
 SELECT b1.rno, b1.bisbn ,b2.bisbn 

 FROM borrow b1, borrow b2

 WHERE b1.rno=b2.rno 

AND b1.bisbn='302-02368-5' AND b2.bisbn='302-03314-5';
```

**外连接**

查询每个读者及其所借图书的情况，即使没有借书的读者也要显示其信息

```sql
 SELECT reader.rno, rname, rage , bisbn, startdate

 FROM reader LEFT OUTER JOIN borrow ON(reader.rno=borrow.rno); 
```

**嵌套查询**

**嵌套查询：**

```sql
SELECT rname 

 FROM reader

 WHERE rno IN

 (SELECT rno 

 FROM borrow 

 WHERE bisbn='302-02368-5'); 
```

#### 带有EXISTS谓词的子查询

EXISTS代表存在量词，用于判断子查询的结果是否存在。

当子查询的结果集不为空时，返回逻辑真值；

当子查询的结果集为空时，返回逻辑假值。

查询所有借过“302-00860-4”图书读者编号和姓名

```sql
SELECT rno, rname  FROM reader

 WHERE EXISTS 

 (SELECT * FROM borrow 

 WHERE rno=reader.rno AND bisbn='302-00860-4');
```

**并操作**

查询借阅ISBN号为“302-02368-5”或“302-03314-5”图书的读者

```sql
SELECT DISTINCT rno FROM borrow
 WHERE bisbn='302-02368-5'
UNION
SELECT DISTINCT rno FROM borrow
 WHERE bisbn='302-03314-5';
```

**交操作**

查询借阅ISBN号为“302-02368-5”和“302-03314-5”图书的读者

```sql
SELECT rno  FROM borrow
 WHERE bisbn='302-02368-5'
INTERSECT
SELECT rno  FROM borrow
 WHERE bisbn='302-03314-5'; 
```

**差操作**

```sql
查询没借过ISBN号为“302-03314-5”图书的读者
SELECT rno 	FROM reader
EXCEPT
SELECT rno 	FROM borrow
	WHERE bisbn='302-03314-5';	

```

## **插入新数据**

```sql
INSERT INTO 表名[(属性列名1 [,属性列名2 , …])]

 VALUES(常量1 [,常量2 , …]);
```

在reader表中，增加一个读者。

| 读者编号 | 姓名   | 性别 | 年龄 | 专业 |
| -------- | ------ | ---- | ---- | ---- |
| 14006    | 孙国庆 | 男   | 17   | 信息 |

```sql
INSERT INTO reader(rno, rname, rgender, rage, rspecialty) VALUES('14006', '孙国庆', '男', 17, 'IF');
```

或

```sql
   INSERT INTO reader
 VALUES('14006', '孙国庆', '男', 17, 'IF');
```

**修改数据**

```sql
UPDATE 表名

  SET 属性列名1=表达式1 [,属性列名2=表达式2, …]

 [WHERE 条件]
```

```sql
CREATE [UNIQUE | CLUSTER] INDEX 索引名
	ON 表名(属性列名1[次序1] [, 属性列名2 [次序2], …]);
UNIQUE表示创建唯一索引。
CLUSTER表示创建聚簇索引。
```

```sql
为读者表的读者编号建一个聚簇索引
	CREATE CLUSTER INDEX reader_rno
		ON reader(rno);
```

**创建视图**

```sql
CREATE VIEW 视图名 [(属性列名1[, 属性列名2, …])]

 AS 子查询

 [WITH CHECK OPTION];
```

建立一个视图，使其包含计算机专业的读者信息

```sql
CREATE VIEW reader_cp
   AS
   SELECT rno, rname, rgender, rage FROM reader
​    WHERE rspecialty='CP' WITH CHECK OPTION;
```







