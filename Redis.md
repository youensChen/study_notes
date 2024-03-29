# Redis

## 目录

![image-20210327151051617](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327151051.png)

![image-20210327151113996](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327151114.png)

## NoSql概述

> 1、MySql单机时代

![image-20210327134804033](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327134804.png)

> 2、Memcached（缓存）+Mysql+垂直拆分（读写分离）

![image-20210327134906580](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327134906.png)

> 3、分库分表+水平拆分+Mysql集群

![image-20210327135219765](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327135219.png)

> 4、如今的年代

数据爆炸，大数据时代，每秒产出非常大的数据。关系型数据库已经无法满足需求。

![image-20210327140127554](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327140127.png)

> 5、目前基本的互联网项目

![image-20210327140523883](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327140523.png)

### 为什么要用NoSql

 ![image-20210327140722722](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327140722.png)

### 什么是NoSql

>NoSql（Not Only Sql）
>
>泛指非关系型数据库

![image-20210327141437399](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327141437.png)

### NoSql特点

![image-20210327141943137](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327141943.png)

![image-20210327142143970](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327142144.png)

![image-20210327142457844](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327142457.png)

### 阿里巴巴演进分析

![image-20210327142940836](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327142940.png)

![image-20210327143500376](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327143500.png)

![image-20210327144717210](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327144717.png)



![image-20210327145017284](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327145017.png)

![image-20210327144915728](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327144915.png)

![image-20210327145331146](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327145331.png)

### NoSQL的四大分类

![image-20210327150151790](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327150151.png)

![image-20210327150213690](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327150213.png)

![image-20210327150329220](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327150329.png)



#### 四者对比

![image-20210327150425903](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327150426.png)

## Redis入门

### 概述

![image-20210327151350004](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327151350.png)

![image-20210327151549681](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327151549.png)

### 安装

#### **Windows安装**

下载

解压

开启Redis服务

![image-20210327152730107](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327152730.png)

使用客户端连接

![image-20210327152649543](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327152649.png)

#### **Linux安装**

1、下载安装包

2、解压

![image-20210327155812536](C:\Users\Youens\AppData\Roaming\Typora\typora-user-images\image-20210327155812536.png)

3、环境安装

```bash
sudo yum install gcc-c++

sudo make

sudo make install
```

![image-20210327160454001](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327160454.png)

4、默认安装路径 ` /user/local/bin`

![image-20210327160821669](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327160821.png)

5、备份配置文件

![image-20210327161036337](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327161036.png)

6、redis默认是前台启动，修改配置文件 

![image-20210327161326062](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327161326.png)

7、启动redis服务（指定配置文件）

![image-20210327161530279](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327161530.png)

8、连接服务

![image-20210327161859010](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327161859.png)

9、查看redis的进程是否开启

![image-20210327162053131](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327162053.png)

10、关闭redis服务

![image-20210327164202588](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327164208.png)

## 性能测试

**redis-benchmark**

![image-20210327164529192](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327164529.png)

测试

```bash
redis-benchmark -h localhost -p 6379 -c 100 -n 10000
```

![image-20210327164931873](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327164931.png)

### 分析

![image-20210327165137806](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327165137.png)

## 基础命令

默认有16个数据库，默认 使用第0个，使用select切换数据库 

![image-20210327200351905](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327200352.png)

### 数据库大小

```bash
27.0.0.1:6379> ping
PONG
127.0.0.1:6379> dbsize # 数据库大小
(integer) 5
127.0.0.1:6379> select 3 # 切换数据库
OK
127.0.0.1:6379[3]> dbsize
(integer) 0
```

### 查看所有keys

```bash
127.0.0.1:6379> keys *
1) "mylist"
2) "myset:__rand_int__"
3) "counter:__rand_int__"
4) "name"
5) "key:__rand_int__"

```

### 清除当前数据库  `flushdb`  

```bash
127.0.0.1:6379> keys *
1) "mylist"
2) "myset:__rand_int__"
3) "counter:__rand_int__"
4) "name"
5) "key:__rand_int__"
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> dbsize
(integer) 0
127.0.0.1:6379> 

```

### 清除所有数据库`flushall`

```bash
127.0.0.1:6379> set name youens
OK
127.0.0.1:6379> get name
"youens"
127.0.0.1:6379> flushall
OK
127.0.0.1:6379> get name
(nil)
127.0.0.1:6379> 

```

### 判断某个key是否存在`exists`

```bash
127.0.0.1:6379> set name youens
OK
127.0.0.1:6379> exists name
(integer) 1
127.0.0.1:6379> exists age
(integer) 0
127.0.0.1:6379> 

```

### 移动key到其他数据库`move key db`

```bash
127.0.0.1:6379> move name 1
(integer) 1
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
1) "name"
127.0.0.1:6379[1]> 

```

### 设置key过期`expire key second`

```bash
127.0.0.1:6379[1]> expire age 10
(integer) 1
127.0.0.1:6379[1]> ttl age
(integer) 5
127.0.0.1:6379[1]> ttl age
(integer) 4
127.0.0.1:6379[1]> ttl age
(integer) -2
127.0.0.1:6379[1]> ttl age
(integer) -2
127.0.0.1:6379[1]> ttl age
(integer) -2
127.0.0.1:6379[1]> keys *
1) "name"

```

### 查看key数据类型`type`

```bash
127.0.0.1:6379[1]> keys *
1) "name"
127.0.0.1:6379[1]> type name
string
127.0.0.1:6379[1]> get name
"youens"

```



### 官网文档

![image-20210327204618970](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327204619.png)



![image-20210327202311054](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327202311.png)

![image-20210327202659169](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327202659.png)

## 五大数据类型

![image-20210327203305521](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327203305.png)

### String

#### **应⽤场景** 

⼀般常⽤在需要计数的场景，⽐如⽤户的访问次数、热点⽂章的点赞转发数量等

#### 连接append

```bash
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> append k1 hello # 如果k1不存在，则创建
(integer) 7
127.0.0.1:6379> get k1
"v1hello"

```

#### 长度strlen

```bash
get k1
"v1hello"
127.0.0.1:6379> strlen k1
(integer) 7
127.0.0.1:6379> append k1 world
(integer) 12
127.0.0.1:6379> strlen k1
(integer) 12
127.0.0.1:6379> get k1
"v1helloworld"

```

#### 自增incr

```bash
set count 0
OK
127.0.0.1:6379> incr count
(integer) 1
127.0.0.1:6379> get count
"1"

```

#### 自减decr

```bash
get count
"1"
127.0.0.1:6379> decr count
(integer) 0
127.0.0.1:6379> get count
"0"

```

#### 步长增减incrby 

```bash
get count
"0"
127.0.0.1:6379> incrby count 10
(integer) 10
127.0.0.1:6379> get count
"10"
127.0.0.1:6379> decrby count 5
(integer) 5
127.0.0.1:6379> get count
"5"

```

#### 子串getrange

```bash
set name "hello,redis"
OK
127.0.0.1:6379> get name
"hello,redis"
127.0.0.1:6379> GETRANGE name 0 2
"hel"
GETRANGE name 0 -1 # 取整个字符串
"hello,redis"

```

#### 替换setrange

```bash
set k1 abcdefg
OK
127.0.0.1:6379> SETRANGE k1 1 xx
(integer) 7
127.0.0.1:6379> get k1
"axxdefg
```

#### 设置过期时间setex(set with expire)

```bash
SETEX k2 10 youens
OK
127.0.0.1:6379> ttl k2
(integer) 6

```

#### 不存在则创建setnx(set if not exits)

```bash
# 在分布式锁中常常使用
set name youens
OK
127.0.0.1:6379> SETNX name redis
(integer) 0
127.0.0.1:6379> get name
"youens"
127.0.0.1:6379> SETNX age 11
(integer) 1
127.0.0.1:6379> get age
"11"

```

#### 批量创建/获取

```bash
127.0.0.1:6379> MSET k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k2"
3) "k1"
127.0.0.1:6379> MGET k1 k2 k3
1) "v1"
2) "v2"
3) "v3"

MSETNX k1 v1 k4 v4 # 批量创建，若一个失败，则所有失败（原子性）
(integer) 0
127.0.0.1:6379> get k4
(nil)
# 对象操作
# 这里的key是一个巧妙的设计user:{id}:{filed}
127.0.0.1:6379> MSET user:1:name youens user:1:age 12
OK
127.0.0.1:6379> MGET user:1:name user:1:age
1) "youens"
2) "12"
127.0.0.1:6379> keys *
1) "k1"
2) "k2"
3) "k3"
4) "user:1:age"
5) "user:1:name"



```

#### 先获取再赋值getset

```bash
GETSET name youens
(nil)
127.0.0.1:6379> get name
"youens"
127.0.0.1:6379> GETSET name chen
"youens"
127.0.0.1:6379> get name
"chen"

```

### List

> 在redis里，llist可以做栈、队列、阻塞队列
>
> 所有的操作list的命令都是 l 开头的
>
> **应⽤场景** 发布与订阅或者说消息队列、慢查询

#### 基本操作

```bash
#   L---------------------R  双端队列dequeue

127.0.0.1:6379> LPUSH list one two # 创建并设置值（栈）
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1 # 获取所有值
1) "three"
2) "two"
3) "one"
LRANGE list 0 1
1) "three"
2) "two"
127.0.0.1:6379> LPOP list # 弹出一个值（栈）
"three"
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> TYPE list
list

# 从尾部插入（队列）
127.0.0.1:6379> RPUSH list four
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "four"
# 从尾部弹出
127.0.0.1:6379> RPOP list
"four"


# 根据下标读取
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LINDEX list 1
"two"
127.0.0.1:6379> LINDEX list 0
"three"
# 根据下标设置
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LSET list 1 change
OK
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "change"
3) "one"



# 获取list的长度
127.0.0.1:6379> LLEN list
(integer) 3



# 移除值
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
127.0.0.1:6379> LREM list 1 one 
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> LREM list 2 three
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1
1) "two"


# 截断操作
127.0.0.1:6379> LRANGE list 0 -1
1) "hello4"
2) "hello3"
3) "hello2"
4) "hello1"
127.0.0.1:6379> LTRIM list 1 2 # 通过下标截取，原来的list会被改变
OK
127.0.0.1:6379> LRANGE list 0 -1
1) "hello3"
2) "hello2"

# 弹出并压入另一个list
127.0.0.1:6379> LRANGE list 0 -1
1) "hello3"
2) "hello2"
127.0.0.1:6379> RPOPLPUSH list list1
"hello2"
127.0.0.1:6379> LRANGE list 0 -1
1) "hello3"
127.0.0.1:6379> LRANGE list1 0 -1
1) "hello2"


# 插入操作
127.0.0.1:6379> LPUSH list one two three
(integer) 3
127.0.0.1:6379> LINSERT list before one zero
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "zero"
4) "one"
127.0.0.1:6379> LINSERT list after one insert
(integer) 5
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "zero"
4) "one"
5) "insert"

```

![image-20210327221304676](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327221304.png)

### Set

>Set的命令都已S开头，对比List不同的是Set里面不能有相同的数据
>
> **应⽤场景**: 需要存放的数据**不能重复**以及需要获取多个数据源**交集**和**并集**等场景

```bash
127.0.0.1:6379> SADD myset 1 2 3 4 # 创建并添加
(integer) 4
127.0.0.1:6379> SRANDMEMBER myset 2
1) "4"
2) "3"
127.0.0.1:6379> SRANDMEMBER myset 2 # 随机获取指定个数
1) "4"
2) "2"
127.0.0.1:6379> SRANDMEMBER myset 2
1) "3"
2) "1"
127.0.0.1:6379> SMEMBERS myset # 取全部
1) "1"
2) "2"
3) "3"
4) "4"


127.0.0.1:6379> SISMEMBER myset 2 # 判断是否存在
(integer) 1
127.0.0.1:6379> SISMEMBER myset 6
(integer) 0


127.0.0.1:6379> SCARD myset # 集合元素个数
(integer) 4
127.0.0.1:6379> SADD myset 5
(integer) 1
127.0.0.1:6379> SCARD myset
(integer) 5


127.0.0.1:6379> SMEMBERS myset 
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
127.0.0.1:6379> SREM myset 3 # 移除
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "1"
2) "2"
3) "4"
4) "5"


127.0.0.1:6379> SMEMBERS myset
1) "1"
2) "2"
3) "4"
4) "5"
127.0.0.1:6379> SPOP myset 1 # 随机删除指定数目的元素
1) "2"
127.0.0.1:6379> SMEMBERS myset
1) "1"
2) "4"
3) "5"


127.0.0.1:6379> SMOVE myset set2 4 # 指定元素移动到另外一个集合
(integer) 1 
127.0.0.1:6379> SMEMBERS myset
1) "1"
2) "5"
127.0.0.1:6379> SMEMBERS set2
1) "4"

############### 集合差集、并集、交集
127.0.0.1:6379> SADD set1 1 2 3 4
(integer) 4
127.0.0.1:6379> SADD set2 3 4 5 6
(integer) 4
127.0.0.1:6379> SMEMBERS set1
1) "1"
2) "2"
3) "3"
4) "4"
127.0.0.1:6379> SMEMBERS set2
1) "3"
2) "4"
3) "5"
4) "6"
127.0.0.1:6379> SDIFF set1 set2  # 差集
1) "1"
2) "2"

127.0.0.1:6379> SINTER set1 set2 # 交集
1) "3"
2) "4"

127.0.0.1:6379> SUNION set1 set2 # 并集
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
6) "6"


```



### Hash

> 类似java里的map，所有的命令都已h开头
>
> **应⽤场景**: 系统中对象数据的存储

```bash
127.0.0.1:6379> HSET person name youens # 创建设置一个k-v
(integer) 1
127.0.0.1:6379> HSET person age 22
(integer) 1
127.0.0.1:6379> HSET person interest beauty
(integer) 1
127.0.0.1:6379> HGET person name # 获取一个k的v
"youens"

127.0.0.1:6379> HMSET person height 170 weight 62 # 一次性设置多个k-v
OK
127.0.0.1:6379> HMGET person name age height weight # 一次性获取多个v
1) "youens"
2) "22"
3) "170"
4) "62"


127.0.0.1:6379> HGETALL person # 获取所有键值对（每两个一组）
 1) "name"
 2) "youens"
 3) "age"
 4) "22"
 5) "interest"
 6) "beauty"
 7) "height"
 8) "170"
 9) "weight"
10) "62"

127.0.0.1:6379> HKEYS person # 获得所有key
1) "name"
2) "age"
3) "height"
4) "weight"
127.0.0.1:6379> HVALS person # 获得所有value
1) "youens"
2) "22"
3) "170"
4) "62"




127.0.0.1:6379> HDEL person interest # 删除k-v
(integer) 1
127.0.0.1:6379> HGETALL person
1) "name"
2) "youens"
3) "age"
4) "22"
5) "height"
6) "170"
7) "weight"
8) "62"

127.0.0.1:6379> HLEN person # 键值对数量
(integer) 4


127.0.0.1:6379> HEXISTS person name # 判断某个k是否存在
(integer) 1
127.0.0.1:6379> HEXISTS person asfsf
(integer) 0


127.0.0.1:6379> HINCRBY person height 10 # 增加指定值
(integer) 180
127.0.0.1:6379> HINCRBY person height -5
(integer) 175


127.0.0.1:6379> HSETNX person name chen # 不存在则创建
(integer) 0
127.0.0.1:6379> HSETNX person love cjy
(integer) 1

```

>hash更适合存储对象

### Zset

> 对比set，Zset是有序的集合，增加了⼀个权重参数 score，使得集合中的元素能够按 score
>
> 进⾏有序排列,类似java里的 TreeSet
>
> 所有命令以 z 开头
>
> **应⽤场景：**需要对数据根据某个**权重**进⾏**排序**的场景。⽐如在直播系统中，实时排⾏信息包
>
> 含直播间在线⽤户列表，各种礼物排⾏榜，弹幕消息（可以理解为按消息维度的消息排⾏
>
> 榜）等信息

```bash
127.0.0.1:6379> ZADD zset 1 one 2 two 3 three # 创建设置
(integer) 3
127.0.0.1:6379> ZRANGE zset 0 -1 # 获取所有值
1) "one"
2) "two"
3) "three"
127.0.0.1:6379> ZREM zset one # 删除指定的值
(integer) 1
127.0.0.1:6379> ZRANGE zset 0 -1
1) "two"
2) "three"


127.0.0.1:6379> ZRANGEBYSCORE zset -inf +inf # 根据分数对范围在[-inf, +inf]范围内元素进行排序
1) "two"
2) "three"
127.0.0.1:6379> ZRANGEBYSCORE zset -inf +inf withscores # 带上分数
1) "two"
2) "2"
3) "three"
4) "3"
127.0.0.1:6379> ZREVRANGE zset 0 -1 withscores # 降序
1) "three" 
2) "3"
3) "two"
4) "2"



127.0.0.1:6379> ZCARD zset # 获取长度
(integer) 2

127.0.0.1:6379> ZCOUNT zset 2 3 # 获取指定score之间的个数
(integer) 2
127.0.0.1:6379> ZCOUNT zset 1 2
(integer) 1

```

![image-20210328154451741](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328154451.png)

## 三种特殊数据类型

### geospatial 地理空间

![image-20210328160441189](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328160441.png)

![image-20210328161144506](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328161144.png)

```bash
127.0.0.1:6379> GEOADD dongguan 113.882535 22.906888 dgut # 添加城市地理数据
(integer) 1 
127.0.0.1:6379> GEOADD dongguan 113.894623 22.923591 songhu
(integer) 1
127.0.0.1:6379> GEOADD dongguan 113.863117 22.925935 donghua
(integer) 1


127.0.0.1:6379> GEOPOS dongguan dgut songhu # 获取指定位置的经纬度
1) 1) "113.88253659009933472"
   2) "22.90688725210608112"
2) 1) "113.89462262392044067"
   2) "22.92359106454612316"


127.0.0.1:6379> GEODIST dongguan dgut songhu km # 获取两位置的直线距离
"2.2327"
127.0.0.1:6379> GEODIST dongguan dgut songhu m
"2232.6967"
127.0.0.1:6379> GEODIST dongguan dgut donghua km
"2.9063"


127.0.0.1:6379> GEORADIUS dongguan 114 23 10 km # 以某个位置为圆心，指定半径在内有的地方（附近的人）
(empty list or set)
127.0.0.1:6379> GEORADIUS dongguan 114 23 100 km
1) "dgut"
2) "songhu"
3) "donghua"
127.0.0.1:6379> GEORADIUS dongguan 114 23 100 km withdist
1) 1) "dgut"
   2) "15.8743"
2) 1) "songhu"
   2) "13.7367"
3) 1) "donghua"
   2) "16.2598"
127.0.0.1:6379> GEORADIUS dongguan 114 23 100 km withdist count 1 # 指定个数
1) 1) "songhu"
   2) "13.7367"


127.0.0.1:6379> GEORADIUSBYMEMBER dongguan dgut 2 km # 以指定地点为圆心，查找附近的位置
1) "dgut"
127.0.0.1:6379> GEORADIUSBYMEMBER dongguan dgut 3 km
1) "dgut"
2) "songhu"
3) "donghua"

```

![image-20210328163045322](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328163045.png)

> geospatial底层使用Zset来实现，所有可以使用Zset的命令来操作geospatial

```bash
127.0.0.1:6379> ZRANGE dongguan 0 -1
1) "dgut"
2) "songhu"
3) "donghua"
127.0.0.1:6379> ZREM dongguan songhu
(integer) 1
127.0.0.1:6379> ZRANGE dongguan 0 -1
1) "dgut"
2) "donghua"

```



### hyperloglogs

![image-20210328164546239](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328164546.png)

> 所有命令都以PF开头

```bash
127.0.0.1:6379> PFADD mykey a b c d e f a # 创建
(integer) 1
127.0.0.1:6379> PFCOUNT mykey # 基数(不一样)长度
(integer) 6 
127.0.0.1:6379> 

127.0.0.1:6379> PFADD mykey1 a f b d k c 
(integer) 1
127.0.0.1:6379> PFCOUNT mykey2
(integer) 7
127.0.0.1:6379> PFCOUNT mykey1
(integer) 6
127.0.0.1:6379> PFMERGE mykey2 mykey mykey1 # 合并mykey和mykey1到mykey2
OK
127.0.0.1:6379> PFCOUNT mykey2
(integer) 7


```



### bitmap

![image-20210328165815631](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328165815.png)



```bash
127.0.0.1:6379> SETBIT bitmap 0 0 # 创建设置
(integer) 0
127.0.0.1:6379> SETBIT bitmap 1 0
(integer) 0
127.0.0.1:6379> SETBIT bitmap 2 1
(integer) 0
127.0.0.1:6379> GETBIT bitmao 1 # 获取
(integer) 0
127.0.0.1:6379> BITCOUNT bitmap  #统计1的个数
(integer) 1
127.0.0.1:6379> BITCOUNT bitmap 0 1 #范围统计1的个数
(integer) 1

```



## 事务

![image-20210328194740820](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328194740.png)

```bash
127.0.0.1:6379> MULTI # 开启事务 一致性、顺序性、排他性
OK
127.0.0.1:6379> set k1 v1 # 入队，并没有执行
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> MSET k3 v3 k4 v4
QUEUED
127.0.0.1:6379> MGET k1 k2 k4
QUEUED
127.0.0.1:6379> EXEC # 执行队列中的所有命令同时关闭事务
1) OK
2) OK
3) OK
4) 1) "v1"
   2) "v2"
   3) "v4"


127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set k5 v5
QUEUED
127.0.0.1:6379> set k6 v6
QUEUED
127.0.0.1:6379> DISCARD # 放弃事务
OK
127.0.0.1:6379> get k6
(nil)


################  命令错误（编译型异常），所有指令都不会执行
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 # 错误的指令
(error) ERR wrong number of arguments for 'set' command # 命令报错
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> EXEC
(error) EXECABORT Transaction discarded because of previous errors.# 事务报错


################  命令错误（运行时异常），没有错误的指令都会执行，而错误的指令会抛异常（没有原子性）
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> INCR k1 # 无法对字符串进行加一操作
QUEUED
127.0.0.1:6379> set k2 v2 # 正常执行
QUEUED
127.0.0.1:6379> get k2 # 正常执行
QUEUED
127.0.0.1:6379> EXEC
1) (error) ERR value is not an integer or out of range
2) OK
3) "v2"

```

## 锁

![image-20210328200853641](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328200853.png)

```bash
127.0.0.1:6379> MSET money 100 coast 0
OK
127.0.0.1:6379> WATCH money  # 监视money
OK
127.0.0.1:6379> MULTI 
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY coast 20
QUEUED
127.0.0.1:6379> EXEC ###正常执行成功
1) (integer) 80
2) (integer) 20
127.0.0.1:6379> MGET money coast
1) "80"
2) "20"

#############模拟多线程，watch加锁（乐观锁），如果有其他线程修改了该线程要操作的值，事务失败。
127.0.0.1:6379> WATCH money #监视money（拿到version）
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY coast 20
QUEUED
###################### 此时，另外一个线程执行
            127.0.0.1:6379> get money #（已修改version）
            "80"
            127.0.0.1:6379> set money 1000
            OK
######################

127.0.0.1:6379> EXEC # 执行之前其他线程已经修改了money（对比version，发现不一样了）
(nil)


```

> 事务失败，从新开始		

![image-20210328203215458](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328203215.png)

## Jedis

> 1、导入坐标

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency>

<!--fastjson-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.62</version>
</dependency>
```

2、连接测试

```java
public class RedisTest {
    public static void main(String[] args) {
        // 1、new Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        // jedis的方法就是之前学的所有命令
        System.out.println(jedis.ping());
    }
}
```

![image-20210328211515744](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328211515.png)

![image-20210328211649557](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328211649.png)

### string

![image-20210328212053820](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328212053.png)

### list

![image-20210328212304714](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328212304.png)

### set

![image-20210328212605795](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328212606.png)

### hash

![image-20210328212749657](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328212749.png)

### 事务

![image-20210328213304159](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328213304.png)

![image-20210328213342561](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328213342.png)



## SpringBoot 整合

![image-20210328214458481](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328214458.png)

![image-20210328215220375](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328215220.png)

### 步骤



1、导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2、配置

```properties
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

3、测试

![image-20210328220547253](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328220547.png)

### 源码



![image-20210328220534811](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328220535.png)

![image-20210329152959242](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329152959.png)

### 自定义RedisTemplate

![image-20210329153504556](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329153504.png)

###  操作redis工具类

![image-20210329154356785](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329154356.png)



## Redis.conf详解

### 单位

![image-20210329155857547](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329155857.png)

单位对大小写不敏感



### INCLUDES包含其他文件

![image-20210329160343900](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329160344.png)

### NETWORK网络

![image-20210329160456857](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329160456.png)



### GENERAL通用

![image-20210329160738313](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329160738.png)



### SNAPSHOTTING快照

![image-20210329161144114](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329161144.png)



### REPLICATION 主从复制相关



### SECURITY安全

![image-20210329161643008](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329161643.png)

### CLIENTS客户端

```bash
maxclients 10000 # 客户端最大连接数
```



### MEMORY MANAGEMENT内存管理

![image-20210329162026616](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329162026.png)



### APPEND ONLY 模式 aof配置

![image-20210329162345812](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329162345.png)



## Redis持久化

![image-20210329162638579](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329162638.png)

### RDB（Redis Database）

![image-20210329162837378](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329162837.png)

![image-20210329163032145](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329163032.png)

==rdb保存的文件是dump.rdb==

==aof保存的文件是appendonly.aof==

#### rdb文件产生

![image-20210329164037512](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329164037.png)

#### 读取rdb文件

![image-20210329164250328](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329164250.png)

#### 优缺点

![image-20210329164444277](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329164444.png)



### AOF（Append Only File）

![image-20210329165455799](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329165455.png)

![image-20210329165524009](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329165524.png)

![image-20210329165602013](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329165602.png)

![image-20210329170121650](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329170121.png)

![image-20210329170202447](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329170202.png)

![image-20210329170427199](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329170427.png)

重新规则说明

![image-20210329170918007](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329170918.png)

#### 扩展

![image-20210329171232271](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329171232.png)



## Redis发布订阅

![image-20210329194600635](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329194600.png)

![image-20210329194709761](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329194709.png)

### 命令

![image-20210329194828069](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329194828.png)

```bash
127.0.0.1:6379> SUBSCRIBE youens # 订阅youens
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "youens"
3) (integer) 1
################发布者发布
	127.0.0.1:6379> PUBLISH youens 'hello, my follower' # 往youens频道发布消息
	(integer) 1
# 接收到发布者发布的消息
1) "message"
2) "youens"
3) "hello, my follower"

```

![image-20210329195517912](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329195518.png)

### 原理

![image-20210329195633509](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329195633.png)

![image-20210329200136487](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329200136.png)



## Redis主从复制

### 概念

![image-20210329200243032](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329200243.png)

![image-20210329200356751](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329200356.png)

### 环境配置

![image-20210329202356083](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329202356.png)

![image-20210329202734541](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329202734.png)

### 一主二从

默认情况下，Redis每个节点都是主机。配置主从复制只需要配置从机即可

```bash
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379 # 认老大
OK
127.0.0.1:6380> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:up
master_last_io_seconds_ago:3
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:21725978fc7e3e9967becbf1771d5f92ce33e229
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14

# 在主机中查看
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=127.0.0.1,port=6380,state=online,offset=98,lag=1
master_replid:21725978fc7e3e9967becbf1771d5f92ce33e229
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:98
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:98

```

配置完成后主机info

![image-20210329203629678](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329203629.png)

==在命令中配置是暂时的，使用配置文件配置主从复制才是永久的==

![image-20210329203921183](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329203921.png)

### 细节

![image-20210329204159203](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329204159.png)

![image-20210329204757250](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329204757.png)

### 主从链表型

![image-20210329205119427](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329205119.png)

![image-20210329205453324](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329205453.png)

### 哨兵模式

==主机宕机后，自动选举一个从机当主机的模式==

![image-20210329210406688](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329210406.png)

![image-20210329210602815](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329210603.png)

![image-20210329210733348](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329210733.png)



#### 测试

1、sentinel.conf

```bash
# 1代表若主机宕机后，会自动选取一个从机当新的主机
sentinel monitor myredis 127.0.0.1 6379 1 
```

2、启动redis-sentinel

```bash
[youens@youens bin]$ redis-sentinel myConfig/sentinel.conf 
5239:X 29 Mar 2021 21:14:23.792 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
5239:X 29 Mar 2021 21:14:23.792 # Redis version=5.0.12, bits=64, commit=00000000, modified=0, pid=5239, just started
5239:X 29 Mar 2021 21:14:23.792 # Configuration loaded
5239:X 29 Mar 2021 21:14:23.793 # You requested maxclients of 10000 requiring at least 10032 max file descriptors.
5239:X 29 Mar 2021 21:14:23.793 # Server can't set maximum open files to 10032 because of OS error: Operation not permitted.
5239:X 29 Mar 2021 21:14:23.793 # Current maximum open files is 4096. maxclients has been reduced to 4064 to compensate for low ulimit. If you need higher maxclients increase 'ulimit -n'.
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 5.0.12 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 5239
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

5239:X 29 Mar 2021 21:14:23.794 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
5239:X 29 Mar 2021 21:14:23.795 # Sentinel ID is 27ce03501d79496a26917fda295b6441d56fe546
5239:X 29 Mar 2021 21:14:23.795 # +monitor master myredis 127.0.0.1 6379 quorum 1
5239:X 29 Mar 2021 21:14:23.796 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:14:23.797 * +slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379


```

此时，若主机宕机

sentinel进程会自动切换主机

```bash
5239:X 29 Mar 2021 21:19:29.058 # +sdown master myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.058 # +odown master myredis 127.0.0.1 6379 #quorum 1/1
5239:X 29 Mar 2021 21:19:29.058 # +new-epoch 1
5239:X 29 Mar 2021 21:19:29.058 # +try-failover master myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.061 # +vote-for-leader 27ce03501d79496a26917fda295b6441d56fe546 1
5239:X 29 Mar 2021 21:19:29.061 # +elected-leader master myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.061 # +failover-state-select-slave master myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.145 # +selected-slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.145 * +failover-state-send-slaveof-noone slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.216 * +failover-state-wait-promotion slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.906 # +promoted-slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.906 # +failover-state-reconf-slaves master myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:29.962 * +slave-reconf-sent slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:30.915 * +slave-reconf-inprog slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:30.915 * +slave-reconf-done slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:31.005 # +failover-end master myredis 127.0.0.1 6379
5239:X 29 Mar 2021 21:19:31.005 # +switch-master myredis 127.0.0.1 6379 127.0.0.1 6380
5239:X 29 Mar 2021 21:19:31.005 * +slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6380
5239:X 29 Mar 2021 21:19:31.005 * +slave slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6380


#此时6380从机变成主机
127.0.0.1:6380> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=127.0.0.1,port=6381,state=online,offset=26685,lag=0
master_replid:0580530ad31b337209039ae774bf8f59aa4d3a7b
master_replid2:21725978fc7e3e9967becbf1771d5f92ce33e229
master_repl_offset:26817
second_repl_offset:21549
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:26817

# 如果宕机的主机重新启动了，那么会将它变成从机
5239:X 29 Mar 2021 21:26:50.101 # -sdown slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6380
5239:X 29 Mar 2021 21:27:00.083 * +convert-to-slave slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6380

```

#### 优缺点

![image-20210329212956301](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329212956.png)

全部配置

![image-20210329213046365](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329213046.png)

![image-20210329213122733](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329213122.png)

![image-20210329213137488](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329213137.png)



## Redis缓存穿透和雪崩

###  缓存穿透

![image-20210329214543905](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329214544.png)

#### 解决方案

![image-20210329214938782](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329214938.png)

![image-20210329215013361](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329215013.png)

![image-20210329215023710](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329215023.png)

### 缓存击穿

![image-20210329215231733](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329215231.png)

### 缓存雪崩

![image-20210329215522962](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329215523.png)

#### 解决方案

![image-20210329215958821](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329215959.png)

