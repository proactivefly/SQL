常用SQL

【查询语句】

```
SELECT * FROM [表名] WHERE ~条件（无条件默认查全部，DISTINCT去重）
```
1、排序 (默认插入顺序排列)
			
```
SELECT * FROM [表名] ORDER BY [fields]
SELECT * FROM [表名] ORDER BY [fields] DESC       -------降序排列
```
2、分页查询

```
SELECT * FROM [表名] LIMIT (n-1)*count,count     count:每页显示条数
```
3、别名
	    
```
SELECT  [别名].id,[别名].username FROM [表名] AS [别名]   ----表别名
SELECT  id  AS  [字段别名]  FROM  [表名]
```
4、WHERE 条件
比较-------  {= ,<,>,>=,<=,!=,<=>,<>}
多个条件 --------{AND, OR}	    	
```
检测NULL:
SELECT * FROM WHERE fields <=> NULL 
SELECT * FROM WHERE id is Null 
```
```
指定范围-------  {BETWEEN AND, NOT BETWEEN AND}
SELECT * FROM [表名] WHERE id BETWEEN 3 AND 10 ----查询编号3-10之间的数据
```
```
指定集合----------{IN ,NOT IN}
SELECT * FROM [表名] WHERE id IN (1,3,5)  ------查询id在1,3,5中间的数据
```
```
模糊查询 ---------{LIKE,NOTLIKE}
% ：代表>0个任意字符
_ : 代表1个任意字符
SELECT * FROM [表名] WHERE name LIKE "张%"   ------查询姓张
SELECT * FROM [表名] WHERE name LIKE "%狗%"  -------查询包含狗的
SELECT * FROM [表名] WHERE name LIKE "__"    -------查询两个字符的
```
5、分组查询

```
SELECT * FROM area GROUP BY areaId     -----按地区分组  
```
----GROUP_CONCAT()得到分组详情【如果不显示详情只显示第一个,id】

```
SELECT id,sex,GROUP_CONCAT(username) FROM user GROUP BY sex 
```

|id | sex | GROUP_CONCAT(username) |
|---|---|---|
| 1 | 男 |张飞，尤玉玺，赵龙 |
| 5 | 女 | 李晓，刘亦菲 |

6、聚合函数
COUNT(),SUM(),MAX(),MIN(),AVG()

```
----查询编号，sex, 用户名详情，以及组中总人数按照sex分组
SELECT id,sex,GROUP_CONCAT(username) AS uers,COUNT(*) AS total_num FROM [user表] GROUP BY sex
```
| id| sex | users | total_num |
|---|---|---|---|
| 1 | 男| 张飞，尤玉玺  |3 |
| 5 | 女 | 李晓，刘亦菲  | 5 |


```
----查询编号，sex, 用户名详情，以及组中最大年龄，最小年龄，平均年龄，年龄总和 总人数按照sex分组
SELECT id,sex,GROUP_CONCAT(username),
	MAX(age) AS max_age,
	MIN(age) AS min_age,
	AVG(age) AS avg_age,
	SUM(age) AS sum_age,
	COUNT(id) AS total_num 
FROM [user表] GROUP BY sex
```
| id | sex | GROUP_CONCAT(username) | max_age | min_age | avg_age | sum_age | total_num |
|---|---|---|---|---|---|---|---|
| 1 | 男 | 李晓，王睿暄，张柏芝 | 56 | 34 | 24 | 156 | 3 |
| 5 | 女 | 张飞，赵云，李小龙 | 23 | 14 | 42 | 255 | 3 |

7、HAVING子句 
----通过HAVING子句对分组结果进行二次筛选（只能对分组后数据用）

```
----查询编号，sex, 用户名详情，以及组中最大年龄，最小年龄，总人数 按照sex分组 &&人数>2的

SELECT id,sex,GROUP_CONCAT(username),
	MAX(age) AS max_age,
	MIN(age) AS min_age,
	COUNT(*) AS total_num 
FROM [user表] GROUP BY sex HAVING COUNT(*)>2 AND MAX(age)>78
```


8、JOIN    
			INNER JOIN 内连接
			            -----用于根据两个或多个表中的列之间的关系，从这些表中查询数据。
user表
|Id_p | name | address |
|---|---|---|
|1 | 张飞 | 北京 |
| 2 | 赵云 | 天津 |
| 3 | 马苏 | 天津 |

订单表
|orderId |orderNo| Id_p |
|---|---|---|
|1 | 12323 | 1 |
| 2 | 12332 | 1 |
| 3 | 12313 | 2 |
| 4 | 12312 | 3|
| 5 | 12322 | 2 |

```
查出  所有用户   都买了了什么东西
SELECT user.name,orders.orderNo FROM user,orders WHERE user.Id_p=order.Id_p

SELECT
	user.name,
	orders.orderNo
FROM user INNER JOIN orders
	ON user.Id_p=order.Id_p
ORDER BY users.name
```

| name| orderNo|
|---|---|
| 张飞| 12323 |
| 张飞 |12332 |
| 赵云 | 12313 |
| 赵云 | 12322 |
| 马苏 | 12312 |


LEFT JOIN  左（看齐）
				     ------从左表 那里返回所有的行，即使在右表中没有匹配的行。
	
user表
|Id_p | name | address |
|---|---|---|
|1 | 张飞 | 北京 |
| 2 | 赵云 | 天津 |
| 3 | 马苏 | 天津 |

订单表
|orderId |orderNo| Id_p |
|---|---|---|
|1 | 12323 | 1 |
| 2 | 12332 | 1 |
| 3 | 12313 | 2 |
| 5 | 12322 | 2 |
| 6 | 12342 | 5 |


```
SELECT user.name, Orders.OrderNo
	FROM Persons
	LEFT JOIN Orders
	ON user.Id_P=Orders.Id_P
ORDER BY user.name
```

结果
| name| orderNo|
|---|---|
| 张飞| 12323 |
| 张飞 |12332 |
| 赵云 | 12313 |
| 赵云 | 12322 |
| 马苏 |           |


RIGHT JOIN
			     ----- 从右表 (table_name2) 那里返回所有的行，即使在左表 (table_name1) 中没有匹配的行。


```
SELECT user.name,Orders.OrderNo
	FROM Persons
	RIGHT JOIN Orders
	ON Persons.Id_P=Orders.Id_P
ORDER BY Persons.name
```
| name| orderNo|
|---|---|
| 张飞| 12323 |
| 张飞 |12332 |
| 赵云 | 12313 |
| 赵云 | 12322 |
|        | 12322 |


FULL JOIN
 	 	          ---只要其中某个表存在匹配，FULL JOIN 关键字就会返回行。

```
列出所有的人，以及他们的定单，以及所有的定单，以及定购它们的人。
SELECT user.name, Orders.OrderNo
	FROM user
	FULL JOIN Orders
	ON user.Id_P=Orders.Id_P
ORDER BY user.name
```
| name| orderNo|
|---|---|
| 张飞| 12323 |
| 张飞 |12332 |
| 赵云 | 12313 |
| 赵云 | 12322 |
|马苏  |           |
|        | 12322 |


9、UNION 
 		    -----UNION 操作符用于合并两个或多个 SELECT 语句的结果集。
 		    --UNION 内部的 SELECT 语句必须拥有相同数量的列。
 		    --列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同
 		    --[注]:默认地，UNION 操作符选取不同的值。如果允许重复的值，请使用 UNION ALL。
 		    --UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名。
 		
china表
| name| orderNo|
|---|---|
| 张飞| 12323 |
| 张a |12332 |
| 赵云 | 12313 |
| 赵a | 12322 |

usa表

| name| orderNo|
|---|---|
| 张飞| 12323 |
| 王宝强 |12332 |
| jack | 12313 |
| tom | 12322 |


```
列出所有在中国和美国的不同的雇员名：
SELECT name FROM china
	UNION
SELECT name FROM usa

```
| name| orderNo|
|---|---|
| 张飞| 12323 |
| 张a |12332 |
| 赵云 | 12313 |
| 赵a | 12322 |
| 王宝强 |12332 |
| jack | 12313 |
| tom | 12322 |

注：--这个命令无法列出在中国和美国的所有雇员。在上面的例子中，我们有两个名字相同的雇员，
	  --他们当中只有一个人被列出来了。UNION 命令只会选取不同的值。(UNION ALL可以)


【插入语句】

```
INSERT INTO [表名] VALUE('fields1','fields2','fields3'....)
INSERT INTO [表名] VALUES('fields1','fields2','fields3'),('fields1','fields2','fields3') ....
```
【修改数据】

```
UPDATE [表名] SET field1=value1,field2=value2... WHERE ~条件（不加条件都会更新）
```
【删除语句】

```
DELETE FROM [表名] WHERE ~条件（无条件默认全部）
```



10、时间函数
		NOW()   ————————当前时间
		DATE_FORMAT(fields,'YYYY-MM-DD')————————格式化   

		每天日期所对应的名称和价格（日期的显示格式是 "YYYY-MM-DD"）。 
		
```
SELECT name,preice,DATE_FORMAT(NOW(),'YYYY-MM-DD') FROM [product表]
```







 
	   
