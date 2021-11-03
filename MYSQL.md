<h3>触发器</h3>

>触发器类型                                                NEW 和 OLD的使用
>
>> INSERT 型触发器									NEW表示将要或者已经新增的数据
>>
>> UPDATE 型触发器								  OLD表示修改之前的数据，NEW表示将要或已经修改后的数据
>>
>> DELETE 型触发器								   OLD 表示将要或者已经删除的数据

<h4>创建触发器</h4>

**--通过触发器记录 tmp 表的数据变更日志 tmp_logs , 包含增加，修改，删除**

<h6>插入的触发器</h6>

```mysql
create trigger tmp_insert_trigger
after insert
on tmp
for each row
begin
	insert into tmp_logs(id, operation, operation_time, operation_id, operation_params) values(null, 'insert', now(), new.id, conact('插入后(id:', new.id, ',name:',new.name, ',age:', new.age, ', salary:', new.salary, ')'));
end$
```

***注意：$是自定义的分隔符***

<h4>查看触发器</h4>

```mysql
show triggers;
```

<h4>删除触发器</h4>

```mysql
drop trigger [schema_name.]trigger_name
```



<h3>MYSQL体系结构</h3>

> 连接层
>
> > 主要完成一些类似连接处理、授权认证、及相关的安全方案
>
> 服务层
>
> > 完成SQL接口，缓存查询，SQL的分析与优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如 过程、函数等。
> >
> > 确定表的查询顺序，是否利用索引等。
>
> 引擎层
>
> > 负责mysql中数据的存储和提取，服务器通过API和存储引擎进行通信
>
> 存储层
>
> > 将数据存储在文件系统上，并完成与存储引擎的交互



<h3>储存引擎</h3>

> 存储引擎就是存储数据，建立所以，更新查询数据等等技术的实现方式。存储引擎是基于表的，而不是基于库的。所以存储引擎也可以被称为表类型，或者编写存储引擎。
>
> 在v5.5之前默认的是myisam; 在v5.5之后默认的存储引擎是innodb

<h5>查看存储引擎</h5>

```mysql
show engines;
```

<h5>各种存储引擎特性</h5>

> 特点									InnoDB										MyISAM									MEMORY									MERGE									NDB
>
> > 存储限制						65TB											有												有												没有											有
> >
> > 事务安全						***支持***											
> >
> > 锁机制							***行锁(适合高并发)***                   	 表锁											表锁											表锁										 行锁
> >
> > B树索引						  支持											  支持										    支持										    支持										支持
> >
> > 哈希索引																																  支持
> >
> > 全文索引						 支持(5.6之后)							  支持
> >
> > 支持外键						 ***支持***											  

***注意：*** innodb 是 mysql 引擎中唯一支持外键的引擎

<h5>外键</h5>

> ON DELETE RESTRICT  ---------> 删除主表数据时，如果有关联记录，不删除；
>
> ON UPDATE CASCADE  ---------> 更新主表时，如果子表有关联记录，更新子表记录；

<h5>InnoDB</h5>

<h6>存储方式</h6>

> InnoDB  存储表和索引有以下两种方式：
>
> > a. 使用共享表空间存储，这种方式创建的表的结构保存在.frm文件中，数据和索引保存在 innodb_data_home_dir和innodb_data_path定义的表空间中，可以是多个文件。
> >
> > b. 使用多表空间存储，这种方式创建的表的表结构任然存在.frm文件中，凡是每个表的数据和索引单独保存在.ibd中。

<h5>MyISAM</h5>

> 不支持事务，也不支持外键

<h6>存储方式</h6>

> 每个MyISAM在磁盘上存储成3个文件，其文件名都和表名相同，但拓展名分别是：
>
> > .frm [存储表定义]
> >
> > .MYD [MYData，存储数据]
> >
> > .MYI(MYIndex，存储索引)



<h3>存储引擎的选择</h3>

> InnoDB
>
> > 对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询以外，还包含很多的更新、删除操作。
>
> MyISAM
>
> > 如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高。



<h3>优化SQL</h3>

<h4>优化SQL步骤</h4>

> 查看SQL执行频率
>
> > show status like 'Com_______'
> >
> > 查询执行的sql【仅限当前连接】频率
> >
> > show global status like 'Com_______'
> >
> > 查询全局的执行的sql频率
> >
> > show gloabl status like 'Innodb_rows_%';
> >
> > 展示innodb相关的影响行数
>
> 定位低效率执行SQL
>
> 可以通过以下两种方式定位执行效率低的SQL语句
>
> > 慢查询日志
> >
> > > 
> >
> > show processlist
> >
> > > 查看当前MySQL在进行的线程，包括线程状态、是否锁表等，可以实时地查看SQL的执行情况，同时对一些缩表操作进行优化。
> > >
> > > ![命令效果](https://i.imgur.com/TI6KxpX.png)
> > >
> > > > id：用户登录mysql时，系统分配的"connection_id"，可以使用函数connection_id()查看
> > > >
> > > > user：显示当前用户，如果不是root，这个名是就像是用户权限范围的sql语句
> > > >
> > > > host：显示这个语句是从哪个ip的哪个端口上发的，可以用来跟踪出现问题语句的用户
> > > >
> > > > db：显示这个进程目前连接的是哪个数据库
> > > >
> > > > command：显示当前连接的执行的命令，一般取值为休眠(sleep)、查询(query)、连接(connect)等
> > > >
> > > > time：显示这个状态持续的时间，单位是秒
> > > >
> > > > state：显示当前连接的sql语句的状态，很重要的列，state描述的是语句执行中的某一个状态，一个sql语句，以查询为例，可能需要经过copying to tmp table、sorting result、sending data等状态才可以完成
> > > >
> > > > info：显示这个sql语句，是判断问题语句的一个重要依据
>
> explain 分析执行计划
>
> >通过以上步骤查询到效率低的SQL语句后，可以通过EXPLAIN或者DESC命令获取MySQL如何执行SELECT语句的信息，包括在SELECT语句执行过程中表如何连接和连接顺序
> >
> >> explain select * from gs_member where id > 19;
> >
> >> ![效果](https://i.imgur.com/AnVAQM6.png)
> >
> >> > id：select查询的序列号，是一组数字，表示的是查询中执行select子句或者是操作表的顺序。id有三种:
> >> >
> >> > a. id相同表示加载表的顺序是从上到下
> >> >
> >> > b. id不同id值越大，优先级越高，越先被执行
> >
> >> > select_type 表示select的类型[从上往下效率越来越低]
> >> >
> >> > > SIMPLE：简单的select查询，查询中不包含子查询或者union
> >> > >
> >> > > PRIMARY：查询中若包含任何复杂的子查询，最外层查询标记为该标识
> >> > >
> >> > > SUBQUERY：在SELECT 或 WHERE 列表中包含子查询
> >> > >
> >> > > ![效果](https://i.imgur.com/Q1FAdjV.png)
> >> > >
> >> > > DERIVED：在FORM列表中包含的子查询，被标记为DERIVED(衍生)MYSQL会递归执行这些子查询，把结果放在临时表中
> >> > >
> >> > > ![效果](https://i.imgur.com/stxwyPP.png)
> >> > >
> >> > > UNION：若第二个SELECT出现在UNION之后，则标记为UNION；若UNION包含在FORM子句的子查询中，外层SELECT将被标记为：DERIVED
> >> > >
> >> > > UNION RESULT：从UNION表获取结果的SELECT
> >> > >
> >> > > ![效果](https://i.imgur.com/luGCugD.png)
> >> >
> >> > table 查询的表
> >> >
> >> > type 显示的是访问类型，是较为重要的一个指标，可取值为：[效率从上到下依次降低]
> >> >
> >> > > ***一般来说，我们需要保证查询至少达到range级别，最好达到ref***
> >> >
> >> > > NULL：MySQL不访问任何表，索引，直接返回结果
> >> > >
> >> > > system：表只有一行记录(等于系统表)，这是const类型的特例，一般不会出现
> >> > >
> >> > > const：表示通过索引一次就找到了，const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快。如将主键置于where列表中，MySQL就能将该查询转换为一个常量，const将“主键”或“唯一”索引的所有部分与常量值进行比较
> >> > >
> >> > > eq_ref：类似ref，区别在于使用的是唯一索引，使用主键的关联查询，关联查询出的记录只有一条。常见于主键或唯一索引扫描
> >> > >
> >> > > ref：非唯一索引扫描，返回匹配某个单独值得所有行。本质上也是一种索引访问，返回所有匹配某个单独值的所有行(多个) 
> >> > >
> >> > > range：只检索给定返回的行，使用一个索引来选择行。where之后出现between，<，>，in 等操作
> >> > >
> >> > > index：index 与 all 的区别为 index类型只是遍历了索引树，通常比all快，all是遍历数据文件
> >> > >
> >> > > all：将遍历全表以找到匹配的行
> >> >
> >> > key 
> >> >
> >> > >possible_key：显示可能应用在这张表的索引，一个或多个
> >> > >
> >> > >key：实际使用的索引，如果为null，则没有使用索引
> >> > >
> >> > >key_len：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好。
> >> >
> >> > rows  扫描行的数量
> >> >
> >> > extra 其他的额外的执行计划信息，在该列展示
> >> >
> >> > ***出现第一个和第二个则需要对语句进行优化，因为效率很差***
> >> >
> >> > > using filesort：说明mysql会对数据使用一个外部的索引排序，而不是按照表的索引顺序进行读取，称为“文件排序”，效率低
> >> > >
> >> > > using temporary：使用了临时表保存中间结果，MySQL在对查询结果排序时使用临时表。常见于order by 和 group by
> >> > >
> >> > > using index：表示相应的select操作使用了覆盖索引，避免访问表的数据行，效率不错。
> >> > >
> >> > > 5.6之后的版本
> >> > >
> >> > > using index condition：使用了索引，但是需要回表查询
> >> > >
> >> > > using where：在查找使用索引的情况下，需要回表去查询所需的数据
> >> > >
> >> > > using index; using where：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询数据
>
> show profile 分析SQL语句
>
> ```mysql
> ##查看是否开启
> select @@have_profiling; ##是否安装
> select @@profiling;  ##是否开启
> set profiling=1;  ##针对当前会话开启
> ```
>
> ![演示](https://i.imgur.com/XZVTdr9.png)
>
> ![演示](https://i.imgur.com/u93CizX.png)
>
> ![演示](https://i.imgur.com/94InOB5.png)
>
> ```mysql
> ##查看之前执行的sql查询的执行时间
> show profiles;
> ```
>
> ```mysql
> ##查询具体的查询的执行详情【id】上面的show profiles查询出来的Id
> show profile for query id;
> ```
>
> > ***TIPS：***
> >
> > ***Sending data 状态表示MySQL线程开始访问数据行并把结果返回给客户端，而不仅仅是返回给客户端。由于在Sending data状态下，MySQL线程往往需要做大量的磁盘读取操作，所以经常是整个查询中耗时最长的状态。***
>
> 
>
> trace 分析优化器执行计划
>
> > MySQL5.6提供了对SQL的跟踪trace，通过trace文件能够进一步了解为什么优化器选择A计划，而不是选择B计划。
> >
> > 打开trace，设置格式为json，并设置trace最大能够使用的内存大小，避免解析过程中因为默认内存过小而不能够完整展示。
> >
> > ```mysql
> > SET optimizer_trace="enabled=on",end_markers_in_json=on;
> > set optimizer_trace_max_mem_size=1000000;
> > ```
> >
> > 执行SQL
> >
> > ```mysql
> > select * from tb_item where id < 4;
> > ```
> >
> > 最后，检查information_schema.optimizer_trace就可以知道MySQL是如何执行SQL的：
> >
> > > ```mysql
> > > select * from information_schema.optimizer_trace\G;
> > > ```
> > >

<h4>索引的使用</h4>

> 创建索引
>
> ```mysql
> create index idx_name on tab_name(field_name);
> ```
>
> 创建联合索引
>
> ```mysql
> create index idx_name on tab_name(field1, field2, field3);
> ```
>
> > 测试1
> >
> > ```mysql
> > select * from tab_name where field1="条件1" and field2="条件2" and field3="条件3"
> > ```
> >
> > ***此时会触发索引***
> >
> > 1.全值匹配，对索引中所有列都指定具体值
> >
> > > 促发索引
> >
> > 2.最左前缀原则
> >
> > > 如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左列开始，并且不跳过索引中的列。
> > >
> > > ***如果查询条件满足最左前缀原则，查询条件的顺序不影响***
> > >
> > > ```mysql
> > > select * from tab_name where field1="条件1" and field3="条件2"
> > > ##如同爬楼梯 爬了第一层 没有第二层 就爬不了第三层
> > > ```
> > >
> > > ***此时走了索引，但是只是走的 field1 的索引，没有走 field3 的索引***
> >
> > 3.范围查询右边的列，不能使用索引。
> >
> > > ```mysql
> > > select * from tab_name where field1="条件1" and field2>"条件2" and field3="条件3";
> > > ```
> > >
> > > ***此时 field1 和 field2 走了索引 但是field3不会走索引***
> >
> > 4.不要在索引列上进行运算操作，索引将失效
> >
> > 5.字符串不加单引号，造成索引失效
> >
> > 6.尽量使用覆盖索引，避免select *
> >
> > > 尽量使用覆盖索引 ( 只访问索引的查询 [索引列完全包含查询列]，减少select * )
> > >
> > > 如果查询列超出索引列，也会降低性能
> > >
> > > ```mysql
> > > select field1 from tab_name where field1="条件1";  //不需要回表查询
> > > ```
> > >
> > > ```mysql
> > > select field1, field2, feild3 from tab_name where field1="条件1";  //不需要回表查询
> > > ```
> > >
> > > ```mysql
> > > select field1, field2, field3, field4 from tab_name where field1="条件1";  //需要回表查询【这里的field4不是复合索引字段】
> > > ```
> >
> > 7.用 or 分割开的条件，如果 or 前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到
> >
> > > ***如果是用 and 连接的，and 前的条件中有索引会走，即使后面的没有索引***
> >
> > 8.以%开头的LIKE模糊查询，索引失败 【前后都模糊查询也不走索引】
> >
> > > ***如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效***
> > >
> > > **解决方案：通过覆盖索引来解决**
> > >
> > > > ```mysql
> > > > select field1, field2, field3 from tab_name where field1 LIKE "%条件1%";  //此时会走索引
> > > > ```
> >
> > 9.如果MySQL评估使用索引比全表更慢，则不使用索引
> >
> > > 比如一个字段设置了单列索引，但是该索引的值重复性很高，当查询的条件是重复性很高的值的时候，MySQL会放弃索引，全表扫描
> >
> > 10.is NULL 和 is NOT NULL  有时索引失效
> >
> > > 如果数据表中的数据查询的字段，null 占多数则 is NOT NULL 走索引
> > >
> > > 如果数据表中的数据查询的字段，有值的数据 占多数则 is NULL 走索引
> >
> > 11.in 走索引，not in 不走索引，索引失败
> >
> > 12.单列索引和符合索引
> >
> > > 尽量使用复合索引，而少使用单列索引
> > >
> > > 创建复合索引
> > >
> > > ```mysql
> > > create index idx_name_sta_address on tab_seller(name, status, address);
> > > ##就相当于创建了三个索引:
> > > ## name
> > > ## name + status
> > > ## name + status + address
> > > ```
> > >
> > > 创建单列索引
> > >
> > > ```mysql
> > > create index idx_seller_name on tab_seller(name);
> > > create index idx_seller_status on tab_seller(status);
> > > create index idx_seller_address on tab_seller(address);
> > > ```
> > >
> > > ***数据库会选择一个最优的索引来使用，并不会使用全部索引***
>
> 查看索引使用情况
>
> > 查询条件
> >
> > ```mysql
> > ##查看当前的使用情况
> > show status like 'Handler_read%';  
> > ##查看全局的使用情况
> > show global status like 'Handler_read%';  
> > ```
> >
> > ![参数介绍](https://i.imgur.com/zkpK33H.png)

SQL优化

> 大批量插入数据[innodb]
>
> > 使用 load 命令通过文件大批量插入数据 
> >
> > > 有序数据的效率较高
> >
> > ```mysql
> > load data local infile '/root/sql.log' into table 'tb_user' fields terminated by ',' lines terminated by '\n';
> > ```
> >
> > 关闭唯一性校验
> >
> > > 在导入数据前执行关闭语句, 关闭唯一性校验，在导入结束后执行回复语句，恢复唯一性校验，可以提高导入效率
> >
> > ```mysql
> > SET UNIQUE_CHECK=0
> > ....
> > SET UNIQUE_CHECK=1
> > ```
> >
> > 手动提交事务
> >
> > > 如果应用使用自动提交的方式，建议在导入前执行 SET AUTOCOMMIT=0，关闭自动提交，导入结束后再执行SET AUTOCOMMIT=1，打开自动提交，也可以提高导入效率
> >
> > ```mysql
> > SET AUTOCOMMIT=0;
> > ...
> > SET AUTOCOMMIT=1;
> > ```
>
> 优化 insert 语句
>
> > 如果需要同时对一张表插入很多行数据，应该尽量使用多个值表的insert语句，这种方式将大大的缩减客户端与数据库之间的连接、关闭等消耗，使得效率比分开执行的单个insert语句快。
> >
> > > 示例
> > >
> > > 优化前
> > >
> > > ```mysql
> > > insert into tb_test values(1, 'Tom');
> > > insert into tb_test values(2, 'Cat');
> > > insert into tb_test values(3, 'Jerry');
> > > ```
> > >
> > > 优化后
> > >
> > > ```mysql
> > > insert into tb_test values(1, 'Tome'), (2, 'Cat'), (3, 'Jerry');
> > > ```
> >
> > 在事务中进行数据插入
> >
> > >```mysql
> > >start transaction;
> > >insert into tb_test values(1, 'Tome'), (2, 'Cat'), (3, 'Jerry');
> > >commit;
> > >```
> >
> > 数据有序插入【主键】
>
> 优化 order by 语句
>
> > 两种排序方式
> >
> > > 1.第一种是通过对返回数据进行排序，也就是通常说的filesort排序，所有不是通过索引直接返回排序结果的排序都叫 fileSort 排序。
> > >
> > > 2.第二种通过有序索引顺序扫描直接返回有序数据，这种情况即为using index，不需要额外排序，操作效率高。
> > >
> > > > ```mysql
> > > > ##表中 id 是主键索引  age_salary是复合索引
> > > > 
> > > > ##这里是 using filesort;
> > > > select * from tb_user order by age;  
> > > > 
> > > > ##这里是 using index;
> > > > select id, age, salary from tb_user order by age;
> > > > 
> > > > ##这里是 using filesort ，因为name没有索引
> > > > select id, age, salary, name from tb_user order by age;
> > > > 
> > > > ##这里是 using index
> > > > select id, age, salary from tb_user order by age desc, salary desc;
> > > > 
> > > > ##这里是 using filesort  因为多字段排序 应该同升同降
> > > > select id, age, salary from tb_user order by age desc, salary asc;
> > > > 
> > > > ##这里只用到部分索引 ，还是会回表查询 using index; using filesort  因为排序字段的顺序没有和复合索引的顺序相同
> > > > select id, age, salary from tb_user order by salary desc, age desc;
> > > > ```
> > > >
> > > > 多字段排序，要么统一升序，要么统一降序，不然不走索引
> > > >
> > > > 多字段排序，排序顺序应与复合索引的顺序一致
> >
> > filesort 的优化
> >
> > > 通过创建合适的索引，能够减少filesort的出现，但是某些情况下，条件限制不能让fielsort消失，那就需要加快filesort的排序操作。对于filesort，MySQL有两种排序算法：
> > >
> > > > 两次扫描算法：MySQL4.1之前，首先根据条件取出排序字段和行指针信息，然后在排序区sort buffer中排序。完成排序后，再根据行指针回表读取记录，该操作可能会导致大量随机 I/O 操作。
> > > >
> > > > 一次扫描算法：一次性取出满足条件的所有字段，然后在排序去sort buffer 中排序后直接输出结果集。排序时内存消耗较大，但是排序效率比两次扫描算法要高。
> > >
> > > MySQL通过比较系统变量max_length_for_sort_data 的大小和 Query 语句取出的字段总大小，来判定是那种排序算法，如果max_length_for_sort_data 更大，那么使用第二种优化之后的算法；否则使用第一种。
> > >
> > > ***可以适当提高 sort_buffer_size 和 max_length_for_sort_data 系统变量，来增大排序区的大小，提高排序效率***
> > >
> > > ```mysql
> > > ##显示变量信息
> > > show variables like 'max_length_for_sort_data';
> > > ```
>
> 优化 group by 语句
>
> > 由于GROUP BY 实际上也同样会进行排序操作，而且与ORDER BY相比，GROUP BY主要只是多了排序之后的分组操作。当然，如果在分组的时候还使用了其他的一些聚合函数，那么还需要一些聚合函数的计算。所以，在GROUP BY 的实现过程中，与ORDER BY 一样也可以利用到索引。
> >
> > > 如果查询包含group by 但是用户想要避免排序结果的消耗，则可以执行order by null 禁止排序，如下：
> > >
> > > ```mysql
> > > ##优化前：
> > > select age,count(*) from user group by age;
> > > ##优化后：
> > > select age,count(*) from user group by age order by null;
> > > ```
>
> 优化嵌套查询[子查询]
>
> >  尽量转化为连接查询
> >
> > >子查询
> > >
> > >![](https://i.imgur.com/1XlgqAv.png)
> > >
> > >联合查询
> > >
> > >![](https://i.imgur.com/uow2mTy.png)
> >
> > > ref 的效率 高于 index
>
> 优化 OR 条件
>
> > 对于包含 OR 的查询子句，如果要利用索引，则 OR 之间的每个条件列都必须用到索引，而且不能使用到复合索引；如果没有索引，则应该考虑增加索引。
> >
> > > ```
> > > ##表中 id 是主键索引  age_salary是复合索引
> > > 
> > > ## 这里不走索引
> > > select * from user where id = 1 or name = 'silence'
> > > ```
> > >
> > > 
> >
> > ***建议使用 union 替换 or***
> >
> > type 显示的是访问类型，是较为重要的一个指标，结果值从好到坏依次是：
> >
> > > system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL

