#############################
# 本文使用mysql workbench 8.0.15

# 快捷键
# 执行当前语句 ctrl+enter
# 执行所有语句或所选语句 crtl+shift+enter
# 格式美化 ctr+b 

# 3种注释类型
#  1. 使用#注释
-- 2. 使用 -- 注释
 /*
 3. 使用 /和*组合 表示注释 
 */
#############################


#############################
# 第3章 使用MySQL
#############################
use crashcourse; #指定使用的数据库
show databases;  # 了解数据库，返回数据库列表
show tables;  # 返回数据库内表的列表
show columns from customers; # 查看customers表中的所有列设置
describe customers;  # 同上，查看customers表中的所有列设置

show status; # 用于显示广泛的服务器状态信息
show create database crashcourse; #查看创建数据库crashcourse的mysql代码语句
show create table productnotes;  #查看创建表productnotes表的mysql代码语句
show grants; #显示授予用户（所有用户或特定用户）的安全权限
# show errors; # 显示服务器错误内容
# show warnings； #显示服务器警告内容


#############################
# 第4章 检索数据
#############################
# select检索单列，多列，所有列
select prod_name from products;  # 从products表中检索prod_name 单列
select prod_id,prod_name,prod_price from products;   # 从products表中检索prod_name，prod_name,prod_price 多列
select * from products;   #  # 从products表中检索所有列，通常情况下，检索不需要的列会降低检索和应用程序的效率

# 使用distinct 去重
select distinct vend_id from products;  # 使用distinct关键字去重，distinc只能放在列名的前面
select distinct vend_id,prod_price from products;  # distinct不仅对前置它的列vend_id起作用，同时也作用于prod_price，两列值有重复，才去重

# 使用limit检索部分行，开始位置为行索引值，索引从0开始
select prod_name from products limit 5; #从第 0 行开始，返回前 5 行
select prod_name from products limit 5,5; #从第 5 行开始，检索 5 行
# 另一种写法
select prod_name from products limit 4 OFFSET 3; #从第 3 行开始，检索 4 行
select prod_name from products limit 3,4; #，同上，从第 3 行开始，检索 4 行

# 行数不够时，mysql只返回它能返回的那么多行
select count(prod_name) from products; # prod_name 共14行，索引为0-13
select prod_name from products limit 10,5; #从第 10 行开始，检索 5 行，行索引10-14，超出范围，只返回10-13共4行数据

# 使用完全限定的表名
select products.prod_name from products;
select products.prod_name from crashcourse.products;


#############################
# 第5章 排序检索数据
#############################

/* 关系型数据库设计理论认为，如果不明确规定排序顺序，
则不应该假定检索出的数据的顺序有意义 */

# order by 子句对输出排序
# 按单列排序 
select prod_name from products order by prod_name;   # 以字母顺序排序prod_name列 
select prod_name from products order by prod_id;  # 使用非检索的列排序数据也是合法的，如使用prod_id顺序排列prod_name
# 按多列排序 
select prod_id, prod_price,prod_name from products order by prod_price, prod_name; #先按价格，再按产品名排序
# 降序排列 desc，desc只作用于直接位于其前面的列名
select prod_id, prod_price,prod_name from products order by prod_price desc; # 按价格降序排列
select prod_id, prod_price,prod_name from products order by prod_price desc, prod_name; #先按价格降序排列，再按产品名升序排列 
select prod_id, prod_price,prod_name from products order by prod_price desc, prod_name desc; #先按价格降序排列，再按产品名降序排列

# 使用order by 和limit组合，找出一列中最高或最低的值
# 顺序：order by子句必须在from子句之后，limit子句必须在order by之后
select prod_price from products order by prod_price desc limit 1; # 最高值 
select prod_price from products order by prod_price limit 1; # 最低值 


#############################
# 第6章 过滤数据
#############################
select prod_name,prod_price from products where prod_price = 2.50;   # 价格等于2.50的产品名、产品价格
select prod_name,prod_price from products where prod_name = "fuses";  # 默认不区分大小写
select prod_name,prod_price from products where prod_price < 10; # 价格小于10的产品名、产品价格
select prod_name,prod_price from products where prod_price <=10; # 价格小于等于10的产品名、产品价格

# 不匹配检查
select vend_id,prod_name from products where vend_id <> 1003; # 检索不是由1003供应商制造的所有产品 
select vend_id,prod_name from products where vend_id != 1003; # 同上，检索不是由1003供应商制造的所有产品 

# 范围值检索，between A and B，包括A和B
select prod_name,prod_price from products where prod_price between 5 and 10; # 价格 大于等于5，小于等于10 的产品名、产品价格

# 空值检查
select prod_name from products where prod_price is null;  # 返回prod_price为空值null的prod_name,无对应数据 
select cust_id from customers where cust_email is null; # 检索cust_email为空值时的cust_id
/* 在通过过滤选择出不具有特定值的行时，你可能希望返回具有NULL值得行。
但是，不行。因为未知具有特殊的含义，数据库不知道他们是否匹配，所以在匹配过滤和不匹配过滤中不返回NULL。
因此，在过滤数据时，一定要验证返回数据中确实给出了被过滤列具有NULL的行。 
*/


#############################
# 第7章 数据过滤
#############################

# and 或 or 操作符连接多个where子句 
# AND 用在WHERE子句中的关键字，用来指示检索满足所有给定条件的行
select vend_id,prod_price,prod_name from products
where vend_id = 1003 and prod_price <= 10; #检索由供应商1003制造且价格小于等于10美元的产品信息
# OR操作符，指示MySQL检索匹配任一条件的行
select prod_name,prod_price from products 
where vend_id = 1002 or vend_id = 1003; # 检索由任一个指定供应商制造的所有产品的产品信息

# and 和 or结合，and优先计算
# 优先计算and，查找vend_id为1003且价格>=10的产品，或者vend_id为1002的产品，不管价格如何  
select prod_name,prod_price from products 
where vend_id = 1002 or vend_id = 1003 and prod_price >= 10;
# 使用圆括号明确运算顺序：查找vend_id为1002或1003，且价格>=10的产品
select prod_name,prod_price from products 
where (vend_id = 1002 or vend_id = 1003) and prod_price >= 10; 

# IN操作符
# IN操作符后跟由逗号分隔的合法值清单，整个清单必须括在圆括号
select prod_name,prod_price from products
where vend_id in (1002,1003) order by prod_name;
# IN操作符完成与OR相同的功能
select prod_name,prod_price from products
where vend_id = 1002 or vend_id = 1003 order by prod_name; # 同上 

# NOT操作符
# 列出1002和1003之外的供应商生产的产品
select prod_name,prod_price from products
where vend_id not in (1002,1003) order by prod_name;
# Mysql支持not对in，between，exsits子句取反 


#############################
# 第8章 用通配符进行过滤 
#############################
/*
通配符：用来匹配值得一部分的特殊字符 
搜索模式：由字面值、通配符或两者组合构成的搜索条件 
Like操作符：为在搜索子句中使用通配符，必须使用like操作符。
指示mysql，其后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。 
*/

-- 通配符类型 
# 百分号 % 通配符 ：表示任何字符（包括0个字符 ）出现任意次数 
# 特殊：注意 % 不能匹配NULL空值！ 
# 找到所有以词jet起头的产品 
select prod_id,prod_name from products where prod_name like "jet%";
# 通配符可以在搜索模式任意位置使用
# 比如下方出现在头尾两处 ，匹配任意位置包含文本anvil的值 
select prod_id,prod_name from products where prod_name like "%anvil%"; 
# 比如下方出现在搜索模式的中间，匹配所有以s开头e结尾的值 
select prod_name from products where prod_name like "s%e"; 

# 下划线 _ 通配符 ：匹配一个字符，不能多不能少 
select prod_id,prod_name from products
where prod_name like "_ ton anvil";

# 技巧：把通配符至于搜索模式的开始处，搜索起来是最慢的！ 


#############################
# 第9章 用正则表达式进行搜索 
#############################

-- 基本字符匹配 
# 查找产品名中含有'1000'的所有行 
select prod_name from products where prod_name regexp "1000";
# .在正则表达式中，匹配任意 一个 字符 
select prod_name from products where prod_name regexp ".000";

-- like 和 正则表达式的区别 ，是否在列值中匹配 
# like在整个列中查找，如果被匹配的文本出现在列值中，匹配不到结果，除非使用通配符 
select prod_name from products where prod_name like "1000" order by prod_name;     #无返回结果 
# like + 通配符
select prod_name from products where prod_name like "%1000" order by prod_name;    # 返回结果'JetPack 1000'
select prod_name from products where prod_name like "%000" order by prod_name;     # 返回结果 'JetPack 1000' 'JetPack 2000'
# Regexp在列值中匹配
select prod_name from products where prod_name regexp ".000" order by prod_name;   # 返回结果 'JetPack 1000' 'JetPack 2000'

# regexp如何匹配整个列，同like效果呢，使用^和$定位符即可 


-- 正则表达式匹配默认不分大小写，需使用BINARY区分大小写  
select prod_name from products where prod_name regexp binary "JetPack .000";

-- 正则表达式的OR操作符： |
select prod_name from products where prod_name regexp "1000|2000" order by prod_name;

-- 正则表达式匹配几个字符之一 [ ]
select prod_name from products where prod_name regexp '[123] Ton' order by prod_name;  # [123]匹配单一字符：1或2或3
select prod_name from products where prod_name regexp '[1|2|3] Ton' order by prod_name;  # [1|2|3]同[123]，匹配单一字符：1或2或3
select prod_name from products where prod_name regexp '1|2|3 ton' order by prod_name; # '1|2|3 ton'匹配1或2或'3 ton'
select prod_name from products where prod_name regexp '[^123]' order by prod_name;  # 取反

-- 正则表达式匹配范围 
select prod_name from products where prod_name regexp '[1-5] Ton' order by prod_name;  # [1-5]匹配1,2,3,4,5

-- 正则表达式匹配特殊字符，必须用\\前导，进行转义 
-- 多数正则使用单反斜杠转义，但mysql使用双反斜杠，mysql自己解释一个，正则表达式库解释一个 
select vend_name from vendors where vend_name regexp "\\." order by vend_name; # ‘\\.'匹配字符.
select vend_name from vendors where vend_name regexp "." order by vend_name;  #  '.'匹配任意字符，每行都会被检索出来

-- 正则表达式匹配字符类 
#    [:alnum:]    任意字母和数字（同[a-zA-Z0-9]） 
#    [:alpha:]    任意字符（同[a-zA-Z]） 
#    [:blank:]    空格和制表（同[\\t]） 
#    [:cntrl:]    ASCII控制字符（ASCII 0到31和127） 
#    [:digit:]    任意数字（同[0-9]） 
#    [:graph:]    与[:print:]相同，但不包括空格 
#    [:lower:]    任意小写字母（同[a-z]） 
#    [:print:]    任意可打印字符 
#    [:punct:]    既不在[:alnum:]又不在[:cntrl:]中的任意字符 
#    [:space:]    包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]） 
#    [:upper:]    任意大写字母（同[A-Z]） 
#    [:xdigit:]    任意十六进制数字（同[a-fA-F0-9]） 
select prod_name from products where prod_name regexp '[:digit:]' order by prod_name; #[:digit:]匹配任意数字 

-- 匹配多个实例 
#    *        0个或多个匹配 
#    +        1个或多个匹配（等于{1,}）
#    ?        0个或1个匹配（等于{0,1}）
#     {n}        指定数目的匹配 
#     {n,}    不少于指定数目的匹配
#    {n,m}    匹配数目的范围（m不超过255）
select prod_name from products where prod_name regexp '\\([0-9] sticks?\\)'
order by prod_name;  # 返回了'TNT (1 stick)'和'TNT (5 sticks)'
select prod_name from products where prod_name regexp '[[:digit:]]{4}'
order by prod_name;  # [[:digit:]]{4}匹配连在一起的任意4位数字

-- 定位符
#    ^            文本的开始 
#    $            文本的结尾 
#    [[:<:]]        词的开始 
#    [[:>:]]        词的结尾
select prod_name from products where prod_name regexp '^[0-9\\.]' order by prod_name; #找出以一个数（包括以小数点开始的数）开始的所有产品
select prod_name from products where prod_name regexp '[0-9\\.]' order by prod_name;  #找出包括小数点和数字的所有产品


-- ^的双重作用 
# 在集合中（用[和]定义），用它来否定该集合
# 用来指串的开始处

-- 不适用数据库表进行正则表达式的测试：匹配返回1，无匹配返回0
select 'hello' regexp '[0-9]'; # 返回 0 
select 'hello' regexp '[:alnum:]'; # 返回 1


#############################
# 第10章 创建计算字段 
#############################

-- 拼接字段 concat()
select concat(vend_name,' (',vend_country,')') from vendors order by vend_name;  

-- 删除数据左侧多余空格 ltrim()
-- 删除数据两侧多余空格 trim()
-- 删除数据右侧多余空格 rtrim()
select concat(rtrim(vend_name),' (',rtrim(vend_country),')') from vendors order by vend_name;

-- as赋予别名
select concat(rtrim(vend_name),' (',rtrim(vend_country),')') as vend_title from vendors order by vend_name;

-- 执行算数计算
select prod_id,quantity,item_price from orderitems where order_num = 20005;

select prod_id,quantity,item_price,quantity * item_price as expanded_price
from orderitems where order_num = 20005;  # 计算总价expanded_price

-- 简单测试计算 
select 2*3;
select trim('abc');
select now();  # 返回当前日期和时间


#############################
# 第11章 利用数据处理函数  
#############################

-- 文本处理函数 
#    left()            返回串左边的字符 
#    length()        返回串的长度 
#    locate()        找出串的一个子串 
#    lower()            将串转换为小写
#    ltrim()            去掉串左边的空格
#    right()            返回串右边的字符 
#    rtrim()            去掉串右边的空格  
#    soundex()        返回串的soundex值
#    substring()        返回子串的字符 
#    upper()            将串转换为大写

-- UPPER()函数 转换文本为大写 
select vend_name, upper(vend_name) as vend_name_upcase from vendors order by vend_name;

# soundex() 描述语音表示的字母数字模式的算法,对串按照发音比较而不是字母比较
select cust_name,cust_contact from customers where cust_contact = 'Y. Lie';  # 无返回 
select cust_name,cust_contact from customers where soundex(cust_contact) = soundex('Y. Lie'); # 按发音搜索 

-- 日期和时间处理函数 
#    adddate()        增加一个日期（天，周等）
#    addtime()        增加一个时间（时、分等）
#    curdate()        返回当前日期 
#    curtime()        返回当前时间 
#    date()            返回日期时间的日期部分     
#    datediff()        计算两个日期之差 
#    date_add()        高度灵活的日期运算函数 
#    date_format()    返回一个格式化的日期或时间串 
#    day()            返回一个日期的天数部分     
#    dayofweek()        对于一个日期，返回对应的星期几 
#    hour()            返回一个时间的小时部分 
#    minute()        返回一个时间的分钟部分 
#    month()            返回一个日期的月份部分 
#    now()            返回当前日期和事件 
#    second()        返回一个时间的秒部分 
#    time()             返回一个日期时间的时间部分 
#    year()            返回一个日期的年份部分 

# 首选的日期格式yyyy-mm-dd，避免多义性 
select cust_id,order_num from orders where order_date = "2005-09-01";
select * from orders; # order_date为datetime数据类型，含有时间信息；如果时间信息不是00:00:00,上句查找无结果
# 按照date()日期进行过滤信息，更可靠 
select cust_id,order_num from orders where date(order_date) = "2005-09-01";

# 检索2005年9月下的订单 
select cust_id,order_num from orders where year(order_date) = 2005 and month(order_date) = 9;
select cust_id,order_num from orders where date(order_date) between "2005-09-01" and "2005-09-30";

-- 数值处理函数 
#    abs()            返回一个数的绝对值
#    cos()            返回一个角度的余弦
#    exp()            返回一个数的指数值
#    mod()            返回除操作的余数
#    pi()            返回圆周率    
#    sin()            返回一个角度的正弦 
#    sqrt()            返回一个数的平方根 
#    tan()            返回一个角度的正切 
 
 
#############################
# 第12章 汇总数据   
#############################

-- 聚类函数 
# avg()            返回某列的平均值 
# count()        返回某列的行数 
# max()            返回某列的最大值 
# min()            返回某列的最小值 
# sum()            返回某列值之和 

-- avg()
# AVG()返回products表中所有产品的平均价格
select avg(prod_price) as avg_price from products;
# 返回特定供应商所提供产品的平均价格
select avg(prod_price) as avg_price from products where vend_id = 1003;
# avg()只能作用于单列，多列使用多个avg()
select avg(item_price) as avg_itemprice,avg(quantity) as avg_quantity from orderitems;

-- count()
# COUNT(*)对表中行的数目进行计数，不忽略空值 
select count(*) as num_cust from customers; 
# 使用COUNT(column)对特定列中具有值的行进行计数，忽略NULL值
select count(cust_email) as num_cust from customers;  

-- max() & min()
# MAX()返回products表中最贵的物品的价格
select max(prod_price) as max_price from products;
# 在用于文本数据时，如果数据按相应的列排序，则MAX()返回最后一行
select max(prod_name) from products; 
# MIN()返回products表中最便宜物品的价格
select min(prod_price) as min_price from products;
# 在用于文本数据时，如果数据按相应的列排序，则MIN()返回最前面一行
select min(prod_name) from products; 

-- sum()
# 检索所订购物品的总数（所有quantity值之和）
select sum(quantity) as items_ordered from orderitems;
select sum(quantity) as items_ordered from orderitems where order_num = 20005;
# 订单20005的总订单金额
select sum(quantity * item_price) as total_price from orderitems where order_num = 20005;

-- 聚类不同值 distinct
# 使用了DISTINCT参数，因此平均值只考虑各个不同的价格
select avg(distinct prod_price) as avg_price from products where vend_id = 1003;
# distinct 只能作用于count(),不能用于count(*)
# distinct 同max(),min()的结合使用，没有意义 

-- 组合聚类函数 
# 4个聚集计算:物品的数目，产品价格的最高、最低以及平均值 
SELECT 
    COUNT(*) AS num_items,
    MIN(prod_price) AS price_min,
    MAX(prod_price) AS price_max,
    AVG(prod_price) AS price_avg
FROM
    products;


#############################
# 第13章 分组计算    
#############################

-- group by 分组 
# 按vend_id排序并分组数据
select vend_id, count(*) as num_prods from products group by vend_id;
# 使用WITH ROLLUP关键字，可以得到每个分组的汇总值，下述语句得到所有分组count(*)的和14 
select vend_id, count(*) as num_prods from products group by vend_id with rollup;

-- having子句 过滤分组 
# where过滤行，having过滤分组 
# WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤
# COUNT(*) >=2（两个以上的订单）的那些分组
select cust_id, count(*) as orders from orders group by cust_id having count(*)>=2;

-- where和having组合使用 
#列出具有2个（含）以上、价格为10（含）以上的产品的供应商
select vend_id,count(*) as num_prods from products where prod_price >=10 group by vend_id having count(*)>=2;
#不加where条件，结果不同 
select vend_id,count(*) as num_prods from products group by vend_id having count(*) >=2;

-- 分组和排序 
# 检索总计订单价格大于等于50的订单的订单号和总计订单价格
select order_num,sum(quantity * item_price) as ordertotal from orderitems group by order_num having sum(quantity * item_price) >=50;
# 按总计订单价格排序输出
SELECT 
    order_num, SUM(quantity * item_price) AS ordertotal
FROM
    orderitems
GROUP BY order_num
HAVING SUM(quantity * item_price) >= 50
ORDER BY ordertotal;


-- select子句总结及顺序 
# 子句            说明                        是否必须使用 
# select        要返回的列或表达式            是 
# from            从中检索数据的表            仅在从表选择数据时使用 
# where            行级过滤                    否 
# group by        分组说明                    仅在按组计算聚集时使用 
# having        组级过滤                      否 
# order by        输出排序顺序                 否
# limit            要检索的行数                 否 


#############################
# 第14章 使用子查询     
#############################

-- 利用子查询进行过滤
# 列出订购物品TNT2的所有客户
SELECT cust_name, cust_contact
FROM customers
WHERE cust_id IN (SELECT cust_id
                  FROM orders
                  WHERE order_num IN (SELECT order_num
                                      FROM orderitems
                                      WHERE prod_id = 'TNT2'));
                                      
                                      
-- 作为计算字段使用子查询
# 对客户10001的订单进行计数
select count(order_num) from orders where cust_id = 10001;
# 显示customers 表中每个客户的订单总数
select cust_name,cust_state, (select count(*) from orders where orders.cust_id = customers.cust_id) as orders
from customers order by cust_name;


#############################
# 第15章 联结表      
#############################

-- 创建联结 
# where子句联结 
select vend_name,prod_name,prod_price 
from vendors,products
where vendors.vend_id = products.vend_id
order by vend_name,prod_name;

-- 笛卡尔积 / 叉联结 
/*由没有联结条件的表关系返回的结果为笛卡尔积。
检索出的行的数目将是第一个表中的行数乘以第二个表的行数。*/

# 删除where联结条件 
# 返回的数据用每个供应商匹配了每个产品，它包括了供应商不正确的产品
select vend_name,prod_name,prod_price 
from vendors,products
order by vend_name,prod_name;

-- 内部联结 inner join ： 表间相等测试 
select vend_name,prod_name,prod_price 
from vendors inner join products
on vendors.vend_id = products.vend_id;

# 编号为20005的订单中的物品及对应情况 
select prod_name,vend_name,prod_price,quantity
from orderitems,products,vendors
where products.vend_id = vendors.vend_id
and orderitems.prod_id = products.prod_id
and order_num = 20005;

# 订购产品TNT2的客户列表
select cust_name,cust_contact
from customers,orders,orderitems
where customers.cust_id = orders.cust_id
and orders.order_num =  orderitems.order_num
and prod_id = 'TNT2';


#############################
# 第16章 创建高级联结      
#############################

-- 使用表别名
# 给列名或计算字段起别名 
 select concat(rtrim(vend_name),' (',rtrim(vend_country),')') as vend_title
 from vendors order by vend_name;
 # 给表起别名 
 select cust_name,cust_contact 
 from customers as c,orders as o,orderitems as oi
 where c.cust_id = o.cust_id
 and oi.order_num = o.order_num
 and prod_id = 'TNT2';
 
-- 自联结 
# ID为DTNTR该物品的供应商生产的其他物品
#方法：子查询 
select prod_id,prod_name from products
where vend_id = (select vend_id from products where prod_id = 'DTNTR');
 #方法：使用联结 
select p1.prod_id,p1.prod_name
from products as p1, products as p2
where p1.vend_id = p2.vend_id
and p2.prod_id = 'DTNTR';

-- 自然联结
# 自然联结使每个列只返回一次
# 方法：通过对表使用通配符*，对所有其他表的列使用明确的子集 
select c.*,o.order_num,o.order_date,oi.prod_id,oi.quantity,oi.item_price
from customers as c,orders as o,orderitems as oi
where c.cust_id = o.cust_id
and oi.order_num = o.order_num
and prod_id = 'FB';
 
-- 外部联结 
# 检索所有客户及其订单
# 方法： 内部联结 
select customers.cust_id,orders.order_num
from customers inner join orders
on customers.cust_id = orders.cust_id;
 
# 检索所有客户及其订单,包括那些没有订单的客户
# 01 ： 左外部联结
select customers.cust_id,orders.order_num
from customers left outer join orders
on customers.cust_id = orders.cust_id;

# 02 ：若使用 右外部联结 结果不同 
select customers.cust_id,orders.order_num
from customers right outer join orders
on customers.cust_id = orders.cust_id;

# 03： 若使用 右外部联结 调换两表位置 结果同01代码相同 
select customers.cust_id,orders.order_num
from orders right outer join customers
on customers.cust_id = orders.cust_id;
 
-- 使用带聚集函数的联结 
 # 检索所有客户分别对应的订单数，inner join 
select customers.cust_name,
       customers.cust_id,
       count(orders.order_num) as num_ord
from customers inner join orders 
on customers.cust_id = orders.cust_id
group by customers.cust_id; 
 
  # 检索所有客户分别对应的订单数，包括没有订单的客户，left outer join 
 select customers.cust_name,
       customers.cust_id,
       count(orders.order_num) as num_ord
from customers left outer join orders 
on customers.cust_id = orders.cust_id
group by customers.cust_id; 


#############################
# 第17章 组合查询      
#############################

-- 使用union 
# 价格小于等于5的所有物品
select vend_id,prod_id,prod_price from products where prod_price <=5;
# 供应商1001和1002生产的所有物品
select vend_id,prod_id,prod_price from products where vend_id in (1001,1002);
# 价格小于等于5的所有物品的列表，而且包括供应商1001和1002生产的所有物品（不考虑价格）
# 方法1 使用union 
select vend_id,prod_id,prod_price from products where prod_price <=5
union
select vend_id,prod_id,prod_price from products where vend_id in (1001,1002);
# 方法2 使用where 
select vend_id,prod_id,prod_price from products 
where prod_price <=5 or vend_id in (1001,1002);

# union默认自动去除重复的行 
# union all，匹配所有行 ，不取消重复行 
select vend_id,prod_id,prod_price from products where prod_price <=5
union all
select vend_id,prod_id,prod_price from products where vend_id in (1001,1002);  # 有一行出现2次 

# 对union组合结果进行排序
# union组合完只能使用一条order by语句，放在最后一个select语句后面 
select vend_id,prod_id,prod_price from products where prod_price <=5
union
select vend_id,prod_id,prod_price from products where vend_id in (1001,1002)
order by vend_id,prod_price;
 

#############################
# 第18章 全文本搜索       
#############################

-- 进行全文本搜索 
# Match() 指定被搜索的列，against()指定要使用的搜索表达式 
select note_text from productnotes where match(note_text) against('rabbit');

# 如果用like语句 
select note_text from productnotes where note_text like '%rabbit%';

# 演示排序如何工作 
/*  注意：RANK (R)在mysql 8.0.2 (reserved)版本中为keyword保留字
当字段名与MySQL保留字冲突时,可以用字符‘’将字段名括起来
或者改为其他名字，比如as rank1等
*/
select note_text, match(note_text) against('rabbit') as 'rank' from productnotes; 

-- 使用查询扩展 
 # 进行一个简单的全文本搜索，没有查询扩展
 select note_text from productnotes where match(note_text) against('anvils');
 # 相同的搜索，这次使用查询扩展
 select note_text from productnotes where match(note_text) against('anvils' with query expansion);

-- 布尔文本搜索
-- 全文本布尔操作符 
#    布尔操作符            说明
#    +                包含，词必须存在 
#    -                排除，词必须不出现
#    >                包含，而且增加等级值 
#    <                包含，且减少等级值 
#    ()                把词组成子表达式（允许这些表达式作为一个组被包含、排除、排列等）
#    ~                取消一个词的排序值
#     *                词尾的通配符
#    “ ”                定义一个短语（与单个词的列表不一样，它匹配整个短语一边包含或排除这个短语）

# 全文本搜索检索包含词heavy的所有行
# 关键字IN BOOLEAN MODE，实际上没有指定布尔操作符，其结果与没有指定布尔方式的结果相同
select note_text from productnotes where match(note_text) against('heavy' in boolean mode);
# -rope* 排除包含rope*（任何以rope开始的词，包括ropes）的行
select note_text from productnotes where match(note_text) against('heavy -rope*' in boolean mode);

# 匹配包含词rabbit和bait的行
select note_text from productnotes where match(note_text) against('+rabbit +bait' in boolean mode);

# 不指定操作符，搜索匹配包含rabbit和bait中的至少一个词的行
select note_text from productnotes where match(note_text) against('rabbit bait' in boolean mode);

# 搜索匹配短语rabbit bait而不是匹配两个词rabbit和bait。 
select note_text from productnotes where match(note_text) against('"rabbit bait"' in boolean mode);

# 匹配rabbit和carrot，增加前者的等级，降低后者的等级
select note_text from productnotes where match(note_text) against('>rabbit <carrot' in boolean mode);

# 必须匹配词safe和combination，降低后者的等级
select note_text from productnotes where match(note_text) against('+safe +(<combination)' in boolean mode);


#############################
# 第19章 插入数据 
#############################

-- 插入完整的行 
# 插入一个新客户到customers表
# 简单但不安全，如果原来表列结构调整，会有问题 
insert into customers values (null,'Pep E. LaPew','100 Main Street','Los Angeles','CA','90046','USA',NULL,NULL);
# 表明括号内明确列名，更安全，稍繁琐 
insert into customers (cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country,cust_contact,cust_email)
values ('Pep E. LaPew','100 Main Street','Los Angeles','CA','90046','USA',NULL,NULL);

-- 插入多个行 
# 方法1： 提交多个insert 语句
insert into customers(cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country)
values('Pep E. LaPew','100 Main Street','Los Angeles','CA','90046','USA');
insert into customers(cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country)
values('M. Martian','42 Galaxy Way','New York','NY','11213','USA');
# 方法2： 只要每条INSERT语句中的列名（和次序）相同，可以如下组合各语句
# 单条INSERT语句有多组值，每组值用一对圆括号括起来，用逗号分隔
insert into customers(cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country)
values('Pep E. LaPew','100 Main Street','Los Angeles','CA','90046','USA'),('M. Martian','42 Galaxy Way','New York','NY','11213','USA');

-- 插入检索出来的值 
# 新建custnew表（非书本内容）
CREATE TABLE `custnew` (
  `cust_id` int(11) NOT NULL AUTO_INCREMENT,
  `cust_name` char(50) NOT NULL,
  `cust_address` char(50) DEFAULT NULL,
  `cust_city` char(50) DEFAULT NULL,
  `cust_state` char(5) DEFAULT NULL,
  `cust_zip` char(10) DEFAULT NULL,
  `cust_country` char(50) DEFAULT NULL,
  `cust_contact` char(50) DEFAULT NULL,
  `cust_email` char(255) DEFAULT NULL,
  PRIMARY KEY (`cust_id`)
) ENGINE=InnoDB;

# 在表custnew中插入一行数据 （非书本内容）
insert into custnew (cust_id,cust_contact,cust_email,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country)
values(null,null,'mysql carsh course@learning.com','Y.CARY','BAKE WAY','NEW YORK','NY','112103','USA');

# 将custnew中内容插入到customers表中 
# 同书本代码不同，这里省略了custs_id,这样MySQL就会生成新值。
insert into customers (cust_contact,cust_email,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country)
select cust_contact,cust_email,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country from custnew;


#############################
# 第20章 更新和删除数据 
#############################

-- update语句 : 删除或更新指定列 
# 更新： 客户10005现在有了电子邮件地址
update customers set cust_email = 'elmer@fudd.com' where cust_id = 10005;
# 更新： 多个列 
UPDATE customers 
SET cust_name = 'The Fudds',
    cust_email = 'elmer@fudd.com'
WHERE cust_id = 10005;

# 删除： 某个列的值，可设置它为NULL（假如表定义允许NULL值）
update customers set cust_email = null where cust_id = 10005;

-- delete 语句： 删除整行而不是某列 
# 从customers表中删除一行
delete from customers where cust_id = 10006;

-- truncate table语句 
# 如果想从表中删除 所有行，不要使用DELETE，可使用TRUNCATE TABLE语句
# TRUNCATE实际是删除原来的表并重新创建一个表，而不是逐行删除表中的数据


#############################
# 第21章 创建和操纵表  
#############################

-- 新建表 create table
# 参书本配套文件create.sql

-- 更新表 alter table 
# 给vendors表增加一个名为vend_phone的列
alter table vendors 
add vend_phone char(20);
# 删除刚刚添加的列
alter table vendors
drop column vend_phone;

# ALTER TABLE的一种常见用途是定义外键
# 以下为书本配套文件create.sql中定义外键的语句 
ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_orders FOREIGN KEY (order_num) REFERENCES orders (order_num);
ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_products FOREIGN KEY (prod_id) REFERENCES products (prod_id);
ALTER TABLE orders ADD CONSTRAINT fk_orders_customers FOREIGN KEY (cust_id) REFERENCES customers (cust_id);
ALTER TABLE products ADD CONSTRAINT fk_products_vendors FOREIGN KEY (vend_id) REFERENCES vendors (vend_id);

-- 删除表
# 删除customers2表（假设它存在）
drop table customers2;

-- 重命名表 
# 使用RENAME TABLE语句可以重命名一个表 (假设存在下述表)
rename table customers2 to customers;
# 对多个表重命名(假设存在下述表)
rename table backup_customers to customer,
             backup_vendors to vendors,
             backup_products to products;


#############################
# 第22章 使用视图   
#############################

/*视图提供了一种MySQL的SELECT语句层次的封装，可用来简化数据处理以及重新格式化基础数据或保护基础数据。 */ 

-- 创建视图 create view
-- 创建视图的语句 show create view viewname
-- 删除视图 drop view viewname
-- 更新视图 1. 先drop后create 2. 直接用create or repalce view

# 创建一个名为productcustomers的视图
create view productcustomers as
select cust_name,cust_contact,prod_id
from customers,orders,orderitems
where customers.cust_id = orders.cust_id
and orders.order_num = orderitems.order_num;
# 检索订购了产品TNT2的客户
select cust_name,cust_contact from productcustomers where prod_id = 'TNT2';

# 用视图重新格式化检索出的数据
# (来自第10章）在单个组合计算列中返回供应商名和位置
select concat(rtrim(vend_name),' (',rtrim(vend_country),')') as vend_title from vendors order by vend_name;
# 若经常使用上述格式组合，可以创建视图 
create view vendorlocations as
select concat(rtrim(vend_name),' (',rtrim(vend_country),')') as vend_title from vendors order by vend_name;
# 检索出以创建所有邮件标签的数据
select * from vendorlocations;

# 用视图过滤不想要的数据
# 定义customeremaillist视图，它过滤没有电子邮件地址的客户
create view customeremaillist as 
select cust_id,cust_name,cust_email from customers
where cust_email is not null;
select * from customeremaillist;

# 使用视图与计算字段
# (来自第10章）检索某个特定订单中的物品，计算每种物品的总价格
select prod_id,quantity,item_price,quantity*item_price as expanded_price from orderitems where order_num = 20005;
# 将其转换为一个视图
create view orderitemsexpanded as 
select order_num,prod_id,quantity,item_price,quantity*item_price as expanded_price from orderitems;
# 创建视图的时候select添加了列名order_num,否则无法按照order_num进行过滤查找 
select * from orderitemsexpanded where order_num = 20005;

# 更新视图 
# 视图中虽然可以更新数据，但是有很多的限制。
# 一般情况下，最好将视图作为查询数据的虚拟表，而不要通过视图更新数据


#############################
# 第23章 使用存储过程    
#############################

-- 创建存储过程 
# 返回产品平均价格的存储过程
delimiter //
create procedure productpricing()
begin
    select avg(prod_price) as priceaverage from products;
end //
delimiter ;
# 调用上述存储过程 
call productpricing();

-- 删除存储过程,请注意:没有使用后面的()，只给出存储过程名。
drop procedure productpricing;

-- 使用参数 out
# 重新定义存储过程productpricing
delimiter //
create procedure productpricing(out pl decimal(8,2), out ph decimal(8,2), out pa decimal(8,2))
begin
    select min(prod_price) into pl from products;
    select max(prod_price) into ph from products;
    select avg(prod_price) into pa from products;
end //
delimiter ;

# 为调用上述存储过程，必须指定3个变量名
call productpricing(@pricelow,@pricehigh,@priceaverage);
# 显示检索出的产品平均价格
select @priceaverage;
# 获得3个值
select @pricehigh,@pricelow,@priceaverage;

-- 使用参数 in 和 out
# 使用IN和OUT参数,存储过程ordertotal接受订单号并返回该订单的合计
delimiter //
create procedure ordertotal(
    in onumber int,                   # onumber定义为IN，因为订单号被传入存储过程
    out ototal decimal(8,2)            # ototal为OUT，因为要从存储过程返回合计
)
begin
    select sum(item_price*quantity) from orderitems 
    where order_num = onumber
    into ototal;
end //
delimiter ;
# 给ordertotal传递两个参数；
# 第一个参数为订单号，第二个参数为包含计算出来的合计的变量名
call ordertotal(20005,@total);
# 显示此合计
select @total;
# 得到另一个订单的合计显示
call ordertotal(20009,@total);
select @total;

-- 建立智能存储过程 
# 获得与以前一样的订单合计，但只针对某些顾客对合计增加营业税

-- Name:ordertotal
-- Parameters: onumber = order number
--                taxable = 0 if not taxable, 1 if taxable
--                ototal  = order total variable
delimiter //
create procedure ordertotal(
    in onumber int,
    in taxable boolean,
    out ototal decimal(8,2)
) comment 'obtain order total, optionally adding tax'
begin
    -- declare variable for total 定义局部变量total
    declare total decimal(8,2);
    -- declare tax percentage 定义局部变量税率 
    declare taxrate int default 6;
    -- get the order total 获得订单合计
    SELECT SUM(item_price * quantity)
    FROM orderitems
    WHERE order_num = onumber INTO total;
    -- is this taxable? 是否要增加营业税？ 
    if taxable then
        -- Yes,so add taxrate to the total 给订单合计增加税率
        select total+(total/100*taxrate) into total;
    end if;
    -- and finally,save to out variable 最后，传递给输出变量 
    SELECT total INTO ototal;
END //
delimiter ;
# 调用上述存储过程，不加税 
call ordertotal(20005,0,@total);
select @total;
# 调用上述存储过程，加税 
call ordertotal(20005,1,@total);
select @total;

# 显示用来创建一个存储过程的CREATE语句
show create procedure ordertotal;

# 获得包括何时、由谁创建等详细信息的存储过程列表
# 该语句列出所有存储过程
show procedure status;
# 过滤模式 
show procedure status like 'ordertotal';


#############################
# 第24章 使用游标     
#############################

-- 创建、打开、关闭游标 
# 定义名为ordernumbers的游标，检索所有订单
delimiter //
create procedure processorders()
begin
    -- decalre the cursor 声明游标 
    declare ordernumbers cursor
    for
    select order_num from orders;
    
    -- open the cursor 打开游标
    open ordernumbers;
    -- close the cursor 关闭游标
    close ordernumbers;
end //
delimiter ;

-- 使用游标数据 
# 例1：检索 当前行 的order_num列，对数据不做实际处理
delimiter //
create procedure processorders()
begin

    -- declare local variables 声明局部变量
    declare o int;
    
    -- decalre the cursor 声明游标 
    declare ordernumbers cursor
    for
    select order_num from orders;
    
    -- open the cursor 打开游标
    open ordernumbers;
    
    -- get order number 获得订单号 
    fetch ordernumbers into o;
    /*fetch检索 当前行 的order_num列（将自动从第一行开始）到一个名为o的局部声明变量中。
    对检索出的数据不做任何处理。*/
        
    -- close the cursor 关闭游标
    close ordernumbers;

END //
delimiter ;

# 例2：循环检索数据，从第一行到最后一行，对数据不做实际处理
delimiter //
create procedure processorders()
begin
    -- declare local variables 声明局部变量
    declare done boolean default 0;
    declare o int;
   
    -- decalre the cursor 声明游标 
    declare ordernumbers cursor
    for
    select order_num from orders;
   
    -- declare continue handler
    declare continue handler for sqlstate '02000' set done =1;
    -- SQLSTATE '02000'是一个未找到条件，当REPEAT由于没有更多的行供循环而不能继续时，出现这个条件。
    
    -- open the cursor 打开游标
    open ordernumbers;
    
    -- loop through all rows 遍历所有行 
    repeat
    
    -- get order number 获得订单号 
    fetch ordernumbers into o;
    -- FETCH在REPEAT内，因此它反复执行直到done为真
    
    -- end of loop
    until done end repeat;
    
    -- close the cursor 关闭游标
    close ordernumbers;

end //
delimiter ;


# 例3：循环检索数据，从第一行到最后一行，对取出的数据进行某种实际的处理
delimiter //
create procedure processorders()
begin
    -- declare local variables 声明局部变量 
    declare done boolean default 0;
    declare o int;
    declare t decimal(8,2);
    
    -- declare the cursor 声明游标
    declare ordernumbers cursor
    for
    select order_num from orders;
    
    -- declare continue handler
    declare continue handler for sqlstate '02000' set done = 1;
    
    -- create a table to store the results 新建表以保存数据
    create table if not exists ordertotals
    (order_num int,total decimal(8,2));
    
    -- open the cursor 打开游标
    open ordernumbers;
    
    -- loop through all rows 遍历所有行
    repeat
    
    -- get order number 获取订单号
    fetch ordernumbers into o;
    
    -- get the total for this order 计算订单金额
    call ordertotal(o,1,t);  # 参见23章代码，已创建可使用
    
    -- insert order and total into ordertotals 将订单号、金额插入表ordertotals内
    insert into ordertotals(order_num,total) values(o,t);
    
    -- end of loop
    until done end repeat;
    
    -- close the cursor 关闭游标
    close ordernumbers;

end // 
delimiter ;
# 调用存储过程 precessorders()
call processorders();
# 输出结果
select * from ordertotals;


#############################
# 第25章 使用触发器      
#############################

-- 创建触发器 
create trigger newproduct after insert on products for each row select 'product added' into @new_pro;
# mysql 5.0以上版本在TRIGGER中不能返回结果集，定义了变量 @new_pro;
insert into products(prod_id,vend_id,prod_name,prod_price) values ('ANVNEW','1005','3 ton anvil','6.09'); # 插入一行 
select @new_pro;  # 显示Product added消息

-- 删除触发器 
drop trigger newproduct;

-- 使用触发器 
# insert触发器
create trigger neworder after insert on orders for each row select new.order_num into @order_num;
insert into orders(order_date,cust_id) values (now(),10001);
select @order_num;

# delete触发器
# 使用OLD保存将要被删除的行到一个存档表中 
delimiter //
create trigger deleteorder before delete on orders for each row
begin
    insert into archive_orders(order_num,order_date,cust_id)
    values(old.order_num,old.order_date,old.cust_id); # 引用一个名为OLD的虚拟表，访问被删除的行
end //
delimiter ;

# update触发器
# 在更新vendors表中的vend_state值时，插入前先修改为大写格式 
create trigger updatevendor before update on vendors 
for each row set new.vend_state = upper(new.vend_state);
# 更新1001供应商的州为china
update vendors set vend_state = 'china' where vend_id =1001;
# 查看update后数据，1001供应商对应的vend_state自动更新为大写的CHINA
select * from vendors;


#############################
# 第26章 管理事务处理 
#############################

-- 事务 transaction 指一组sql语句
-- 回退 rollback 指撤销指定sql语句的过程
-- 提交 commit 指将未存储的sql语句结果写入数据库表
-- 保留点 savepoint 指事务处理中设置的临时占位符，可以对它发布回退（与回退整个事务处理不同）

-- 控制事务处理
# 开始事务及回退 
select * from ordertotals;   # 查看ordertotals表显示不为空
start transaction;           # 开始事务处理 
delete from ordertotals;     # 删除ordertotals表中所有行
select * from ordertotals;   # 查看ordertotals表显示 为空
rollback;                     # rollback语句回退 
select * from ordertotals;   # rollback后，再次查看ordertotals表显示不为空

# commit 提交 
start transaction;
delete from orderitems where order_num = 20010;
delete from orders where order_num = 20010;
commit;   # 仅在上述两条语句不出错时写出更改 

# savepoint 保留点 
# 创建保留点
savepoint delete1;
# 回退到保留点 
rollback to delete1;
# 释放保留点 
release savepoint delete1;

-- 更改默认的提交行为 
set autocommit = 0;  # 设置autocommit为0（假）指示MySQL不自动提交更改


#############################
# 第27章 全球化和本地化
#############################

-- 字符集和校对顺序
# 查看所支持的字符集完整列表
show character set;
# 查看所支持校对的完整列表,以及它们适用的字符集
show collation;
# 确定所用系统的字符集和校对
show variables like 'character%';
show variables like 'collation%';
# 使用带子句的CREATE TABLE，给表指定字符集和校对
create table mytable
(
    column1 int,
    column2 varchar(10)
) default character set hebrew 
  collate hebrew_general_ci;
# 除了能指定字符集和校对的表范围外，MySQL还允许对每个列设置它们
create table mytable
(
    column1 int,
    column2 varchar(10),
    column3 varchar(10) character set latin1 collate latin1_general_ci
)default character set hebrew 
 collate hebrew_general_ci;
# 校对collate在对用ORDER BY子句排序时起重要的作用
# 如果要用与创建表时不同的校对顺序排序,可在SELECT语句中说明 
select * from customers order by lastname,firstname collate latin1_general_cs;


#############################
# 第28章 安全管理
#############################

-- 管理用户
# 需要获得所有用户账号列表时
# mysql数据库有一个名为user的表，它包含所有用户账号。user表有一个名为user的列
use mysql;
select user from user;

-- 创建用户账号 
# 使用create user
create user ben identified by 'p@$$w0rd';
# 重命名一个用户账号
rename user ben to bforta;
# 删除用户账号 
drop user bforta;
# 查看赋予用户账号的权限
show grants for bforta;
# 允许用户在（crashcourse数据库的所有表）上使用SELECT，只读
grant select on crashcourse.* to bforta;
# 重新查看赋予用户账号的权限，发生变化 
show grants for bforta;
# 撤销特定的权限
revoke select on crashcourse.* from bforta;
# 简化多次授权
grant select,insert on crashcourse.* to bforta;

-- 更改口令

# 原来课本中使用的password()加密函数，在8.0版本中已经移除 
# password() :This function was removed in MySQL 8.0.11.
set password for bforta = 'n3w p@$$w0rd';  


-- 如果不指定用户名，直接修改当前登录用户的口令 
set password = 'n3w p@$$w0rd';


#############################
# 第29章 数据库维护 
#############################

# 分析表 键状态是否正确
analyze table orders;
# 检查表是否存在错误 
check table orders,orderitems;
check table orders,orderitems quick; # QUICK只进行快速扫描
# 优化表OPTIMIZE TABLE，消除删除和更新造成的磁盘碎片，从而减少空间的浪费
optimize table orders;