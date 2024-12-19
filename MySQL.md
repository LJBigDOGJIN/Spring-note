#### 什么是数据库

数据库就是存一堆数据信息的集合。

什么是E-R图

E-R图（entity relationship diagram）就是实体关系图 提供了表示实体类型、属性和联系的方法

实体：通常是现实世界的业务对象，也可以使用一些逻辑对象。在er图中，实体使用矩形表示。

属性：即某个实体拥有的属性，属性用来描述组成实体的要素，对产品设计来说可以理解为字段。在er途中属性使用椭圆形来表示。

联系：即实体和实体之间的关系，在er图中用菱形表示。

示例：学生与课程之间的E-R图

![image-20240903171208376](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240903171208376.png)

#### 数据库三范式

第一范式：不可再分，即一个字段不能够再被分为其他字段。

第二范式：在第一范式的基础上消除了

第三范式：

#### 主键和外键

主键用于表示一个唯一元组，主键不能为空，不能重复，一张表只能有一个主键

外键是其他表的主键，用来与表建立一定的联系，主键可以为空，可以重复

drop、delete和truncate的区别

drop直接删除表，不保留表结构

truncate删除表数据不删除表结构

delete删除一行数据

truncate和drop属于DDL语句，不能回滚，不能触发trigger，而delete是DML语句，操作会放到rollback segment中，等事务提交时生效

> DDL:数据库定义语言，主要是定义库内的对象，表等
>
> DML：数据库操作语言，主要是对数据库表中数据的操作，包括增删改查

执行速度:drop>truncate>delete

> drop执行时需要释放掉表空间
>
> truncate执行时不需要记录日志，还会把表的自增值和索引恢复到初始数据
>
> delete执行时需要记录binlog日志，故操作比较慢，这也是为什么delete操作能够回滚的原因

数据库设计分为那几个阶段

需求分析

概念设计

逻辑结构设计

物理结构设计

数据库实施

数据库的运营维护

#### 数据库数据类型

数值型、字符型，日期时间型

数值型：int、tinyint 、smallint、mediumint、bigint、float、double、decimal

字符型：char、varchar，text，longtext，tinytext、mediumText

日期：time、date、datetime、timestamp

![image-20240906103132895](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240906103132895.png)

整数的UNSIGNED属性作用

不允许修饰的整数为负数，且容量能扩大一倍，对于从 0 开始递增的 ID 列，使用 UNSIGNED 属性可以非常适合，因为不允许负值并且可以拥有更大的上限范围，提供了更多的 ID 值可用。

char和varchar的作用

char是不可变字符，varchar是可变字符，

char存储时会在后面补零凑齐位数，varchar不用

#### DATETIME 和 TIMESTAMP 的区别是什么？

DATETIME 类型没有时区信息，TIMESTAMP 和时区有关。

TIMESTAMP 只需要使用 4 个字节的存储空间，但是 DATETIME 需要耗费 8 个字节的存储空间。但是，这样同样造成了一个问题，Timestamp 表示的时间范围更小。

- DATETIME：1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
- Timestamp：1970-01-01 00:00:01 ~ 2037-12-31 23:59:59

NULL和  '' 的区别

NULL表示一个不确定的值，两个null比较不一定相等，select null=null 结果为null（数据库中没有布尔类型，用0或null表示false），但是当使用distinct、order by、group by时又默认null是相等的

数据表中存储NULL是要占空间的，而''不占空间

判断null值只能用 is null  或者is not null，不能用其他运算符，但'' 可以用运算符来比较。

NULL会影响聚合函数的使用，SUM、AVG、MIN、MAX不会统计null值，COUNT取决于括号内的参数

如果参数是*时，则会统计null值，如果参数是具体的列名时则不会统计null值。

在数据库中因为没有布尔值，则在库中经常使用tinyint(1) 来表示布尔值，1代表true、0代表false

#### 数据库基本架构

连接器、查询缓存器，分析器，优化器，执行器

![image-20240906130325665](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240906130325665.png)

**连接器**：身份认证等登录相关（登录数据库时）

**查询缓存器**：会存储查询的缓存，sql执行时会先来这查缓存（mysql8.0以后被删除）

**分析器**：分析sql的语法，检验是否正确

**优化器**：按照mysql认为的最优方案去执行

**执行器**：用来执行sql，但在执行之前会查看是否具有权限

**存储引擎**：负责数据的存储和读取

#### MYSQL存储引擎

我比较常见的存储引擎有innodb，myisam。MYSQL默认使用的是INNODB，下面这张图还有其他存储引擎

![image-20240906131030060](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240906131030060.png)

在这些存储引擎中只有innodb是事务型存储引擎，其他存储引擎都不支持事务

具体不在这阐述，详解要单开一个文档。

MYISAM和INNODB的区别 

|                            | MYISAM       | INNODB           |
| -------------------------- | ------------ | ---------------- |
| 是否支持行级锁             | 只支持表级锁 | 表级和行级都支持 |
| 是否支持事务               | 不支持       | 支持             |
| 是否支持外键               | ×            | √                |
| 是否支持MVCC               | ×            | √                |
| 索引                       | 非聚簇       | 聚簇             |
| 数据库异常崩溃后的安全恢复 | ×            | √                |
| INNODB的性能比MYISAM要高   |              |                  |

![image-20240906134455150](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240906134455150.png)

#### 数据库事务

事务四大特性：原子性、一致性、隔离性、持久性

原子性：一组数据库操作要么全部成功，要么全部失败（如果有一个操作出现问题的话所有操作全部都要回滚）

一致性：数据的状态在事务提交前后都必须是一致的，例如转账，转帐前和转账后的双方的总钱数应该是不变的。

隔离性：事务与事务之间是互不影响、相互独立的。

持久性：对数据库数据的改变应该是永久的，不会因某些故障而改变。

#### 并发事务出现的问题

脏读、不可重复读、幻读

脏读：读到还未提交的数据

不可重复读：在一个事务内两次读取到的信息不一致（第一次读取到信息后，有其他事务对该数据进行操作，再次读取该数据时，与第一次读到的不一样）

幻读：在一个事务内两次读取到的数据数量不一致（事务1在读取数据后，事务2来插入或者删除了几条数据，事务1再次读取时发现和第一次读取到的数据数量上有差别）

四种隔离级别

未提交读：什么问题都没解决

已提交读：解决了幻读问题

可重复读：解决了幻读和不可重复读

串行化：所有事务都串行执行，不会导致幻读、不可重复读和脏读，但效率很慢

![image-20240906142020427](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240906142020427.png)

MySQL默认隔离级别是 可重复读 （REPEATABLE-READ）

#### 行级锁和表级锁

行级锁是存储引擎层面的锁，针对索引加的锁 ，行级锁的粒度更细，只对当前操作的行记录加锁，不锁整张表，冲突小，读写速度更快

表锁范围是当前操作的整张表，不针对索引，容易产生冲突，高并发下写效率较慢

行级锁使用时注意事项：INNODB行锁是针对索引加的锁，表级锁针对非索引，当使用DELETE、UPDATE时的where条件如果没有命中索引或者索引失效时，就会给表中的所有行加上锁

innodb行锁种类

间隙锁：一定范围内的锁，不包括记录本身（用来解决幻读问题，当事务A读取一定范围的数据时，通过加间隙锁，事务B在往其中插入数据后，A在读取该范围内的数据时，不会看到事务B插入的数据）

记录锁：也叫行锁，单个记录的锁

临建锁：（Record Lock+Gap Lock ）临建锁是记录锁和间隙锁的组合，锁定一个范围，包含记录本身，主要目的是为了解决幻读问题（MySQL 事务部分提到过）。记录锁只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁。

在InnoDB默认的隔离级别REPEATABLE　READ中行锁默认使用的是临建锁

#### 共享锁和排他锁

共享锁又叫读锁，在读取数据时允许其他事务共享该锁（即多个事务可以同时获得该锁）

排他锁又叫写锁或独占锁，当一个事务获取排他锁时，不允许其他事务在获取该锁，同时当一个事务拥有排他锁时，也不允许该事务获得其他任意的锁。

#### 意向共享锁和意向排他锁

意向锁主要是针对表锁来设计的，当想对某张表加锁时，应先判断这张表或表中某行记录是否持有锁，若通过遍历每行数据来判断某个记录是持有锁的效率很低，所以推出意向锁。当表想要加锁时，必须先获得意向锁。

意向共享锁：事务有意向表中某条记录加共享锁

意向排他锁：事务有意向表中加排他锁

#### 当前读和快照读

先了解一下什么叫快照：快照就是一条记录的历史版本（譬如一条记录年龄为18，进行修改操作后为20，那18就是一条历史版本）

快照读（一致性非锁定读） 就是一般的select语句，但不包括加锁的查询语句

```sql
SELECT ... FOR UPDATE
# 共享锁 可以在 MySQL 5.7 和 MySQL 8.0 中使用
SELECT ... LOCK IN SHARE MODE;
# 共享锁 可以在 MySQL 8.0 中使用
SELECT ... FOR SHARE;
```

只有在事务隔离级别为RC和RR时InnoDB才会使用一致性非锁定读

- RC：读取被锁定行的最新一条快照数据
- RR：读取事务开始时的第一条快照数据

当前读（一致性锁定读）  常见的查询类型如下(应该是加锁的）：

```sql
# 对读的记录加一个X锁
SELECT...FOR UPDATE
# 对读的记录加一个S锁
SELECT...LOCK IN SHARE MODE
# 对读的记录加一个S锁
SELECT...FOR SHARE
# 对修改的记录加一个X锁
INSERT...
UPDATE...
DELETE...
```

#### 如何分析SQL

可以使用EXPLAIN 来分析SQL的执行计划，执行计划是值一条SQL经过优化器的优化后，具体的执行过程。

`EXPLAIN` 并不会真的去执行相关的语句，而是通过 **查询优化器** 对语句进行分析，找出最优的查询方案，并显示对应的信息。

`EXPLAIN` 适用于 `SELECT`, `DELETE`, `INSERT`, `REPLACE`, 和 `UPDATE`语句，我们一般分析 `SELECT` 查询较多。

```sql

mysql> EXPLAIN SELECT `score`,`name` FROM `cus_order` ORDER BY `score` DESC;
+----+-------------+-----------+------------+------+---------------+------+---------+------+--------+----------+----------------+
|id| select_type |table|partitions|type|possible_keys|key|key_len|ref|rows|filtered|Extra|
+----+-------------+-----------+------------+------+---------------+------+---------+------+--------+----------+----------------+
|1|   SIMPLE  | cus_order|NULL|    ALL |     NULL   | NULL|NULL | NULL|997572| 100.00|Using filesort |
+----+-------------+-----------+------------+------+---------------+------+---------+------+--------+----------+----------------+
1 row in set, 1 warning (0.00 sec)
```

![image-20240909165814170](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240909165814170.png)



##### 查看执行计划

id：如果id值相等则从上往下依次执行，如果id值不等，则id值越大则执行的优先级越高，如果行引用其他行的并集结果，则该值可以为 NULL。

select_type：查询类型，用于区分普通查询，联合查询，子查询等复杂的查询

> - simple:简单查询,不包含union或子查询
> - primary:若查询中包含子查询或其他部分,外层的select将被标记为primary
> - subquery:子查询中第一个select
> - union:在union语句中,union后出现的语句
> - derived:在from中出现的子查询将被标记为derived
> - union result:union查询的结果

table:查询用到的表名,除了正常的表名,也可以是以下值

> - <unionM,N>:表示本行引用了id为M,N行的UNION的结果集
> - <derivedN>:表示本行引用了id为N的表所产生的派生表的结果,派生表有可能产自FROM子句中的子查询
> - <subqueryN>:表示本行引用了id为N的表所产生的物化子查询结果(不清楚啥意思)

type:查询执行的类型,从最优到最差的顺序为

system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL

各种类型的具体含义如下

> - system: 如果表所使用的引擎对于表行数统计是精确的,并且表中只有一条记录的情况下是system类型的查询
> - const:表中只有一条符合条件的数据记录,一次查询就可以找到,常用于使用主键或唯一索引的字段作为查询条件
> - eq_ref:当联表查询时,前面一张表的行在当前表只有一行数据与之对应
> - ref:使用普通索引查询结果时,可能找到多个符合条件的行
> - index_merge:当查询条件用到了多个索引时,表示开启了index merge 优化
> - range:对索引列进行范围查询时,执行计划中的key表示哪个索引被使用
> - index:查询遍历了整棵树的索引,和all类似,但index 扫描的时索引,所以速度要快很多
> - all:全盘扫描

possible_keys:表示可能用到的索引，如果这一列weiNUll，则表示没有可能用到的索引

key：表示MySql实际使用到的索引。如果为null则表示未用到索引。

key_len：表示MySQL实际使用的索引的最大长度，当使用联合索引时，有可能是多个列的长度和。在满足需求的前提下越短越好。如果key显示为null则key_len也显为NULL。

rows：rows列表示根据统计信息以及选用情况，大致估算出找到所需的记录或所需读取的行数，数值越小越好。

Extra：这列包含了MySQL解析查询的额外信息，通过这些信息，可以更准确二点理解MySQL到底是如何执行查询的：

常见的值如下：

- Using filesort：在排序时使用了外部的索引排序，没有使用到表内索引进行排序。
- Using temporary：MySQL需要创建临时表来存储查询的结果，常见于 ORDER BY、GROUP BY
- Using index：表明查询使用了覆盖索引，不用回表，查询效率高
- Using index condition：表示优化器选择使用了索引条件下推这个特性
- Using where：表明查询使用了where子句进行条件过滤。一般在没有使用到索引的情况下出现
- Using jion buffer：连表查询的方式，表示当被驱动表没有使用索引的时候，MySQL会先将驱动表读出来放到join buffer中，在遍历被驱动表与驱动表进行查询。

##### sql优化

当我们查询数据库数据时，如果写了一些比较复杂的sql，要是处理不当会导致我们的查询效率非常慢，此时我们就需要来对sql语句进行一定的优化。下面就来谈谈如何对sql进行优化。

**首先考虑语句**：

1.查询时 尽量避免使用select * 来查询，要使用具体的查询字段 ，

```sql
-- 比如使用 
select name ,age,sex from user;
-- 而不是
select * from user;
```

2.联表查询时，要使用小表来驱动大表（意思就是用数据量小的表来联结数据量大的表）

> 什么是驱动表，什么是被驱动表，这两个概念在查询中有时容易让人搞混，有下面几种情况，大家需要了解。
>
> 1. 当连接查询没有where条件时
>    1. left join 前面的表是驱动表，后面的表是被驱动表
>    2. right join 后面的表是驱动表，前面的表是被驱动表
>    3. inner join / join 会自动选择表数据比较少的作为驱动表
>    4. straight_join(≈join) 直接选择左边的表作为驱动表（语义上与join类似，但去除了join自动选择小表作为驱动表的特性）
> 2. 当连接查询有where条件时，带where条件的表是驱动表，否则是被驱动表

```sql
## 假设现在有两张表 t1 和 t2 ，t2的数据量要大于t1
select name,age ,sex,id from t1 left jion t2 where t1.id=t2.id
```

3.当使用条件查询时，条件当中使用函数也会降低整条sql的查询效率

4.谨慎使用union关键字，因为union会对两个结果集进行去重操作，比较耗时效率低下，要合并的话使用union all不会去重，效率相对较高

5.还有一些 in 、or等使用时需要注意

6.group by 优化 :

- 如果对分组后的结果没有排序要求，建议在group by 后面加上order by null 因为group by会默认排序。
- 尽量让group by过程用上表的索引，确认方法是explain结果里没有Using temporary 和 Using filesort。
- 使用where 代替having 因为 having是在分组之后才进行筛选，如果where能够提前筛选则会降低开销。

7.区分in和exist: 主要是造成驱动顺序的改变，如果是exist，则以外层表为驱动表，先访问外层表。如果是in则是以内层表为驱动表，会先执行子查询。综上所述，exist适合外表小内表大的情况，而in适合内表小外表大的情况。

```sql
select * from 表A where id in (select id from 表B)
 ## 上面的语句相当于：
select * from 表A where exists(select * from 表B where 表B.id=表A.id)
```



**索引**

1.条件查询时 where 后对索引列进行操作时，会导致索引失效。

2.字符串类型必须要带引号才行，否则会发生隐式类型转化导致索引失效

3.查询中使用范围筛选时，范围条件右边的列不会走索引

```sql
## 其中username, age, phone都有索引，只有username和age会生效，phone的索引没有用到。
select * from user where username='123' and age>20 and phone='1390012345',
```

4.尽量使用覆盖索引来减少回表查询。

5.在使用or时，要保证or前后的两个条件都有索引，否则会导致索引失效

6.在模糊查询时，使用前模糊查询会导致索引失效

```sql

## 这种情况会导致索引失效
select name,age from user where name like '%嗷嗷'
```

7.SQL 性能优化 explain 中的 type：至少要达到 range 级别，要求是 ref 级别，如果可以是 consts 最好。

> consts：单表中最多只有一个匹配行（主键或者唯一索引），在优化阶段即可读取到数据。ref：使用普通的索引 range：对索引进行范围检索。当 type=index 时，索引物理文件全扫，速度非常慢。



#### 数据库语法基础复习

语法分类

DML 数据库操作语句：对数据库数据的操作，包括insert 、delete、update和select  （数据库CRUD四大操作）

DDL数据库定义语言：核心操作 create、alter、drop、truncate

DCL数据库控制语言：可以控制对数据库数据的操作权限，核心操作是：grant、revoke

##### 增删改查

增加数据

> 插入一行
>
> INSERT INTO user
> VALUES (10, 'root', 'root', 'xxxx@163.com');
>
> 插入多行
>
> INSERT INTO user
> VALUES (10, 'root', 'root', 'xxxx@163.com'), (12, 'user1', 'user1', 'xxxx@163.com'), (18, 'user2', 'user2', 'xxxx@163.com');
>
> 插入部分字段
>
> INSERT INTO user(username, password, email)
> VALUES ('admin', 'admin', 'xxxx@163.com');
>
> 插入查询出来的字段
>
> INSERT INTO user(username)
>  	SELECT name
>  FROM account;

更新数据

> UPDATE user
> SET username='robot', password='robot'
> WHERE username = 'root';

删除数据

> delete from user where username='root'

清空数据

> truncate table user

查询数据

DISTINCT用来去重

limit用来限制查询条数（常用来做分页查询）

ASC升序查询（默认为升序）

DESC降序查询

> -- *号代表所有字段
>
> select * from user
>
> select * from user order by age ASC
>
> 查询部分字段
>
> SELECT prod_id, prod_name, prod_price
> FROM products;
>
> -- 返回前 5 行
> SELECT * FROM mytable LIMIT 5;

##### 排序

Order by 用来按一列或者多列

ASC 升序（默认）

DESC 降序

```sql
SELECT * FROM products
ORDER BY prod_price DESC, prod_name ASC;
```

##### 分组

Group by  按一列或者多列进行分组

排序后为每一组返回一个记录，通常和聚合函数连用（count 、max、sum和avg等）

还能为分组后的每一组结果根据某一列进行排序

group by 还可以根据多列来进行分组，当根据多列分组时只有几列相同时才会被分为一组，举个例子，如下图表所示：

![image-20240924144012653](D:\TXT\图片文件\image-20240924144012653.png)

可以看到李少华的名字出现三次，但是性别这列可以看出来李少华有一个男的个一个女的，显然是两个人，但是当我们分组时如果只按name分，那么就会将这重名的两人混在一起，如下:

```sql
select sum(score) SumGrade from user group by name;
```

结果：![image-20240924145054991](D:\TXT\图片文件\image-20240924145054991.png)

此时为了区分重名的两人，应该再加一列sex分组条件，此时只有name和sex相等时才会被分为一组，具体如下：

```sql
select sum(score) SumGrade from user group by name,sex;
```

此时是正确的结果：

![image-20240924145413551](D:\TXT\图片文件\image-20240924145413551.png)

```sql
-- 分组
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name;
-- 分组后排序
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name;

```

###### having

相当与sql的where ，不过having只能配合group by使用，且在group by 后面的子句中使用，where在group by之前使用，having能使用聚合函数

```sql
SELECT cust_name, COUNT(*) AS NumberOfOrders
FROM Customers
WHERE cust_email IS NOT NULL
GROUP BY cust_name
HAVING COUNT(*) > 1;
```

##### 子查询

子查询是嵌套在较大查询中的 SQL 查询，也称内部查询或内部选择，包含子查询的语句也称为外部查询或外部选择。简单来说，子查询就是指将一个 `select` 查询（子查询）的结果作为另一个 SQL 语句（主查询）的数据来源或者判断条件。

子查询可以嵌入 `SELECT`、`INSERT`、`UPDATE` 和 `DELETE` 语句中，也可以和 `=`、`<`、`>`、`IN`、`BETWEEN`、`EXISTS` 等运算符一起使用。

子查询常用在 `WHERE` 子句和 `FROM` 子句后边：

- 当用于 `WHERE` 子句时，根据不同的运算符，子查询可以返回单行单列、多行单列、单行多列数据。子查询就是要返回能够作为 `WHERE` 子句查询条件的值。
- 当用于 `FROM` 子句时，一般返回多行多列数据，相当于返回一张临时表，这样才符合 `FROM` 后面是表的规则。这种做法能够实现多表联合查询。

> 注意：MYSQL 数据库从 4.1 版本才开始支持子查询，早期版本是不支持的。子查询需要放在()内

```sql
-- 子查询作为where的子句
select column_name [, column_name ]
from   table1 [, table2 ]
where  column_name operator
    (select column_name [, column_name ]
    from table1 [, table2 ]
    [where])
    -- 子查询在from后时相当于一张临时表，故我们要给其起一个别名
    select column_name [, column_name ]
from (select column_name [, column_name ]
      from table1 [, table2 ]
      [where]) as temp_table_name
where  condition
-- 子查询的子查询
SELECT cust_name, cust_contact
FROM customers
WHERE cust_id IN (SELECT cust_id
                  FROM orders
                  WHERE order_num IN (SELECT order_num
                                      FROM orderitems
                                      WHERE prod_id = 'RGAN01'));

```

##### Where

where用于条件查询，缩小结果范围

`WHERE` 子句用于过滤记录，即缩小访问数据的范围。

`WHERE` 后跟一个返回 `true` 或 `false` 的条件。

`WHERE` 可以与 `SELECT`，`UPDATE` 和 `DELETE` 一起使用。

可以在 `WHERE` 子句中使用的操作符。

![image-20240903203145042](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240903203145042.png)

##### IN和Between

- `IN` 操作符在 `WHERE` 子句中使用，作用是在指定的几个特定值中任选一个值。
- `BETWEEN` 操作符在 `WHERE` 子句中使用，作用是选取介于某个范围内的值。

```sql
select * from student where age in (16,18,19);

select * from student where age between 18 and 25;
```

##### AND、OR、NOT

都表示对条件的处理

AND 要前后两个条件都满足、OR 前后只要有一个为真即可、NOT表示对条件的否定

```sql
AND:
select * from student where name ='zhangsan' and age=18;
OR:
select * from student where name='hh' or name ='wangwu';
NOT:
select * from student where age not between 15 and 20;

```

##### LIKE

只有对where子句使用用来匹配字符串

like支持两个通配符匹配选项 %和 _

%表示匹配任意数量的任意字符

_表示匹配单个任意字符

（通配符置于开头处匹配效率非常底下）

```sql
SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '%bean bag%';
-- 
SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '__ inch teddy bear';
```

##### 连接查询

连接分为左连接、右连接、外连接和内连接

JION

表连接时，ON为判断条件，当ON后面的条件成立时，会将这两张表里的数据拼在一起，当条件不成立时会将右表字段的空值拼在一起，判断条件时不光可以用=号，您可以使用多个运算符来连接表，例如 =、>、<、<>、<=、>=、!=、`between`、`like` 或者 `not`，但是最常见的是使用 =。

```sql
select table1.column1, table2.column2...
from table1
join table2
on table1.common_column1 = table2.common_column2;
```

当做连接的两张表中有同名字段时，我们需要用表明.字段名来加以区分

```sql
# join....on
select c.cust_name, o.order_num
from Customers c
inner join Orders o
on c.cust_id = o.cust_id
order by c.cust_name;

# 如果两张表的关联字段名相同，也可以使用USING子句：join....using()
select c.cust_name, o.order_num
from Customers c
inner join Orders o
using(cust_id)
order by c.cust_name;
```

ON和WHERE的区别

 做连接查询时ON会根据On后面的条件生成一张临时表

where是对表的数据进行筛选，如果有临时表话，那么where应该是作用在临时表生成之后，不会决定临时表的生成

![image-20240904145530818](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240904145530818.png)

使用where也能实现连表的效果

```sql
select * from table1 t1,table2 t2 where t1.id=t2.id;

# 隐式内连接
select c.cust_name, o.order_num
from Customers c, Orders o
where c.cust_id = o.cust_id
order by c.cust_name;

# 显式内连接
select c.cust_name, o.order_num
from Customers c inner join Orders o
using(cust_id)
order by c.cust_name;
```

UNION (ALL)

union 用来合并两个查询的结果集，需要注意以下几点：

union两边的select 查询的字段必须一样、顺序也必须一致

 union distinct 可以简写为 union 默认对合并的结果集去重

uninon all 不对合并的结果集去重

（总的来说 union 的执行效率较慢）

JION 和 UNION的区别 

jion可以连接不同的列，但union必须两边的列必须相同且顺序一致

jion是将数据拼接为一行（水平），而union是将数据组装为多列（垂直）

##### 函数

left(str,len) ：从str左边返回len长度的字符串

right(str,len): 从str右边返回len长度的字符串

以上两个函数如果len值超过str的最大长度，则返回str全部，len为小于0的数字时则返回空串，若len为null时，则返回null

upper(str):大写

lower(str)：小写

ltrim(str):去掉左边的空格

rtrim(str):去掉右边的空格

length() 返回字符串长度，单位为字节

soundex(str) 转换为语音值 ，可以将一个字符串转换为描述其语音表示的字母数字模式。

```sql
SELECT *
FROM mytable
WHERE SOUNDEX(col1) = SOUNDEX('apple')
```

日期和时间处理

AddDate  增加一个日期（天、周等）

AddTime 增加一个时间（时、分等）

CurDate()  返回当前日期

CurTime() 返回当前时间

| `Date()`        | 返回日期时间的日期部分         |
| --------------- | ------------------------------ |
| `DateDiff()`    | 计算两个日期之差               |
| `Date_Add()`    | 高度灵活的日期运算函数         |
| `Date_Format()` | 返回一个格式化的日期或时间串   |
| `Day()`         | 返回一个日期的天数部分         |
| `DayOfWeek()`   | 对于一个日期，返回对应的星期几 |
| `Hour()`        | 返回一个时间的小时部分         |
| `Minute()`      | 返回一个时间的分钟部分         |
| `Month()`       | 返回一个日期的月份部分         |
| `Now()`         | 返回当前日期和时间             |
| `Second()`      | 返回一个时间的秒部分           |
| `Time()`        | 返回一个日期时间的时间部分     |
| `Year()`        | 返回一个日期的年份部分         |

##### 数值处理

| 函数   | 说明   |
| ------ | ------ |
| SIN()  | 正弦   |
| COS()  | 余弦   |
| TAN()  | 正切   |
| ABS()  | 绝对值 |
| SQRT() | 平方根 |
| MOD()  | 余数   |
| EXP()  | 指数   |
| PI()   | 圆周率 |
| RAND() | 随机数 |

##### 汇总

| 函 数     | 说 明            |
| --------- | ---------------- |
| `AVG()`   | 返回某列的平均值 |
| `COUNT()` | 返回某列的行数   |
| `MAX()`   | 返回某列的最大值 |
| `MIN()`   | 返回某列的最小值 |
| `SUM()`   | 返回某列值之和   |

`AVG()` 会忽略 NULL 行。

汇总的时候也可以使用Distinct来统计不重复的值

```sql
SELECT AVG(DISTINCT col1) AS avg_col
FROM mytable
```

#### 数据定义

DDL语法

数据库操作：

1、创建数据库

```sql
create database test;
```

2、删除数据库

```sql
drop database test;
```

3、使用数据库

```sql
use database test;
```

数据表操作

1、建表语句

```sql
CREATE TABLE user (
  id int(10) unsigned NOT NULL COMMENT 'Id',
  username varchar(64) NOT NULL DEFAULT 'default' COMMENT '用户名',
  password varchar(64) NOT NULL DEFAULT 'default' COMMENT '密码',
  email varchar(64) NOT NULL DEFAULT 'default' COMMENT '邮箱'
) COMMENT='用户表';
```

2、根据已有的表创建新表

```sql
create table newTable as select * from table1;
```

3、删除数据表

```sql
drop table test;
```

4、修改数据表

​	添加列

```sql
alter table user add age int(2);
```

​	删除列

```sql
alter table user drop colum age;
```

​	修改列

```sql
alter table user modify colum age tinyint
```

​	添加主键

```sql
alter table use add primary key (id);
```

​	删除主键

```sql
alter table user drop primary key;
```

#### 视图

视图是一种虚拟表，无法建立使用索引，操作和普通表一样。

视图能起到简化操作的作用，避免复杂的联表查询

视图只提供一部分数据，且能够限制用户只读的权限，保证数据安全性

```sql
-- 创建视图
CREATE VIEW top_10_user_view AS
SELECT id, username
FROM user
WHERE id < 10;
-- 删除视图
DROP VIEW top_10_user_view;
```

索引

索引是能够提高数据检索效率的一种数据结构（相当于书前面的目录）

索引又分为聚簇索引和非聚簇索引，

索引可以增加数据的查询效率，但对增删改的速度会造成一定的影响，因为索引本质上是通过平衡二叉树实现，当我们修改或增加一个节点时，需要维护这颗数的平衡性，这需要一定时间。而索引也需要占据一部分磁盘空间。

创建索引

```sql
create index user_index on user(id);
```

添加索引

```sql
alter table user add index user_index(id)
```

创建唯一索引

```sql
create unique index user_index on user(id)
```

删除索引

```sql
alter table user drop index user_index;
```





#### MVCC

MVCC（Multi-Version Concurrency Control）:多版本并发控制，mvcc是一种并发控制机制，用来保证多个事务独写数据时的一致性和隔离性，是通过在每个数据行上维护多个版本来实现的，当事务修改数据时，是直接创建一个数据快照，而不是直接修改原数据。

- 读操作

查询语句一般都是快照读（快照就相当于数据行的一个历史版本，可能有多个历史版本，但是数据库会定时的清理不常用的历史快照），当一个数据有多个快照版本时，会读取不超过当前事务时间的最新的一条快照数据。

- 写操作

进行写操作时，事务会创建一个新版本，将修改的数据写入新版本后保存在数据库中，新版本的数据携带着当前事务的版本号，方便其他事务独写。历史数据仍然存在供其他事务快照读

怎么实现的？

mvcc的实现依赖于隐藏字段、read view和 undo log。Innodb通过数据行的db_trx_id和read view 来判断数据是否可见，若不可见则通过undo log 来找到过往历史版本，在同一个事务中，用户只能看到事务创建read view之前已经提交的修改或事务本身做的修改

**隐藏字段**

db_trx_id:表示最后一次操作该行数据的事务id。

db_roll_ptr：回滚指针，指向改行的undo log。如果数据从未被修改过，则为空

db_row_id:如果该表没有主键或者唯一非空索引时，innodb会使用row_id来生成索引



Read View

> ```
> class ReadView {  
> /* ... */
> private:  
> trx_id_t m_low_limit_id;     /* 大于等于这个 ID 的事务均不可见 */  
> trx_id_t m_up_limit_id;       /* 小于这个 ID 的事务均可见 */      
> trx_id_t m_creator_trx_id;    /* 创建该 Read View 的事务ID */    
> trx_id_t m_low_limit_no;      /* 事务 Number, 小于该 Number 的 Undo Logs 均可以被 Purge */  
> ids_t m_ids;                  /* 创建 Read View 时的活跃事务列表 */  
> m_closed;                     /* 标记 Read View 是否 close */
> }
> ```

![image-20241218144746165](D:\TXT\图片文件\image-20241218144746165.png)

undo log

undo log 有两种用途，一是将数据回滚到修改之前的状态，二是当读数据时如果当前行被占用或者对该事务不可见时，可以通过undo log来读取快照数据。

在innodb中 undo log 分为两种，insert undo log 和 update undo log。

insert undo log ：insert  过程中产生的undo log，由于insert操作只对当前事务可见，对其他事务不可见。所以在事务结束之后产生的undo log可以直接删除

而update undo log 是在delete 和update过程中产生的undo log ，且关系到MVCC机制，所以在事务完成之后不能直接删除。当事务提交之后放入undo log 链表（每次新修改的数据版本都指向上一个版本，所以链首的数据就是最新的数据，链尾的数据是最老的数据），等待purge线程进行最后的删除操作。

![image-20241218151050971](D:\TXT\图片文件\image-20241218151050971.png)

![image-20241218155548819](D:\TXT\图片文件\image-20241218155548819.png)

**RC和RR隔离级别下MVCC的差异**

RC和RR两种隔离级别在多版本控制机制中的差异主要体现在Read View 的生成时机上。

RC隔离级别是每次在select前都会重新生成新的Read View（m_ids列表）

RR隔离级别下是在一个事务中只有在第一次select前才会生成Read View（m_ids列表），

如下图是一个多事务读写的例子：

> 初始数据是 name='菜花'

![image-20241218165912594](D:\TXT\图片文件\image-20241218165912594-1734614711794-3.png)

T4时间：
