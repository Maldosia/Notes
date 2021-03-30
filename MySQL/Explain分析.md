### 语法

```sql
--示例
explain select * from table_name from id = 1;
```

### 输出解释

|               |                                                              |
| ------------- | ------------------------------------------------------------ |
| id            | sql执行顺序的标识，从大到小执行                              |
| select_type   | simple(简单查询)：查询不包括子查询和union；primary(复杂查询)：复杂查询中最外层的select；subquery(子查询)：包含在select中的子查询(不在from内)；derived：包含在from中的子查询，mysql会将结果存放到一个临时表；union、union result |
| table         | 正在访问的那张表                                             |
| type          | 关联类型或访问类型，mysql决定如何查找表中的行；system>const>eq_ref>ref>fulltext>ref_or_full>index_merge>unique_subquery>range>index>all |
| possible_keys | 可能使用哪些索引来查找                                       |
| key           | 实际采用哪个索引来优化                                       |
| key_len       | 在索引里使用的字节数，通过这个值可以算出具体使用了索引的哪个列 |
| ref           | 在key列记录的索引中，表查找值所用的列或常量。常见的有：const、func、null、字段名 |
| rows          | 估计要读取并检测的行数，不是结果集的行数                     |
| extra         | 额外信息                                                     |

#### type说明

```tex
const,system：
	对查询的某部分进行优化并将其转化成一个常量(可以看show warning的结果)。用于primary key或unique key的所有列与常数比较时，所以表最多有一个匹配行，读取1次，速度比较快。
eq_ref:
	primary key或unique key索引的所有部分被连接使用，最多只会返回一条符合条件的记录。简单的select查询不会出现这个type。
ref:
	相比eq_ref，不使用唯一索引，而是使用普通索引或唯一性索引的部分前缀，索引和某个值比较可能会得到多个符合条件的记录。
ref_or_null:
	类似ref，但是可以搜索值为null的记录。
index_merge:
	使用了索引合并的优化方法。
range:
	通常出现在in(),between,>,<等操作中，使用一个索引来给定范围的行。
index;
	扫描索引树，通常比all快。
all:
	全表扫描。
```

#### extra说明

```text
distinct:
	一旦找到了与行相联合匹配的行，就不再搜索。
using index:
	对表的请求列都是在同一索引的部分，返回的列数据只使用了索引中的信息，而没有再去访问表中的行记录，性能高。
using where:
	在存储引擎检索行后再进行过滤。就是先读取整行数据，再按where条件进行检查，丢弃不符合的数据。
using temporary:
	需要创建一张临时表来处理查询，一般需要优化。
using filesort:
	对结果使用一个外部索引排序，而不是按索引次序从表里读取数据。此时mysql会根据连接类型浏览符合条件的记录，并保存关键字和行指针，然后排序关键字并按照顺序检索数据，一般需要优化。
```

