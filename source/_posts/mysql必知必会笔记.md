---
title: mysql必知必会笔记
date: 2019-01-15 13:41:05
tags:
---
# chapter3  了解数据库和表

DOS命令行登录指令：mysql -u 用户名 -p   然后回撤，键入password；               
每一条mysql语句必须以“;”结束才能正确执行
选择数据库：use 数据库名；        
查看数据库的信息：show databases;返回一个数据库列表；          
查看数据库内表的列表：show tables；   
查看某一个表的表列：show columns from 表名；每一个字段返回一行，也可以使用describe 表名；来查看表的信息
查看用来创建特定数据库的mysql语句：show create database 数据库名；
查看用来创建特定表的mysql语句：show create table 表名；
# chapter4 检索数据
#### 检索单个列表
命令：select 列名 from 表名；
所需列名在select关键字后面给出，from给出从其中检索数据的表名。   
SQL语句不区分大小写，但是便于阅读和调试，对所有的关键字使用大写，所有的列名和表名都使用小写。      
在处理SQL语句时，其中所有空格都被忽略。SQL语句可以在一行上给出，也可以分成许多行。
#### 检索多个列
命令： SELECT 列名，列名，列名 FROM 表名；        
选择多个列时，要在列名中间加逗号，但最后一个列名后不加
#### 检索所有的列
命令：SELECT * FROM 表名；      
如果给定一个通配符(*),则返回列表的所有列                                  
一般除非你确实你需要表中的每个列，否则不要使用通配符，检索不需要的列通常会降低检索和应用程序的性能
#### 检索不同的行
当你检索某一个字段时，会检索这个字段的所有行，但是可能这些行中有许多相同的字段，而你只需不同的字段，这时你只需要使用 DISTINCT关键字；        
命令：SELECT DISTINCT 列名 FROM 表名；                   
DINSTINCT关键字必须放在列名的前面
不能不分使用DISTINCT  
DINSTINCT关键字应用于所有列而不仅仅是前置它的列。如果给出 SELECT DISCINCT 列名，列名 FROM 表名；除非指定的列不同，否则所有行都将被检索出来。           
也就是同时指定了两个字段，就是两个字段的字都是相同的行才会被排除在外。
#### 限制结果
SELECT语句返回所有匹配的行，他们可能是指定表中的每个行，为了返回第一行或前几行，课使用LIMIT句子：                 
命令：SELENT 列名 FROM 表名  LIMIT num；        
例：如果num=5，则返回的结果不多以5行，如果想显示后五行这可以使用：     
命令：SELECT 列名 FROM 表名 LIMIT 5,5；这样就会显示从第六行到第10行的内容；      
其中第一个数为开始检索的位置，第二个数为检索的条数。

带一个值的LIMIT总是从第一行开始，给出的数为返回的行数，带两个值的LIMIT可以指定从行号为第一个值的位置开始。

行0    检测出来的第一行为行0而不是行1，因此LIMIT 1,1，将检测出第二行而不是第一行。

行数不够时，只返回能返回的行。
#### 使用完全限定的表名
SELECT  表名.列名  from 数据库名.表名

# chapter5 排序检索数据
#### 排序数据
关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有意义
为了明确地排序用SELECT语句检索出的数据，可使用 ORDER BY子句。ORDER BY 子句取一个或多个列的名字，据此对输出进行排序。                                   
命令：SELECT  列名  FROM  表名  ORDER  BY  列
####  按多个列排序
按多个列排序，只需要指定列名，列名之间用逗号分开即可：
SELECT 列名1，列名2，列名3 FROM 表名  ORDER BY 列名1，列名2；                              
检索出数据后，先按列名1排序，再按列名2排序。要注意：只有在有相同的列名1时，才对列名2进行排序
#### 指定排序方向
数据排序不限于升序（A到Z）。这只是默认的排序顺序，还可以使用ORDER BY子句以降序（Z到A）顺序排序，为了进行降序排序必须z指定DESC 关键字。                          
命令行：SELECT 列名1，列名2，列名3 FROM 表名  ORDER BY 列名1 DESC；

DESC关键字只能应用到直接位于其前面的列名，如果想在多个列之间进行排序，必须对每个列指定DESC关键字  
命令：SELECT 列名1，列名2，列名3 FROM 表名 ORDER BY 列名1 DESC，列名2 DESC；

与DESC相反的关键字是ASC，不过默认的都是升序  

ORDER  BY子句的位置
在给出ORDER BY子句时，应该保证它位于FROM子句之后，如果使用LIMIT，它必须位于ORDER BY之后。使用的次序不对将产生错误消息。   

# 过滤数据
#### 使用WHERE子句
在SELECT语句中，数据根据WHERE子句中指定的搜索条件进行过滤，WHERE子句在表名（FROM子句）之后给出：              
命令：SELECT 列名1，列名2 FROM 表名 WHERE 列名=值                
where子句的位置，在同时使用ORDER BY和WHERE子句时，应该让ORDER BY位于WHERE之后            
####WHERE子句操作符
（1）= 等于 （2）<> 不等于（3）!=  不等于（4） < 小于 （5）<= 小于等于  （6）> 大于
（7） >= 大于等于  （8） BETWEEN  在指定的两个值之间              
在编写sql语句时何时使用引号：                
如果将值与串类型的列进行比较，则需要限定限定引号。用来与数值列进行比较的值不用引号
##### 范围值查找
命令： SELECT 列名1，列名2 FROM 表名 WHERE BETWEEN 值1  AND 值2；                    
在使用BETWEEN时，必须指定两个值--所需范围的低端值和高端值。这两个值必须用AND关键字分割。BETWEEN匹配范围中所有的值，包括指定的开始值和结束值。
#####空值检查
NULL 无值，它与字段包含0，空字符串或仅仅包含空格不同。
SELECT语句有一个特殊的WHERE子句，可用来检查具有NULL值的列，这个WHERE语句就是  IS NULL          
命令：SELECT  列名1 FROM  表名 WHERE 列名2 IS NULL；
此条语句会查询出列2为NULL值的列名1的字段


NULL与不匹配

在通过过滤选择出不具有特定值的行时，你可能希望返回具有NULL值的行。但是，不行。因为未知具有特殊含义，数据库不知道他们是否匹配，所以在匹配过滤或不匹配过滤时不返回它们。

因此在过滤数据时，一定要验证返回数据中确实给出了被过滤列具有NULL的行

# 数据过滤
## 组合WHERE子句
Mysql允许给出多个WHERE子句。这些子句可以以两种方式使用：以AND子句的方式或OR子句的方式
###  AND操作符
为了通过不止一个列进行过滤，可使用AND操作符给WHERE子句附条件
命令：SELECT 列名1，列名2，列名3  FROM  表名  WHERE  列名1=值  AND 列名2<=z值;
### OR操作符
OR操作符与AND操作符不同，它指示MYSQL检索匹配任一条件的行
命令：
命令：SELECT 列名1，列名2，列名3  FROM  表名  WHERE  列名1=值  OR 列名2<=z值;
###  计算次序
WHERE可以包含任意数量的AND和OR操作符，允许两者结合以进行复杂和高级的过滤
AND在计算次序中的优先级比OR高，对于这种情况有时可以使用括号进行分组
命令：SELECT 列名1,列名2,列名3 FROM 表 WHERE （列名1=值 OR 列名2<=值）AND 列名3=值
## IN操作符
圆括号在WHERE子句中海油另外一种用法。IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。IN取的合法值是由逗号分隔的清单，全部括在圆括号中。
例如：IN操作符后面跟有  逗号  分隔的清单，整个清单必须在圆括号中
命令：SELECT 列名1，列名2，列名3 FROM 表 WHERE 字段 IN（值1，值2）ORDER  BY  列名；
使用IN操作符的优点：
（1）	在使用长的合法选项清单时，IN操作符的语法更清楚且更直观
（2）	在使用IN时，计算的次序更容易管理
（3）	IN操作符一般比OR操作符清单执行更快
（4）	IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建立WHERE子句。
##NOT操作符
WHERE子句中的NOT操作符有且只有一个功能，那就是否定它之后所跟的任何条件
例：SELECT 列名1，列名2，列名3  FROM 表名 WHERE  字段1 NOT IN（值1，值2）ORDER BY 字段2；

MYSQL支持使用NOT对IN，BETWEEN和EXISTS子句取反，这与多数其他的DBMS允许使用NOT对各种条件取反有很大的差别

#用通配符进行过滤
##  LIKE操作符
通配符：用来匹配值的一部分的特殊字符
### 百分号（%）通配符
在搜索中，%表示任何字符出现任意次数
例如：SELECT 列名1，列名2，列名3 FROM 表名 WHERE 列名1  LIKE ‘jet%’;
在执行这条子句时，将检索任意以jet起头的词。%告诉MYSQL接收jet之后的任意字符，不管它有多少字符。
通配符可在搜索模式中任意位置使用，并且可以使用多个通配符。
SELECT 列名1，列名2 FROM  表名  WHERE  字段  LIKE ‘%jet%’;匹配包含’jet’的文本。
SELECT 列名1，列名2 FROM  表名  WHERE  字段  LIKE  ‘s%e’;匹配以s开头和以e结尾的文本。
尾空格可能会干扰通配符的匹配。注意 ‘%’不能匹配 NULL值
### 下划线（-）通配符
另外一个有用的通配符是下划线（-）。下划线的用途与%一样，单下划线只匹配单个字符而不是多个字符。
与%能匹配0个字符不一样，（-）总是匹配一个字符，不能多也不能少。

###通配符的几个技巧
（1）	不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。(搜索速度慢)
（2）	在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的额开始出。把通配符置于搜索模式的开始处，搜索起来是最慢的。
（3）	仔细注意通配符的位置。如果放错地方，可能不会反悔想要的数据。

# 用正则表达式进行搜索
##基本的字符匹配
例：SELECT 列名1 FROM 表 WHERE  列名1 REGEXP ‘1000’ORDER  BY 列名1；

LIKE与REGEXP之间的差别：
SELECT 列名1 FROM 表 WHERE  列名1  LIKE ‘1000’ORDER  BY 列名1；
SELECT 列名1 FROM 表 WHERE  列名1  REGEXP ‘1000’ORDER  BY 列名1；
执行上面的两条语句，第一条不会返回数据，而第二条语句返回一行

LIKE匹配整个列。如果被匹配的文本在列值中出现，LIKE将不会找到它，相应的行也不被返回（除非使用通配符）。而REGEXP在列值内进行匹配，如果被匹配的文本在列值中出现，REGEXP将会找到它，相应的行将被返回

### 进行OR匹配
为搜索两个串之一，使用 |
例如：SELECT 列名1，列名2 FROM 表名 WHERE 列名 REGEXP  ‘1000|2000’ ORDER BY字段;

### 匹配几个字符之一
匹配任何单一字符。如果想要匹配匹配特定字符，可通过指定一组用[]括起来的字符来完成例如：
SELECT 列名1 FROM WHERE 字段1 REGEXP ‘[123]TOM‘;
[123]意识是匹配1或2或3
集合也可以被否定，即他们将匹配除指定字符外的任何东西。
为一个否定字符集，在集合的开始处放置一个^即可。因此，尽管[123]匹配字符1,2或3；但[^123]却匹配出这些字符外的任何东西

###范围匹配
集合可用来定义要匹配的一个或多个字符  例:[123456789]
为了简化这种类型的集合，可以使用‘-‘   [1-9]
范围不限于数字  字符也是可以的  [a-z]

###匹配特殊字符
‘.‘匹配任意字符
为了匹配特殊字符，必须用\\为前导。\\-表示查找-，\\.表示查找’ . ’;

### 匹配多个实例
重复单元符：
（1）	 0个或多个匹配 （2） +  1个或多个匹配 （3）？ 0个或1个匹配
（4）{n}  指定数目的匹配 （5）{n,} 不少于指定数目的匹配  (6)（n,m）匹配数目的范围（m不超过255）
###  定位符
为了匹配特定位置的文本需要使用定位符
（1）^  文本的开始（2） $ 文本的结尾（3）[[:<:]] 词的开始（4）[[:>:]] 词的结尾

# chapter10  创建计算字段
##字段拼接
将值连接到一起构成单个值，可以利用CONCAT（）函数将两个列拼接起来；                
多数的DBMS使用+或||来实现拼接，Mysql则使用Concat（）函数来实现。
示例：SELECT CONCAT（列名1,'(',列名2，‘)’) From 表名 ORDER BY 列名；            
CONCAT（）拼接串，即把多个串连接起来形成一个较长的串，需要一个或多个指定的串，各个串之间用逗号分隔

如果要删除数据右侧的空格，则可以使用RTRIM（）函数来完成
例如：                  
命令：SELECT CONCAT（RTrim（列名1），'(',RTrim(列名2)，')'）FROM 表名;      
RTRIM（字段）去掉值右边所有的空格。去除右边的空格用Ltrim();
## 使用别名’
SQl语句中的别名用AS关键字赋予.      
命令：SELECT CONCAT（RTrim（列名1），‘（’，RTRIM（列名2），')'） AS 别名 FROM 表名；
## 执行算数计算
MYSQL算术操作表：（1）+  加（2） -  减号（3）* 乘（4）/      
命令：SELECT  列名1，列名2，列名1*列名2 AS 别名 FROM 表名 WHERE 列名1=值；         
另外SELECT Now（）利用Now（）函数返回当前日期和时间。

# 使用数据处理函数
UPPER（字段）函数将文本转换为大写              
常用的文本处理函数：            
（1）left()  返回串左边的字符  （2）Length（）  返回串的长度
（3）Locate（）找出串的一个子串 （4）Lower（）  将串转换为小写
（5）Ltrim()  去掉串左边的空格  （6）Right（）  返回串右边的字符
（7）Rtrim()  去掉串右边的空格  （8）Soundex()  返回串的SOUDEX值
（9）SubString() 返回子串的字符  （10）Upper()  将串转换为大写

其中Soundex()考虑了类的发音字符和音节，使得能对串进行发音比较而不是字符字母比较               
例如：SELECT 列名1，列名2 FROM 表名 WHERE Soundex（列名1）=Soundex（'Y Lie'）;
## 日期和时间处理函数
日期和时间采用响应的数据类型和特殊的格式存储，以便能快速和有效地排序和过滤
不过是插入或更新表值还是用WHERE子句进行过滤，日期必须为格式yyyy-mm-dd；应该总是使用4位数字的年份。

DATE(order_date)函数只是MySQL仅提取列的日期部分，当然如果只想要时间值可以使用TIME（）函数

如果先要单独的提取某年某月的数据则可以使用以下指令：               
SELECT  列名1，列名2 FROM 表名  WHERE DATE（日期字段） BETWEEN '2005-09-01' AND '2005-09-30';          
另外还可以使用：              
SELECT 列名1，列名2 FROM 表名  WHERE YEAR（日期字段）=2005 AND MONTH（日期字段）=9；
##  数值处理函数
常用的数字处理函数：
函数:(1) abs()   返回一个数的绝对值   （2）Cos（）  返回一个角度的余弦 （3）Exp（）  返回一个数的指数值      
（4）Mod（）  返回除操作的余数（5）Pi  返回圆周率  （6）Rand（）  返回一个随机数                              
（7）Sin（）  返回一个角度的正弦  （8）Sqrt()   返回一个数的平方根  （9） Tan（）  返回一个角度的正切。

# 汇总数据
##  聚集函数
（1）AVG（）   返回某列的平均值  （2）COUNT（）  返回某列的行数    
（3）MAX（）   返回某列的最大值  （4）MIN（）  返回某列的最小值   
（5）SUM（）   返回某列值之和
### AVG（）
命令：SELECT AVG（列1） AS 别名  FROM 表名； 返回所有列的平均值
命令：SELECT AVG（字段） AS  别名  FROM 表名 WHERE  字段=值；用来确定特定列或行的平均值；         
AVG（）函数忽略列值为NULL的行，如果要获得多个列的平均值，必须使用多个AVG（）函数
### COUNT（）
COUNT（）函数有两种使用方式               
（1）COUNT（*）对表中的行的数目进行计数，不管列表中包含的是空值（NULL）还是非空置                 
（2）COUNT（column） 对特定列中具有值的行进行计数，忽略NULL值  

另外MIN（），MAX（），SUM（）函数忽略列值为NULL的行。

## 聚集不同的值
（1）对所有的行执行计算，指定ALL参数或不给参数（因为ALL是默认行为）      
（2）只包含不同的值，指定DISTINCT

ALL为默认  ALL参数不需要指定，因为它是默认行为。如果不指定DISTINCT，则假定为ALL；

命令：SELECT  AVG（DISNTINCT 列名） AS 别名 FROM 表名；

如果指定列名，则DISTINCT只用用于COUNT（）。DISCINCT不能用于COUNT（*）；  DISTINCT必须使用列名，不能用于计算或表达式

## 组合聚集函数
组合聚集函数用逗号隔开；
命令：SELECT COUNT（*） AS 别名，MAX（列名） AS 别名 ，MIN（列名） AS 别名  FROM  表名；

# 分组数据
 ##创建分组
 分组是在SELECT语句的GROUP BY子句中建立的           
 命令：SELECT 列名1  COUNT（*） AS 别名  FROM  表名 GROUP BY 列名1；        
 GROUP BY  会按照  列名1 的值进行分组  ，相同的值分为一组                        
 使用GROUP BY 不必指定要计算和估值的每个分组。系统会自动完成。GROUP BY子句指示MYSQL分组数据，然后对每个分组而不是整个结果集进行聚集。

 再具体使用GROUP BY子句前，需要注意的：

      （1）GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。
      （2）如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算
      （3）GROUP BY子句中列出的每个列都必须是检索列或有效的表达式。如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式。
      （4）除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子句中给出
      （5） 如果分组中给出具有NULL值，则NULL将作为一个分组返回。如果列中有多个NULL值，他们讲分为一组。
      （6）GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前

## 过滤分组
MYSQL过滤分组不是使用WHERE，而是使用HAVING来代替。                 
HAVING非常类似于WHERE，目前为止所学过的所有类型的WHERE子句都可以用HAVING来代替。唯一的差别是WHERE是过滤行，而HAVING过滤分组。
命令：
SELECT  列名1，COUNT（*） AS 别名  FROM 表名 GROUP BY 列名1 HAVING COUNT（*）>=2;        

WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤，WHERE排除的行不包括在分组中。

命令：SELECT 列名1，COUNT（*） AS 别名 FROM 表名 WHERE 列名2 >= 值 GROUP BY 列名1 HAVING COUNT（*）>=值。

##  分组和排序
GROUP BY和ORDER BY经常完成相同的工作，但他们是非常不同的。              
ORDER BY：     
排序产生的输出；任意列都可以使用（甚至非选择的列也可以使用）

GROUP BY：
分组行。但输出可能不是分组的顺序；只可能使用选择列或则表达式，而且必须使用每个选择列表达式；如果与聚集函数一起使用列，则必须使用

命令：SELECT 列名1，SUM（列名2*列名3） AS 别名 GROUP BY 列名1 HAVING SUM（列名2*列名3）>=值 ORDER BY 别名；
## SELECT子句顺序
在SELECT语句使用时必须遵循的次序：          
######（1）SELECT    要返回的列或表达式          必须使用      
######（2）FROM    从中检索数据的表      仅在从表选择数据时使用                       
######（3） WHERE  行级过滤    不一定要使用                                   
######（4）GROUP BY  分组说明    仅在按组计算聚集时使用                  
######（5）HAVING  组级过滤     不一定要使用                    
######（6）ORDER BY  输出顺序排序   不一定要使用              
######（7）LIMIT  要检索的行数    不一定要使用

#使用子查询
嵌套在其他查询语句中的查询                         
可以把一条SELECT语句返回的结果用于另一条SELECT语句的WHERE查询                        
在SELECT语句时，子查询总是从内向外处理                                   

命令：SELECT  列名1，列名2 FROM 表名 WHERE 列名3 IN（SELECT 列名3 FROM 表名）；

对于嵌套的子查询的数目没有限制，不过在实际使用时由于性能的限制，不能嵌套太多的子查询。

     列必须匹配   在WHERE子句使用子查询，应该保证SELECT语句具有与WHERE子句相同数目的列。通常，子查询返货单个列并且与单个列匹配，但如果需要也可以使用多个列。
## 作为计算字段使用子查询

命令：SELECT 列名1，列名2 （SELECT COUNT（*） FROM 表名1 WHERE 表名1.列名3=表名2.列名3） AS别名  FROM 表名2 ORDER BY 字段1；

相关子查询   在涉及外部查询的子查询时，任何时候只要列名可能又多义性，就不许使用语法（表名和列名由一个句点分隔）

# 联结表
######外键
外键为某个表的一列，它包含另一个表的主键值                  
例如：                         
命令：SELECT vend_name,prod_name,prod_price FROM vendors,products WHERE vendors.vend_id=products.vend_id ORDER BY vend_name,prod_name;

vendors和products是两个表
## WHERE子句的重要性
在一条SELECT语句中联结几个表时，相应的关系是运行中构造的，在数据库表的定义中不存在能指示Mysql如何如何对表进行联结的东西，必须要自己做这件事情

    笛卡尔积：由于没有联结条件的表关系返回的结果为笛卡尔积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

##  内部联结

  目前为止所用的联结称为等值联结，它基于两个表之间的相等测试。这种联结也称为内部联结。

命令：SELECT 列名1，列名2，列名3 FROM  表名1  INNER  JOIN  表名2  ON  表名1.列名4=表名2.列名5；
联结条件用特定的ON子句而不是WHERE子句给出。传递给ON的实际条件与传递给WHERE的相同。

### 联结多个表

    一条SELECT语句中可以联结的表的数目没有限制。创建联结的基本规则也相同。
    命令：SELECT 列名1，列名2，列名3 FROM 表1，表2，表3 WHERE 表1.列1=表2.列2 AND 表2.列1=表3.列1 AND  列名=值；

#  创建高级联结
SQL允许给表名其别名的理由：
    （1）缩短SQL语句
    （2）允许在单挑SELECT语句中多次使用相同的表

##  使用不同类型的联结

#### 自联结
使用联结来查询
      SELECT p1.列名1，p2.列名2 FROM 表名1 AS  别名1， 表名1 AS 别名2  WHERE p1.列名=p2.列名 AND p2.列名2=值

#### 自然联结
无论何时对表进行联结，应该至少有一个列出现在不止一个表中，标准的联结返回所有数据，甚至相同的列出现多次。自然联结排除多次出现，使每个列只返回一次。

      自然联结是这样一种联结，其中你只能选择那些唯一的列。这一般是通过对表使用通配符（SELECT *），对所有其他表的列使用明确的子集来完成的。
SELECT c.* ,o.列名 FROM 表名1 AS c，表名2 AS o WHERE c.列名=o.列名 AND 字段=值；         
在上面这条语句中，通配符只对第一个表使用。所有其他列明确列出，所以没有重复的列被检索出来

#### 外部联结

例如：
   SELECT customers.cust_id,orders.order_num FROM customers LEFT OUTER JOIN orders ON customers.cust_id=orders.cust_id;

   键值OUTER JOIN来指定联结的类型。但是，与内部联结关联两个表中的行不同的是，外部联结还包括没有关联的行。在使用OUTER JOIN 语法时，必须使用RIGHT 或LEFT关键字指定包括其所有行的表。上面的例子使用LEFT OUTER JOIN 从FROM子句的左边表中选择所有行。为了从右边的表中选择所有行，应该使用RIGHT OUTER JOIN。
####使用带聚集函数的联结

      命令：SELECT  customers.cust_name customers.cust_id COUNT(orders.order_num) AS num_ord FROM customers INNER JOIN orders ON customers_cust_id=orders.cust_id;
此SELECT语句使用INNER JOIN将customers和orders表互相关联。函数调用 COUNT(orders.order_num)对每个客户的订单计数，将他作为num_ord返回。

#组合查询
###组合查询
MYSQL允许执行多个查询（多条SELECT语句），并将结果作为单个查询结果集返回。这些组合查询通常称为并（union）或符合查询

有两种情况需要使用组合查询
      （1）在单个查询中从不同的表返回类似结构的数据；
      （2）对单个表执行多个查询，按单个查询返回数据
### 创建组合查询
    利用UNION操作符来组合数条SQL查询
####使用UNION
UNION的使用很简单所做的只是给出每条SELECT语句，在各条语句之间方上关键字UNION

UNION中的每个查询必须相同的列，表达式或聚集函数

    UNION从查询结果中会自动出去重复的行，这是UNION的默认行为，但是如果需要，可以改变它，想要返回所有匹配的行，可使用UNION ALL

SELECT语句的输出用ORDER BY子句排序。在用UNION组合查询是，只能使用一条ORDER BY子句，他必须出现在最后一条SELECT语句之后。

#全文本搜索
##使用全文本搜索
###启用全文本搜索支持
一般在创建表时启用全文本搜索。CREATE  TABLE 语句接受FULLTEXT子句，它给出被索引列的一个逗号分隔列表：
示例：
CREATE TABLE 表名{
Note_id  int  NOT NULL AUTO_INCREMENT,
Prod_id  char(10)  NOT NULL,
Note_date  datetime  NOT null,
Noet_text  text  null,
PRIMARY KEY(Note_id),
FULLTEXT(Note_text)
}
这些列中有一个名为note_text的列，为了进行全文本搜索，MySQL根据子句FULLTEXT（note_text）的指示对它进行索引。这里的FULLTEXT索引单个列，如果需要你也可以指定多个列。

在定义之后，MySQL自动维护该索引。在增加，更新或删除行时，索引随之自动更新。

## 进行全文搜索
在索引之后，使用两个函数Match()和Against()执行全文本搜索，其中Match()指定被搜索的列，Against()指定要使用的搜索表达式。

命令：SELECT note_text FROM 表名 WHERE  Match（note_text）Against(‘rabbit’);

Match(note_text)指示MySQL针对指定的列进行搜索，Against（’rabbit’）指定词rabbit作为搜索文本

使用完整的Match()说明  传递给Match()的值必须与FULLTEXT（）定义中的相同，如果指定多个列，则必须列出它们（而且次序正确）。

搜索不区分大小写

全文搜索一个重要的部分就是对结果排序。具有较高等级的行先行返回

##使用查询扩展
（1）	首先，进行一个基本的全文本搜索，找出与搜索条件匹配的所有行。

（2）	其次，MYSQL检查这些匹配行并选择所有有用的词

（3）	再其次，MYSQL再次进行全文本搜索，这次不仅使用原来的条件，而且还使用所有有用的词

使用扩展查询：
SELECT  列名 FROM 表名 WHERE Match（列名） Against（’要搜索的文本’ WITH QUERY EXPANSION）；
###布尔文本搜索

1.	要匹配的词

2.	要排斥的词（如果某行包含这个词，则不返回该行，即使它包含其他指定的词也是如此）

3.	排列提示（指定某些词比其他词更重要，更重要的词等级更高）；

4.	表达式分组

5.	另外一些内容

即使没有FULLTEXT索引也可以使用

例如：SELECT 列名 FROM 表名 WHERE Match(列名) Against（’heavy –rope*’ IN BOOLEAN MODE）;返回包含 heavy  但排除 rope的行

#插入数据
INSERT是用来插入行到数据库表的，插入可以你几种方式使用：
（1）	插入完整的行；
（2）	插入行的一部分
（3）	插入多行
（4）	插入某些查询的结果

##插入完整的行
INSERT  表名（字段1，字段2，字段3）VALUES（值1，值2，值3）；
对每个列必须提供一个值

省略列：
满足一下条件：
（1）	该列定义为允许NULl值
（2）	在表定义中给出默认值，这表示如果不给出值，将使用默认值

插入多个行
命令：INSERT INTO 表名（字段1，字段2，字段3）VALUES（值1，值2，值3），（值1，值2，值3）；

## 插入检索出的数据
INSERT INTO 表名（字段1，字段2，字段3）SELECT  字段1，字段2，字段3  FROM 表名；

#更新和删除数据
为了更新（修改）表中的数据，可使用UPDATE语句。可采用两种方式使用UPDATE：
（1）	更新表中特定行；
（2）	更新表中所有行。
命令:UPDATE  表名 SET 字段1=值1，字段2=值2 WHERE 字段3=值3；

IGNORE关键字  如果用UPDATE语句更新多行，并且在更新这些行中的一行或多行时出现一盒错误，整个UPDATE操作被取消（错误放生前更新的所有行被恢复到它们原来的值）。即使发生错误，也要继续更新，可使用IGNORE关键字：UPDATE  IGNORE  表名 ……

## 删除数据
（1）	从表中删除特定的行
（2）	从表中删除所有行
命令：DELETE FROM 表名 WHERE 字段=值

#创建和操纵表
##表创建基础
为利用CREATE TABLE 创建表：
（1）	新表的名字，在关键字CREATE TABLE之后给出；
（2）	表列的名字和定义你，用逗号分隔。
实际的表定义括在圆括号之中，各列之间用逗号分隔，表的主键可以在创建表时用PRIMARY KEY（字段）关键字指定。整条语句由圆括号后的分号结束。

如果你仅想在一个表不存在时创建它，应该在表名后给出IF  NOT  EXISTS。

## 使用NULL值
NULL值就是没有值或缺值。允许NNU’LL值的列也允许在插入行时不给出该列的值。不允许NULL值的列不接受该列没有值的行，换句话说，在插入和更新行时该列必须有值。

每个表列或者是NULL列，或则是NOT NULl列，这种状态在创建时由表的定义规定。
CREATE TABLE orders（
  Num INT  NOT NULL AUTO_INCREMENT,          
  Date  datetime  NOT NULL,                  
  Id  int NOT NULL,              
  PRIMARY KEY (id)
）  
三个列都需要值，因此三个列都含有关键字NOT NULL
不指定NOT NULL时，默认是NULL值
## 主键
主键值必须唯一。表中的每个行必须具有唯一的主键值。如果主键使用单个列，则它的值必须唯一。如果使用多个列，则这些列的组合值必须唯一

主键只能使用不允许NULL值的列，允许NULL值的列不能作为唯一标识

###AUTO_INCREMENT
每当增加一行时自动增长

###指定默认值
如果在插入时没有给出值，MySQL允许指定此时使用的默认值。默认值用CREATE TABLE语句的列定义中的GEFAULT关键字指定。
例：quantity  int  NOT  NULL  DEAFULT  1,

##更新表
为更新表定义，可使用ALTER TABLE语句。但是，理想状态下，当表中存储数据以后，该表就不应该再被更新

ALTER  TABLE  表名
ADD  字段   类型,    //添加一个列
DROP  COLUMN   列名，  //删除一个列

ALTER TABLE的一种常见用途是定义外键

ALTER  TABLE  表名  
ADD  CONSTRAINT   fk_order_orders   FOREING  KEY (order_num)  REFERENCES  orders(prod_id);

##删除表
DROP  TABLE  表名；

####重命名表
RENAME  TABLE 原表名  TO  新表名


#chapter 22  使用视图
视图是虚拟的表，与包含数据的表不一样，视图只包含使用时动态检索数据的查询

## 为什么使用视图
（1） 重用SQL语句              
（2）简化复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节                   
（3）使用表的组成部分而不是整个表                    
（4）保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限                   
（5）更改数据格式和表示，视图可以返回与底层表的表示和格式不同的数据                

性能问题：因为视图不包含数据，所以每次使用视图时都必须处理查询执行时所需的任一个检索

## 使用视图
视图创建：                              
（1）视图用CREATE VIEW 语句来创建。                         
（2）使用SHOW CREATE VIEW 视图名；来查看创建视图的语句；                              
（3）使用DROP删除视图，其语法为DROP VIEW 视图名；。                      
（4）更新视图时可以先用DROP再用CREATE，也可以直接用CREATE OR REPLACE VIEW。如果要更新视图不存在，则会创建，如果存在，则会替换。        

## 利用视图简化复杂的联结
视图最常见的应用之一是隐藏复杂的SQL，这通常会涉及联结。                              
命令：                             
CREATE VIEW 视图名 AS SELECT。。。。。。；
AS后面跟着的是具体的SQL语句。   


      WHERE子句与WHERE子句   如果从视图中检索数据时使用了一条WHERE子句，则两组子句（一组在视图中，另一组是传递给视图的）将自动组合。


      一般，应该讲视图用于检索而不用于更新

#视图与存储过程
存储过程                     
      存储过程可以使得对数据库的管理、以及显示关于数据库及其用户信息的工作容易得多。存储过程是   SQL   语句和可选控制流语句的预编译集合，以一个名称存储并作为一个单元处理。存储过程存储在数据库内，可由应用程序通过一个调用执行，而且允许用户声明变量、有条件执行以及其它强大的编程功能。  

                存储过程可包含程序流、逻辑以及对数据库的查询。它们可以接受参数、输出参数、返回单个或多个结果集以返回值。
                可以出于任何使用   SQL   语句的目的来使用存储过程
 
            
视图                                                                                         
  视图是一个虚拟表，其内容由查询定义。同真实的表一样，视图包含一系列带有名称的列和行数据。但是，视图并不在数据库中以存储数据值集形式存在。行和列数据来自由定义视图的查询所引用的表，并且在引用视图时动态生成。

# 存储过程

## 执行存储过程
MySQL称存储过程的执行为调用，因此MYSQL执行存储过程的语句为CALL。CALL 接受存储过程的名字以及传递给它的任意参数。

      命令：CALL  productpricing（@pricelow，@pricehigh，@priceaverage），其中执行名为productpricing的存储过程，他计算并返回产品的最低，最高和平均价格。
## 创建存储过程
命令： CREATE  PROCEDURE  存储过程名（）            
BEGIN              
    SELECT。。。。。（mysql语句）；                
END；

利用CREATE PROCEDURE   存储过程名（）来创建，如果存储过程有参数，它们将在（）中列举出来，此过程若没有参数，但后跟的（）仍需要。BEGIN和END语句用来限定存储过程的存储体。

      存储过程实际上是一种函数，所以存储过程名后需要有（）符号
## 删除存储过程
存储过程被创建以后，被保存在服务其上以供使用，直至被删除。
命令： DROP  PROCEDURE  存储过程名；

当过程存在 想要删除时（不存在不产生错误）：DROP  PROCEDURE  IF   EXISTS。

##  使用参数
 变量：内存中一个特定的位置，用来临时存储数据

 命令： CREATE  PROCEDURE  productpricing（

      OUT  p1  DECIMAL（8,2），                     
      OUT  ph  DECIMAL（8,2），                   
      OUT  pa  DECIMAL（8,2）                  
 ）                 
 BEGIN  

      SELECT  MIN（prod_price）                           
      INTO  p1                         
      FROM  products;                     
      SELECT  MAX（prod_price）                       
      INTO  ph                     
      FROM  products;              
      SELECT  priceaverage（prod_price）                  
      INTO  pa                       
      FROM  products;                     
END;

每个参数必须具有指定的类型，这里使用十进制。关键字OUT用来指出响应的参数用来从存储过程中传出一个值。

MYSQL支持IN（传递给存储过程），OUT（从存储过程传出）和INOUT（对存储过程传入和传出）类型的参数。                        
这里用SELECT语句，用来检索，然后保存到相应的变量（通过指定INTO关键字）

注意：不能一个参数返回多个行和列。这就是为甚么前面的例子要使用三个参数（3个SELECT语句）的原因。

调用过程：                   
命令： CALL productpricing（@pricelow，@pricehigh，@priceaverage）;                   
由于此存储过程要求三个参数，因此必须正好传递3个参数。

变量名必须都以@开头。

为了显示数据则用一下命令：SELECT  @变量名；

##  建立智能存储过程

--Name：ordertotal                  
--parameter：number=  order number                     
--           taxable= 0 if  not taxable ,1 if taxable                        
--           ototal  = order total  variable                   

CREATE  PROCEDURE  ordertotal(

    IN  onumber  INT,
    IN taxable  BOOLEAN ,
    OUT ototal DECIMAL（8,2）

  )COMMENT ‘Obtain order total’                              
  BEGIN

        --DECLARE variable for total
        DECLARE total DECIMAL（8,2）
        --DECLARE variable for total
        DECLARE taxrate  INT  DEFAULT  6；//声明变量

        SELECT 。。。。。FROM  表名 INTO total；

        IF  taxable  THEN  
              SELECT。。。。。。。。。。。。；
        END IF；
        SELECT  total  INTO  otatal；
   END



   DECLEAR 声明数据

  COMMENT关键字：给出次关键字，将咋啊SHOW PROCEDURE  STATUS 的结果中显示

  SHOW PROCEDURE  STATUS：将列出所有存储过程                
  SHOW PROCEDURE  STATUS  LIKE  存储过程名；列出指定的存储过程列表。

##  检查创建过程

    SHOW  CREATE PROCEDURE  存储过程名；显示存储过程的创建语句。
#使用游标
    游标是一个存储在MySQL服务器上的数据库查询，它不是一条SELECT语句，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据

    MYSQL游标只能用于存储过程

## 使用游标的几个明确步骤
    （1）	在能够使用游标前，必须声明它。在这个过程实际上没有检索数据，它只是定义要使用的SELECT语句。
    （2）	一旦声明后，必须打开游标以供使用。这个过程用前面定义的SELECT语句把数据实际检索出来
    （3）	对于天佑数据的游标，根据需要取出各行
    （4）	在结束游标使用时，必须关闭游标。
    在声明游标后，可根据需要频繁地打开和关闭游标。在游标打开后，可根据需要频繁地执行取操作

###创建游标
    游标用DECLARE语句创建。DECLARE命名游标，并定义相应的SELECT语句，根据需要带WHERE和其他子句。

    例如：
    CREATE PROCEDURE  processorders()
    BEGIN
            DECLARE  ordernumbers  CURSOR
            FOR
            SELECT  order_num  FROM  orders;
    END;

    DECLARE 语句用来定义和命名游标，这里为ordernumbers。存储过程处理完成后，游标就消失了（因为它局限于存储过程）

###  打开和关闭游标
    OPEN  ordernumbers；在处理OPEN语句时执行查询

    CLOSE  ordernumbers；CLOSE释放游标使用的所有内部内存和资源

    在一个游标关闭后，如果没有重新打开，则不能使用它。但是使用声明过的游标不需要再次声明，用OPEN语句打开它就可以了

### 使用游标数据
    在一个游标打开后，可以使用FETCH语句分别访问它的每一行。
    FETCH指定检索什么数据（所需的列），检索出来的数据存储在什么地方。


    CREATE PROCEDURE  processorders()
    BEGIN
            DECLARE o INT；//声明数据
            //声明游标
            DECLARE  ordernumbers  CURSOR
            FOR
            SELECT  order_num  FROM  orders;
            //打开游标
            OPEN ordernumbers；
            //检索数据
            FETCH  ordersnumbers  INTO  o;
            //关闭游标
            CLOSE  ordernumbers；
    END;

    FETCH用来检索当前行的游标order_num列（将自动从第一行开始）到一个名为o的局部声明的变量中。对检索出的数据不做任何处理

    循环：
             REPEAT
                    要执行的SQL语句
             UNITL  变量名  END  REPEAT；//反复执行直到  变量为真时  停止

#使用触发器
    某条MySQL语句在事件发生时自动执行。这就是触发器。

    触发器是MySQL响应以下任意语句而自动执行的一条MySQL语句（或位于BEGIN和END语句之间的一组语句）：
    （1）	DELETE
    （2）	INSERT
    （3）	UPDATE
##  创建触发器
    创建触发器时需要给出的4条信息：
    （1）唯一的触发器名
    （2）触发器关联表
    （3）触发器应该响应的活动（DELETE，INSERT或UPDATE）
    （4）触发器何时执行
  注意：                              
    保持每个数据库的触发器名唯一                   
  触发器用CREATE  TRIGGER语句创建：                 
  命令：                             
  CREATE  TRIGGER   触发器名1  AFTER  INSERT  ON  表名1 FOR   EACH  ROW  SELECT ‘Product added’；             
  CREATE TRIGGER用来创建名为  触发器1的新触发器，触发器可在一个操作发生之前或之后执行，这里给出了AFTER  INSERT，所以触发器将在INSERT语句执行后执行。而FOR EACH  ROW则表示在每个插入行执行‘Product added’

  只有表才支持触发器。单一个触发器不能与多个事件相关联。

##  删除触发器
      DROP TRIGGER  触发器名；
##使用触发器
###INSERT触发器
      (1)在INSERT触发器代码内，可引用一个名NEW的虚拟表，访问被插入的行
      (2)在BEFORE  INSERT触发器中，NEW中的值可以被更新
      (3)对于AUTO_INCREMENT列，NEW在INSERT执行之前包含0，，在INSERT执行之后包含新的自动生成的值。

      CREATE TRIGGER  neworder  AFTER INSERT ON orders FOR EACH ROW  SELECT  NEW.order_num;
      在插入一个新订单到orders表时，MySQL生成一个新订单号并保存到order_num.触发器从NEW.order_num取得这个值并返回它。此触发器必须按照AFTER INSERT执行，因为在BEFORE INSERT语句执行之前，新order_num还没有生成

      测试触发器：
      INSERT INTO orders（order_date,cust_id） VALUES(NEW(),10001);
      order_num由MySQL自动生成，而且被返回
### DELETE触发器
      （1）在DELETE触发器内，可以引用一个名为OLD的虚拟表，访问被删除的行
      （2）OLD中的值全都是只读的，不可更新

      例如：CREATE  TRIGGER  触发器名1 BEFORE DELETE ON orders
            FOR  EACH  ROW
            BEGIN
                 INSERT  INTO archive_orders（order_num,order_date,cust_id）
                 VALUES(OLD.order_num,OLD_order_date,OLD.cust_id);
            END;
    使用BEGIN   END块：触发器能容纳多条SQL语句
###UPDATE  触发器
      （1）在UPDATE触发器代码中，可以引用一个名为OLD的虚拟表访问以前（UPDATE更新以前）的值，引用一个名为NEW的虚拟表访问更新的值
      （2）在BEFORE UPDATE触发器中，NEW中的值可能也被更新。
      （3）OLD中的值全都是只读，不能更新

#管理事务处理
事务处理可以用来维护数据库的完整性，它保证成批的MySQL操作要么完全执行，要么不执行

### 控制事务处理
      管理事务处理的关键在于将SQL语句组分解为逻辑块，并明确规定数据何时应该回退。何时不应该回退。

      MYSQL使用下面的语句来标识事务的开始：
      START  TRANSACTION

      MySQL的ROLLBACK命令用来回退SQL语句：
      SELECT  * FROM 表名；
      START TRANSACTION；
      DELETE  FROM 表名；
      SELECT * FROM  表名；
      ROLLBACK；
      ROLLBACK语句回退START  TRANSACTION之后的所有语句。
###使用COMMIT
      一般的MySQL语句都是直接针对数据库表执行和编写的，这就是所谓的隐含提交，即提交操作是自动进行的。
      但是，在事务处理块中，提交不会隐含地进行。为了进行明确的提交，使用COMMIT语句
      START   TRANSACTION；
      DELETE  FROM  表名  WHERE  字=值；
      DELETE  FROM  表名1 WHERE  字段1=值；
      COMMIT；
      最后的COMMIT仅在不出错时写出更改。如果取消，会被自动取消。
隐含事务关闭：当COMMIT或ROLLBACK语句执行后，事务会自动关闭

###使用保留点
      简单的ROLLBACK和COMMIT语句就可以写入或撤销整个事务处理。但是，只是对简单的事务处理才能这样做，更复杂的事务处理可能需要部分提交或回退。

      为了支持回退部分事务处理，必须能在事务处理块中合适的位置放置占位符  这些占位符称为保留点。

      创建占位符：
      SAVEPOINT  标识符1；
      为了回退到本例给出的保留点：
      ROLLBACK  TO  标识符；

      保留点在事务完成后（执行一条ROLLBACK或COMMIT）后自动释放。也可以用RELASE  SAVEPOINT明确地释放保留点。

为了指示MySQL不自动提交更改，需要使用一下语句：
     SET  autocommit=0；
autocommit标志决定是否自动提交更改，不管有没有COMMIT语句。

#安全管理
## 访问控制
        MySQL服务器的安全基础是：用户应该对他们需要的数据具有适当的访问权，既不能多也不能少。
