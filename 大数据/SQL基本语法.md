## 概述

- 操作关系型数据库的编程语言，定义了一套操作关系型数据库统一标准

- 注释
  - 单行注释：-- 注释内容 或 # 注释内容
  - 多行注释：/* 注释内容 */
- 分类 
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718215401308.png" alt="image-20230718215401308" style="zoom:33%;" />
- 关系型数据库
  - 建立在关系模型基础上，由多张相互连接的二维表组成的数据库

- 操作指令不区分大小写

## 基本语法

### DDL

#### 数据库操作

- 查询现有数据库`show databases ;`
- 创建数据库`create database [ if not exists ] 数据库名 [ default charset 字符集 ] [ collate 排序规则 ] ;`
- 删除数据库`drop database [ if exists ] 数据库名 ;`

#### 表操作

- 在操作数据库之前需要先切换到改数据库下

  - 切换数据库`use 数据库名 ;`

- 查询当前数据库所有的表`show tables;`

- 查看指定表的结构`desc 表名 ;`

- 查看指定表的建表语句`desc 表名 ;`

- 建表

  - ```sql
    CREATE TABLE 表名(
    字段1 字段1类型 [ comment 字段1注释 ],
    字段2 字段2类型 [comment 字段2注释 ],
    字段3 字段3类型 [comment 字段3注释 ],
    ......
    字段n 字段n类型 [comment 字段n注释 ]
    ) [ comment 表注释 ] ;
    ```

- 数据类型

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718221307579.png" alt="image-20230718221307579" style="zoom: 33%;" />
    - 还具有一些附加参数
    - 声明为无符号`age tinyint unsigned`
    - 声明(总最大位数，小数部分位数)`score double(4,1)`
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718221618281.png" alt="image-20230718221618281" style="zoom:33%;" />
    - char是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关 。而varchar是变长字符串，指定的长度为最大占用长度

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718221742671.png" alt="image-20230718221742671" style="zoom:33%;" />

- 修改

  - 添加字段`ALTER TABLE 表名 ADD 字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];`
  - 修改数据类型`ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);`
  - 修改字段名和字段类型`ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];`

- 删除`ALTER TABLE 表名 DROP 字段名;`

- 修改表名`ALTER TABLE 表名 RENAME TO 新表名;`

- 修改表`DROP TABLE [ IF EXISTS ] 表名;`

### 图形界面操作(DataGrip)

- 展示所有数据库
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718230237049.png" alt="image-20230718230237049" style="zoom:33%;" />
- 在DataGrip中执行SQL语句
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718230337064.png" alt="image-20230718230337064" style="zoom:33%;" />

### DML

- 给指定字段添加数据`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);`
  - 为全部字段添加`INSERT INTO 表名 VALUES (值1, 值2, ...);`
  - 支持批量添加`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;`
- 修改数据 `UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ;`
  - 省略条件时对整张表进行操作 
- 删除数据`DELETE FROM 表名 [ WHERE 条件 ] ;`
  - 删除的是整条记录

### DQL

- 基本查询（不带任何条件）
  - `SELECT 字段1, 字段2, 字段3 ... FROM 表名 ;`
  - \* 号代表查询所有字段
  - 为字段设置别名`SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名;`
  - 过滤重复结果`SELECT DISTINCT 字段 FROM 表名;`
  
- ```sql
  SELECT [ALL | DISTINCT] <目标列表达式>[,<目标列表达式>]…
    FROM <表名或视图名>[,<表名或视图名>]…
    [WHERE <条件表达式>]
    [GROUP BY <列名> [HAVING <条件表达式>]]
    [ORDER BY <列名> [ASC|DESC]…]
    
    SELECT * FROM Student
    WHERE Id>10
    GROUP BY Age HAVING AVG(Age) > 20
    ORDER BY Id DESC
  ```

- 条件查询（WHERE）
  - `SELECT 字段列表 FROM 表名 WHERE 条件列表 ;`
  
  - 逻辑运算符与c一致
  
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718232001802.png" alt="image-20230718232001802" style="zoom:33%;" />
  
  - **LIKE** 子句通过通配符来将一个值同其他相似的值作比较,百分号代表**零个、一个或者多个字符**。下划线则代表**单个**数字或者字符。两个符号可以一起使用。
  
    - ```sql
       SELECT FROM table_name
          WHERE column LIKE '%XXXX%'
      
      
          SELECT FROM table_name
          WHERE column LIKE 'XXXX_'
      ```
  
- 聚合函数（count、max、min、avg、sum）
  - 将一列数据作为一个整体，进行纵向计算 。
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718232051722.png" alt="image-20230718232051722" style="zoom:33%;" />
  - `SELECT 聚合函数(字段列表) FROM 表名 ;`
  - 例：统计西安地区员工的年龄之和`select sum(age) from emp where workaddress = '西安';`
  - 统计记录的总数目`SELECT COUNT(*) FROM employee_tbl ;`
  
- 分组查询（group by）
  - `SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组后过滤条件 ];`
  
  - ```sql
    SELECT ID, NAME, AGE, ADDRESS, SALARY
    FROM CUSTOMERS
    GROUP BY age
    HAVING COUNT(age) >= 2;
    ```
  
  - 执行顺序：where > 聚合函数 > having 
  
  - 可以根据多维条分组，用`,`分隔
    - `select workaddress, gender, count(*) '数量' from emp group by gender , workaddress;`
  
- 排序查询（order by）
  - `SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;`
  - ASC : 升序(默认值)；DESC: 降序
  - 例子：查询所有年龄小于等于35岁员工的姓名和年龄，并对查询结果按年龄升序排序，如果年龄相同按入职时间降序排序。
    - `select name , age from emp where age <= 35 order by age asc , entrydate desc;`
  
- 分页查询（limit）
  - `ELECT 字段列表 FROM 表名 LIMIT 起始索引, 每页查询记录数 ;`
  -  起始索引从0开始
    - 如果查询的是第一页数据，起始索引可以省略
  - 例子：查询性别为男，且年龄在20-40 岁(含)以内的前5个员工信息，对查询的结果按年龄升序排序，年龄相同按入职时间升序排序。
    - `select * from emp where gender = '男' and age between 20 and 40 order by age asc ,entrydate asc limit 5 ;`
  
- 执行顺序
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718232931513.png" alt="image-20230718232931513" style="zoom: 50%;" />

- DISTINCT

  - 同 SELECT 语句一起使用，可以去除所有重复记录，只返回唯一项。
  - `SELECT DISTINCT...`
  
- AND和OR

  - 配合WHERE使用

  - ```sql
    SELECT ID, NAME, SALARY 
        FROM CUSTOMERS
        WHERE SALARY > 2000 OR age < 25;
    ```

- UNION

  - 将两个或者更多的 **SELECT 语句**的运算结果**组合**起来。

  - ```sql
    SELECT Txn_Date FROM Store_Information
    UNION
    SELECT Txn_Date FROM Internet_Sales;
    ```

  - UNION AL用于将两个 SELECT 语句的结果组合在一起，重复行也包含在内。

- INTERSECT

  - 用于组合两个 SELECT 语句，但是只返回两个 SELECT 语句的结果中**都有**的行。

- EXCEPT 子句

  - 组合两个 SELECT 语句，并将第一个 SELECT 语句的结果中**存在**，但是第二个 SELECT 语句的结果中**不存在**的行返回。

- 索引

  - 创建索引`CREATE [UNIQUE] [CLUSTER] INDEX <索引名> ON <表名>(<列名>[<次序>][,<列名>[<次序>]]…);`

    - ```sql
      -- 建立学生表索引：单一字段Id索引倒序
      CREATE UNIQUE INDEX INDEX_SId ON Student (Id DESC);
      -- 建立学生表索引：多个字段Id、Name索引倒序
      CREATE UNIQUE INDEX INDEX_SId_SName ON Student (Id DESC,Name DESC);

  - 删除索引

    - ```sql
      -- 删除学生表索引 INDEX_SId
      DROP INDEX INDEX_SId;

- 视图：一种**虚拟**的表，以**预定义的 SQL 查询**的形式存在的数据表的成分
  - 以用户或者某些类型的用户感觉自然或者直观的方式来组织数据；
  - 限制对数据的访问，从而使得用户仅能够看到或者修改（某些情况下）他们需要的数据
  - 它不包含数据，只是保存了一个SQL**查询的结果**。因为视图是基于底层的真实表创建的，所以当真实表的数据发生变化时，视图也会相应地**呈现出最新**的数据。
  - ```sql
    CREATE VIEW view_name AS
    SELECT column1, column2, ...
    FROM table_name
    WHERE condition;
    ```

### DCL

#### 管理用户

- 查询用户列表`select * from mysql.user;`
- 创建用户`CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`
- 修改用户密码`ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;`
- 删除用户`DROP USER '用户名'@'主机名' ;`
- 主机名表示允许用户用于访问的主机`%`表示任意

#### 权限控制 

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718233405279.png" alt="image-20230718233405279" style="zoom:33%;" />
- 查询权限`SHOW GRANTS FOR '用户名'@'主机名' ;`
- 授予权限`GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';`
- 撤销权限`REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名'`
- 多个权限之间，使用逗号分隔
- 授权时， 数据库名和表名可以使用 * 进行通配，代表所有。

## 补充

### 多表查询

-  多表关系
   - 一对多：一个部门对应多个员工
     - 在多的一方建立外键，指向一的一方的主键
   - 多对多：一个学生可以选修多门课程，一门课程也可以供多个学生选择
     - 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键
     - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719002252246.png" alt="image-20230719002252246" style="zoom:33%;" />
   - 一对一：用户 与 用户详情的关系
     - 在任意一方加入外键，关联另外一方的主键（相当于对一个表进行了拆分合并）
-  查询多个表`select * from emp , dept;`使用逗号分隔
   - 默认返回表的笛卡尔积
   - 同样可以附加条件`select * from emp , dept where emp.dept_id = dept.id`
     - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719002651015.png" alt="image-20230719002651015" style="zoom:33%;" />

#### 链接JOIN

- 分类

  - 内连接（INNER JOIN）：当两个表中都存在匹配时，才返回行。


  - 左连接（LEFT JOIN）：返回左表中的所有行，如果左表中行在右表中没有匹配行，则结果中右表中的列返回空值。

  - 右连接（RIGHT JOIN）：恰与左连接相反，返回右表中的所有行，如果右表中行在左表中没有匹配行，则结果中左表中的列返回空值。

  - 全连接（FULL JOIN）：返回左表和右表中的所有行。当某行在另一表中没有匹配行，则另一表中的列返回空值
    - 如果所用的数据库不支持全连接，比如 MySQL，那么你可以使用 UNION ALL子句来将左连接和右连接结果组合在一起

- 内连接

  - `SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;`
  - 显示语法`select e.name, d.name from emp e inner join dept d on e.dept_id = d.id;`
    - 为表设置别名`tablea 别名1 , tableb 别名2`
    - 一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段。

- 外连接

  - 左外连接`SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;`
  - 右外连接`SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;`
  - 查询emp表的所有数据, 和对应的部门信息`select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;`

- 自链接

  - `SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;`
  - 自连接必须对同一个表设置不同的别名
  - 查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来`select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = b.id;`

- 联合查询

  - `UNION` 运算符选择的各个 `SELECT` 语句必须拥有相同的数量的列，列也必须拥有相似的数据类型，同时在相同的顺序上。

  - union all 会将全部的数据**直接**合并在一起，union 会对合并之后的**数据去重**。

  - ```mysql
    SELECT 字段列表 FROM 表A ...
    UNION [ ALL ]
    SELECT 字段列表 FROM 表B ....;
    ```

- 子查询(嵌套查询)

  - `SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );`
  - 标量子查询（子查询结果为单个值）
    - 查询返回的结果是单个值（数字、字符串、日期等）
    - 查询 "销售部" 的所有员工信息`select * from emp where dept_id = (select id from dept where name = '销售部');`
  - 列子查询(子查询结果为一列)
    - **子查询返回**的结果是**一列**（可以是多行，挑选字段）
    - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719094414164.png" alt="image-20230719094414164" style="zoom:33%;" />
    - 查询 "销售部" 和 "市场部" 的所有员工信息`select * from emp where dept_id in (select id from dept where name = '销售部' or name = '市场部');`
    - 查询比研发部其中任意一人工资高的员工信息`select * from emp where salary > any ( select salary from emp where dept_id = (select id from dept where name = '研发部') );`
  - 行子查询(子查询结果为一行)
    - 子查询返回的结果是一行（可以是多列）
    - 由于只有一行，因此通常直接使用=
    - 查询与 "张无忌" 的薪资及直属领导相同的员工信息：`select * from emp where (salary,managerid) = (select salary, managerid from emp where name = '张无忌');`
  - 表子查询(子查询结果为多行多列)
    - 子查询返回的结果是多行多列，这种子查询称为表子查询。
    - 通常使用in
    - 查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息：`select * from emp where (job,salary) in ( select job, salary from emp where name = '鹿杖客' or name = '宋远桥' );`