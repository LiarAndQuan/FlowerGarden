# 1. 数据库相关概念

**DB：数据库（Database）**
即存储数据的“仓库”，其本质是一个文件系统。它保存了一系列有组织的数据。

**DBMS：数据库管理系统（Database Management System）**
是一种操纵和管理数据库的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控 制。用户通过数据库管理系统访问数据库中表内的数据。

**SQL：结构化查询语言（Structured Query Language）**
专门用来与数据库通信的语言

SQL语言功能可以分为以下三类 :
- **DDL（Data Definition Languages 数据定义语言）** 这些语句定义了不同的数据库、表、视图、索引等数据库对象，还可以用来创建、删除、修改数据库和数据表的结构
- **DML（Data Manipulation Language 数据操作语言）** 用于添加、删除、更新和查询数据库记录，并检查数据完整性
- **DCL（Data Control Language 数据控制语言）** 用于定义数据库、表、字段、用户的访问权限和安全级别

四个默认数据库 : 
- **“information_schema”** 主要保存 MySQL 数据库服务器的系统信息， 比如数据库的名称、数据表的名称、字段名称、存取权限、数据文件所在的文件夹和系统使用的文件夹，等等 
- **“performance_schema”** 可以用来监控 MySQL 的各类性能指标。 
- **“sys”** 主要作用是以一种更容易被理解的方式展示 MySQL 数据 库服务器的各类性能指标，帮助系统管理员和开发人员监控 MySQL 的技术性能。 
- **“mysql”** 数据库保存了 MySQL 数据库服务器运行时需要的系统信息，比如数据文件夹、当前使用的 字符集、约束检查信息，等等

# 2. select ... from ... where的增强之路

### as

>as可以重命名一个列, 紧跟列名，也可以在列名和别名之间加入关键字AS，别名使用双引号，以便在别名中包含空格或特 殊的字符并区分大小写,AS 可以省略

![[Attachments/Images/Pasted image 20230423160202.png]]

### distinct

>distinct可以去除重复行, 其实是对后面所有列名的组合进行去重, 需要放在所有列名的前面

![[Attachments/Images/Pasted image 20230423143917.png]]

### order by

>使用order by排序, order by在字句的末尾, 可以对多列进行排序
>- asc(ascend): 升序, 默认
>- desc(descend): 降序

![[Attachments/Images/Pasted image 20230423150115.png]]

### limit

>格式: limit \[位置偏移量 , ]  行数
>MySQL 8.0中可以使用“limit 3 offset 4”，意思是获取从第5条记录开始后面的3条记录，和“limit 4,3;”返回的结果相同
>limit字句必须放在整个select语句后面

![[Attachments/Images/Pasted image 20230423150659.png]]

### group by与having

>可以使用group by字句将表中的数据分为若干组
>可以根据多个字段分组

![[Attachments/Images/Pasted image 20230423162844.png]]

>group by中使用with rollup可以在查询出的分组记录之后增加一条记录, 该记录计算查询出的所有记录的总和
>当使用ROLLUP时，不能同时使用ORDER BY子句进行结果排序，即ROLLUP和ORDER BY是互相排斥的

![[Attachments/Images/Pasted image 20230423163745.png]]

>having过滤分组:
>- 行已经被分组
>- 使用了聚合函数

![[Attachments/Images/Pasted image 20230423164329.png]]

### inner join

>内连接: 合并具有同一列的两个以上的表的行, 结果集中不包含一个表与另一个表不匹配的行
>
>select 字段列表
>from a inner join b
>on a.c = b.c

![[Attachments/Images/Pasted image 20230423153148.png]]

### left join , right join

>两个表在连接过程中除了返回满足连接条件的行以外还返回左（或右）表中不满足条件的 行 ，这种连接称为左（或右） 外连接。没有匹配的行时, 结果表中相应的列为空(NULL)

>有些员工没有部门

![[Attachments/Images/Pasted image 20230423153927.png]]

>有些部门没有员工

![[Attachments/Images/Pasted image 20230423154004.png]]

### union

>利用UNION关键字，可以给出多条SELECT语句，并将它们的结果组合成单个结果集
>合并时，两个表对应的列数和数据类型必须相同，并且相互对应,  各个SELECT语句之间使用UNION或UNION ALL关键字分隔
>union: 返回两个查询的结果集的并集，去除重复记录
>union all: 返回两个查询的结果集的并集。对于两个结果集的重复部分，不去重

![[Attachments/Images/Pasted image 20230423155422.png]]

其实相当于select * from employees  where email like '%a%' or department_id>90;

![[Attachments/Images/Pasted image 20230423160109.png]]



# 3. 聚合函数

1. avg, sum

>求一组数据的平均值, 总和

![[Attachments/Images/Pasted image 20230423161858.png]]

2. min, max

>最小值和最大值

![[Attachments/Images/Pasted image 20230423161917.png]]

3. count

>count(\*)返回表中记录总数
>count(expr)返回expr不为空的记录总数

# 4. 子查询

>子查询指一个查询语句嵌套在另一个查询语句内部的查询

### 单行子查询

>只返回一条结果
>例 : 返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id和工资

![[Attachments/Images/Pasted image 20230423170024.png]]

### 多行子查询

>返回多行结果, 使用多行比较操作符

|  操作符   |                          含义                           |
|:---------:|:-------------------------------------------------------:|
|    in     |                        任意一个                         |
| any /some | 需要和单行比较操作符一起使用,和子查询返回的某一个值比较 |
|    all    | 需要和单行比较操作符一起使用，和子查询返回的所有值比较  |
|           |                                                         |

例: 返回其它job_id中比job_id为‘IT_PROG’部门任一工资低的员工的员工号、姓名、job_id 以及salary

![[Attachments/Images/Pasted image 20230423171336.png]]

### 相关子查询

>如果子查询的执行依赖于外部查询，通常情况下都是因为子查询中的表用到了外部的表，并进行了条件关联，因此每执行一次外部查询，子查询都要重新计算一次，这样的子查询就称之为关联子查询

例: 查询员工中工资大于本部门平均工资的员工的last_name,salary和其department_id

![[Attachments/Images/Pasted image 20230423172718.png]]

还可以在from中使用子查询

![[Attachments/Images/Pasted image 20230423173534.png]]

>from型的子查询：子查询是作为from的一部分，子查询要用()引起来，并且要给这个子查询取别名， 把它当成一张“临时的虚拟的表”来使用

### exists和not exist

>关联子查询通常也会和exists操作符一起来使用，用来检查在子查询中是否存在满足条件的行
>not exist关键字表示如果不存在某种条件，则返回true，否则返回false

例: 查询公司管理者的employee_id，last_name，job_id，department_id信息

![[Attachments/Images/Pasted image 20230423174845.png]]

例: 查询departments表中，不存在于employees表中的部门的department_id和department_name

![[Attachments/Images/Pasted image 20230423175026.png]]

# 5. 创建和管理表

几种常用的数据类型

|   数据类型    |                                      描述                                       |
|:-------------:|:-------------------------------------------------------------------------------:|
|      int      |                  从-2^31到2^31-1的整型数据。存储大小为 4个字节                  |
|  char(size)   |               定长字符数据。若未指定，默认为1个字符，最大长度255                |
| varchar(size) |              可变长字符数据，根据字符串实际长度保存，必须指定长度               |
|  float(M,D)   | 单精度，占用4个字节，M=整数位+小数位，D=小数位。 D<=M<=255,0<=D<=30，默认M+D<=6 |
|  double(M,D)  |              双精度，占用8个字节，D<=M<=255,0<=D<=30，默认M+D<=15               |
| decimal(M,D)  |    高精度小数，占用M+2个字节，D<=M<=65，0<=D<=30，最大取值范围与DOUBLE相同。    |
|     date      |                          日期型数据，格式'YYYY-MM-DD'                           |
|     blob      |                       二进制形式的长文本数据，最大可达4G                        |
|     text      |                             长文本数据，最大可达4G                              |


### 操作数据库

```mysql
#创建数据库
create database if not exists 数据库名 character set 字符集;

#查看当前所有的数据库
show databases;

#查看当前正在使用的数据库,使用的一个 mysql中的全局函数
select database();

#查看指定库下所有的表
show tables from 数据库名;

#查看数据库的创建信息
show create database 数据库名;
desc 数据库名;

#使用或者切换数据库
use 数据库名;

#更改数据库字符集
alter database 数据库名 character set 字符集;

#删除数据库
drop database if exists 数据库名;
```

### 操作表

```mysql
#创建空表
create table [if not exists] 表名(
	字段1, 数据类型 [约束条件] [默认值],
	字段2, 数据类型 [约束条件] [默认值],
	字段3, 数据类型 [约束条件] [默认值],
	……
	[表约束条件]
);

#将查询结果作为新表的数据
create table 表名 as select ...;

#查看表数据结构
show create table 表名;
dese 表名;

#追加一个列
alter table 表名 add 字段名 字段类型 [first|after 字段名];

#修改一个列
alter table 表名 modify 字段名 新字段类型 [default] [first|after 字段名];

#重命名一个列
alter table 表名 change 列名 新列名 新数据类型;

#删除一个列
alter table 表名 drop 字段名

#重命名表
alter table 表名 rename to 新表名;
rename table 表名 to 新表名;

#删除表
drop table if exists 数据表1 [,数据表2 ..., 数据表n];

#清空表
truncate table 表名;  #不能回滚
delete from 表名;  #可以回滚
```

# 6. 增删改

```mysql
#插入数据
insert into 表名(column1 [, column2, ..., columnn])
values
(value1 [, value2, ..., valuen]),
(value1 [, value2, ..., valuen]),
...
(value1 [, value2, ..., valuen]);

#将查询结果插入表中
insert into 目标表名(column1 [, column2, ...,columnn])
select (column1 [, column2, ...,columnn]) [from ... where ...];

#更新数据
update 表名
set column1 = value1 ,column2 = value2, ..., cloumnn = valuen;
[where ...];

#删除数据
delete from 表名
[where ...];
```

# 7. 约束

### not null

>限定某个字段/某列的值不允许为空

```mysql
#建表时添加非空约束
create table 表名(
	字段名 数据类型 not null
)

#建表后添加非空约束,相当于修改列
alter table 表名 modify 字段名 数据类型 not null;

#删除非空约束,同样相当于修改列
alter table 表名 modify 字段名 数据类型;
```

### unique

>用来限制某个字段/某列的值不能重复
>
>特点:
>- 唯一约束可以是某一个列的值唯一, 也可以多个列组合的值唯一
>- MySQL会给唯一约束的列上默认创建一个唯一索引
>- 如果创建唯一约束时未指定名称，如果是单列，就默认和列名相同；如果是组合列，那么默认和( ) 中排在第一个的列名相同。也可以自定义唯一性约束名

```mysql
#建表时添加唯一约束
create table 表名(
	字段名 数据类型 unique, #单列
	[constraint 约束名] unique (字段名1 [, 字段名2 , ..., 字段名n]);
)

#建表后指定唯一约束
alter table 表名 add unique (字段名1 [, 字段名2 , ..., 字段名n]);
alter table 表名 modify 字段名 字段类型 unique; #相当于修改列

#删除唯一约束
alter table 表名 drop index 唯一约束索引名;
```

### primary key

>用来唯一标识表中的一行记录
>
>特点:
>- 主键约束相当于唯一约束+非空约束的组合，主键约束列不允许重复，也不允许出现空值
>- 一个表最多只能有一个主键约束
>- 主键约束对应着表中的一列或者多列（复合主键)
>- MySQL的主键名总是primary

```mysql
#建表时添加主键
create table 表名(
	字段名 数据类型 primary key,
	[constraint 约束名] primary key (字段名1 [, 字段名2 , ..., 字段名n]);
)

#建表后添加主键
alter table 表名 add primary key (字段名1 [, 字段名2 , ..., 字段名n]);

#删除主键
alter table 表名 drop primary key;
```

### auto_increment

>某个字段的值自增
>特点:
>- 一个表最多只能有一个自增长列
>- 自增长列约束的列必须是键列（主键列，唯一键列）
>- 自增约束的列的数据类型必须是整数类型

```mysql
#建表时添加auto_increment
create table 表名(
	字段名 数据类型 primary key|unique auto_increment,
)

#建表后添加,相当于修改列
alter table 表名 modify 字段名 数据类型 auto_increment;

#删除自增约束,相当于修改列
alter table 表名 modify 字段名 数据类型;
```

### foreign key

>限定某个表的某个字段的引用完整性
>
>特点:
>- 从表的外键列，必须引用/参考主表的主键或唯一约束的列
>- 在创建外键约束时，如果不给外键约束命名，默认名不是列名，而是自动产生一个外键名
>- 当主表的记录被从表参照时，主表的记录将不允许删除，如果要删除数据，需要先删除从表中依赖该记录的数据，然后才可以删除主表的数据
>- 在“从表”中指定外键约束，并且一个表可以建立多个外键约束
>- 当创建外键约束时，系统默认会在所在的列上建立对应的普通索引, 索引名是外键的约束名
>- 删除外键约束后, 必须手动删除对应的索引

```mysql
#建表时添加外键约束
create table 表名(
	字段 数据类型,
	[constraint 外键约束名称] foreign key (从表的某个字段) references 主表名(被参考字段)
)

#建表后添加外键约束
alter table 从表名 add [constraint 约束名] foreign key (从表字段) references 主表名(被参考字段)

#删除外键约束,分为两步
alter table drop foreign key 约束名;
alter table drop index 索引名;
```

约束等级
- cascade : 在父表上update/delete记录时，同步update/delete掉子表的匹配记录
- set null : 在父表上update/delete记录时，将子表上匹配记录的列设为null，但是要注意子 表的外键列不能为not null
- no action : 如果子表中有匹配的记录，则不允许对父表对应候选键进行update/delete操作
- restrict(默认等级) : 同no action， 都是立即检查外键约束
- set default : 父表有变更时，子表将外键列设置成一个默认的值
- 对于外键约束,最好采用 on update cascade on delete restrict

### check

>检查某个字段的值是否符号xx要求，一般指的是值的范围

```mysql
#建表时添加check约束
create table 表名(
	字段名 字段类型 check(条件)
)

#建表后添加check约束,相当于修改表
alter table 表名 add [constraint 约束名] check(约束条件);

#删除check约束
alter table 表名 drop check 约束名;
```

### default

>给某个字段/某列指定默认值，一旦设置默认值，在插入数据时，如果此字段没有显式赋值，则赋值为默 认值

```mysql
#建表时添加default
create table 表名(
	字段名 数据类型 default 默认值
)

#建表后添加default,相当于修改列
alter table 表名 modify 字段名 数据类型 default 默认值

#删除default,相当于修改列
alter table 表名 modify 字段名 数据类型
```

# 8. 视图

>视图是一种虚拟表 ，本身是不具有数据，占用很少的内存空间

```mysql
#创建视图
create [or replace]
[algorithm = {undefined|merge|temptable}]
view 视图名 [(字段列表)]
as select...
[with [cascaded|local] check option]

#查看视图详细定义信息
show create view 视图名称;

#修改视图,也可以create or replace
alter view 视图名 as select ...;

#删除视图
drop view if exists 视图名1 [, 视图名2, ..., 视图名n];
```

MySQL支持使用INSERT、UPDATE和DELETE语句对视图中的数据进行插入、更新和删除操作。当视图中的数据发生变化时，数据表中的数据也会发生变化，反之亦然

但是以下情况视图不可更新
- 在定义视图的时候**指定了“algorithm= temptable”**，视图将不支持insert和delete操作
- 视图中**不包含基表中所有被定义为非空又未指定默认值的列**，视图将不支持insert操作
- 在定义视图的select语句中**使用了join联合查询** ，视图将不支持insert和delete操作
- 在定义视图的select语句后的字段列表中**使用了数学表达式或子查询** ，视图将不支持insert, 也不支持update
- 在定义视图的SELECT语句后的字段列表中**使用dinsinct 、 聚合函数 、group by 、 having 、 union等**，视图将不支持insert、update、delete；
- 在定义视图的select语句中**包含了子查询，而子查询中引用了from后面的表**，视图将不支持 insert、update、delete

# 9. 存储过程和存储函数

### 变量

>变量分为**系统变量**以及**用户自定义变量**

#### 系统变量

>系统变量分为全局系统变量（需要添加 global 关键字）以及会话系统变量（需要添加 session 关键字), 如果不写，默认会话级别
>每一个MySQL客户机成功连接MySQL服务器后，都会产生与之对应的会话 , 会话期间，MySQL服务实例会在MySQL服务器内存中生成与该会话对应的会话系统变量，这些会话系统变量的初始值是全局系统变量值的复制

```mysql
#查看所有全局|会话变量
show global | session variables;

#查看满足条件的部分系统|会话变量
show global | session variables like '% %';

#查看指定的系统|会话变量的值
select @@global.变量名 | @@session.变量名;
select @@变量名;

#修改系统/会话变量的值,只会临时生效
set @@global.变量名 | @@session.变量名 = value;
set global | session 变量名 = value;
```

>使用set global语句设置的变量值只会临时生效
>mysql8.0新增了set persist命令 , mysql会将该命令的配置保存到数据目录下的mysqld-auto.cnf文件中, 下次启动时会读取该文件 ,实现全局变量的持久化

#### 用户变量

>MySQL 中的用户变量以 一个“@” 开头。根据作用范围不同，又分为会话用户变量和局部变量
>- 会话用户变量：作用域和会话变量一样，只对当前连接会话有效
>- 局部变量：只在 BEGIN 和 END 语句块中有效。局部变量只能在存储过程和函数中使用

```mysql
#定义会话用户变量
set @变量名 = value;
select @变量名 = 表达式 [from ...];
select 表达式 into @变量名 [from ...];

#查看会话用户变量
select @变量名;

#在存储过程/函数中定义局部变量
declare 变量名 数据类型 [default 默认值];

#为局部变量赋值 
set 变量名 = value; 
select 表达式 into 变量名 [FROM ...];

#查看局部变量
select @变量名;
```

### 定义条件与处理程序

>定义条件是事先定义程序执行过程中可能遇到的问题
>处理程序定义了在遇到问题时应当采取的处理方式，并且保证存储过程或函数在遇到警告或错误时能继续执行

#### 定义条件

>定义条件就是给MySQL中的错误码命名，这有助于存储的程序代码更清晰。它将一个错误名字和指定的错误条件 关联起来。这个名字可以随后被用在定义处理程序的 declare handler语句中

```mysql
#定义条件
declare 错误名称 condition for 错误码（或错误条件）
```

错误码的说明 : 
- MySQL_error_code 和 sqlstate_value 都可以表示MySQL的错误
	- MySQL_error_code是数值类型错误代码
	- sqlstate_value是长度为5的字符串类型错误代码
- 在ERROR 1418 (HY000)中，1418是MySQL_error_code，'HY000'是sqlstate_value

![[Attachments/Images/Pasted image 20230426233852.png]]

#### 处理程序

>可以为SQL执行过程中发生的某种类型的错误定义特殊的处理程序

```mysql
#定义处理程序
declare 处理方式 handler for 错误类型 处理语句;
```

说明 : 
- 处理方式有三个取值 : 
	- continue : 遇到错误继续执行
	- exit : 遇到错误退出
	- undo : 遇到错误撤销之前的操作
- 错误类型 : 
	- sqlstate '字符串错误码' : 表示长度为5的sqlstate_value类型的错误代码
	- MySQL_error_code ：匹配数值类型错误代码
	- 错误名称 ：表示declare ... condition定义的错误条件名称
	- sqlwarning ：匹配所有以01开头的sqlstate错误代码
	- not found ：匹配所有以02开头的sqlstate错误代码
	- sqlexception ：匹配所有没有被sqlwarning或not found捕获的sqlstate错误代码
- 处理语句 : 如果出现上述条件之一，则采用对应的处理方式，并执行指定的处理语句。语句可以是 像“ SET 变量 = 值 ”这样的简单语句，也可以是使用 BEGIN ... END 编写的复合语句

![[Attachments/Images/Pasted image 20230426233711.png]]

### 流程控制

#### if

```mysql
if 条件1 then 操作1
[elseif 条件2 then 操作2]
...
[else 操作n]
end if;
```

#### case

```mysql
#用法1: 相当于switch
case 表达式
when value1 then 操作1
when value2 then 操作2
...
else 操作n
end case;

#用法2: 类似多重if
case
when 条件1 then 操作1
when 条件2 then 操作2
...
else 操作n
end case;
```

#### loop

>loop循环语句用来重复执行某些语句。loop内的语句一直重复执行直到循环被退出（使用leave子句），跳出循环过程 
>相当于while (true)

```mysql
[loop_label :] loop
...
end loop [loop_label];
```

#### while

>while语句创建一个带条件判断的循环过程。while在执行语句执行时，先对指定的表达式进行判断，如果为真，就执行循环内的语句，否则退出循环

```mysql
[while_label :] while 循环条件 do
...
end while [while_label];
```

#### repeat

>repeat语句创建一个带条件判断的循环过程 , 相当于do while

```mysql
[repeat_label :] repeat
...
until 条件
end repeat [repeat_label]
```

#### leave与iterate

>leave相当于break
>iterate相当于continue
>都需要在后面写上label实现跳转

### 存储过程

>一组经过预先编译 的 SQL 语句的封装

```mysql
#创建存储过程
delimiter 新的结束标记
create procedure 存储过程名(in|out|inout 参数名 参数类型, ...)
[characteristics ...]
begin
	...
end 新的结束标记
delimiter ;

#调用in模式的参数
call xx('参数');

#调用out模式的参数
set @name;
call xx(@name);
select @name; #查看值

#调用inout模式的参数
set @name = 'quan';
call xx(@name);
select @name

#查看创建信息
show create procedure 存储过程名;

#修改存储过程,只是修改相关特性
alter procedure 存储过程名 [characteristics ...];

#删除存储过程
drop procedure if exists 存储过程名;
```

说明:
- in : 当前参数为输入参数, 如果没有定义参数种类， 默认就是 IN ，表示输入参数
- out : 当前参数为输出参数, 执行完成之后，调用这个存储过程的客户端或者应用程序就可以读取这个参数返回的值了
- inout : 当前参数既可以为输入参数，也可以为输出参数
- characteristics 表示创建存储过程时指定的对存储过程的约束条件，其取值信息如下
	- **language sql** : 说明存储过程执行体是由SQL语句组成的，当前系统支持的语言为SQL
	- **\[not] deterministic** : 指明存储过程执行的结果是否确定 , deterministic表示结果是确定 的。每次执行存储过程时，相同的输入会得到相同的输出。not deterministicC表示结果是不确定 的，相同的输入可能得到不同的输出。如果没有指定任意一个值，默认为not Ddeterministic
	- **{ contains sql | no sql | reads sql data | modifies sql data }** : 指明子程序使用SQL语句的限制
		- contains sql(默认)表示当前存储过程的子程序包含SQL语句，但是并不包含读写数据的SQL语句
		- no sql表示当前存储过程的子程序中不包含任何SQL语句
		- reads sql data表示当前存储过程的子程序中包含读数据的SQL语句
		- modifies sql data表示当前存储过程的子程序中包含写数据的SQL语句
	- **sql security { definer | invoker }** : 执行当前存储过程的权限，即指明哪些用户能够执行当前存储过程
		- definer (默认) 表示只有当前存储过程的创建者或者定义者才能执行当前存储过程
		- invoker 表示拥有当前存储过程的访问权限的用户能够执行当前存储过程
	- **comment 'string'** : 注释信息
- delimiter : 因为MySQL默认的语句结束符号为分号‘;’。为了避免与存储过程中SQL语句结束符相冲突，需要使用 delimiter改变存储过程的结束符
- declare : 用来声明变量，使用的位置在于 BEGIN…END 语句中间，而且需要在其他语句使用之前进行变量的声明
- set : 对变量赋值
- select ... into ... : 将从数据表中查询的结果存放到变量中,也就是为变量赋值

例1 : 返回指定id的salary

![[Attachments/Images/Pasted image 20230425181049.png]]

例2 : 实现累加运算，计算 1+2+…+n 等于多少

![[Attachments/Images/Pasted image 20230425181434.png]]

### 存储函数

```mysql
#创建存储函数
delimiter $
create function 函数名(参数名 参数类型) #默认为in参数
returns 返回值类型
[characteristics ...]
begin
	...
	return ...
end $
delimiter ;

#调用存储函数
select 函数名(参数列表);

#查看创建信息
show create function 存储函数名;

#修改存储函数,只是修改相关特性
alter function 存储函数名 [characteristics ...];

#删除存储函数
drop function if exists 存储函数名;

```

例 : 创建存储函数count_by_id()，参数传入dept_id，该函数查询dept_id部门的员工人数，并返回，数据类型为整型

![[Attachments/Images/Pasted image 20230425182657.png]]

### 游标

>游标，提供了一种灵活的操作方式，让我们能够对结果集中的每一条记录进行定位，并对指向的记录中的数据进行操作的数据结构。游标让 SQL 这种面向集合的语言有了面向过程开发的能力

```mysql
#第一步,声明游标
declare cursor_name cursor for select_statement;

#打开游标
open cursor_name;

#使用游标,将此行查询结果写入变量中
fetch cursor_name into var_name [, var_name];

#关闭游标
close cursor_name;
```

例 : 创建存储过程“get_count_by_limit_total_salary( )”，声明IN参数 limit_total_salary，DOUBLE类型；声明 OUT参数total_count，INT类型。函数的功能可以实现累加薪资最高的几个员工的薪资值，直到薪资总和 达到limit_total_salary参数的值，返回累加的人数给total_count

![[Attachments/Images/Pasted image 20230427224044.png]]

# 10. 触发器

>触发器是由事件来触发某个操作
>所谓事件就是指用户的动作或者触发某项行为, 如果定义了触发程序，当数据库执行这些语句时候，就相当于事件发生了，就会自动激发触发器执行相应的操作

```mysql
#创建触发器
create trigger 触发器名称
{before | after} {insert | update | delete} on 表名
for each row
begin
...
end;

#查看触发器
show triggers;
show create trigger;

#删除触发器
drop trigger if exists 触发器名称;
```

例 : 定义触发器“salary_check_trigger”，基于员工表“employees”的INSERT事件，在INSERT之前检查 将要添加的新员工薪资是否大于他领导的薪资，如果大于领导薪资，则报sqlstate_value为'HY000'的错 误，从而使得添加失败

![[Attachments/Images/Pasted image 20230427230705.png]]

上面触发器声明过程中的NEW关键字代表INSERT添加语句的新记录