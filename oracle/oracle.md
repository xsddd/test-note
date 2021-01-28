## ROUND
Round函数用法：  
截取数字   
格式如下：`ROUND(number,decimals)`  
其中：
+ number 待做截取处理的数值
+ decimals 指明需保留小数点后面的位数。  
可选项，忽略它则截去所有的小数部分，并四舍五入。  
如果为负数则表示从小数点开始左边的位数，相应整数数字用0填充，小数被去掉。  
需要注意的是，和trunc函数不同，对截取的数字要四舍五入。

## (+)
Oracle  外连接(OUTER JOIN)

左外连接(左边的表不加限制)  
右外连接(右边的表不加限制)  
全外连接(左右两表都不加限制)  
对应SQL：`LEFT/RIGHT/FULL OUTER JOIN`。   
通常省略OUTER关键字， 写成：`LEFT/RIGHT/FULL JOIN`。

在左连接和右连接时都会以一张A表为基础表，该表的内容会全部显示，然后加上A表和B表匹配的内容。 如果A表的数据在B表中没有记录。 那么在相关联的结果集行中列显示为空值（NULL）。

对于外连接， 也可以使用`(+)`来表示。 关于使用`(+)`的一些注意事项：

+ `(+)`操作符只能出现在WHERE子句中，并且不能与OUTER JOIN语法同时使用。
 当使用`(+)`操作符执行外连接时，如果在WHERE子句中包含有多个条件，则必须在所有条件中都包含`(+)`操作符。
+ `(+)`操作符只适用于列，而不能用在表达式上。
+ `(+)`操作符不能与OR和IN操作符一起使用。
+ `(+)`操作符只能用于实现左外连接和右外连接，而不能用于实现完全外连接。
## NVL
从两个表达式返回一个非 null 值。

语法

`NVL(eExpression1, eExpression2)`

参数  
eExpression1, eExpression2

如果 eExpression1 的计算结果为 null 值，则 NVL( ) 返回 eExpression2。如果 eExpression1 的计算结果不是 null 值，则返回 eExpression1。eExpression1 和 eExpression2 可以是任意一种数据类型。如果 eExpression1 与 eExpression2 的结果皆为 null 值，则 NVL( ) 返回 `NULL`。

返回值类型

字符型、日期型、日期时间型、数值型、货币型、逻辑型或 null 值

说明

在不支持 null 值或 null 值无关紧要的情况下，可以使用 NVL( ) 来移去计算或操作中的 null 值。

`select nvl(a.name,'空') as name from student a join school b on a.ID=b.ID`

## 表空间
表空间相关系统视图：
+ DBA_DATA_FILES 用于查询所有数据文件的信息
+ DBA_free_space 用于查询表空间的空闲区间信息
+ dba_tablespaces 用于查询所有表空间的信息

所有表的总空间大小
```
select
  tablespace_name as 表空间名,
  sum(bytes)
from DBA_DATA_FILES
group by tablespace_name;
```
所有表的空闲空间大小
```
select
  a.tablespace_name as               表空间名,
  nvl(sum(b.bytes), 0) / 1024 / 1024 bytes
from DBA_DATA_FILES a, DBA_free_space b
where a.tablespace_name = b.tablespace_name (+) and a.file_id = b.file_id (+)
group by a.tablespace_name;
```
表空间使用情况
```
select
  c.tablespace_name                                         表空间,
  round(a.bytes / 1024 / 1024 / 1024, 2) || 'G'             表空间大小,
  round((a.bytes - b.bytes) / 1024 / 1024 / 1024, 2) || 'G' 已使用空间,
  round(b.bytes / 1024 / 1024 / 1024, 2) || 'G'             剩余空间,
  round(b.bytes / a.bytes * 100, 2) || '%'                  剩余百分比
from (select
        tablespace_name,
        sum(bytes) bytes
      from DBA_DATA_FILES
      group by tablespace_name) a,
  (select
     a.tablespace_name,
     nvl(sum(b.bytes), 0) bytes
   from DBA_DATA_FILES a, DBA_free_space b
   where a.tablespace_name = b.tablespace_name (+) and a.file_id = b.file_id (+)
   group by a.tablespace_name) b,
  dba_tablespaces c
where a.tablespace_name = b.tablespace_name (+) and a.tablespace_name = c.tablespace_name
order by round(b.bytes / 1024 / 1024, 2);
```