# MySql学习笔记

#### MySql逻辑结构

![image-20210319110513787](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319110513787.png)

#### MyISAM和InnoDB对比

![image-20210319103120027](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319103120027.png)

#### Sql Joins 七种方式

![img](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220112154344.png)



### Mysql show index from

本文主要讲解一下mysql show index 的语法，以 show index from bbs_posts 为例，先看下执行效果如图

![img](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220104182641.png)

#### 1、Table   

表名

#### 2、Non_unique  

如果索引不能包括重复值则为0，如果可以则为1。也就是平时所说的唯一索引。

#### 3、Key_name  

索引名称，如果名字相同则表明是同一个索引，而并不是重复，比如上图中的第二、三条数据，索引名称都是index_fID_lastTime，其实是一个联合索引。

#### 4、Seq_in_index 

索引中的列序列号，从1开始。上图中的二、三条数据，Seq_in_index一个是1一个是2，就是表明在联合索引中的顺序，我们就能推断出联合索引中索引的前后顺序。

#### 5、Column_name 

索引的列名。

#### 6、Collation  

列以什么方式存储在索引中，大概意思就是字符序。在MySQLSHOW INDEX语法中，有值’A’（升序）或NULL（无分类）。默认的类型是utf8_general_ci，这样的大小写不敏感，比如下面两个sql会出现相同的查询结果：

      select * from Table where content = 'Yes'
      select * from Table where content = 'yes'
    
    这样可能不符合你的要求，你需要大小写敏感的情况，你可以修改字段字符集类型，如下sql
    
    alter table bbs_posts modify column content varchar(5000) NOT NULL collate utf8_bin;
    
    这样修改以后就OK了。

#### 7、Cardinality  

基数的意思，表示索引中唯一值的数目的估计值。基数根据被存储为整数的统计数据来计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL使用该索引的机会就越大。我们知道某个字段的重复值越少越适合建索引，所以我们一般都是根据Cardinality来判断索引是否具有高选择性，如果这个值非常小，那就需要重新评估这个字段是否适合建立索引。因为MySQL数据库中有各种不同的存储引擎，而每种存储引擎对于B+树索引的实现又各不相同。所以对Cardinality统计时放在存储引擎层进行的，至于它是如何统计更新的在这里就不再做更深入的介绍了。

#### 8、Sub_part 

前置索引的意思，如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为NULL。如上图所示，除了index_content那行显示4外，其他的都是NULL，表明index_content是一个长度为4的前置索引。对于BLOB，TEXT，或者很长的VARCHAR类型的列，必须使用前缀索引，因为MySQL不允许索引这些列的完整长度，这会让索引变得大且慢。选择长度的诀窍在于要选择足够长的前缀以保证较高的选择性，同时又不能太长以便节约空间。下面是计算前置索引长度的一般方法：    

      select count(distinct left(content,3))/count(*) from bbs_posts as sel3
    
      select count(distinct left(content,4))/count(*) from bbs_posts as sel4
    
      select count(distinct left(content,5))/count(*) from bbs_posts as sel5
    
     最后算出来那个长度的基数接近完整列的选择行就OK了，完整列 select count(distinct content)/count(*) from bbs_posts

#### 9、Packed  

指示关键字如何被压缩。如果没有被压缩，则为NULL。压缩一般包括压缩传输协议、压缩列解决方案和压缩表解决方案。

#### 10、Null 

如果列含有NULL，则含有YES。比如上图中的lastOperateTime其中就包含null，我们知道建立索引的列是不允许为Null的，单列索引不存Null值，复合索引不存全为Null的值，如果列允许为Null，可能会得到“不符合预期”的结果集。我这里是为了更好的给大家展示故意构造了一些数据。

#### 11、Index_type 

索引类型，Mysql目前主要有以下几种索引类型：FULLTEXT，HASH，BTREE，RTREE。    

    1). FULLTEXT
    即为全文索引，目前只有MyISAM引擎支持。其可以在CREATE TABLE ，ALTER TABLE ，CREATE INDEX 使用，不过目前只有 CHAR、VARCHAR ，TEXT 列上可以创建全文索引。全文索引并不是和MyISAM一起诞生的，它的出现是为了解决WHERE name LIKE “%word%"这类针对文本的模糊查询效率较低的问题。
    2). HASH
    由于HASH的唯一（几乎100%的唯一）及类似键值对的形式，很适合作为索引。    HASH索引可以一次定位，不需要像树形索引那样逐层查找,因此具有极高的效率。但是，这种高效是有条件的，即只在“=”和“in”条件下高效，对于范围查询、排序及组合索引仍然效率不高。
    3). BTREE    BTREE索引就是一种将索引值按一定的算法，存入一个树形的数据结构中（二叉树），每次查询都是从树的入口root开始，依次遍历node，获取leaf。这是MySQL里默认和最常用的索引类型。
    4). RTREE
    RTREE在MySQL很少使用，仅支持geometry数据类型，支持该类型的存储引擎只有MyISAM、BDb、InnoDb、NDb、Archive几种。相对于BTREE，RTREE的优势在于范围查找。

#### 12、 Comment Index_comment  

注释的意思。
————————————————
版权声明：本文为CSDN博主「阿康_徐来」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/cdx_akang/article/details/83900427



### MySql索引

````txt
索引存储在硬盘，能加快查询的速度，但会减慢更新、插入、删除的速度（需要操作数据和索引）
````



![image-20210319111313578](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319111313578.png)

#### 索引结构

##### B-Tree

每个节点都有指向数据的指针（红色）

![image-20210319111508594](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319111508594.png)

##### B+ Tree

Mysql选用这个，效率虽然没有B-Tree高（B-Tree每次从硬盘读取的数据部分较少，可能导致缺页，造成多次IO），但是一个节点存储更多数据，节省空间，B+Tree需要遍历到叶子才有指向数据的指针（紫色）

![image-20210319112158868](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319112158868.png)



#### 聚簇索引和非聚簇索引

InnoDB主键是聚簇索引（例如找1-6的数，只需要找1和6，其余的在中间），非主键是非聚簇索引（需要全部遍历）

![image-20210319113655051](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319113655051.png)



#### 索引分类

![image-20210319114715322](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319114715322.png)

#### 建索引的时机

![image-20210319115324561](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319115324561.png)

#### Explain SQL性能分析

![image-20210319120258607](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319120258607.png)

![image-20210320113924764](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320113924764.png)

##### 名称字段解释

###### id

![image-20210319120416233](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319120416233.png)

###### 查询类型

![image-20210319120731513](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319120731513.png)

###### 访问类型

![image-20210320110536171](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320110536171.png)

![image-20210320111119553](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320111119553.png)

###### 额外

![image-20210320113633081](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320113633081.png)

### 优化

#### 复合索引的结构

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210320154449.png)

```
最左前缀法则 
说明：索引层次按照建立索引的顺序分层，如：
create index idx_age_deptid_name on emp(age, deptid, name);
则查询的时候where条件是age和depId都会用到索引，而如果查询条件是age和name则只会用到age索引（跳了一层），而如果查询条件是depid和name，则一个索引都用不到。
```

#### 单表

##### 失效情况



![image-20210320162345746](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320162345746.png)

1. 不符合最左前缀原则
2. 给字段加函数、计算、类型转换会照成索引失效。如LEFT(age， 3) = ‘abc’
3. 范围查询右边（按建索引时的顺序）的字段索引失效。建索引时把可能范围查询的字段（金额、日期）放在最右边。
4.  不等于会使索引失效（联想索引树结构）。
5. is not null索引失效。
6. like "%ab"会照成索引失效，但是“ab%”不会。
7. name=123会照成索引失效，mysql会自动将数字123转化为字符串，不符合第2条规则，照成索引失效。JavaBean成员类型必须和数据库类型一致。

##### 建议

![image-20210320162959692](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320162959692.png)



#### 多表

##### 关联查询优化

A left join B，A为驱动表（应该尽量把数据量较小的表作为驱动表）

<img src="https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320163807932.png" alt="image-20210320163807932" style="zoom:50%;" />



A inner join B，谁有索引谁为被驱动表（MySql自动选择）

##### 建议

![image-20210320165745997](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320165745997.png)

##### 子查询优化

![image-20210320170314072](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210320170314072.png)

#### Order By

1. 无过滤条件，无论有没有索引，必排序（Using filesort），使用limit也可以用到索引。
2. 与索引顺序不一致，排序 
3. 方向方（一个升序、一个降序），排序

![image-20210321162520531](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321162520531.png)

#### Union

```
Union：对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序；
Union All：对两个结果集进行并集操作，包括重复行，不进行排序；
Intersect：对两个结果集进行交集操作，不包括重复行，同时进行默认规则的排序；
Minus：对两个结果集进行差操作，不包括重复行，同时进行默认规则的排序。
```

#### 覆盖索引（不要select *）

```sql
-- 如果查询条件用不上索引，如
select * from emp where age <> 20;
-- 则使用覆盖索引，即用具体的字段名代替*
select id,name,from emp where age <> 20;
```

#### STRAIGHT_JOIN

```txt
功能与inner join一致，但是A STRAIGHT_JOIN B明确左边(A)是驱动表，右边(B)是被驱动表。
使用注意事项：
1、明确功能
2、明确两个表的数量级
```

#### 慢查询日志

```sql
-- 慢查询日志（需要手动开启）里记录了sql执行时间大于最长时间的sql的相关信息
show variables like '%slow_query_log%';
show variables like '%long_query_time%';
-- 使用mysqldumpslow命名进行分析
```

#### 全局日志

记录所有操作，当遇到诡异事情时，无法定位，可尝试打开

#### show processlist

展现进程

![image-20220225005923787](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220225005930.png)

```sql
-- 出现死锁
show processlist
kill 进程号
```

### 视图

![image-20210321205849517](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321205849517.png)

### 主从复制

#### 复制的基本原理

![image-20210321210734314](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321210734314.png)

#### 主从复制配置

##### 主机配置过程

my.ini

![image-20210321215305823](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321215305823.png)

##### bin_log类型

![image-20210321215209157](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321215209157.png)

##### 从机配置

my.cnf

![image-20210321215559904](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321215559904.png)

##### 关闭防火墙

![image-20210321215800603](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321215800603.png)

##### 建立用户

![image-20210321221548309](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321221548309.png)



### MyCat

```txt
数据库中间件，在数据库和java程序之间，对于java程序来说，mycat就是逻辑数据库。
对于读写分离，java程序需要配置不同的数据源，且要对读写进行判断，十分麻烦。
使用mycat，可以对java程序屏蔽不同数据源。
```

#### 读写分离

![image-20210322163153575](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210322163153575.png)

#### 数据分片

![image-20210322163254554](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210322163254554.png)

![image-20210322163312919](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210322163312919.png)

#### 原理

![image-20210322163433133](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210322163433133.png)

![image-20210322165026109](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210322165026109.png)

![image-20210322173901734](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210322173901734.png)

#### 开启读写分离

![image-20210322180448017](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210322180448017.png)

#### 分库

```
分库原则，将不会有join关系的表进行分库。
```

![image-20210323161033234](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323161033234.png)

![image-20210323145831680](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323145831680.png)

```
准备n个干净的库，创建好在不同的主机，然后修改mycat的schema.xml文件配置主机和对应的节点。然后连接mycat，创建表，实现分库。
```

#### 分表

![image-20210323170339995](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323170339995.png)

![image-20210323161050842](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323161050842.png)

![image-20210323165509179](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323165509179.png)

#### 全局序列ID

![image-20210323174120867](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323174120867.png)