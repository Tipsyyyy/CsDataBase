### 基本操作

- 启动停止

  - ```shell
    net start mysql80
    net stop mysql80
    ```

- 远程来连接到数据库

  - ```shell
    mysql [-h 127.0.0.1] [-P 3306] -u root -p
    参数：
    -h : MySQL服务所在的主机IP
    -P : MySQL服务端口号， 默认3306
    -u : MySQL数据库用户名
    -p ： MySQL数据库用户名对应的密码
    ```

### 函数

#### 字符串函数

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718234241706.png" alt="image-20230718234241706" style="zoom:33%;" />
- select可以用于在不涉及具体表的时候调用函数`select concat('Hello' , ' MySQL');`
- 在特定字段前补0`update emp set workno = lpad(workno, 5, '0');`

#### 数值函数

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718234717399.png" alt="image-20230718234717399" style="zoom:33%;" />

#### 日期函数

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718234754387.png" alt="image-20230718234754387" style="zoom:33%;" />

- 查询所有员工的入职天数，并根据入职天数倒序排序。`select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by entrydays desc;`

#### 流程函数

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718234958469.png" alt="image-20230718234958469" style="zoom:33%;" />

- ```mysql
  select
  id,
  name,
  (case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end )'数学',
  (case when english >= 85 then '优秀' when english >=60 then '及格' else '不及格'end ) '英语',
  (case when chinese >= 85 then '优秀' when chinese >=60 then '及格' else '不及格'end ) '语文'
  from score;
  ```

### 约束

- 作用于表中字段上的规则，用于限制存储在表中的数据。

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230718235839084.png" alt="image-20230718235839084" style="zoom:33%;" />

  - AUTO_INCREMENT约束：自动增长
    - 自动生成唯一且自增的数

- 创建时在字段后加上约束关键字`id int AUTO_INCREMENT PRIMARY KEY COMMENT 'ID唯一标识',`

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719000035204.png" alt="image-20230719000035204" style="zoom: 50%;" />

- 外键约束：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719000413682.png" alt="image-20230719000413682" style="zoom: 50%;" />

  - 创建表时设置约束

    - ```mysql
      CREATE TABLE 表名(
      字段名 数据类型,
      ...
      [CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
      );
      ```

  - 添加约束`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名)REFERENCES 主表 (主表列名) [ON UPDATE 更新行为 ON DELETE 删除行为];`

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719001259251.png" alt="image-20230719001259251" style="zoom:50%;" />

  - 删除`ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;`

  - 外键的作用

    - **参照完整性约束**：外键的主要目的是防止**无效数据的输入**。如果一个字段是另一个表中主键的外键，则不能添加或修改该字段的值，除非这个值在主表中存在。例如，如果在学生表中有一个字段是课程ID，这个课程ID在课程表中是主键，那么在学生表中，就不能输入不存在的课程ID。
    - **关联和联接表格**：外键也可以用于关联两个表的数据。通过在 SQL 查询中使用 JOIN 语句，可以获取到两个或多个表中相关联的数据。
    - **级联操作**：有时，当在一个表中更新或删除一个记录时，你可能希望自动更新或删除另一个表中与之相关联的记录。这是通过定义级联更新或级联删除的外键来实现的。例如，如果在学生和课程两个表之间存在一个外键关系，那么删除课程表中的一门课程时，会连带删除选修了这门课程的所有学生的选课记录，以保持数据的一致性。
    - 主次之分：假设我们有两个表，一个是学生表，一个是课程表。在课程表中，课程代码是主键，在学生表中，选课代码是外键。在这个例子中，课程表就是主表，学生表是从表。

  - 添加了外键之后，再删除父表数据时产生的约束行为，我们就称为**删除/更新行为**

    - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719001620294.png" alt="image-20230719001620294" style="zoom: 50%;" />

### 事务

- 事务是一组操作的集合，它是一个不可分割的工作单位ACID属性
  - **原子性**（Atomicity）：事务是**不可分割的最小操作单元**，要么全部成功，要么全部失败。
  - **一致性**（Consistency）：事务完成时，必须使**所有的数据都保持一致状态。**
  - **隔离性**（Isolation）：数据库系统提供的隔离机制，保证事务在**不受外部并发操作影响**的独立环境下运行。
  - **持久性**（Durability）：事务一旦提交或回滚，它对数据库中的**数据的改变就是永久的**。
- 事务是一个或多个SQL语句组成的一个执行单元。只有当该单元内的**所有操作都成功完成时**，该事务才会被提交。如果其中**任何操作失败，事务将被回滚**，所有的操作都**不会生效**。
- 控制事物
  - 开启事务`START TRANSACTION`
    - 具体操作语句在开启与提交之间
  - 提交事务`COMMIT`
  - 回滚事务`ROLLBACK;`

- 并发问题
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719101107601.png" alt="image-20230719101107601" style="zoom:33%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719101120117.png" alt="image-20230719101120117" style="zoom:33%;" />
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719101135718.png" alt="image-20230719101135718" style="zoom:33%;" />

#### 事物隔离级别

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230719102656148.png" alt="image-20230719102656148" style="zoom:33%;" />

- 读未提交（Read Uncommitted）：
  - 最低的隔离级别，事务间相互影响最大。
  - 允许一个事务读取另一个事务**尚未提交的数据。**
  - 可能导致脏读（Dirty Read），即读取到未提交的数据。
- 读已提交（Read Committed）：
  - 保证一个事务只能读取到**已提交的数据**。
  - 事务在执行过程中看到的数据是**稳定的**，不会读取到其他事务已提交的数据变化。
  - 可能导致不可重复读（Non-repeatable Read），即同一事务内两次读取同一数据，结果不一致。（强调单挑数据的修改）
- 可重复读（Repeatable Read）：
  - 保证一个事务多次读取**同一数据**时结果始终一致。
  - 在事务执行期间，其他事务对该数据进行修改也不会影响到当前事务的读取。
  - 可能导致幻读（Phantom Read），即同一事务内两次查询结果集不一致。（强调数据总条数的增减，而不局限于单条的变化）
- 串行化（Serializable）：
  - 最高的隔离级别，完全串行化执行每个事务，确保不会发生并发问题。
  - 通过锁机制来实现事务的完全隔离。
  - 避免了脏读、不可重复读和幻读的问题，但性能较低，一次只能执行一个事务。
- 查看事务隔离级别`SELECT @@TRANSACTION_ISOLATION;`
- 设置事务隔离级别`SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE }`

#### 死锁

- 当两个或者多个事务无限期地等待一个资源时，就会发生死锁。解决死锁的常见方法包括：
  - **预防死锁**：例如，设定锁的超时时间。
  - **死锁检测和恢复**：定期检查死锁，一旦检测到，挑选一个事务进行回滚。