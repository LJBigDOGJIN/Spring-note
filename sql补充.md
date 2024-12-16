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