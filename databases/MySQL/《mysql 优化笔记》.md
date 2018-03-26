# mysql 优化笔记

[TOC]

## 表的优化和列类型选择

### 表的优化

1. 定长与变长分离

    - 定长
        - id int, 占4个字节
        - char(4) 占4个字符长度,也是定长, time 
    
    即每一单元值占的字节是固定的.核心且常用字段,宜建成定长,放在一张表。

    - 变长
        - varchar
        - text
        - blob
        
    这种变长字段,适合单放一张表, 用主键与核心表关联起来。

2. 常用字段与不常用字段分离

    - 需要结合网站具体的业务来分析,分析字段的查询场景,查询频度低的字段,单拆出来。

3. 在1对多，需要关联统计的字段上，添加冗余字段

    - 看如下BBS的效果

    ![image](https://note.youdao.com/yws/public/resource/0fbd89b18ba6fa0de992414908a0b609/xmlnote/2E45B916560341E19B75E7648CAB53D6/2232)
    
    每个版块里,有N条帖子, 在首页,显示了版块信息,和版块下的帖子数.
    这是如何做的?
    
    boardid | boardname | postnum
    ---|---|---
    8 | 开班信息        | 2
    9 | 每日视频及代码  | 1

    postid | boardid | title
    ---|---|---
    123 | 8  | 论坛开张了
    129 | 8  | 灌水
    133 | 9  | 来一帖

    如果`board`表只有前`2`列,则需要取出版块后,再查`post`表
    
    ```
    select count(*) from post group by board_id
    ```
    得出每个版块下的帖子数。
    
    如果有`postnum`字段,每发一个帖子时,对`postnum`字段`+1`; 
    再查询版块下的帖子数时, 只需要1条语句直接查`boardid`,
    
    ```
    select boradid, boardname,postnum from board
    ```

    典型的 **"空间换时间"**

### 列选择原则

1. 字段类型优先级 `整型 > date,time > enum,char > varchar > blob`
    - 列的特点分析:
        
        列类型 | 特点
        ---|---
        整型 | 定长,没有国家/地区之分,没有字符集的差异
        time | 定长,运算快,节省空间. 考虑时区,写sql时不方便`where > '2005-10-12'`
        enum | 能起来约束值的目的,内部用整型来存储,但与char联查时,内部要经历串与值的转化
        Char | 定长, 考虑字符集和(排序)校对集
        varchar | 非定长 要考虑字符集的转换与排序时的校对集,速度慢
        text/Blob | 无法使用内存临时表

    - 附: 关于date/time的选择,大师的明确意见
    
        http://www.xaprb.com/blog/2014/01/30/timestamps-in-mysql/
    
    - 例子
        
        性别:  以`utf8`为例
        ```
        char(1) # 3个字长字节
        enum('男','女') # 内部转成数字来存,多了一个转换过程
        tinyint() # 0 1 2  定长1个字节.
        ```

2. 够用就行,不要慷慨 如`smallint,varchar(N)`

    - 原因: 大的字段浪费内存,影响速度。
    
    - 以年龄为例
        ```
        tinyint unsigned not null # 可以存储255岁,足够. 
        int # 浪费了3个字节
        varchar(10) # varchar(300) 存储的内容相同,但在表联查时,varchar(300)要花更多内存
        ```

3. 尽量避免用`null`

    - 原因: `null`不利于索引,要用特殊的字节来标注。在磁盘上占据的空间其实更大。
    - 实验：
        
        可以建立2张字段相同的表,一个允许为`null`,一个不允许为`null`,各加入1万条,查看索引文件的大小.
        
        可以发现,为`null`的索引要大些。(mysql5.5里,关于`null`已经做了优化,大小区别已不明显)
        
        另外: `null`也不便于查询
        ```
        where 列名 = null;   
        where 列名 != null; 都查不到值
        where 列名 is null ,或is not null 才可以查询.
        ```
        
        ```
        create table dictnn (
            id int,
            word varchar(14) not null default '',
            key(word)
        ) engine myisam charset utf8mb4;
        
        create table dictyn (
            id int,
            word varchar(14),
            key(word)
        )engine myisam charset utf8mb4;
        ```
        
        ```
        alter table dictnn disable keys;
        alter table dictyn disable keys;
        ```
        
        ```
        insert into dictnn select id,if(id%2,word,'') from dict limit 10000;
        insert into dictyn select id,if(id%2,word,null) from dict limit 10000;
        ```
        
        ```
        alert table dictnn enable keys;
        alter table dictyn enable keys;
        ```

- `Enum`列的说明

    1. `enum`列在内部是用整型来储存的
    2. `enum`列与`enum`列相关联速度最快
    3. `enum`列比`(var)char` 的弱势 --- 在碰到与`char`关联时,要转化.     要花时间.
    4. 优势在于,当`char`非常长时,`enum`依然是整型固定长度.
        当查询的数据量越大时,`enum`的优势越明显.
    5. `enum`与`char/varchar`关联,因为要转化,速度要比`enum->enum,char->char`要    慢,但有时也这样用 --- 在数据量特别大时,可以节省`IO`.
    
    - 实验：
    
    ```
    create table t2 (
        id int,
        gender enum('man','woman'),
        key(gender)
    )engine myisam charset utf8mb4;
    
    create table t3 (
        id int,
        gender char(5) not null default '',
        key(gender)
    )engine myisam charset utf8mb4;
    
    ```
    
    ```
    alter table t2 disable keys;
    alter table t3 disable keys;
    ```
    
    ```
    insert into t2 select id,if(id%2,'man','woman') from dict limit 10000;
    insert into t3 select id,if(id%2,'man','woman') from dict limit 10000;
    ```
    
    ```
    alter table t2 enable keys;
    alter table t3 enable keys;
    ```
    
    ```
    mysql> select count(*) from t2 as ta,t2 as tb where ta.gender=tb.gender
    mysql> select count(*) from t3 as ta,t3 as tb where ta.gender=tb.gender
    ```

    列<---->列 | 时间
    ---|---
    Enum<--->enum | 10.53
    Char<---->char | 24.65
    Enum<---->char | 18.22
    
    如果`t2`表的优势不明显, 加大`t3`的`gender`列 ,`char(15)`, `char(20)...`
    随着`t3` `gender`列的变大,`t2`表优势逐渐明显.

    - 原因：无论`enum('manmaman','womanwomanwoman')` 枚举的字符多长,内部都是用整型表示, 在内存中产生的数据大小不变,而`char`型,却在内存中产生的数据越来越多.
    
    - 总结: `enum` 和`enum`类型关联速度比较快`Enum` 类型 节省了`IO`
    
## 索引优化策略

### 文件储存类型

#### MySQL 创建并管理的数据库文件

**查看数据库物理文件存放位置**

`.frm`文件：存储数据表的框架结构，文件名与表名相同，每个表对应一个同名`frm`文件，与操作系统和存储引擎无关，即不管`MySQL`运行在何种操作系统上，使用何种存储引擎，都有这个文件。

除了必有的`.frm`文件，根据`MySQL`所使用的存储引擎的不同（`MySQL`常用的两个存储引擎是`MyISAM`和`InnoDB`），存储引擎会创建各自不同的数据表文件。

```
MySQL [(none)]> show global variables like "%datadir%";
+---------------+--------------+
| Variable_name | Value        |
+---------------+--------------+
| datadir       | /data/mysql/ |
+---------------+--------------+
1 row in set (0.00 sec)
```

#### innodb 数据表文件

从物理意义上来讲，`InnoDB`表由**共享表空间**、**日志文件组**（redo文件组）、**表结构定义文件**组成。若将`innodb_file_per_table`设置为`on`，则系统将为每一个表单独的生成一个`table_name.ibd`的文件（独占表空间），在此文件中，存储与该表相关的数据、索引、表的内部数据字典信息。表结构文件则以`.frm`结尾，这与存储引擎无关。


共享表空间以及独占表空间都是针对数据的从物理意义上来讲

> 共享表空间:  会把表集中存储在一个系统表空间里。即每一个数据库的所有表的数据，索引文件全部放在一个文件中。该文件目录默认的是服务器的数据目录。 默认的文件名为:`ibdata1`  初始化为`10M`。

> 独占表空间:  每一个表分别创建一个表空间，这时。在对应的数据库目录里每一个表都有`.ibd`文件(这个文件包括了单独一个表的数据内容以及索引内容)。


**查看是哪种类型的表空间**

```
MySQL [(none)]> show variables like '%innodb_file_per_table%';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_file_per_table | ON    |
+-----------------------+-------+
1 row in set (0.00 sec)
```

**查看共享表空间的数据文件存储路径**

```
MySQL [(none)]> show variables like 'innodb_data%';
+-----------------------+------------------------+
| Variable_name         | Value                  |
+-----------------------+------------------------+
| innodb_data_file_path | ibdata1:12M:autoextend |
| innodb_data_home_dir  |                        |
+-----------------------+------------------------+
2 rows in set (0.00 sec)
```

Innodb 独占表空间

```
-rw-rw---- 1 mysql mysql  15K Mar  5 11:43 test.frm # 表结构
-rw-rw---- 1 mysql mysql  11M Mar  5 17:31 test.ibd # 表数据
```

#### MyISAM 数据库表文件

`MyISAM` 表是独立于操作系统的，这说明可以轻松地将其从`Windows`服务器移植到`Linux`服务器；每当我们建立一个`MyISAM引`擎的表时，就会在本地磁盘上建立三个文件，文件名就是表名。例如，我建立了一个`MyISAM`引擎的`test`表，那么就会生成以下三个文件

```
-rw-rw---- 1 mysql mysql  2K  Mar  5 17:31 test.frm # 表结构
-rw-rw---- 1 mysql mysql  90K Mar  5 11:43 test.MYD # 表数据
-rw-rw---- 1 mysql mysql  15K Mar  5 11:43 test.MYI # 表索引
```

### 索引类型

#### B-tree 索引

目前 `MySQL` 的索引都是基于平衡树算法但稍有不同

 - `NDB` 引擎,使用的是`T-tree`
 - `Myisam`,`innodb`中,默认用`B-tree`

 B-tree 可理解为 **"排好序的快速查找结构"**。

#### hash 索引

在`memory`表里,默认是`hash`索引, `hash`的理论查询时间复杂度为`O(1)`

**问**:

 - 既然`hash`的查找如此高效,为什么不都用`hash`索引?

**答**:

 - **位置随机**：`hash`函数计算后的结果是随机的,如果是在磁盘上放置数据,
    以主键为`id`为例, 那么随着`id`的增长, `id`对应的行在磁盘上的位置也为随机.
 - **不能优化范围查找**.
 - **不能利用前缀索引**: 比如 在`btree`中, `field`列的值`helloword`,并加索引
    查询 `xx=helloword`,自然可以利用索引, `xx=hello` 也可以利用索引(左前缀索引),因为`hash('helloword') 和 hash('hello')` 两者的关系仍为随机.
 - **不能优化排序**.
 - **必须回行**: 就是说通过索引拿到数据位置,必须回到表中取数据.
 

### B-tree 索引的常见误区

 - 在`where`条件常用的列上都加上索引

    - 例: `where cat_id=3 and price>100` # 查询第`3`个栏目，`100`元以上的商品
    - 误: `cat_id` 和 `price`上都加上索引。
    - 错: 只能用上`cat_id`或`price`索引，因为是独立的索引，同时只能用上`1`个。

 - 在多列上建立索引后，查询哪个列，索引都将发挥作用
 
    - 误: 多列索引上，索引发挥作用，需要满足左前缀要求 （**索引的左前缀原则**）。
    - 例: 以 `index(a,b,c)` 为例 （**注意顺序有关**）

    语句 | 索引是否发挥作用
    ---|---
    where a=3   |	是，只使用了a列
    where a=3 and b=5  |  是，使用了a,b列
    where a=3 and b=5 and c=4  |  是，使用了a,b,c列
    where b=3 / where c=4   |	否
    where a=3 and c=4	|   a 列能发挥索引，c 不能
    where a=3 and b>10 and c=7  |	a 能利用，b 能利用，c 不能利用
    where a=3 and b like 'xxxx%' and c=7   |	a 能用，b 能用，c 不能用
    
    为便于理解, 假设`ABC`各`10`米长的木板, 河面宽`30`米.
    全值索引是则木板长`10`米,
    
    `Like`,左前缀及范围查询, 则木板长`6`米,
    
    自己拼接一下,能否过河对岸,就知道索引能否利用上.
    
    如上例中, `where a=3 and b>10, and c=7`,
    
    `A`板长`10`米,`A`列索引发挥作用
    
    `A`板正常接`B`板, `B`板索引发挥作用
    
    `B`板短了,接不到`C`板, `C`列的索引不发挥作用
    
    ```
    create table t4 (
        c1 tinyint(1) not null default 0,
        c2 tinyint(1) not null default 0,
        c3 tinyint(1) not null default 0,
        c4 tinyint(1) not null default 0,
        c5 tinyint(1) not null default 0,
        index c1234(c1,c2,c3,c4)
    );
    ```
    
    ```
    insert into t4 values (1,3,5,6,7),(2,3,9,8,3),(4,3,2,7,5);
    ```
    
    - A 语句
    
    ```
    select * from t4 where c1=1 and c2=2 and c4>3 and c3=3
    ```

    `c1=x and c2=x and c4>x and c3=x` == `c1=x and c2=x and c3=x and c4>x`
    
    因此 `c1`,`c2`,`c3`,`c4`都能用上. 如下:
    
    ```
    mysql> explain select * from t4 where c1=1 and c2=2 and c4>3 and c3=3 \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: t4
             type: range
    possible_keys: c1234
              key: c1234
          key_len: 4 #可以看出c1,c2,c3,c4索引都用上
              ref: NULL
             rows: 1
            Extra: Using where 
    ```
    - B 语句
    
    `select * from t4 where c1=1 and c2=2 and c4=3 order by c3`
    
    `c1`，`c2`索引用上了，在`c2`用到索引的基础上，`c3`是排好序的，因此不用额外排序，而`c4`没发挥作用。

    ```
    mysql> explain select * from t4 where c1=1 and c2=2 and c4=3 order by c3 \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: t4
             type: ref
    possible_keys: c1234
              key: c1234
          key_len: 2
              ref: const,const
             rows: 1
            Extra: Using where
    1 row in set (0.00 sec)
    
    mysql> explain select * from t4 where c1=1 and c2=2 and c4=3 order by c5 \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: t4
             type: ref
    possible_keys: c1234
              key: c1234
          key_len: 2
              ref: const,const
             rows: 1
            Extra: Using where; Using filesort
    1 row in set (0.00 sec)
    ```

    - C 语句
    
    ```
    select * from t4 where c1=1 and c4=2 group by c3,c2
    ```

    只用到`c1`索引,因为`group by c3`,`c2`的顺序无法利用`c2`,`c3`索引
    
    ```
    mysql> explain select * from t4 where c1=1 and c4=2 group by c3,c2 \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: t4
             type: ref
    possible_keys: c1234
              key: c1234
          key_len: 1 #只用到c1,因为先用c3后用c2分组,导致c2,c3索引没发挥作用
              ref: const
             rows: 1
            Extra: Using where; Using temporary; Using filesort
    1 row in set (0.00 sec)
    
    mysql> explain select * from t4 where c1=1 and c4=2 group by c2,c3 \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: t4
             type: ref
    possible_keys: c1234
              key: c1234
          key_len: 1
              ref: const
             rows: 1
            Extra: Using where
    1 row in set (0.00 sec)
    ```
    
    - D 语句
    
    ```
    select * from t4 where c1=1 and c5=2 order by c2,c3
    ```

    `c1`确定的基础上,`c2`是有序的,`c2`之下`c3`是有序的,因此`c2`,`c3`发挥的排序的作用.因此,没用到`filesort`
    
    ```
    mysql> explain select * from t4 where c1=1 and c5=2 order by c2,c3 \G  
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: t4
             type: ref
    possible_keys: c1234
              key: c1234
          key_len: 1
              ref: const
             rows: 1
            Extra: Using where
    1 row in set (0.00 sec)
    ```
    
    - E 语句
    
    ```
    select * from t4 where c1=1 and c2=3 and c5=2 order by c2,c3
    ```

    这一句等价于 `select * from t4 where c1=1 and c2=3 and c5=2 order by c3`.
    
    因为`c2`的值是固定的,参与排序时并不考虑
    
    ```
    mysql> explain select * from t4 where c1=1 and c2=3 and c5=2 order by c2,c3 \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: t4
             type: ref
    possible_keys: c1234
              key: c1234
          key_len: 2
              ref: const,const
             rows: 1
            Extra: Using where
    1 row in set (0.00 sec)
    ```

    - 一道面试题: 
    
    有商品表, 有主键,`goods_id`,  栏目列`cat_id`, 价格`price`
    
    - **问**: 在价格列上已经加了索引,但按价格查询还是很慢,可能是什么原因,怎么解决?
    
    - **答**: 在实际场景中,一个电商网站的商品分类很多,直接在所有商品中,按价格查商品,是极少的,一般客户都来到分类下,然后再查.
    
    - **改正**: 去掉单独的`price`列的索引, 加`(cat_id,price)`复合索引再查询.
    
### 聚簇索引与非聚簇索引

`MyISAM`与`innodb`引擎,索引文件的异同

- MyISAM

![MyISAM](https://note.youdao.com/yws/public/resource/0fbd89b18ba6fa0de992414908a0b609/xmlnote/B9E1CABAAC1144BA8BE92C570AD9AABF/2842)

- innodb

![innodb](https://note.youdao.com/yws/public/resource/0fbd89b18ba6fa0de992414908a0b609/xmlnote/C2C46051B7584E49983BE75AE4536E2C/2846)


 - `innodb`的主索引文件上 直接存放该行数据,称为聚簇索引,次索引指向对主键的引用
 - `MyISAM`中, 主索引和次索引,都指向物理行(磁盘位置).

**注意:** `innodb`来说,

1. 主键索引既存储索引值,又在叶子中存储行的数据
2. 如果没有主键, 则会`unique key`做主键 
3. 如果没有`unique key`,则系统生成一个内部的`rowid`做主键.
4. `innodb`主键的索引结构中,既存储了主键值,又存储了行数据,这种结构称为 **"聚簇索引"**

- 聚簇索引 

    - **优势**: 根据主键查询条目比较少时,不用回行(数据储存于主键节点下)
    - **劣势**: 如果碰到不规则数据插入时,会造成频繁的页分裂.
    
- 聚簇索引的页分裂过程

![聚簇索引的页分裂过程](https://note.youdao.com/yws/public/resource/0fbd89b18ba6fa0de992414908a0b609/xmlnote/EBE7605D640E47EF8BECE5205A34339F/2645)

- 实验

    聚簇索引使用随机值导致页频繁分裂影响速度

- 过程

    建立`innodb`表, 利用`php`连接`mysql`,分别规则插入`10000`条数据,不规则插入`10000`条数据观察时间的差异,体会聚簇索引,页分裂的影响.  

```
create table t5(
    id int primary key,
    c1 varchar(500),
    c2 varchar(500),
    c3 varchar(500),
    c4 varchar(500),
    c5 varchar(500),
    c6 varchar(500)
) engine innodb charset utf8;

create table t6(
    id int primary key,
    c1 varchar(500),
    c2 varchar(500),
    c3 varchar(500),
    c4 varchar(500),
    c5 varchar(500),
    c6 varchar(500)
) engine innodb charset utf8;
```

- testinnodb.php

```
$time_start = microtime_float();

$str = str_repeat('hello',100);
for($i=1;$i<=10000;$i++) {
   $sql = "insert into t5 values ($i,'$str' , '$str' , '$str' , '$str' , '$str' , '$str'
)";
   //echo $sql;
   mysql_query($sql , $conn);
}

$time_end = microtime_float();
echo 'seq insert cost' , ($time_end - $time_start) , "seconds\n";
function microtime_float()
{
    list($usec, $sec) = explode(" ", microtime());
    return ((float)$usec + (float)$sec);
}
```

- rndinnodb.php

```
$base = range(1,10000);
shuffle($base);

$time_start = microtime_float();
$str = str_repeat('hello',100);
foreach($base as $i) {
   $sql = "insert into t6 values ($i,'$str' , '$str' , '$str' , '$str' , '$str' , '$str'
)";
   //echo $sql;
   mysql_query($sql , $conn);
}

$time_end = microtime_float();
echo 'rand insert cost' , ($time_end - $time_start) , "seconds\n";

function microtime_float()
{
    list($usec, $sec) = explode(" ", microtime());
    return ((float)$usec + (float)$sec);
}
```

字段数	| 混乱程度(步长) | 顺序1000条(秒数) | 乱序1000条(秒数) | 顺序写入page页数 | 乱序写入page数
---|---|---|---|---|---
1	| 1     | 54.365    | 53.438    | 62    | 91
10	| 10	| 53.413    | 62.940    | 235   | 1301
10	| 100   |           | 64.18     |       | 1329
10  | 1000  |           | 67.512    |       | 1325

通过上面的规律可以看出

1. `innodb`的`buffer_page`很强大.

2. 聚簇索引的主键值,应尽量是连续增长的值,而不是随机值.

**不要用随机字符串或`UUID`**, 否则会造成大量的页分裂与页移动.

### 高性能索引策略

对于`innodb`而言,因为节点下有数据文件,因此节点的分裂将会比较慢.

对于`innodb`的主键,尽量用整型,而且是递增的整型.

如果是无规律的数据,将会产生的页的分裂,影响速度.

#### 索引覆盖

索引覆盖是指：如果查询的列恰好是索引的一部分,那么查询只需要在索引文件上进行,不需要回行到磁盘再找数据.

这种查询速度非常快,称为 **索引覆盖**

#### 理想的索引

1. 查询频繁高
2. 区分度高
3. 长度小
4. 尽量能覆盖常用查询字段.


索引长度直接影响索引文件的大小,影响增删改的速度,并间接影响查询速度(**占用内存多**).

- 针对列中的值,从左往右截取部分,来建索引

1. 截的越短, 重复度越高,区分度越小, 索引效果越不好
2. 截的越长, 重复度越低,区分度越高

索引效果越好,但带来的影响也越大 --> 增删改变慢,并间影响查询速度.

所以, 我们要在  **区分度** + **长度**  两者上,取得一个平衡.

惯用手法: **截取不同长度,并测试其区分度**

```
mysql> select count(distinct left(word,6))/count(*) from dict; 
+---------------------------------------+
| count(distinct left(word,6))/count(*) |
+---------------------------------------+
|                                0.9992 |
+---------------------------------------+
1 row in set (0.30 sec)
```

![单词长度与区分度](https://note.youdao.com/yws/public/resource/0fbd89b18ba6fa0de992414908a0b609/xmlnote/32403E89459D4F1E980E4553B3E9E687/2672)

**对于一般的系统应用: 区别度能达到 0.1 ,索引的性能就可以接受.**

- 对于左前缀不易区分的列 ,建立索引的技巧

如 url 列

```
http://www.baidu.com
http://www.zixue.it
```

列的前11个字符都是一样的,不易区分, 可以用如下2个办法来解决

1. 把列内容倒过来存储,并建立索引 

    ```
    Moc.udiab.www//:ptth
    Ti.euxiz.www//://ptth
    ```

    这样左前缀区分度大

2. 伪`hash`索引效果

    同时存`url_hash`列

3. 多列索引

 - 多列索引的考虑因素---  
     
    列的查询频率 , 列的区分度, 
    以`ecshop`商城为例, `goods`表中的`cat_id`,`brand_id`,做多列索引
    从区分度看,`brand_id`区分度更高
    
    ```
    mysql> select count(distinct cat_id) / count(*) from  goods;
    +-----------------------------------+
    | count(distinct cat_id) / count(*) |
    +-----------------------------------+
    |                            0.2903 |
    +-----------------------------------+
    1 row in set (0.00 sec)
    
    mysql> select count(distinct brand_id) / count(*) from  goods;
    +-------------------------------------+
    | count(distinct brand_id) / count(*) |
    +-------------------------------------+
    |                              0.3871 |
    +-------------------------------------+
    1 row in set (0.00 sec)
    ```
    
    但从商城的实际业务业务看, 顾客一般先选大分类 --> 小分类 --> 品牌,
    最终选择 `index(cat_id,brand_id)`来建立索引

    有如下表(`innodb`引擎), `sql`语句在笔记中, 
    给定日照市,查询子地区, 且查询子地区的功能非常频繁,
    如何优化索引及语句 ?

    ```
    +------+-----------+------+
    | id   | name      | pid  |
    +------+-----------+------+
    | .... | .... | .... |
    | 1584 | 日照市 | 1476 |
    | 1586 | 东港区 | 1584 |
    | 1587 | 五莲县 | 1584 |
    | 1588 | 莒县    | 1584 |
    +------+-----------+------+
    ```

    1. 不加任何索引,自身连接查询
    
    ```
    mysql> explain select s.id,s.name from it_area as p inner join it_area as s on p.id=s.pid   where p.name='日照市' \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: p
             type: ALL
    possible_keys: NULL
              key: NULL
          key_len: NULL
              ref: NULL
             rows: 3263
            Extra: Using where
    *************************** 2. row ***************************
               id: 1
      select_type: SIMPLE
            table: s
             type: ALL
    possible_keys: NULL
              key: NULL
          key_len: NULL
              ref: NULL
             rows: 3263
            Extra: Using where; Using join buffer
    2 rows in set (0.00 sec)
    ```

    2. 给`name`加索引
    
    ```
    mysql> explain select s.id,s.name from it_area as p inner join it_area as s on p.id=s.pid   where p.name='日照市' \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: p
             type: ref
    possible_keys: name
              key: name
          key_len: 93
              ref: const
             rows: 1
            Extra: Using where
    *************************** 2. row ***************************
               id: 1
      select_type: SIMPLE
            table: s
             type: ALL
    possible_keys: NULL
              key: NULL
          key_len: NULL
              ref: NULL
             rows: 3243
            Extra: Using where; Using join buffer
    2 rows in set (0.00 sec)
    ```
    
    3. 在`pid`上也加索引

    ```
    mysql> explain select s.id,s.name from it_area as p inner join it_area as s on p.id=s.pid   where p.name='日照市' \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table: p
             type: ref
    possible_keys: name
              key: name
          key_len: 93
              ref: const
             rows: 1
            Extra: Using where
    *************************** 2. row ***************************
               id: 1
      select_type: SIMPLE
            table: s
             type: ref
    possible_keys: pid
              key: pid
          key_len: 5
              ref: big_data.p.id
             rows: 4
            Extra: Using where
    2 rows in set (0.00 sec)
    ```

    - 延迟关联
    
    ```
    mysql> select * from it_area where name like '%东山%';
    +------+-----------+------+
    | id   | name      | pid  |
    +------+-----------+------+
    |  757 | 东山区 |  751 |
    | 1322 | 东山县 | 1314 |
    | 2118 | 东山区 | 2116 |
    | 3358 | 东山区 | 3350 |
    +------+-----------+------+
    4 rows in set (0.00 sec)
    ```

    分析: 这句话用到了索引覆盖没有?
    
    答: 没有
    
    1.查询了所有列, 没有哪个索引,覆盖了所有列.
    
    2.`like '%xx%'`,左右都是模糊查询, `name`本身,都没用上索引
    
    第 2 种做法:
    
    ```
    select a.* from it_area as a inner join (select id from it_area where name like '%东山%') as t on a.id=t.id;
    ```

    ```
    Show profiles; 查看效率:
    |18 | 0.00183800 | select * from it_area where name like '%东山%'  
    |20 | 0.00169300 | select a.* from it_area as a inner join (select id from it_area where name like '%东山%') as t on a.id=t.id
    ```
    
    第 2 种做法,虽然语句复杂,但速度却稍占优势.
    
    第 2 种做法中, 内层查询,只沿着`name`索引层顺序走, `name`索引层包含了`id`值的.
    
    所以,走完索引层之后,找到所有合适的`id`,再通过`join`, 用id一次性查出所有列. 走完`name`列再取.
    
    第 1 种做法: 沿着`name`的索引文件走, 走到满足的条件的索引,就取出其`id`,
    并通过`id`去取数据, 边走边取.
    
    通过`id`查找行的过程被延后了 --> 这种技巧,称为 **"延迟关联"**.
    
#### 索引与排序
    
排序可能发生2种情况:

1. 对于覆盖索引,直接在索引上查询时,就是有顺序的, `using index`
2. 先取出数据,形成临时表做`filesort`(文件排序,但文件可能在磁盘上,也可能在内存中)

优化目标-----取出来的数据本身就是有序的! 利用索引来排序.

比如: 

- `goods`商品表, `(cat_id,shop_price)`组成联合索引
- `where cat_id=N order by shop_price` ,可以利用索引来排序
- `select goods_id,cat_id,shop_price from goods order by shop_price`; ( `using where`,按照`shop_price`索引取出的结果,本身就是有序的)

- `select goods_id,cat_id,shop_price from goods order by click_count`; (`using filesort`用到了文件排序,即取出的结果再次排序)


#### 重复索引与冗余索引

- **重复索引**: 是指 在同1个列(如`age`), 或者 顺序相同的几个列`(age,school)`, 建立了多个索引,
称为重复索引, 重复索引没有任何帮助,只会增大索引文件,拖慢更新速度, 去掉.

- **冗余索引**:是指 2 个索引所覆盖的列有重叠,称为冗余索引
比如 `x`,`m`列, 加索引`index x(x)`,`index xm(x,m)`
`x`,`xm`索引, 两者的`x`列重叠了,  这种情况,称为冗余索引.甚至可以把 `index mx(m,x)` 索引也建立, `mx`, `xm` 也不是重复的,因为列的顺序不一样.


#### 索引碎片与维护

在长期的数据更改过程中,索引文件和数据文件,都将产生空洞,形成碎片.
我们可以通过一个`nop`操作(不产生对数据实质影响的操作), 来修改表.

**比如:** 

- 表的引擎为`innodb`,可以 `alter table xxx engine innodb`
- `optimize table 表名`,也可以修复.

**注意:** 

- 修复表的数据及索引碎片,就会把所有的数据文件重新整理一遍,使之对齐.
- 这个过程,如果表的行数比较大,也是非常耗费资源的操作.
所以,不能频繁的修复.

如果表的`Update`操作很频率,可以按`周/月`,来修复.

如果不频繁,可以更长的周期来做修复.

## sql语句优化

1. `sql` 语句的时间花在哪儿 ?

    **答:** 
    
    - 等待时间
    - 执行时间.
    
    这两个时间并非孤立的,如果单条语句执行的快了,
    对其他语句的锁定的也就少了.所以,我们来分析如何降低执行时间.

2. `sql`语句的执行时间,又花在哪儿了?

    **答：**

    - 查 --> 沿着索引查,甚至全表扫描 
    - 取 --> 查到行后,把数据取出来(`sending data`)

3. `sql`语句的优化思路？

    **答:** 

    - 不查:
    
    通过业务逻辑来计算,比如论坛的注册会员数,我们可以根据前3个月统计的每天注册数, 用程序来估算.
    
    - 少查:
    
    尽量少取数据,少取行. 我们观察新闻网站,评论内容等,一般一次性取列表 10-30条左右.
    
    - 必须要查:
        - 尽量走索引.
        - 取尽量少的列.
            - 比如  `select * from tableA`,  取出所有列, 不建议.
            - 比如  `select * from tableA,tableB`, 取出A,B表的所有列, 不建议.

### 用 explain 来分析语句

`explain`的列分析

#### id: 代表`select`语句的编号

如果是连接查询,表之间是平等关系, `select` 编号都是 1 ,从 1 开始. 如果某`select`中有子查询,则编号递增.

```
mysql> explain select goods_id,goods_name from  goods where goods_id in (sele
ct goods_id from  goods where cat_id=4) \G
*************************** 1. row ***************************
           id: 1
  select_type: PRIMARY
        table:  goods
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 31
        Extra: Using where
*************************** 2. row ***************************
           id: 2
  select_type: DEPENDENT SUBQUERY
        table:  goods
         type: unique_subquery
possible_keys: PRIMARY,cat_id
          key: PRIMARY
      key_len: 3
          ref: func
         rows: 1
        Extra: Using where
2 rows in set (0.00 sec)
```

#### select_type: 查询类型

![select_type-查询类型](https://note.youdao.com/yws/public/resource/0fbd89b18ba6fa0de992414908a0b609/xmlnote/WEBRESOURCE76ef440c6917532f30f033ec75651525/2765)

#### table: 查询针对的表

有可能是：

- 实际的表名  如 `select * from t1`;
- 表的别名    如 `select * from t2 as tmp`;
- `derived`  如`from`型子查询时
- `null`     直接计算得结果,不用走表


#### possible_key: 可能用到的索引

**注意**: 系统估计可能用的几个索引,但最终,只能用 1 个.

#### key: 最终用的索引.
#### key_len: 使用的索引的最大长度
#### type: 是指查询的方式,非常重要,是分析**查数据过程**的重要依据可能的值

##### all: 意味着从表的第 1 行,往后,逐行做全表扫描,运气不好扫描到最后一行.

**例:** 把`goods_name`列上的索引去掉, 并根据`goods_name`来查询
    
```mysql
mysql> explain select goods_name from goods where goods_name='诺基亚N85' \G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: goods
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 31
        Extra: Using where
1 row in set (0.00 sec)
```
    
- `index`比`all`性能稍好一点，

> 通俗的说: `all` 扫描所有的数据行,相当于`data_all` `index` 扫描所有的索引节点,相当于`index_all`

- ２ 种情况可能出现 `all`

    - 索引覆盖的查询情况下, 能利用上索引,但是又必须全索引扫描.
    
    ```mysql
    mysql> explain select goods_id from  goods where goods_id=1 or goods_id+1>20
    \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table:  goods
             type: index
    possible_keys: PRIMARY
              key: PRIMARY
          key_len: 3
              ref: NULL
             rows: 31
            Extra: Using where; Using index
    1 row in set (0.00 sec)
    
    mysql> explain select goods_id,click_count from  goods where goods_id=1 or go
    ods_id+1>20 \G
    *************************** 1. row ***************************
               id: 1
      select_type: SIMPLE
            table:  goods
             type: ALL
    possible_keys: PRIMARY
              key: NULL
          key_len: NULL
              ref: NULL
             rows: 31
            Extra: Using where
    1 row in set (0.00 sec)

    ```

- 是利用索引来进行排序,但取出所有的节点

```mysql
select goods_id from  goods order by goods_id desc;
```
    
**分析:** 没有加 `where` 条件, 就得取所有索引节点,同时,又没有回行,只取索引节点.
再排序,经过所有索引节点.
    
```mysql
mysql> explain select goods_id from  goods order by goods_id asc\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table:  goods
         type: index
possible_keys: NULL
          key: PRIMARY
      key_len: 3
          ref: NULL
         rows: 31
        Extra: Using index
1 row in set (0.00 sec)
```
    
##### range: 意思是查询时,能根据索引做范围的扫描

```mysql
mysql> explain select goods_id,goods_name,shop_price from  goods where goods
id >25 \G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table:  goods
         type: range
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 3
          ref: NULL
         rows: 8
        Extra: Using where
1 row in set (0.00 sec)
```
 
##### ref: 意思是指 通过索引列,可以直接引用到某些数据行
     
```mysql
mysql> explain select goods_id,goods_name from  goods where cat_id=4 \G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table:  goods
         type: ref
possible_keys: cat_id
          key: cat_id
      key_len: 2
          ref: const
         rows: 3
        Extra:
1 row in set (0.00 sec)
```
        
在这个例子中,通过`cat_id`索引 指向`N`行`goods`数据,来查得结果.
        
##### eq_ref: 是指,通过索引列,直接引用某 1 行数据,常见于连接查询中

```mysql
mysql> explain select goods_id,shop_price from  goods innert join ecs_catego
y using(cat_id) where goods_id> 25 \G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: innert
         type: range
possible_keys: PRIMARY,cat_id
          key: PRIMARY
      key_len: 3
          ref: NULL
         rows: 8
        Extra: Using where
*************************** 2. row ***************************
           id: 1
  select_type: SIMPLE
        table: ecs_category
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 2
          ref: shop.innert.cat_id
         rows: 1
        Extra: Using index
2 rows in set (0.00 sec)
```

##### const, system, null  这 3 个分别指查询优化到常量级别, 甚至不需要查找时间.

一般按照主键来查询时,易出现`const`,`system`

或者直接查询某个表达式,不经过表时, 出现`NULL`
    
```mysql
mysql> explain select goods_id,goods_name,click_count from  goods wher
_id=4 \G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table:  goods
         type: const
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 3
          ref: const
         rows: 1
        Extra:
1 row in set (0.00 sec)

mysql> explain select max(goods_id) from  goods \G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: NULL
         type: NULL myisam表的max,min,count在表中优化过,不需要\真正查找,为NULL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: NULL
        Extra: Select tables optimized away
1 row in set (0.00 sec)
```
    
#### ref: 指连接查询时, 表之间的字段引用关系.

```mysql
mysql> explain select goods_id,cat_name,goods_name from  goods inner join ec
_category using(cat_id) where ecs_category.cat_name='' \G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table:  goods
         type: ALL
possible_keys: cat_id
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 31
        Extra:
*************************** 2. row ***************************
           id: 1
  select_type: SIMPLE
        table: ecs_category
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 2
          ref: shop. goods.cat_id
         rows: 1
        Extra: Using where
2 rows in set (0.00 sec)
```

#### rows: 是指估计要扫描多少行

#### extra: 附加信息

- **index**： 是指用到了索引覆盖,效率非常高
- **using where**： 是指光靠索引定位不了,还得`where`判断一下 
- **using temporary**： 是指用上了临时表, `group by` 与 `order by` 不同列时,或`group by`,`order by`别的表的列.
- **using filesort**： 文件排序(文件可能在磁盘,也可能在内存)

### in 型子查询引出的陷阱

**问题:** 在`ecshop`商城表中,查询6号栏目的商品,应该怎么查 (注：6号是一个大栏目)

**最直观的查询:** `mysql> select goods_id,cat_id,goods_name from  goods where cat_id in (select
cat_id from ecs_category where parent_id=6)`;

**误区:** 给我们的感觉是, 先查到内层的 6 号栏目的子栏目,如`7,8,9,11`
然后外层, `cat_id in (7,8,9,11)`

**事实:** 如下图, `goods`表全扫描, 并逐行与`category`表对照,看`parent_id=6`是否成立

![goods表全扫描](https://note.youdao.com/yws/public/resource/0fbd89b18ba6fa0de992414908a0b609/xmlnote/15F97A05F8914F6E847652ABB25BAEBC/2789)

**原因:** `mysql`的查询优化器,针对`in`型做优化,被改成了`exists`的执行效果.当`goods`表越大时, 查询速度越慢.

**改进:** 用连接查询来代替子查询

```mysql
 explain select goods_id,g.cat_id,g.goods_name from  goods as g inner join (select cat_id from ecs_category where parent_id=6) as t using(cat_id) \G
```

内层 `select cat_id from ecs_category where parent_id=6` ; 用到`parent_id`索引, 返回 4 行

```mysql
+--------+
| cat_id |
+--------+
|      7 |
|      8 |
|      9 |
|     11 |
+--------+ 
```

形成结果,设为`t`

```mysql
*************************** 3. row ***************************
           id: 2
  select_type: DERIVED
        table: ecs_category
         type: ref
possible_keys: parent_id
          key: parent_id
      key_len: 2
          ref:
         rows: 4
        Extra:
3 rows in set (0.00 sec)
```

- 第 2 次查询

`t` 和 `goods` 通过 `cat_id` 相连, 
因为 `cat_id` 在 `goods` 表中有索引, 所以相当于用`7,8,9,11`快速匹配上 `goods` 的行.

```mysql
*************************** 2. row ***************************
           id: 1
  select_type: PRIMARY
        table: g
         type: ref
possible_keys: cat_id
          key: cat_id
      key_len: 2
          ref: t.cat_id
         rows: 6
        Extra:
```

- 第 1 次查询

是把上面 2 次的中间结果,直接取回

```mysql
*************************** 1. row ***************************
           id: 1
  select_type: PRIMARY
        table: <derived2>
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 4
        Extra: 
```

### exists 子查询

**题:** 查询有商品的栏目

按上面的理解,我们用`join`来操作,如下:

```mysql
mysql> select c.cat_id,cat_name from ecs_category as c inner join  goods as g on c.cat_id=g.cat_id group by cat_name;
```

- 优化 1:  在`group`时, 用带有索引的列来`group`, 速度会稍快一些,另外,
用`int`型 比 `char` 型 分组,也要快一些.


- 优化 2: 在`group`时, 我们假设只取了 A 表的内容,`group by` 的列,尽量用 A 表的列,会比B表的列要快

- 优化 3: 从语义上去优化

```mysql
select cat_id,cat_name from ecs_category where exists(select *from  goods where  goods.cat_id=ecs_category.cat_id)

| 36 | 0.00039075 | select c.cat_id,cat_name from ecs_category as c inner join  goods as g on c.cat_id=g.cat_id group by cat_name
| 37 | 0.00038675 | select c.cat_id,cat_name from ecs_category as c inner join  goods as g on c.cat_id=g.cat_id group by cat_id
| 38 | 0.00035650 | select c.cat_id,cat_name from ecs_category as c inner join  goods as g on c.cat_id=g.cat_id group by c.cat_id
| 40 | 0.00033500 | select cat_id,cat_name from ecs_category where exists (select * from  goods where  goods.cat_id=ecs_category.cat_id)
```

### from 子查询

**注意:** 内层`from`语句查到的临时表, 是没有索引的.

**所以:** `from`的返回内容要尽量少.
 
 `min/max`优化 在表中,一般都是经过优化的. 如下地区表
 
 
 id	| area | pid
 ---|---|---
 1	|   中国   |  0
 2	|   北京	  |  1
 ... |   ...    | ...
 3115 | ... |	3113

我们查`min(id)`, `id`是主键,查`min(id)`非常快.

但是,`pid`上没有索引, 现在要求查询 `3113` 地区的`min(id)`;

```mysql
select min(id) from it_area where pid=69;
```  

试想`id`是有顺序的,(默认索引是升续排列), 因此,如果我们沿着`id`的索引方向走

那么  第 1 个 `pid=69` 的索引结点,他的`id`就正好是最小的`id`.

```mysql
select  id  from it_area use index(primary) where pid=69 limit 1;

| 12 | 0.00128100 | select min(id) from it_area where pid=69                         |
| 13 | 0.00017000 | select id from it_area  use index(primary) where pid=69  limit 1 |
```

改进后的速度虽然快,但语义已经非常不清晰,不建议这么做,仅仅是实验目的.


### count() 优化

1. MyISAM 的`count()`非常快

    答: 是比较快,但仅限于查询表的 **所有行** 比较快, 因为 MyISAM 对行数进行了存储.
一旦有条件的查询, 速度就不再快了.尤其是`where`条件的列上没有索引.

2. 假如,`id<100` 的商家都是我们内部测试的,我们想查查真实的商家有多少?

    `select count(*) from lx_com where id>=100;`  ( 1000 多万行用了 6.X 秒)

**小技巧:**

- `select count(*) from lx_com;`
- `select count(*) from lx_com where id<100;`
- `select count(*) frol lx_com - select count(*) from lx_com where id<100;`
- `select (select count(*) from lx_com) - (select count(*) from lx_com where id<100)`

### group by 优化

**注意:**

1. 分组用于统计,而不用于筛选数据.比如: 统计平均分,最高分,适合, 但用于筛选重复数据,则不适合. 
以及用索引来避免临时表和文件排序

2. 以 `A` , `B` 表连接为例 ,主要查询 `A` 表的列, 
那么 `group by`, `order by` 的列尽量相同,而且列应该显示声明为 `A` 的列

### union 优化

**注意:**

`union all` 不过滤 效率提高,如非必须,请用 `union all` 因为 `union` 去重的代价非常高, 放在程序里去重.


### limit 及翻页优化

`limit offset,N`  当 `offset` 非常大时, 效率极低.

原因是 `mysql` 并不是跳过 `offset` 行,然后单取 `N` 行.

而是取 `offset+N` 行,返回放弃前 `offset` 行,返回 `N` 行.

效率较低,当 `offset` 越大时,效率越低

**优化办法:**

1. 从业务上去解决

    办法: 不允许翻过100页,以百度为例,一般翻页到70页左右.

2. 不用`offset`,用条件查询.

    **例:**
    
    ```mysql
    mysql> select id,name from lx_com limit 5000000,10;
    +---------+--------------------------------------------+
    | id      | name                                       |
    +---------+--------------------------------------------+
    | 5554609 | 温泉县人民政府供暖中心          |
    ..................
    | 5554618 | 温泉县邮政鸿盛公司                |
    +---------+--------------------------------------------+
    10 rows in set (5.33 sec)
    
    mysql> select id,name from lx_com where id>5000000 limit 10;
    +---------+--------------------------------------------------------+
    | id      | name                                                   |
    +---------+--------------------------------------------------------+
    | 5000001 | 南宁市嘉氏百货有限责任公司                |
    .................
    | 5000002 | 南宁市友达电线电缆有限公司                |
    +---------+--------------------------------------------------------+
    10 rows in set (0.00 sec)
    ```
    
    - 问题: 2 次的结果不一致.
    - 原因: 数据被物理删除过,有空洞.
    - 解决: 数据以逻辑删除，不以物理删除.
    
    最终在页面上显示数据时,逻辑删除的条目不显示即可.
    
    (一般来说,大网站的数据都是不物理删除的,只做逻辑删除 ,比如 `is_delete=1`)

