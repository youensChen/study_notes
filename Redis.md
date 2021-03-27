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