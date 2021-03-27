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

#### 步长增减

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

#### 先创建再赋值getset

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



### Hash



### Zset



## 三种特殊数据类型



### geospatial



### hyperloglogs



### bitmaps

