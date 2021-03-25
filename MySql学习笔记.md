# MySql学习笔记

#### MySql逻辑结构

![image-20210319110513787](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319110513787.png)

#### MyISAM和InnoDB对比

![image-20210319103120027](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319103120027.png)

#### Sql Joins 七种方式

![image-20210319104227707](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210319104227707.png)



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