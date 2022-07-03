

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
> -  其他默认参数：{write_concurrency,false}, {read_concurrency,false}, {decentralized_counters,false}   

### dets:open_file/1

`open_file(Filename) -> {'ok', Reference} | {'error', Reason}`

> 打开已存在的表

### dets:open_file/2

`open_file(Name, [Arg]) -> {'ok', Name} | {'error', Reason}`



> ```erlang
>     Arg  :: {'access', access()}
>           | {'auto_save', auto_save()}
>           | {'estimated_no_objects', non_neg_integer()}
>           | {'file', file:name()}
>           | {'max_no_slots', no_slots()}
>           | {'min_no_slots', no_slots()}
>           | {'keypos', keypos()}
>           | {'ram_file', boolean()}
>           | {'repair', boolean() | 'force'}
>           | {'type', type()}
> ```
>
> 其中Args列表的值如下
>
> - 表的类型 type：set | bag | duplicate_bag, 默认为 set
> -  访问权限 access：read（只读） | read_write（读写），默认read_write
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





# gen_udp



# observer

## start/0

> 打开进程信息监控gui程序

