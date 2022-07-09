

# 语法

## 通信原语

### send

```erlang
% 向 Pid 进程发送Msg
Pid ! Msg.
% 向在Node上注册的进程RegName发送消息
{RegName, Node} ! Msg.
```





# ets & dets

> 这个模块为并发访问提供了一些有限的支持。保证对单个对象的所有更新都是原子的和隔离的。这意味着对单个对象的更新操作要么成功，要么完全失败，没有任何影响(原子性) ，而且其他进程(隔离)无法看到更新的中间结果。一些更新了许多对象的函数声明它们甚至保证了整个操作的原子性和隔离性。在数据库术语中，隔离级别可以看作是“可序列化的”，就好像所有隔离操作都是按照严格的顺序一个接一个地串行执行的。
>
> 在遍历期间修改会导致不正确结果，

![image-20220703203010554](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/202207032030595.png)

## 查看信息

### i/0

`i() -> ok`

> 显示终端上所有 ETS 表的信息。

### i/1

`i(Table) -> ok`

> 在终端上浏览表。



### info/1

`info(Table) -> InfoList | undifined`

> 查看某个表的详情信息

### all/0

`all() -> [Table]`

> 返回节点上所有表的列表。命名表由其名称指定，未命名表由其表标识符指定。
>
> 不能保证返回列表中的一致性。“在”ets 期间由其他进程创建或删除的表: all ()调用是否包含在列表中。只有在 ets: all ()调用之前创建/删除的表才能保证被包含/排除。

### whereis/0

`whereis(TableName) -> tid() | undefined`

> 返回表名对应的tid

## 建表

### new/2

`new(Name, Options) -> table()`

**注意：ordered_set类型key比较使用 ==，其他类型使用 =:=**

> 创建一个新表并返回一个可以在后续操作中使用的表标识符。表标识符可以发送到其他进程，以便在节点内的不同进程之间共享表。
>
> Options默认值：
> [set, protected, {keypos,1}, {heir,none}, {write_concurrency,false}, {read_concurrency,false}, {decentralized_counters,false}]
>
> 其中Option列表的值如下
>
> - 表的类型：set | ordered_set | bag | duplicate_bag, 默认为 set
> - 访问权限：public（所有进程可读写） | protected(拥有者进程可读写、其他进程可读) | private（只有拥有者具有访问权限），默认为protected
> - named_table：表可以通过Name来访问
> - {keypos,Pos}：指定元组中Pos位置作为Key，默认为1
> - 继承进程：{heir,Pid,HeirData} | {heir,none}，当进程终止时，Pid将继承其表，并发送消息，默认为{heir,none}
> - 其他默认参数：{write_concurrency,false}, {read_concurrency,false}, {decentralized_counters,false}   

### dets:open_file/1

`open_file(Filename) -> {'ok', Reference} | {'error', Reason}`

> 打开已存在的表

### dets:open_file/2

`open_file(Name, [Arg]) -> {'ok', Name} | {'error', Reason}`



> ```erlang
>  Arg  :: {'access', access()}
>        | {'auto_save', auto_save()}
>        | {'estimated_no_objects', non_neg_integer()}
>        | {'file', file:name()}
>        | {'max_no_slots', no_slots()}
>        | {'min_no_slots', no_slots()}
>        | {'keypos', keypos()}
>        | {'ram_file', boolean()}
>        | {'repair', boolean() | 'force'}
>        | {'type', type()}
> ```
>
> 其中Args列表的值如下
>
> - 表的类型 type：set | bag | duplicate_bag, 默认为 set
> - 访问权限 access：read（只读） | read_write（读写），默认read_write
> - 自动保存 auto_save(在一段时间后自动保存到磁盘) 默认180000
> - 文件名  file 指定文件名
> - {keypos,Pos}：指定元组中Pos位置作为Key，默认为1
> - {ram_file, boolean()} 可将dets存储在RAM中，默认为false    



## 插入

### insert/2

`insert(Tab, tuple() | [tuple()]) -> true`

> 即使在插入对象列表时，整个操作也保证是原子的和隔离的
>
> - 如果设置了表类型为`set`，并且插入对象的键与表中任何对象的键匹配(=:=)，则替换旧对象。
> - 如果表类型为 `order_set`，并且插入对象的键与表中任何对象的键相等(==)，则替换旧对象。
> - 如果列表包含多个具有匹配键的对象，并且表类型为`set`，则将插入一个对象，而该对象未定义。如果键比较相同，则表类型 `order_set` 也是如此。

### insert_new/2

`insert_new(Tab, tuple() | [tuple()]) -> boolean()`

> 如果待插入的key都是未在原来的表里存在的，则都插入，否则都不插入并返回false



## 删除

### dets:close/1

`close(Name) -> ok | {error | Reason}`

> 关闭表。只有打开表的进程才能关闭表。
>
> 在系统停止之前，所有打开的表必须关闭。如果尝试打开未正确关闭的表，Dets 会自动尝试修复该表。

### delete/0

` delete(Table) -> true`

> 删除整个表

### delete/1

` delete(Table, Key) -> true`

> 从表格中删除所有键为 Key 的对象。即使不存在键为 Key 的对象，此函数也会成功。

### delete_all_objects/1

`delete_all_objects(Table) -> true`

> 删除 ETS 表中的所有对象。操作保证是原子的并且是隔离的。

###  delete_object/2

` delete_object(Table, Object) -> true`

> 从 ETS 表中删除确切的对象 Object，保留具有相同键但有其他差异的对象(对于类型 bag 很有用)。在 copy _ bag 表中，对象的所有实例都将被删除

### select_delete/2

`select_delete(Tab, MatchSpec) -> NumDeleted`

> `MatchSpec`由`ets:fun2ms/1`获得，注意match的返回值需为`true`

### match_delete/2

` match_delete(Tab, Pattern) -> true`

> `Pattern`:: atom() | tuple()
>
> ```erlang
> 6> ets:match(T, '$1'). % Matches every object in table
> [[{rufsen,dog,7}],[{brunte,horse,5}],[{ludde,dog,5}]]
> 7> ets:match(T, {'_',dog,'$1'}).
> [[7],[5]]
> 8> ets:match(T, {'_',cow,'$1'}).
> []
> ```

## 查询

### lookup/2

`lookup(Tab, Key) -> [tuple()]`

> 返回表表中键为 Key 的所有对象的列表。
>
> 1、对于类型为 set 或 order _ set 的表，函数返回空列表或具有一个元素的列表，因为不能有多个对象具有相同的键。对于 bag 或 copy _ bag 类型的表，该函数返回一个任意长度的列表。
>
> 2、无论表的大小如何，类型 set、 bag 和 copy _ bag 的表中的插入和查找时间是常数。对于 order _ set 数据类型，时间与对象数量的(二进制)对数成正比。

### lookup_element/3

`lookup_element(Tab, Key, Pos) -> term() | [term()]`

> 对于类型为 `set` 或 `order_set` 的表 Table，函数返回键为 Key 的对象的第`Pos` 元素。
>
> 对于 `bag` 或 `copy_bag` 类型的表，函数返回一个列表，其中包含键 Key 的每个对象的 第`Pos` 元素。
>
> ==如果没有key匹配，则函数 `exit(badarg)`==

### match/2

`match(Tab, Pattern) -> [[term()]]`

> 根据模式模式匹配表表中的对象。
>
> 模式是一个可以包含以下内容的术语:
>
> - Bound parts (Erlang terms)
> - '_' that matches any Erlang term
> - Pattern variables '$N', where N=0,1,...
>
> ```erlang
> 6> ets:match(T, '$1'). % Matches every object in table
> [[{rufsen,dog,7}],[{brunte,horse,5}],[{ludde,dog,5}]]
> 7> ets:match(T, {'_',dog,'$1'}).
> [[7],[5]]
> 8> ets:match(T, {'_',cow,'$1'}).
> []
> ```
>
> 如果在模式中指定了==键==，则匹配非常有效。如果没有指定键，也就是说，如果它是变量或下划线，则必须搜索整个表。如果表非常大，则搜索时间可能很长。

### match/3

`match(Tab, Pattern, Limit) -> {[[term()]], Continuation} | '$end_of_table'`

### match_object/2

`match_object(Tab, Pattern) -> [tuple()]`

> 根据模式模式匹配表表中的对象。
>
> 如果在模式中指定了键，则匹配非常有效。如果没有指定键，也就是说，如果它是变量或下划线，则必须搜索整个表。如果表非常大，则搜索时间可能很长。

==match和match_object的区别是==

```erlang
1> ets:new(table, [named_table, bag]).
table
2> ets:insert(table, [{items, a, b, c, d}, {items, a, b, c, a}, {cat, brown, soft, loveable, selfish}, {friends, [jenn,jeff,etc]}, {items, 1, 2, 3, 1}]).
true

3> ets:match(table, {items, '$1', '$2', '_', '$1'}). % match只取 '$N'的部分
[[a,b],[1,2]]
4> ets:match(table, {items, '$114', '$212', '_', '$6'}).
[[d,a,b],[a,a,b],[1,1,2]]
5> ets:match_object(table, {items, '$1', '$2', '_', '$1'}).% match_object取全部
[{items,a,b,c,a},{items,1,2,3,1}]
6> ets:delete(table).
true
```



### member/2

`member(Tab, Key) -> boolean()`

> 工作原理类似于`lookup/2`，但不返回对象。如果一个或多个表元素具有键 Key，则返回 true，否则为 false。



### select/2

`select(Tab, MatchSpec) -> [term()]`

> 返回将匹配规范 MatchSpec 应用于存储在 table Name 中的所有或部分对象的结果。
>
> `MatchSpec`由`ets:fun2ms/1`获得

### select_count/2

`select_count(Tab, MatchSpec) -> NumMatched`

> 使用匹配规范匹配 Table 中的对象。如果某个对象的匹配规范返回 **true**，则将该对象视为匹配并计数。

### take/2

`take(Tab, Key) -> [tuple()]`

> 返回并移除表表中键为 Key 的所有对象的列表。



## 修改

### setopts/2

> 设置表选项。在创建表之后，唯一允许设置的选项是传承者。调用进程必须是表所有者。

### select_replace/2



### rename/2

`rename(Tab, Name) -> Name`

> 将已命名的表表重命名为新名称 Name。然后，不能使用旧名称访问表。重命名未命名的表没有任何效果。

### give_away/3

`give_away(Tab, Pid, GiftData :: term()) -> true`

> 使进程 Pid 成为表的新所有者。如果成功，消息`{'ETS-TRANSFER'，Table，FromPid，GiftData }`将发送给新所有者。
>
> 进程 Pid 必须是活动的、本地的，并且不是表的所有者。调用进程必须是表所有者。
>
> 注意，此函数不影响表的选项继承权。例如，表所有者可以将表的继承者设置为自己，然后将表`give_away`，然后在接收方终止时将表拿回来



## 遍历

### first/1

`first(Tab) -> Key | '$end_of_table'`

> 返回表表中的第一个键 Key。对于 order _ set 表，返回 Erlang 术语顺序中的第一个键。对于其他表类型，根据表的内部顺序返回第一个键。如果表为空，则返回“ $end _ of _ table”。

### last/1

`last(Tab) -> Key | '$end_of_table'`

> 返回类型 order _ set 的 Table 中根据 Erlang 术语顺序的最后一个键 Key。对于其他表类型，该函数是 first/1的同义词。如果表为空，则返回“ $end _ of _ table”。

### next/2

`next(Tab, Key1) -> Key2 | '$end_of_table'`

> 返回表中键 Key1后面的下一个键 Key2。对于 table type order _ set，返回 Erlang 术语顺序中的下一个键。对于其他表类型，根据表的内部顺序返回下一个键。如果不存在下一个键，则返回“ $end _ of _ table”。

### prev/2

`prev(Tab, Key1) -> Key2 | '$end_of_table'`

> 返回上一个键 Key2，按照 Erlang 术语顺序返回键 key1之前的键。对于其他表类型，该函数是 next/2的同义词。如果以前的键不存在，则返回“ $end _ of _ table”。

### foldl/3

`foldl(Function, Acc0, Tab) -> Acc1 `

> 如果表为空，则返回 Acc0。这个函数类似于 list: foldl/3。除了 order _ set 表之外，table 元素以未指定的顺序被遍历，其中它们首先被遍历到最后。
>
> 如果函数将对象插入到表中，或者另一个进程将对象插入到表中，那么这些对象可以(取决于键顺序)包含在遍历中。



## 持久化与转换

### ets:tab2list/1

`tab2list(Tab) -> [Object]`

> 返回表格中所有对象的列表。

### ets:tab2file/2

`tab2file(Tab, Filename) -> 'ok' | {'error', Reason}`

> 将表转储为文件。
>
> 相当于 `ets:tab2file(Table, Filename,[])`

### ets:tab2file/3

`tab2file(Tab, Filename, [Option]) -> 'ok' | {'error', Reason}`

### ets:to_dets/2

`to_dets(Tab, DetsTab) -> dets:tab_name()`

> 用已经打开的 ETS 表中名为 Table 的对象填充已经创建/打开的 Dets 表。在插入对象之前，Dets 表被清空。

### ets:from_dets/2

`from_dets(Tab, DetsTab) -> 'true'`

> 用已经打开的 Dets 表 DetsTab 中的对象填充已经创建的 ETS 表。除非覆盖，否则保留 ETS 表中的现有对象。
>
> 如果任何表不存在或 Dets 表未打开，则引发 badarg 异常。

### dets:to_ets/2

`to_ets(Name, EtsTab) -> EtsTab | {'error', Reason}`

> 将 Dets 表 Name 的对象插入 ETS 表 EtsTab 中。未指定插入对象的顺序。除非覆盖，否则保留 ETS 表的现有对象。

### dets:from_ets/2

`from_ets(Name, EtsTab) -> 'ok' | {'error', Reason}`

> **删除**表 Name 的所有对象，然后插入 ETS 表 EtsTab 的所有对象。对象以未指定的顺序插入。在调用 ETS: safe _ fixtable/2时，ETS 表必须是公共的或者由调用进程拥有。

## fun2ms/1

> 在erl文件中需要导入 -include_lib("stdlib/include/ms_transform.hrl")，
>
> 在shell中无需导入，
>
> 参数为高阶函数，如

```erlang
ets:fun2ms(
          fun({A, B}) when A > 10 ->
              {A, B}
          end
          ).

% 如果需要统计数量或者指定删除，高阶函数的返回值需要 true
```



# elang

## 创建进程

### spawn/1 

`spawn(Fun) -> Pid`

### spawn/2

`spawn(Node, Fun) -> Pid`

### spawn/3

`spawn(Module, Function, Args) -> Pid`

### spawn/4

`spawn(Node, Module, Function, Args) -> Pid`

## 连接进程

### link/1 

`link(Pid | Port) -> true`

> 1、如果是port，那么调用者和port必须在同一个节点
>
> 2、两个进程之间只有一个连接，重复调用`link/1`没有效果
>
> 3、连接进程的消息形式：`{'EXIT', SenderPID, Reason}`
>
> 4、进程终止会导致与其有直接或间接连接的进程终止（除了系统进程）

### unlink/1

`unlink(Pid | Port) -> true`



## 监控进程

### monitor/2

`monitor(process, Pid或者注册的名字) -> Ref`

`monitor(port, PortId) -> Ref`

`monitor(time_offset, Item :: clock_service) -> Ref`

> 1、监视的进程终止触发监视器会受到消息：`{'DOWN', Ref, Type, Object, Reason}`
>
> 2、Type:: process | port | time_offset
>
> 3、Object为被监控的实体，如果监控时使用**名字**监控，则为`{名字, Node}`，否则为pid或port
>
> 4、Reason为进程退出原因，或者为`noproc`进程或端口不存在，`noconncetion`与被监控进程所在节点没有连接

### demonitor/1

`demonitor(Ref) -> true`

### demonitor/2

`demonitor(Ref, [flush | info]) -> bool`

> 1、flush从邮箱中移除（如果有的话）一个`{_, Ref, _, _, _}`的消息，返回true
>
> 2、info：监视器被正确移除返回true，否则返回false
>
> 3、如果flush与info相结合，则在需要刷新时返回 false，否则返回 true。



## 创建并连接进程

>注意：创建和连接是个**原子**操作

### spawn_link/1 

`spawn_link(Fun) -> Pid`

### spawn_link/2

`spawn_link(Node, Fun) -> Pid`

### spawn_link/3

`spawn_link(Module, Function, Args) -> Pid`

### spawn_link/4

`spawn_link(Node, Module, Function, Args) -> Pid`

## 创建并监控进程

> 注意：创建和监控是个**原子**操作

### spawn_monitor/1 

`spawn_monitor(Fun) -> {Pid, Ref}`

### spawn_monitor/2

`spawn_monitor(Node, Fun) -> {Pid, Ref}`

### spawn_monitor/3

`spawn_monitor(Module, Function, Args) -> {Pid, Ref}`

### spawn_monitor/4

`spawn_link(Node, Module, Function, Args) -> {Pid, Ref}`

## 注册进程

### register/2

`register(Name, Pid | Port) -> true`

### registered/0

`registered() -> [Name]`

> 放回所有已注册的名字atom

### unregister/1

`unregister(Ref) -> true`

### whereis/1

`whereis(RegName) -> Pid | Port | undifined`

> 返回名字对应的pid或port

## 节点

### node/0

`node() -> Node`

> 返回单前节点名字，如果节点不存在，则返回 nonode@nohost。

### node/1

`node(Pid | Port | Ref) -> Node`

> 返回 Arg 起源的节点。Arg 可以是进程ID、引用或端口。如果 Arg 源自本地节点，并且本地节点不存在，则返回 nonode@nohost

### nodes/0

`nodes() -> [Node]`

> 返回通过正常连接连接到此节点的所有**可见**节点的列表

### nodes/1

`nodes(visible | hidden | connected | this | known) -> [Node]`

> 1、相等调用 [node()] = nodes(this), nodes(connected) = nodes([visible, hidden]), nodes() = nodes(visible).

###  disconnect_node/1

` disconnect_node(Node) -> boolean() | ignored`

> 如果断开连接成功，返回 true，否则返回 false。如果本地节点不存在，则返回ignored。

### monitor_node/2

`monitor_node(Node, true | false) -> true`

> 1、监视节点节点的状态。如果 Flag 为 true，则打开监视。如果 Flag 为 false，则关闭监视。
>
> 2、如果 Node 死亡或不存在，消息{ nodedown，Node }将传递给进程。如果一个进程对 `monitor_ Node(Node，true)`进行了**两次**调用，并且 Node 终止，则会向该进程传递**两条** nodedown 消息。如果没有到 Node 的连接，则尝试创建一个连接。如果失败，则会传递一条nodedown 消息

### is_alive/0

`is_alive() -> true | false`

> 如果本地节点是活动的(即节点可以是分布式系统的一部分) ，则返回 true，否则返回 false。如果节点以下列方式开始，则该节点为活动节点:
>
> - erl -name longname
> - erl -sname shortname



## 其他

### process_flag/2

`process_flag(trap_exit, true) -> OldBoolean`

> 当 `trap_exit` 设置为 true 时，到达进程的退出信号被转换为{‘ EXIT’，From，Reason }消息，这些消息可以作为普通消息接收。如果 `trap_exit` 设置为 false，则如果进程接收到非正常的退出信号，并且退出信号被传播到其链接进程，则该进程退出。应用程序进程通常不会捕获出口。

`process_flag(error_handler, Module::atom) -> OldModule::atom`

### exit/1

`exit(Reason) -> no_retuen()`

> 1、引发一个异常，原因为`Reason`,所以没有返回值，引发异常的原因是为了终止进程。这个函数不同于`error/1,2,3`，因为它导致了一个不同类的异常，并且有一个不包括调用堆栈中函数列表的原因。
>
> 2、如果一个进程调用 `exit (kill)`并且没有捕获异常，那么它将以 `kill` 原因结束，并且向所有链接的进程发出 `kill` (不是`killed`)的退出信号。这种具有退出原因的退出信号可以==被链接进程捕获==。注意，这意味着具有出口原因 kill 的信号根据发送方式的不同而表现不同，因为如果一个进程用 erlang: exit/2将这样的信号发送给另一个进程，则该信号将是==不可捕获==的。

### exit/2

`exit(Pid, Reason) -> true`

> 1、如果`Reason`是任何术语，除了`normal`或`kill`，并且 P 是由 Pid 标识的进程或端口，则应用下列行为:
>
> - 如果 P 没有`process_flag(trap_exit, true)`，则 P 以`Reason`原因退出
> - 如果 P 有`process_flag(trap_exit, true)`，那么退出信号将转换为消息{‘ EXIT’，From，Reason } ，其中 From 是发送退出信号的进程的进程ID，并传递给 P 的消息队列
>
> 2、如果`Reason`是术语`normal`，而 Pid 是进程 P 的标识符，且P 与调用 erlang: exit (Pid，Normal)的进程A不是同已进程（即不是自己exit自己），则应用以下行为
>
> - 如果 P 捕获退出信号，那么退出信号将转换为消息{‘ EXIT’，From，normal} ，其中 From 是发送退出信号并传递到 P 的消息队列的进程的进程ID					
> - 如果 P 没有捕获退出信号，则信号无效
>
> 3、如果 `Reason` 是 `kill`，也就是说，如果调用 `exit (Pid，kill)` ，则向由 Pid 标识的进程发送一个==不可捕获==的退出信号，该进程无条件退出，退出原因被终止。监控进程Pid或与Pid连接的进程会收到消息Pid的退出原因为==killed==(从kill被改为killed)。
>
> 4、 当进程自己调用自己，即`erlang:exit(self(), normal)`
>
> - 如果 P 捕获退出，退出信号将转换为消息{‘ EXIT’，From，Normal } ，其中 From 是 P 的进程自己的ID，并传递给 P 的消息队列
> - 如果 P 没有捕获出口，则 P 以正常理由出口

### apply/2

`apply(Fun, Args) -> term()`

### apply/3

`apply(Module, Function, Args) -> term()`



### send_after/3

` erlang:send_after(Time, Dest, Msg) -> TimerRef`





### send_after/4

` erlang:send_after(Time, Dest, Msg, Options) -> TimerRef`

> 启动计时器。当计时器过期时，消息 Msg 被发送到由 Dest 标识的进程。除了超时消息的格式之外，这个函数的工作方式与 `erlang: start_timer/4`完全一样。

```erlang
Types
Time = integer()
Dest = pid() | atom()
Msg = term()
Options = [Option]
Abs = boolean()
Option = {abs, Abs}
TimerRef = reference()
```





# maps





# lists





# math





# net_kernel

## start/2

`start(Name, Options) -> {ok, pid() | {error, Reason}}`



> 把非分布式节点变为分布式节点

## connect_node/1

`connect_node(Node) -> true | false | ignored`

> 建立到 Node 的连接。如果已建立或已建立连接，或 Node 是本地节点本身，则返回 true。如果连接尝试失败，返回 false，如果本地节点不存在，则忽略。

# rpc

## call/4

 `call(Node, Module, Function, Args) -> Res | {badrpc, Reason}`

> 相当于 `rpc:call(Node, Module, Function, Args, infinity)`

## call/5

 `call(Node, Module, Function, Args, Timeout) -> Res | {badrpc, Reason}`

> 1、如果应答在调用超时后到达，则不会污染调用方的消息队列（即不会进入消息队列）。
>
> 2、{ badrpc，Reason }将在下列情况下返回:
>
> - The called function fails with an `exit` exception.
> - The called function fails with an `error` exception.
> - The called function **returns** a term that matches `{'EXIT', _}`.
> - The called function **throws** a term that matches `{'EXIT', _}`.
>
> 3、Res 在下列情况下返回
>
> - The called function **returns** normally with a term that does **not** match `{'EXIT',_}`.
> - The called function **throws** a term that does **not** match `{'EXIT',_}`.
>
> 

## async_call/4

`async_call(Node, Module, Function, Args) -> Key`

> 异步调用

## yield/1

`yield(Key) -> term()`

> 获取异步调用的返回值

# global

## register_name/2

`register_name(Name, Pid) -> yes | no`

## register_name/3

`register_name(Name, Pid, Resovle) -> yes | no`

## re_register_name/2

`re_register_name(Name, Pid) -> yes | no`

> 更换名字

## unregister_name/1

`unregister_name(Name) -> term()`

## whereis_name/1

`whereis_name(Name) -> Pid | undefined`



## send/2

`send(Name, Msg)`

> 向节点进程发送消息

# gen_tcp

## listen/2

`listen(Port, [Option]) -> {ok, ListenSocket} | {error, Reason}`

> 如果 Port = = 0，则底层操作系统分配一个可用的端口号，使用 inet:Port/1检索它。
>
> Option如下
>
> - list | binary 接收到的数据包以什么形式传递
>
> - {active, true | false | once | N}套接字模式
> - 主动模式消息格式： `{tcp, Socket, Data}`

## accept/1

`accept(ListenSocket) -> {ok, Socket} | {error, Reason}`

> 相当于`gen_tcp:accept(ListenSocket, infinity)`

## accept/2

`accept(ListenSocket, Timeout) -> {ok, Socket} | {error, Reason}`

> 接受侦听套接字上的传入连接请求。套接字必须是从 listen/2返回的套接字。Timeout 以毫秒为单位指定超时值。默认为`infinity`。

## recv/2

`recv(Socket, Length) -> {ok, Packet} | {error, Reason}`

> 相当于`gen_tcp:recv(Socket, Length, infinity)`

## recv/3

`recv(Socket, Length, Timeout) -> {ok, Packet} | {error, Reason}`

> 以被动模式接收来自套接字的数据包。已关闭的套接字由返回值{ error，close }表示。
>
> 只有当套接字处于**原始模式**并表示要读取的字节数时，参数长度才有意义。如果 Llength 为0，则返回所有可用字节。如果 Llength > 0，则返回正确的 Llength 字节，或者返回一个错误; 当从另一端关闭套接字时，可能丢弃少于 Llength 字节的数据。
>
> 可选的 Timeout 参数指定以毫秒为单位的超时。



## send/2

`send(Socket, Packet) -> ok | {error, Reason}`

> 在套接字上发送数据包。

## close/1

`close(Socket) -> ok`

> 关闭 TCP 套接字。
>
> 请注意，在大多数 TCP 实现中，执行关闭并不能保证在远程端检测到关闭之前将任何数据发送给接收方。如果希望保证将数据传递给收件人，有两种常见的方法可以实现这一点。
>
> - 使用 `gen_tcp: shutdown(Sock，write)`向对方发出不再发送数据的信号，并等待套接字的读取端关闭。
> - 使用套接字选项{packet，N }(或类似的选项)使接收方在知道已经接收到所有数据时关闭连接成为可能。

## shutdown/2

` shutdown(Socket, write | read) -> ok | {error, Reason}`

> 关闭套接字在一个或两个方向。
>
> `write`关闭向socket写的功能，从socket读功能还打开。
>
> `如果 How = = read 或者在 Socket 端口中没有缓冲的输出数据，套接字将立即关闭，并且任何遇到的错误都将在 Rational 中返回。



## connect/3

`connect(Address, Port, Options) -> {ok, Socket} | {error, Reason}`

## connect/4

`connect(Address, Port, Options, Timeout) -> {ok, Socket} | {error, Reason}`

> 使用 IP 地址连接到主机上的 TCP 端口端口上的服务器。参数地址可以是主机名或 IP 地址。
>
> 可选的 Timeout 参数指定以毫秒为单位的超时。默认为infinity。
>
> 如果套接字处于{ active，N }模式(详见 `inet: setopts/2`)并且其消息计数器下降到0，则传递以下消息以指示套接字==已转换到被动==(`{ active，false }`)模式: `{tcp_passive, Socket}`
>
> 

## controlling_process/2

`controlling_process(Socket, Pid) -> ok | {error, Reason}`

> 为套接字分配一个新的控制过程 PID。控制进程是从套接字接收消息的进程。如果由当前==控制进程==以外的任何其他进程调用，则返回{ error，not _ owner }。如果 Pid 标识的进程不是现有的==本地== Pid，则返回{ error，badarg }。{ error，badarg }在执行此函数期间关闭 Socket 的某些情况下也可能返回。
>
> 如果套接字设置为`active`模式，此函数将把**调用方邮箱中的任何消息传输到新的控制进程**。如果在传输过程中有任何其他进程与套接字进行交互，则传输可能无法正常工作，并且消息可能会保留在调用方的邮箱中。例如，在传输完成之前更改套接字活动模式可能会导致这种情况。

# gen_udp

## connect/3

`connect(Socket, Address, Port) -> ok | {error, Reason}`

> 连接 UDP 套接字只意味着存储 SockAddr 指定的指定(目标)套接字地址，以便系统知道将数据发送到哪里。
>
> 这意味着在发送数据报时不需要指定目标地址。也就是说，我们可以使用 send/2。
>
> 这还意味着套接字将只接收来自这个地址的数据。

## open/1

`open(Port) -> {ok, Socket} | {error, Reason}`

## open/2

`open(Port, [Opt]) -> {ok, Socket} | {error, Reason}`

> 将 UDP 端口号(Port)与调用进程关联。
>
> Opt值可为：
>
> - list | binary 接收到的数据包作为什么类型传递
> - {active, true | false | once | N}套接字模式

## close/1

`close(Socket) -> ok`

> UDP套接字关闭。

## recv/2

` recv(Socket, Length) -> {ok, RecvData} | {error, Reason}`

## recv/3

`` recv(Socket, Length, Timeout) -> {ok, RecvData} | {error, Reason}``

> 以被动模式(`{active, false}`)从套接字接收数据包。可选参数 Timeout 指定以毫秒为单位的超时。默认值为`infinity`。
>
> 

## send/2

 `send(Socket, Packet) -> ok | {error, Reason}`

> 在已连接的套接字上发送数据包(参见 `connect/2`和 `connect/3`)。

## send/3

`send(Socket, Destination, Packet) -> ok | {error, Reason}`

> 将数据包发送到指定的目的地。
>
> 此函数等效于 `gen_udp:send (Socket, Destination, [], Packet)`。

## send/4

`send(Socket, Host, Port, Packet) -> ok | {error, Reason}`

> 将数据包发送到指定的主机和端口。
>
> 此子句等效于 `gen_udp:send (Socket、 Host、 Port、[]、 Packet)`。

## send/5

`send(Socket, Host, Port, AncData, Packet) -> ok | {error, Reason}`

> 向指定的 Host 和 Port 发送数据包，并使用辅助数据 PacData。
>
> 参数 Host 可以是主机名或套接字地址，Port 可以是端口号或服务名原子。它们被解析成一个 Destination，然后这个函数等效于 send (Socket、 Destination、 Anc Data、 Packet) ，在那里读取辅助数据。



## controlling_process/2

`controlling_process(Socket, Pid) -> ok | {error, Reason}`

> 为套接字分配一个新的控制过程 PID。控制进程是从套接字接收消息的进程。如果由当前控制进程以外的任何其他进程调用，则返回`{ error，not_owner }`。如果 Pid 标识的进程不是现有的本地 Pid，则返回`{ error，badarg }`。`{ error，badarg }`在执行此函数期间关闭 Socket 的某些情况下也可能返回。

# observer

## start/0

> 打开进程信息监控gui程序





# gen_server

>  **gen_server工作流程** 
>
>  1. 调用`gen_server:start_link(Name, CallBackMod, StartArgs, Options)`来启动服务器
>
>  2. 第一个回调函数是 `CallBackMod:init(StartArgs)`，它必须返回`{ok, State}`， State作为`handle_call/3`的第三个参数重新出现
>
>  3. 停止服务器，`handle_call(stop, From, Tab)`返回`{stop, normal, stopped, Tab}`，它会停止服务器，第二个参数（normal）
>
>      被作为 `terminate(Reason, State)`的 第一个参数；第三个参数(stopped)会成为my_bank:stop/0的返回值
>
>  4. 要调用服务器，客户端需要调用`gen_server:call(Name, Request)`。它最终回调的是回调模块里的`handle_call/3`，其中Request作为`handle_call/3`的第一个参数，From为客户端PID，State为服务端状态
>
>  5. `handle_call/3`一定要有返回值给客户端，如果`handle_call/3`返回值为`{noreply...}`或者`{stop, Reason, State}`，
>
>      则需在`handle_call/3`结束前手动调用`gen_server:reply(From, Reply)`给客户端一个返回值（或者把返回值的任务交给其他进程）
>
>  6. `handle_call/3`一般会返回`{reply, Reply, NewState}`,Reply会作为客户端`gen_server:call/2`的返回值，NewState作为服务端的新状态
>
>  7. `gen_server:call/2`对应`handle_call/3`（有返回值，称为rpc）；`gen_server:cast/2`对应`handle_cast/3`（没有返回值，称为播发）
>
>  8. `handle_info/2`用来处理不是通过`gen_server:call/2`和`gen_server:cast/2`发给服务器的消息，如服务器连接到别的进程收到`{'EXIT', Pid, Why}`或者其他知道服务器PID的进程向他发送的消息（`PID ! Msg`）
>
>  9. 服务器停止或者崩溃前都会回调 `terminate(Reason, State)`，服务器将以Reason终止，需要在这里做必要的清理工作，也可以将此时的服务器状态保存到磁盘或者发送给其他进程
>
>  10. `code_change/3` 会在系统执行软件升级时由版本处理子系统调用

## 创建

### start/3

`start(Mod, Args, Options) -> start_ret()`

### start/4

`start(ServerName, Mod, Args, Options) -> start_ret()`

> 创建一个独立的 gen _ server 进程，也就是说，一个 `gen_server` 进程不是监督树的一部分，因此没有监督者。



### start_link/3

`start_link(Mod, Args, Options) -> start_ret() `



### start_link/4

`start_link(ServerName, Mod, Args, Options) -> start_ret() `

> 创建 `gen_server` 进程作为监视树的一部分。这个函数将由主管直接或间接地调用。例如，它确保 `gen_server` 进程链接到管理员。

```erlang
Types:

ServerName = 
      {local,LocalName } 通过使用 register/2 使得gen_server进程 在本地注册为LocalName
    | {global,GlobalName} 通过使用 global:register_name/2 使得gen_server进程 在全局注册为 GlobalName
    | {via,Module,ViaName} 
 LocalName = atom()
 GlobalName = ViaName = term()
Module = atom()
Args = term()
Options = [start_opt()]
start_opt() =
    {timeout, Timeout :: timeout()} % Gen_server 进程被允许花费多少毫秒进行初始化或终止，而 start 函数返回{ error，timeout }。
    |  {spawn_opt, SpawnOptions :: [proc_lib:spawn_option()]} |
    enter_loop_opt()
start_ret() = 
    {ok,Pid} 创建并初始化成功
    | ignore  
    | {error,Reason}  失败，如{error,{already_started,Pid}} 表示 FsmName创建的进程已存在
 Pid = pid()
 Error = {already_started,Pid} | term()
```





## 调用

### call/2

`call(server_ref(), Request) -> Reply`

### call/3

`server_ref(), Request, Timeout) -> Reply`

> 通过发送请求并等待应答到达或超时发生，对 `gen_server` 进程的 `ServerRef` 进行==同步==调用。`Gen_server` 进程调用 `Module: handle_call/3`来处理请求。



### multi_call/2

> 对**多个节点**上名字**都**为`Name`的gen_server进程进行同步调用





### cast/2

`cast(server_ref(), Request) -> Reply`

> 向 `gen_server` 进程的 `ServerRef` 发送一个异步请求并立即返回 ok，忽略目标节点或 `gen_server` 进程是否不存在。`Gen_server` 进程调用 `Module: handle_cast/2`来处理请求。

## 停止

### stop/1

`stop(ServerRef) -> ok`



### stop/3

`stop(ServerRef , Reason, Timeout) -> ok`

> 命令 ServerRef 指定的通用服务器退出，并使用指定的“Resaon”(默认为“normal”) ，然后等待服务器终止。Gen _ server 进程在退出之前调用 `Module: terminate/2`。
>
> 如果gen_server以预期的原因终止，则函数返回 ok。除了`normal`、`shutdown`或`{shutdown,Term}` 以外的任何其他原因都会导致使用 `logger(3)`发出错误报告。默认的`Reason`是`normal`。
>
> `Timeout` 是一个大于零的整数，它指定等待通用 gen_server终止或等待原子无穷大无限等待的毫秒数。默认值为`infinity`。如果未在指定时间内终止，则会引发超时异常。	
>
> 如果进程不存在，调用将以 noproc 原因退出调用进程，如果连接到运行服务器的远程节点失败，则以`{ nodedown，Node }`原因退出调用进程。

```erlang
Types:

server_ref() =
    pid() |
    (LocalName :: atom()) |
    {Name :: atom(), Node :: atom()} |
    {global, GlobalName :: term()} |
    {via, RegMod :: module(), ViaName :: term()}


Reason = term()
Timeout = int()>0 | infinity
```



## 回复

### reply/2

` reply(Client, Reply) -> ok`

> `Gen_server` 进程可以使用这个函数向`call/2,3`或 `multi_call/2,3,4`的客户端==显式==发送一个应答，当应答不能在 `Module: handle _ call/3`的返回值中传递（即返回值形式为`{noreply...}`）时。





## 回调函数

### init/1

` Module:init(Args) -> Result`

```erlang
Args = term()
Result = {ok,State} 初始化成功
  | {ok,State,Timeout} 初始化成功，进程在Timeout毫秒内没有收到请求或消息会超时
  | {ok,State,hibernate} 初始化成功，进程进入休眠状态，等待下一条消息到达
  | {ok,State,{continue,Continue}} 初始化成功，该进程将执行Module:handle_continue/2函数
  | {stop,Reason} 初始化失败，原因是Reason
  | ignore 初始化失败，原因是normal
 State = term()
 Timeout = timeout()
 Reason = term()
```

> 每当使用 `start/3、4`、 `start_monitor/3、4`或 `start_link/3、4`启动 `gen_server` 进程时，新进程将调用此函数进行初始化。
>
> 



### handle_call/3

` Module:handle_call(Request, From, State) -> Result`

> `Request`为请求，`From`为客户端PID， State为服务端旧状态
>
> ```erlang
> Result如下：  
> {reply,Reply,NewState}  有返回值,Reply将放回给客户端,服务器以NewState继续运行
> | {reply,Reply,NewState,Timeout}   有返回值,Reply将放回给客户端,服务器以NewState继续运行,进程在Timeout毫秒内没有收到请求或消息会超时
> | {reply,Reply,NewState,hibernate}  有返回值,Reply将放回给客户端,服务器以NewState继续运行,进程进入休眠状态，等待下一条消息到达
> | {reply,Reply,NewState,{continue,Continue}} 有返回值,Reply将放回给客户端,服务器以NewState继续运行,该进程将执行Module:handle_continue/2函数
> | {noreply,NewState} 没有返回值,服务器以NewState继续运行
> | {noreply,NewState,Timeout} 没有返回值,服务器以NewState继续运行,进程在Timeout毫秒内没有收到请求或消息会超时
> | {noreply,NewState,hibernate}  没有返回值,服务器以NewState继续运行,进程进入休眠状态，等待下一条消息到达
> | {noreply,NewState,{continue,Continue}}   没有返回值,服务器以NewState继续运行,该进程将执行Module:handle_continue/2函数
> | {stop,Reason,Reply,NewState} 服务器停止,并给调用者发送一个回应
> | {stop,Reason,NewState} 服务器停止,不给调用者发送一个回应
> ```

### handle_cast/2

` Module:handle_cast(Request, State) -> Result`

> `Request`为请求，`From`为客户端PID， State为服务端旧状态
>
> ```erlang
> Result如下：  
> {noreply,NewState} 没有返回值,服务器以NewState继续运行
> | {noreply,NewState,Timeout} 没有返回值,服务器以NewState继续运行,进程在Timeout毫秒内没有收到请求或消息会超时
> | {noreply,NewState,hibernate}  没有返回值,服务器以NewState继续运行,进程进入休眠状态，等待下一条消息到达
> | {noreply,NewState,{continue,Continue}} 没有返回值,服务器以NewState继续运行,该进程将执行Module:handle_continue/2函数
> | {stop,Reason,NewState} 服务器停止,不给调用者发送一个回应
> ```



### handle_info/3

` Module:handle_info(Info, State) -> Result`

> `Request`为请求， State为服务端旧状态
>
> ```erlang
> Result如下：  
> {noreply,NewState}  没有返回值,服务器以NewState继续运行
> | {noreply,NewState,Timeout}  没有返回值,服务器以NewState继续运行,进程在Timeout毫秒内没有收到请求或消息会超时
> | {noreply,NewState,hibernate}  没有返回值,服务器以NewState继续运行,进程进入休眠状态，等待下一条消息到达
> | {noreply,NewState,{continue,Continue}} 没有返回值,服务器以NewState继续运行,该进程将执行Module:handle_continue/2函数
> | {stop,Reason,NewState} 服务器停止,不给调用者发送一个回应
> ```



# gen_fsm

> 工作流程
>
> 1. ```erlang
>     gen_fsm 回调函数和导出函数对应关系
>     -----------------------------
>     gen_fsm:start_link ----->Module:init/1 % 初始化
>     gen_fsm:send_event ----->Module:StateName/2 % 处理异步事件
>     gen_fsm:send_all_state_event ----->Module:handle_event/3 % 处理异步全局事件
>     gen_fsm:sync_send_event ----->Module:StateName/3 % 处理同步事件
>     gen_fsm:sync_send_all_state_event ----->Module:handle_sync_event/4 % 处理全局同步事件
>     ------>Module:handle_info/3
>     ------>Module:terminate/3
>     ------>Module:code_change/4
>         
>     % 处理普通事件： 是 fsm 在 特定状态 接收到某个 特定事件 触发回调函数  
>     % 处理全局事件： 是 fsm 在 任意状态 接收到某个 特定事件 触发回调函数
>     % 区分是普通事件和全局事件的唯一标准是，看
>     ```



## 创建

### start/3

`start(Mod, Args, Options) -> Result`

### start/4

`start(FsmName, Mod, Args, Options) -> Result`

> 创建一个独立的 gen_fsm 进程，也就是说，这个进程不是监督树的一部分，因此没有监督者。

```erlang
Types:
FsmName = {local,Name} | {global,GlobalName}
  | {via,Module,ViaName}
 Name = atom()
 GlobalName = ViaName = term()
Module = atom()
Args = term()
Options = [Option]
 Option = {debug,Dbgs} | {timeout,Time} | {spawn_opt,SOpts}
  Dbgs = [Dbg]
   Dbg = trace | log | statistics
    | {log_to_file,FileName} | {install,{Func,FuncState}}
  SOpts = [term()]
Result = {ok,Pid} | ignore | {error,Error}
 Pid = pid()
 Error = {already_started,Pid} | term()
```



### start_link/3

`start_link(Mod, Args, Options) -> Result `



### start_link/4

`start_link(FsmName, Mod, Args, Options) -> Result `

> 创建一个 gen _ fsm 进程作为监视树的一部分。该函数将由主管直接或间接地调用。例如，它确保 gen _ fsm 进程链接到主管。



```erlang
Types:

FsmName = 
      {local,Name} 通过使用 register/2 使得 fsm进程 在本地注册为Name
    | {global,GlobalName} 通过使用 global:register_name/2 使得 fsm进程 在全局注册为 GlobalName
    | {via,Module,ViaName} 
如果不提供名字，则fsm进程不注册
 Name = atom()
 GlobalName = ViaName = term()
Module = atom()
Args = term()
Options = [Option]
 Option = 
      {debug,Dbgs}  
    | {timeout,Time} 
    | {spawn_opt,SOpts}
  Dbgs = [Dbg]
   Dbg = trace | log | statistics
    | {log_to_file,FileName} | {install,{Func,FuncState}}
  SOpts = [SOpt]
   SOpt - see erlang:spawn_opt/2,3,4,5
Result = 
    {ok,Pid} 创建并初始化成功
    | ignore  
    | {error,Error}  失败，如{error,{already_started,Pid}} 表示 FsmName创建的进程已存在
 Pid = pid()
 Error = {already_started,Pid} | term()
```



## 调用

### send_event/2

`send_event(FsmRef, Event) -> ok`

> 将事件==异步==发送到 gen _ fsm 进程的 FsmRef 并立即返回 ok。Gen _ fsm 进程调用 `Module: StateName/2`来处理事件，其中 StateName 是 gen_fsm 进程当前状态的名称。

```
Types:

FsmRef = 
     Name 
  | {Name,Node} 
  | {global,GlobalName}
  | {via,Module,ViaName} | pid()
 Name = Node = atom()
 GlobalName = ViaName = term()
Event = term()

FsmRef can be any of the following:
The pid
Name, if the gen_fsm process is locally registered
{Name,Node}, if the gen_fsm process is locally registered at another node
{global,GlobalName}, if the gen_fsm process is globally registered
{via,Module,ViaName}, if the gen_fsm process is registered through an alternative process registry
```



### send_all_state_event/2

`send_all_state_event(FsmRef, Event) -> ok`

> 将事件==异步==发送到 gen _ fsm 进程的 FsmRef 并立即返回 ok。Gen _ fsm 进程调用 `Module: handle _ event/3`来处理事件。



### sync_send_event/2

`sync_send_event(FsmRef, Event) -> Reply`



### sync_send_event/3

`sync_send_event(FsmRef, Event, Timeout) -> Reply`

> ==同步== 将事件发送到 gen _ fsm 进程的 FsmRef，并==等待回复或超时发生==。Gen_fsm 进程调用 `Module: StateName/3`来处理事件，其中 StateName 是 gen _ fsm 进程当前状态的名称。



```erlang
Types:

FsmRef = Name | {Name,Node} | {global,GlobalName}
  | {via,Module,ViaName} | pid()
 Name = Node = atom()
 GlobalName = ViaName = term()
Event = term()
Timeout = int()>0 | infinity % Timeout 是一个大于零的整数，它指定等待应答的毫秒数，或无限长的原子等待时间。默认值为5000。如果在指定的时间内没有收到应答，则函数调用失败。
Reply = term()
```



### sync_send_all_state_event/2

`sync_send_all_state_event(FsmRef, Event) -> Reply`



### sync_send_all_state_event/3

`sync_send_all_state_event(FsmRef, Event, Timeout) -> Reply`

> ==同步== 将全局事件发送到 gen_fsm 进程的 FsmRef，并等待回复或超时发生。`Gen_fsm` 进程调用 `Module: handle_sync_event/4`来处理事件



```erlang
Types:

FsmRef = Name | {Name,Node} | {global,GlobalName}
  | {via,Module,ViaName} | pid()
 Name = Node = atom()
 GlobalName = ViaName = term()
Event = term()
Timeout = int()>0 | infinity
Reply = term()
```



## 停止

### stop/1

`stop(FsmRef) -> ok`



### stop/3

`stop(FsmRef, Reason, Timeout) -> ok`

> 命令有限状态机使用指定的`Reason`退出，并等待它终止。Gen _ fsm 进程在退出之前调用 `Module: terminate/3`。
>
> 如果fsm以预期的原因终止，则函数返回 ok。除了`normal`、`shutdown`或`{shutdown,Term}` 以外的任何其他原因都会导致使用 `error_logger: format/2`发出错误报告。默认的`Reason`是`normal`。
>
> `Timeout` 是一个大于零的整数，它指定等待通用 FSM 终止或等待原子无穷大无限等待的毫秒数。默认值为`infinity`。如果泛型有限状态机未在指定时间内终止，则会引发超时异常。



```erlang
Types:

FsmRef = Name | {Name,Node} | {global,GlobalName}
  | {via,Module,ViaName} | pid()
 Node = atom()
 GlobalName = ViaName = term()
Reason = term()
Timeout = int()>0 | infinity
```







## 回调函数

### init/1

`init(Args) -> Result`

```erlang
Args = term(),该参数由 gen_fsm:start_link/4 中传过来
Result = {ok,StateName} 初始化成功,
  | {ok,StateName,Timeout} 初始化成功，进程在Timeout毫秒内没有收到请求或消息会超时
  | {ok,StateName,hibernate} 初始化成功，进程进入休眠状态，等待下一条消息到达
  | {stop,Reason} 初始化失败，原因是Reason
  | ignore 初始化失败，原因是normal
 StateName = atom(), StateName为状态名，表示下一个被调用的回调函数
 Timeout = timeout()
 Reason = term()
```

### StateName/2

`StateName(Event, StateData) -> Result`

**处理异步事件，==不需要==有返回值给事件发送者**

> 每个可能的函数都应该有一个实例状态名。 每当 `gen_fsm` 接收到使用==`gen_fsm:send_event/2`==发送的事件时，与当前事件同名的 `StateName`回调函数会被调用来处理事件。 如果发生超时，也会调用它。

```erlang
Result = {next_state, NextStateName :: atom(), NextState :: #test_state{}} % NextStateName表示处理完当前事件后fsm状态转为NextStateName状态
    |    {next_state, NextStateName :: atom(), NextState :: #test_state{}, timeout()} 
    |    {next_state, NextStateName :: atom(), NextState :: #test_state{}, hibernate} 
    |    {stop, Reason :: term(), NewState :: #test_state{}}). % 回调Module:terminate(Reason,StateName,NewStateData)
```



### StateName/3

`StateName(Event, From, StateData) -> Result`

**处理同步事件，==需要==有返回值给事件发送者**

> 对于每个可能的状态名称，此函数都有一个实例。每当 `gen_fsm` 进程接收到使用 ==`gen_fsm:sync_send_event/2,3`==发送的事件时，将调用与当前状态名 StateName 同名的此函数的实例来处理该事件。

```erlang
Event = term()
From = {pid(),Tag}
StateData = term()
Result = {reply,Reply,NextStateName,NewStateData}
  | {reply,Reply,NextStateName,NewStateData,Timeout}
  | {reply,Reply,NextStateName,NewStateData,hibernate}
  | {next_state,NextStateName,NewStateData}
  | {next_state,NextStateName,NewStateData,Timeout}
  | {next_state,NextStateName,NewStateData,hibernate}
  | {stop,Reason,Reply,NewStateData} | {stop,Reason,NewStateData}
 Reply = term()
 NextStateName = atom()
 NewStateData = term()
 Timeout = int()>0 | infinity
 Reason = normal | term()
```



### handle_event/3

`handle_event(Event, StateName, StateData) -> Result`

**处理全局异步事件，==不需要==有返回值给事件发送者**

> 只要 `gen_fsm` 进程接收到使用 ==`send_all_state_event/2`==发送的事件，就会调用该函数来处理该事件。
>
> ==`StateName`== 是 `gen_fsm` 进程的==当前==状态名

```erlang
Event = term()
StateName = atom()
StateData = term()
Result = 
    {next_state,NextStateName,NewStateData}
  | {next_state,NextStateName,NewStateData,Timeout}
  | {next_state,NextStateName,NewStateData,hibernate}
  | {stop,Reason,NewStateData}
 NextStateName = atom()
 NewStateData = term()
 Timeout = int()>0 | infinity
 Reason = term()
```



### handle_sync_event/4

`handle_sync_event(Event, From, StateName, StateData) -> Result`

**处理全局同步事件，==需要==有返回值给事件发送者**

> 每当 `gen_fsm` 进程接收到使用 ==`sync_send_all_state_event/2,3`==发送的事件时，将调用此函数来处理该事件。
>
> StateName 是 `gen_fsm` 进程的当前状态名。



```erlang
Event = term()
From = {pid(),Tag}
StateName = atom()
StateData = term()
Result = 
    {reply,Reply,NextStateName,NewStateData}
  | {reply,Reply,NextStateName,NewStateData,Timeout}
  | {reply,Reply,NextStateName,NewStateData,hibernate}
  | {next_state,NextStateName,NewStateData}
  | {next_state,NextStateName,NewStateData,Timeout}
  | {next_state,NextStateName,NewStateData,hibernate}
  | {stop,Reason,Reply,NewStateData} | {stop,Reason,NewStateData}
 Reply = term()
 NextStateName = atom()
 NewStateData = term()
 Timeout = int()>0 | infinity
 Reason = term()
```

### handle_info/3

`handle_info(Info, StateName, StateData) -> Result`

> 当接收到除同步或异步事件(或系统消息)之外的任何其他消息时，`gen_fsm`进程将调用该函数。

```erlang
Info = term() % 接收到的消息
StateName = atom() % 当前状态
StateData = term() 
Result = 
    {next_state,NextStateName,NewStateData}
  | {next_state,NextStateName,NewStateData,Timeout}
  | {next_state,NextStateName,NewStateData,hibernate}
  | {stop,Reason,NewStateData}
 NextStateName = atom()
 NewStateData = term()
 Timeout = int()>0 | infinity
 Reason = normal | term()
```



### terminate/3

`terminate(Reason, StateName, StateData) -> void`

> 当即将终止时，`gen_fsm` 进程调用该函数。它是 `init/1`的对立面，做任何必要的清理。当它返回时，`gen _ fsm` 进程将终止为 `Reason`。==忽略返回值==。

```erlang
Reason = normal | shutdown | {shutdown,term()} | term()
StateName = atom()
StateData = term()
```









