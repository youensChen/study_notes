# 第一章 启程

## 使用Erlang shell

![image-20220614142120294](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141421354.png)

![image-20220614142050550](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141420678.png)

![image-20220614142152558](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141421592.png)

![image-20220614142158001](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141421036.png)

![image-20220614142212118](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141422179.png)

## Erlang基础知识

### 数值类型

整除使用 ==div== 运算符

```erlang
10> 5/2.
2.5
11> 5 div 2.
2
```

求余使用 ==rem== （remainder）运算符

```erlang
12> 5 rem 2.
1

```

表示某进制数： **Base#Value**

```erlang
13> 2#1010. 二进制
10
14> 8#0677. 八进制
447
15> 16#AE. 十六进制
174
16> 3#22. 三进制
8

```

### 不可变的变量

Erlang中，变量必须以**大写字母开头**，其次，==变量不可变（一个变量只能被赋值一次）==

```erlang
18> One =1.
1
19> One=1.
1
20> Un = Uno = One = 1.
1
21> b().
One = 1
Un = 1
Uno = 1
ok
22> Two = One + One.
2
23> Two = 2. 
2
24> b().            
One = 1
Two = 2
Un = 1
Uno = 1
ok
25> One = 2.
** exception error: no match of right hand side value 2

26> 47 = 45+2.
47
27> 47 = 45+3.
** exception error: no match of right hand side value 48

```

**=== 操作符 负责比较操作并且在不相等时报错==**

![image-20220614143551843](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141435914.png)

### 原子

![image-20220614143742135](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141437208.png)

![image-20220614143838803](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141438858.png)

![image-20220614143844417](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141438459.png)

![image-20220614144046346](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141440588.png)

==原子不能动态创建，一个原子需要8字节==

Erlang保留字

![image-20220614144139615](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141441685.png)

### 布尔代数

```erlang
29> true and false.
false
30> false or true.
true
31> true xor false. 异或
true
32> true xor true. 
false
33> not false.
true
34> not (true and true).
false

```

![image-20220614144443440](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141444494.png)

``` erlang
35> false andalso true. 短路and
false
36> true orelse false. 短路or
true

```

![image-20220614144832670](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141448768.png)

```erlang
37> 5 =:= 5.
true
38> 1 =:= 0.
false
39> 1 =/= 0.
true
40> 1 =/= 1.
false
41> 5 == 5.0.
true
42> 5 /= 5.0.
false
43> 5 =:= 5.0.
false
44> 1 /= 1.0.
false
```

|      总结      | 相等性比较 | 不等性比较 |
| :------------: | :--------: | :--------: |
|  **精确比较**  |  **=:=**   |  **=/=**   |
| **非精确比较** |   **==**   |   **/=**   |

加号左右类型需匹配

比较符号则无需匹配

``` erlang
46> 8+ dsdd.
** exception error: an error occurred when evaluating an arithmetic expression
     in operator  +/2
        called as 8 + dsdd
48> 0 == false.
false
49> 1 == true.
false
50> 999999999999999< '1'.
true

```

Erlang 中其实没有布尔值true和false，它们都是原子值

不同类型比较

![image-20220614164139248](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141641348.png)

### 元组

![image-20220614164409897](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141644957.png)

```erlang
60> X = 2, Y = 3.
3
61> Point = {X, Y}.
{2,3}
62> Point.
{2,3}

```

![image-20220614164750834](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141647923.png)

![image-20220614164800839](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141648923.png)

### 列表

![image-20220614164849551](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141648611.png)

![image-20220614164913696](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141649786.png)==Erlang中字符串就是列表==，在纯数字列表中，至少有一个数字不是字符时（ascii)，打印时才会保留数值形式

![image-20220614165039149](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141650223.png)

++ 操作符可以合并列表， -- 操作符列表删除元素

```erlang
65> [1,2,3]++[3,4].
[1,2,3,3,4]
66> [1,2,3,3]--[3,4].
[1,2,3]

```

![image-20220614165450793](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141654893.png)

![image-20220614165518166](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141655215.png)

```erlang
67> hd([1,2,3,4]).
1
68> tl([1,2,3,4]).
[2,3,4]

```

![image-20220614165605795](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141656869.png)

![image-20220614165704927](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141657000.png)

![image-20220614165724157](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141657197.png)

![image-20220614165819573](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141658612.png)

```erlang
69> length([1,2,3]).
3
70> length([1,2,3|4]).
** exception error: bad argument
     in function  length/1
        called as length([1,2,3|4])
        *** argument 1: not a list
71> length([1,2,3|[]]).
3
72> length([1,2,3|[4]]).
4
```

![image-20220614170050477](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141700573.png)

### 列表推导式

![image-20220614170442697](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141704764.png)

```erlang
75> List = [2*N || N <- [1,2,3,4]].
[2,4,6,8]
76> List.
[2,4,6,8]

```

![image-20220614170618957](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141706009.png)

```erlang
78> [X || X<-[1,2,3,4,5,6,7,8,9,10], X rem 2 =:= 0].
[2,4,6,8,10]
80> [X || X<-[1,2,3,4,5,6,7,8,9,10], X rem 2 =:= 0, X rem 4 =:= 0]. 且条件
[4,8]
```

![image-20220614171025724](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141710771.png)

![image-20220614171222554](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141712727.png)

![image-20220614171334637](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141713725.png)

## 处理二进制数据

### 位语法

![image-20220614171916536](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141719604.png)

![image-20220614171955753](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141719841.png)

![image-20220614172205471](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141722605.png)

![image-20220614172212132](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141722219.png)

![image-20220614172316451](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141723560.png)

待学...



# 第二章 模块

### 什么是模块

![image-20220614172536907](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141725002.png)

```erlang
81> element(2, {a,b,c}).
b
82> lists:seq(1,10).
[1,2,3,4,5,6,7,8,9,10]
83> seq(1,10).      
** exception error: undefined shell command seq/2

```

### 创建模块

![image-20220614172850597](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206141728716.png)

### 导出函数

![image-20220614224847804](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142248893.png)

![image-20220614224940661](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142249747.png)

### 定义函数

![image-20220614225020338](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142250420.png)

Erlang中，注释只能是单行的

![image-20220614225146091](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142251170.png)

### 引入函数

![image-20220614225309576](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142253624.png)

尽量不要引入函数

![image-20220614225410735](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142254848.png)

### 编译代码

![image-20220614230002328](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142300422.png)

![image-20220614230036853](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142300921.png)

#### 编译选项

![image-20220614230135675](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142301833.png)

![image-20220614230234273](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142302329.png)

![image-20220614230402026](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142304087.png)

![image-20220614230446815](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142304916.png)

### 定义宏

![image-20220614230550236](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142305348.png)

![image-20220614230615441](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142306546.png)

预定义宏

![image-20220614230716796](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142307902.png)

![image-20220614230902031](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142309110.png)

### 模块中的其他内容

![image-20220614231110732](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142311819.png)

![image-20220614231255710](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206142312780.png)

# 第三章 函数

## 模式匹配

其他语言

![image-20220615003314937](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150033328.png)

Erlang模式匹配

![image-20220615003357176](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150033234.png)

![image-20220615003644505](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150036574.png)

### 模式进阶

![image-20220615005119008](C:\Users\youens\AppData\Roaming\Typora\typora-user-images\image-20220615005119008.png)

### 变量绑定

##### 例子一

![image-20220615005414001](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150054080.png)

解释

![image-20220615005628626](C:\Users\youens\AppData\Roaming\Typora\typora-user-images\image-20220615005628626.png)

##### 例子二

![image-20220615005921044](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150059208.png)

## 卫语句（guard）

![image-20220615010124133](C:\Users\youens\AppData\Roaming\Typora\typora-user-images\image-20220615010124133.png)

![image-20220615010146603](C:\Users\youens\AppData\Roaming\Typora\typora-user-images\image-20220615010146603.png)

, ; 和 andalso orelse的区别

![image-20220615010458952](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150104062.png)

![image-20220615010743597](C:\Users\youens\AppData\Roaming\Typora\typora-user-images\image-20220615010743597.png)

注意，卫语句中不能使用自定义函数

![image-20220615010849193](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150108293.png)

## if表达式

![image-20220615011021888](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150110942.png)



![image-20220615011811981](C:\Users\youens\AppData\Roaming\Typora\typora-user-images\image-20220615011811981.png)

使用多个卫语句

![image-20220615012010284](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150120347.png)



条件覆盖比 true分支可读性更高

![image-20220615012439586](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150124651.png)

![image-20220615012450768](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150124844.png)

## case...of表达式

![image-20220615012652476](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150126594.png)

配合卫语句

![image-20220615012859218](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150128286.png)



# 第四章 类型

## 动态强类型

![image-20220615014042746](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150140872.png)

![image-20220615014238040](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150142128.png)

![image-20220615014256615](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150142695.png)

## 类型装换

![image-20220615014421827](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150144899.png)

![image-20220615014512248](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150145325.png)

![image-20220615014553207](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150145307.png)

## 数据类型检测函数

![image-20220615014852727](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150148820.png)

![image-20220615015114630](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206150151723.png)

# 第五章 递归

## 递归的工作原理

函数式编程语言中通常没有循环结构，使用递归来代替循环

![image-20220615141126370](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151411564.png)

### 列表的长度

![image-20220615141326974](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151413077.png)

### 列表长度尾递归实现

![image-20220615141534229](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151415380.png)

![image-20220615141701444](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151417491.png)

![image-20220615141859015](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151418138.png)

![image-20220615142517943](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151425046.png)

## 更多递归函数

### duplicate函数

![image-20220615142822030](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151428102.png)

```erlang
%% 尾递归返回N个Item组成的列表
duplicate(N, Item) when N > 0 ->
    duplicate(N, Item, []).
duplicate(0, _, Result) ->
    Result;
duplicate(N, Item, Result) ->
    duplicate(N - 1, Item, [Item | Result]).
```

### reverse函数

![image-20220615144015335](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151440401.png)

```erlang
%% 尾递归反转List
revert_list(List) ->
    revert(List, []).

revert([], Revert_List) ->
    Revert_List;
revert([Head | Rest], Revert_List) ->
    revert(Rest, [Head | Revert_List]).
```

### sublist函数

![image-20220615144813007](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151448112.png)

``` erlang
%% 尾递归获取列表前N个元素
%%sublist(List, N) when N > 0 ->
%%    sublist(List, N, []).
sublist(List, N) when N > 0 ->
    lists:reverse(sublist(List, N, [])).


sublist(_, 0, Result) ->
    Result;
sublist([], _, Result) ->
    Result;
sublist([First | Rest], N, Result) ->
    sublist(Rest, N-1, [ First | Result]).
```

![image-20220615145819491](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151458582.png)

![image-20220615145828090](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151458175.png)

### zip函数

![image-20220615150029939](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151500013.png)

```erlang
%% 尾递归实现列表合并
zip(ListA, ListB) ->
    lists:reverse(zip(ListA, ListB, [])).

zip([], [], Result) ->
    Result;
zip([H1 | T1], [H2 | T2], Result) ->
    zip(T1, T2, [{H1, H2} | Result]).
```

### 快速排序

```erlang
%% 快速排序 递归实现
quick_sort([]) ->
    {};
quick_sort([Pivot | Rest]) ->
    {Smaller, Larger} = partition(Pivot, Rest, [], []),
    quick_sort(Smaller) ++ Pivot ++ quick_sort(Larger).

% 分割
partition(_, [], Smaller, Larger) -> {Smaller, Larger};
partition(Pivot, [H|T], Smaller, Larger) -> 
    if
        H =< Pivot -> partition(Pivot, T, [H | Smaller], Larger);
        H > Pivot -> partition(Pivot, T, Smaller, [H | Larger])
    end.

% ---------------------------------
列表推导式实现
quick_sort([]) ->
    {};
quick_sort([Pivot | Rest]) ->
    quick_sort([Smaller || Smaller <- Rest, Smaller <= Pivot])
    ++ [Pivot] ++
    quick_sort([Larger || Larger <- Rest, Larger > Pivot]).
% 缺点 遍历两次列表

% 最终实现 lists:sort/1

```

## 不仅是列表

树

![image-20220615162243613](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151622768.png)

![image-20220615162226992](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151622081.png)

# 第六章 高阶函数

![image-20220615162744889](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151627969.png)

## 一切都是函数

![image-20220615163138149](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151631218.png)

![image-20220615163155159](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151631213.png)

## 匿名函数

![image-20220615163220999](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151632095.png)

![image-20220615163258953](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151632048.png)

### 匿名函数的其他用途

![image-20220615163610543](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151636635.png)

![image-20220615163616579](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151636710.png)

### 函数的作用域和闭包

#### 作用域

![image-20220615163826954](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151638070.png)

![image-20220615164033430](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151640529.png)

![image-20220615164125382](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151641472.png)

![image-20220615164254310](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151642420.png)

![image-20220615164443840](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151644975.png)



#### 闭包

![image-20220615164538844](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151645928.png)

### 映射、过滤器、折叠

#### 过滤器

![image-20220615164818434](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151648527.png)

归纳

![image-20220615164835902](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151648964.png)

![image-20220615164859263](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151648367.png)

#### 折叠

![image-20220615165002038](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151650165.png)

![image-20220615165108954](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151651136.png)

归纳

![](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151650454.png)

### lists模块中提供的抽象

![image-20220615165249405](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151652483.png)

![image-20220615165333611](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151653756.png)



# 第七章 错误和异常

![image-20220615165633246](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151656396.png)

## 错误编译

![image-20220615165656229](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151656280.png)

### 编译期错误

![image-20220615165815966](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151658056.png)

![image-20220615165851645](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151658812.png)

![image-20220615165918299](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151659472.png)

![image-20220615170041403](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151700484.png)

### 逻辑错误

![image-20220615170100742](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151701879.png)

### 运行时错误

![image-20220615170155811](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151701885.png)

![image-20220615170212302](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151702404.png)

![image-20220615170235760](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151702833.png)

![image-20220615170315605](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151703723.png)

![image-20220615170403915](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151704021.png)

![image-20220615170423271](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151704333.png)

![image-20220615170436894](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151704970.png)

![image-20220615170450591](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151704754.png)

![image-20220615170523294](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151705394.png)

## 引发异常

![image-20220615170553619](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151705686.png)



### error

![image-20220615170658312](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151706411.png)

![image-20220615170838967](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151708124.png)

### exit

![image-20220615170826683](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151708769.png)

![image-20220615171042643](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151710807.png)



### throw

![image-20220615171115960](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151711070.png)

![image-20220615171207158](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151712320.png)

## 处理异常

### 处理不同类型异常

![image-20220615171245597](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151712730.png)

![image-20220615171329529](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151713623.png)

![image-20220615171514846](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151715909.png)

### catch后的after语句

![image-20220615171750457](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151717562.png)

### 尝试多个表达式

![image-20220615171825090](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151718165.png)

![image-20220615171945981](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151719086.png)

![image-20220615172040347](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151720537.png)

### 更多选择

![image-20220615172142266](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151721386.png)

![image-20220615172310119](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151723286.png)



## 在树中使用try

![image-20220615173039113](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151730226.png)

![image-20220615173112729](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206151731844.png)

# 第八章 用函数式思维解决问题



# 第九章 常用数据结构

## 记录record

### 定义记录

![image-20220615201051791](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152010907.png)

![image-20220615201130728](C:\Users\youens\AppData\Roaming\Typora\typora-user-images\image-20220615201130728.png)

![image-20220615201325799](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152013934.png)

![image-20220615201352049](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152013158.png)

![image-20220615201434998](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152014174.png)

### 读取记录地段值

![image-20220615201629933](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152016044.png)

![image-20220615201647406](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152016537.png)

![image-20220615201716592](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152017634.png)

![image-20220615201918785](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152019934.png)

![image-20220615201959406](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152019531.png)

### 更新记录

![image-20220615202103915](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152021056.png)

### 共享记录定义

![image-20220615202418097](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152024204.png)

![image-20220615202512777](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152025879.png)



## 键值存储

#### 小数据量存储

![image-20220615202700432](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152027490.png)

##### proplist

![image-20220615202752874](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152027953.png)

![image-20220615202812098](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152028177.png)

![image-20220615202937901](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152029063.png)

##### orddict

![image-20220615203026498](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152030643.png)

![image-20220615203116800](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152031863.png)

![image-20220615203130169](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152031234.png)



#### 大数据量存储：字典和通用平衡树

##### 字典

![image-20220615203237876](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152032963.png)

##### 通用平衡树

![image-20220615203420466](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152034637.png)

![image-20220615203631113](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152036288.png)

## 集合

![image-20220615203754981](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152037132.png)

![image-20220615203945338](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152039475.png)

![image-20220615204037467](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152040571.png)

![image-20220615204115992](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152041120.png)

## 有向图

![image-20220615204138914](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152041044.png)

## 队列

![image-20220615211016996](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152110155.png)

![image-20220615211123427](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152111540.png)

![image-20220615211219564](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202206152112688.png)

