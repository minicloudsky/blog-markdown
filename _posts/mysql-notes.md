---
title: Mysql必知必会笔记
date: 2019-09-22 15:35:30
categories: mysql
tags:
- mysql
---

## 概念

##### 数据库是一个以某种方式有组织的形式存储的数据集合

##### 数据库(database) 保存有组织的数据的容器。（一个文件或者一组文件）

##### 数据库表特性:  定义数据如何存储，存储什么数据，数据如何分解。

##### 模式(scheme): 关于数据库和表布局以及特性的信息

##### 列(column): 表中一个字段

##### 行(row): 表中的一个记录

##### 主键(primary key) : 一列(或者一组列 ，其值能够唯一区分表中每一行

##### 成为主键条件：

1. 任意两行都不具有相同主键值
2. 每行都必须有一个主键值(主键列不允许NULL值)
<!-- more -->
##### 主键习惯:

1. 不更新主键列的值
2. 不重样主键列的值
3. 不在主键列中使用可能会更改的值

查看数据库

`show databases;`

查看表

`show tables;`

查看表的字段

`show columns from shops(table_name);`

显示服务器状态

`show status`

## 检索

`select * from girl_firends;`

`select name from my_girlfriends;`

##### 检索出不同值

`select distinct vend_id from products;`

##### limit返回第一行或者前几行

`select prod_name from products limit 5;`

##### 为得出下一个5行，可以指定要检索的开始后和行数

`select prod_name from products limit 5,5;` // 返回从行5开始的5行

##### limit 3,4 从行3开始的4行

##### 使用完全限定的表名

``SELECT`
	`*` 
`FROM`
	`develops.weapp_store_region` 
	LIMIT 5`

## 排序检索

`order by` 子句取一个或多个列名，对输出进行排序

```sql
`SELECT`
	`*` 
`FROM`
	`develops.weapp_store_region` 
`ORDER BY`
`NAME `
```



##### 按多列排序

parent_id相同时候按照type排序

```sql
``SELECT`
	`*` 
`FROM`
	`develops.weapp_store_region` 
`ORDER BY`
	`parent_id,`
	type;` 
```

指定排序方向，默认升序(ASC)

```sql
``SELECT`
	`*` 
`FROM`
	`develops.weapp_store_region` 
`ORDER BY`
	parent_id desc`
```

多列排序，按类型降序排列，再对id排序

```sql
``SELECT`
	`* 
FROM`
	`develops.weapp_store_region` 
`ORDER BY`
	type desc,id;`
```

##### DESC关键字只应用到直接位于其前面的列名

##### 使用order by 和limit组合，找出一个列中最高或者最低的值

找出最昂贵的物品

`select prod_price from products order by prod_price desc limit 1;`

prod_price desc确保按照由最贵到最低排序，limit 1只返回一行

##### order by必须是select语句中的最后一个子句

## 过滤数据

##### where子句操作符

| 操作符  | 说明               |
| ------- | ------------------ |
| =       | 等于               |
| <>      | 不等于             |
| !=      | 不等于             |
| <       | 小于               |
| <=      | 大于等于           |
| >       | 大于               |
| >=      | 大于等于           |
| BETWEEN | 在指定的两个值之间 |

##### 检查单个值

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	NAME = "深圳市";
```

##### mysql匹配时候默认不匹配大小写

##### 不匹配检查

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	id <> 100;
```

##### between 检索范围

```sql
``SELECT`
	`*` 
`FROM`
	`weapp_store_region` 
`WHERE`
	id between 30 and 50;`
```

##### 空值检查 NULL 无值 (no value,该字段可以不包含值)

`select prod_name from products where product_price is null;`

##### and 操作符

``SELECT`
	`*` 
`FROM`
	`weapp_store_region` 
`WHERE`
	parent_id = 0 and state=0`

##### or 检索匹配任一条件的行

``SELECT`
	`*` 
`FROM`
	`weapp_store_region` 
`WHERE`
	parent_id = 0 or parent_id=1`

##### sql在处理or操作符前，会有限处理and操作符，因此注意加括号

##### in 操作符用来指定条件范围，范围中的每个条件都可以进行匹配，in取和法制的由逗号分隔的清单，全都括在圆括号中

`select prod_name,prod_price from products where vend_id in (111,222) order by prod_name;`

##### in操作符优点:

1. 语法清楚直观
2. 计算次序更容易管理 (使用操作符少)
3. in 操作符一般比or操作符执行更快
4. in 最大优点是可以包含其他select子句，可以更动态的创立where子句

##### not 操作符有气质有一个功能，否定它后面跟的一切条件

`select prod_name,prod_price from products where vend_id not in (100,103) order by prod_name`

##### mysql支持使用not对 in ，between，exists取反

## 通配符进行过滤

##### like指示MySQL对后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较

##### 百分号(%)通配符,在搜索串中,%表示任何字符出现任意次数,注意%不能匹配NULL

找出 “北”开头的所有地区，%告诉mysql接受北后面的任意字符

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	name like '北%'
```

在任意位置包含"北"

```sql
SELECT

- FROM
  weapp_store_region 
  WHERE
  name like '%伊%'

```

##### 下划线(_)通配符，用途和%一样，但是下划线只匹配单个字符而不是多个字符，与%能匹配0个字符不一样，_总是匹配一个字符

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	pinyin like '_eijing'
```

​       通配符匹配花费时间比较多，如何其他操作可以完成，尽量少用通配符，除非绝对必要，尽量不要把他们放在搜索模式的开始处，把通配符置于搜索模式开始处是最慢的，仔细注意通配符位置，位置放错可能得不到想要的结果。

## 使用正则表达式

##### mysql中正则表达式为通用正则表达式的一部分子集

```sql
SELECT
 FROM
  weapp_store_region 
  WHERE
  pinyin REGEXP 'luoyang'


```

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	id REGEXP '.00'
.匹配所有字符，结果会返回100、200,300 。。
```

like匹配整个列，如果被匹配的文本仅仅在列值中出现，like将不会找到它，相应的行业不被返回(除非使用通配符).而 REGEXP在列值内进行匹配，如果被匹配的文本在列值中出现，REGEXP将会找到它，响应的行将被返回，mysql中的正则表达式匹配不区分大小写，为区分大小写，可以使用binary关键字，如 where prod_name REGEXP BINARY  'JetPack .00'

##### or 匹配，匹配其中之一

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	name like '郑州|深圳'
```

##### 匹配几个字符之一

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	pinyin REGEXP '[abcdefg]eijing'
```

[abcdefg]是另一种形式的or语句，是[a|b|c|d|e|f|g]的缩写

```
字符集合否定，在集合的开始处放置^即可，如[^123]匹配除这些字符外的任何东西
```

```
匹配范围,[1-9]匹配数字1-9，[a-z]匹配字母a-z
匹配特殊字符，必须用\\作为前导，\\-表示查找-，\\.表示查找.

```

| 元字符 | 说明     |
| ------ | -------- |
| \\\f   | 换页     |
| \\\n   | 换行     |
| \\\r   | 回车     |
| \\\t   | 制表     |
| \\\v   | 纵向制表 |

​                                                                    表 2 - 空白元字符

##### 匹配字符类

| 类         | 说明                                               |
| ---------- | -------------------------------------------------- |
| [:alnum:]  | 任意字母和数字(同[a-zA-Z0-9])                      |
| [:alpha:]  | 任意字符（同[a-zA-Z]）                             |
| [:blank:]  | 空格和制表(同[a-zA-Z])                             |
| [:cntrl:]  | ASCII控制字符(ASCII 0到31和127)                    |
| [:digit:]  | 任意数字(同0-9)                                    |
| [:graph:]  | 与[:print:]相同，但不包括空格                      |
| [:lower:]  | 任意小写字母(同[a-z])                              |
| [:print:]  | 任意可打印字符                                     |
| [:punct:]  | 既不在[:alnum:]又不在[:cntrl:]中的任意字符         |
| [:space:]  | 包括空格在内的任意空白字符，(同[\\\f\\\n\\\r\\\v]) |
| [:upper:]  | 任意大写字母(同A-Z)                                |
| [:xdigit:] | 任意十六进制数字(同[a-fA-F0-9])                    |

##### 匹配多个实例

重复元字符

| 元字符 | 说明                       |
| ------ | -------------------------- |
| *      | 0个或多个匹配              |
| +      | 一个或多个匹配(等于{1,})   |
| ?      | 0个或一个匹配(等于{0,1})   |
| {n}    | 指定数目的匹配             |
| {n,}   | 不少于指定数目的匹配       |
| {n,m}  | 匹配数目的范围(m不超过255) |

```sql
select prod_name 
from products
where prod_name REGEXP '\\([0-9] sticks?\\)'
order by prod_name
\\([0-9] 匹配 (数字0-9
stciks? stick,s出现0次或者一次
\\) 匹配 )
```

匹配连在一起的4位数字

```sql
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	parent_id REGEXP '[[:digit:]]{4}'
```

##### 定位符，匹配特殊位置的文本

定位元字符

| 元字符  | 说明           |
| ------- | -------------- |
| ^       | 匹配文本的开始 |
| $       | 匹配文本的结尾 |
| [[:<:]] | 词的开始       |
| [[:<:]] | 词的结尾       |

##### 匹配数字开头

```
SELECT
	* 
FROM
	weapp_store_region 
WHERE
	parent_id REGEXP '^[0-9\\.]'
```

##### ^的双重用途，在集合中(用[和]定义),用它来否定集合，否则，用来指串 的开始处。

使用REGEXP起类似like的作用，like和REGEXP的不同在于，like匹配整个串，而regexp匹配子串，利用定位符，通过用^开始每个表达式，用$结束每个表达式，可以使regexp的作用和like一样。

## 创建计算字段

##### 拼接(concatenate)  将值联结到一起构成单个值，使用concat()函数来拼接两个列。

```sql
SELECT
CONCAT(name,'-',pinyin)
FROM
	weapp_store_region 
	LIMIT 10;


```

##### 去除左侧，右侧空格 ltrim(), rtrim()

```sql
SELECT
RTRIM(name)
FROM
	weapp_store_region 
	LIMIT 10;
```

##### 使用别名（别名也称作导出列，别名是字段或者值的替换名）使用关键词as

```sql
SELECT
	CONCAT( RTRIM( NAME ), '_', LTRIM( pinyin ) ) AS address 
FROM
	weapp_store_region 
	LIMIT 10;
```

##### 执行算数计算

```sql
SELECT
	parent_id * type AS result
FROM
	weapp_store_region 
	LIMIT 10;
```

##### mysql算数运算符有 + - * /  ， ()区分优先顺序

##### 测试计算

```sql
select 2*3
select trim('  abc  ')
select now()
```

## 数据处理函数

##### 常用的文本处理函数

| 函数                    | 说明              |
| ----------------------- | ----------------- |
| LEFT(str,len)           | 返回串左边的字符  |
| LENGTH(str)             | 返回串的长度      |
| LOCATE(substr,str)      | 找出串的一个子串  |
| LOWER(str)              | 将串转换为小写    |
| LTRIM(str)              | 去掉左边空格      |
| RIGHT(str,len)          | 返回串右边的字符  |
| RTRIM(str)              | 去掉串右边的空格  |
| SOUNDEX(str)            | 返回串的soundex值 |
| SUBSTRING(str FROM pos) | 返回子串的字符    |
| UPPER(str)              | 将串转换为大写    |

#####        soundex是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。soundex考虑了类似的发音字符和音节，使得能对串进行发音比较而不是字母比较，虽然soundex不是sql概念，但mysql(就像多数dbms一样)都提供对soundex的支持。

##### 常用日期和时间处理函数

| 函数                                | 说明                           |
| ----------------------------------- | ------------------------------ |
| ADDDATE(date,INTERVAL expr unit)    | 增加一个日期(天、周等)         |
| ADDTIME(expr1,expr2)                | 增加一个时间(时、分等)         |
| CURDATE()                           | 返回当前日期                   |
| CURTIME()                           | 返回当前时间                   |
| DATE()                              | 返回日期时间的日期部分         |
| DATEDIFF('2019-09-01','2019-10-04') | 计算两个日期之差               |
| Date_Add()                          | 高度灵活的日期运算函数         |
| Date_Format()                       | 返回一个格式化的日期或时间串   |
| Day()                               | 返回一个日期的天数部分         |
| DayOfWeek()                         | 对于一个日期，返回对应的星期几 |
| Hour()                              | 返回一个日期的小时部分         |
| Minute()                            | 返回一个日期的分钟部分         |
| Second()                            | 返回一个日期的秒部分           |
| Month()                             | 返回一个日期的月份部分         |
| Now()                               | 返回当前的日期和时间           |
| Time()                              | 返回一个日期时间的时间部分     |
| Year()                              | 返回一个日期的年份部分         |

##### samples:

```sql
select cust_id,order_num 
from orders 
where date(order_date) = '2005-09-01'; //取出日期
```

```sql
检索月份内数据
1 检索每个月份开始和结束天日期
select cust_id,order_num
from orders
where date(order_date) beetween '2009-09-01' and '2009-09-30'
2 不需要记住每个月有多少天
select cust_id,order_num 
from orders
where year(order_date) = 2005 and month(order_date) = 9;
```

##### 数据处理函数

在主要DBMS中，数据处理函数是最一致最统一的函数。

| 函数   | 说明               |
| ------ | ------------------ |
| ABS(X) | 返回一个数的绝对值 |
| COS(X) | 余弦               |
| exp()  | 指数值             |
| mod()  | 返回除操作的余数   |
| Pi()   | 返回圆周率         |
| rand() | 返回一个随机数     |
| SIN(X) | 返回一个角度的正弦 |
| sqrt() | 返回一个数的平方根 |
| tan()  | 返回一个角度的正切 |

## 汇总数据

获取想要的汇总信息

##### SQL聚集函数

| 函数    | 说明             |
| ------- | ---------------- |
| AVG()   | 返回某列的平均值 |
| COUNT() | 返回某列的行数   |
| MAX()   | 返回某列的最大值 |
| MIN()   | 返回某列的最小值 |
| SUM()   | 返回某列之和     |

```sql
// avg(0函数忽略列值为NULL的行)
SELECT
	AVG( weight ) as avg_weight 
FROM
	media
```

```sql
//过滤出vend_id=1003的产品平均价格
select avg(prod_price) as avg_price from products where vend_id = 1003;
```

##### count函数进行计数，可利用count()确定表中行的数目或符合特定条件的行的数目

1. 使用 count(*)对表中行的数目进行计数，不管表列中包含的是空值(NULL)还是非空值。

2. 使用count(column)对特定列中具有值的行进行计数，忽略NULL值。

   ```sql
   //对所有行计数，不管行中各列有什么值
   SELECT
   	count( * ) AS num_case 
   FROM
   	`zc_app_case_category`
   ```

   ```sql
   //只对具有weight的分类计数
   SELECT
   	count( weight ) AS num_case 
   FROM
   	`zc_app_case_category`
   ```

   

