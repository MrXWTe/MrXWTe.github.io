---
layout: post
title: MySQL common commands
date: 2019-06-26 21:00:36 +0800
excerpt: This blog describes the common commands for MySQL.
author: 霜月初六
img:  # Add image post (optional)
tags: [MySQL]
---



# MySQL常用命令

### MySQL服务

```mysql
-- 通过“windows服务管理器”启动MySql服务
net start mysql;

-- 通过“windows服务管理器”停止MySql服务
net stop mysql;

-- 登录MySQL数据库
mysql -hlocalhost -uroot(用户名) -p123456(登陆密码);
```



***



### 数据库的基本操作

```mysql
-- 查看所有数据库：
show databases;

-- 创建数据库:
create database 数据库名;

-- 指定编码方式创建数据库:
create database 数据库名 character set 编码方式

-- 查看某个已创建好的数据库:
show create database 数据库名;

-- 修改数据库编码:
alter database 数据库名称 default character set 编码方式 collate 编码方式_bin ;
eg: alter database xiaoxu default character set gbk collate gbk_bin;

-- 删除数据库:
drop database 数据库名;

-- 使用、选定数据库:
use 数据库名;

-- 查看当前正在使用的数据库:
select database();
```



***



### 数据表的基本操作

```mysql
-- 创建数据表:
create table 表名(
    字段名 1,数据类型[完整性约束条件],
    字段名 2,数据类型[完整性约束条件],
    ...
    字段名 n,数据类型[完整性约束条件]
);
eg:
create table tbl_user(
    id int(32) primary key auto_increment,
    name varchar(32) not null,
    age varchar(32) not null
);

-- 查看数据表:
show tables;-- 查看所有数据表
show create table 表名;-- 查看创建数据表
describe 表名;-- 查看数据表详细信息
desc 表名;-- 上条的简写形式

-- 添加一个字段：
alter table 表名 add 字段名 类型(长度) [约束];  

-- 修改表名:
alter table 旧表名 rename [to] 新表名;
rename table 旧表名 [to] 新表名;

-- 修数据表编码方式:
set names gbk;

-- 修改字段名:
alter table 表名 change 旧字段名 新字段名 新数据类型;
eg:将数据表grade中的name字段改为username，数据类型保持不变
alter table grade change name username varchar(20) ;

-- 修改字段的数据类型:
alter table 表名 modify 字段名 数据类型 [约束];
eg:将数据表grade中的id字段的数据类型由int(11)修改为int(20)
alter table grade modify id int(20) ;

-- 删除字段：
alter table 表名 drop 列名;

-- 删除数据表:
drop table 表名;
```



***



### 数据库表记录的基本操作

```mysql
-- 插入(增加)一条记录修改表记录：
insert into 表名 (字段, 字段, 字段) values (值, 值, 值);
insert into 表名 values(值1,值2,值3……);

-- 修改表记录删除表记录：
update 表名 set 字段名=值, 字段名=值, 字段名=值……;-- 会将字段的所有值都修改
update 表名 set字段名=值, 字段名=值, 字段名=值…… where 条件;-- 修改条件指定的记录

-- 删除表记录：
delete from 表名;
delete from 表名 where 条件;-- 删除后id不会重置

-- 说说delete与truncate的区别？
delete删除的时候是一条一条的删除记录，它配合事务，可以将删除的数据找回。
truncate删除，它是将整个表摧毁，然后再创建一张一模一样的表。它删除的数据无法找回。

-- 查询记录：
select * from 表名;# 查询表内所有记录
select 字段, 字段, ... from 表名;# 查询特定字段的记录
select * from 表名 as 别名;# 用别名替换表名(as可以省略)
select 字段 字段别名 from 表名;# 用字段别名替换表别名(as可以省略)
select distinct(字段) from product;# 去除字段重复值显示
select pname,price+10 from product;# 将字段进行 四则运算 后显示

-- 条件查询记录：
select * from 表名 where id=(>、<、!=)?;    
select * from 表名 where name like '%士%';# 模糊查询，查询字段中有‘士’的记录
select * from 表名 where id=(3,6,9);
select * from 表名 where name like '%士%' and(or) id>3;# 组合查询

-- 排序：
select * from 表名 order by id;# 默认升序
select * from 表名 order by id desc;# 降序
select * from 表名 where name like '%士%' order by id;# 组合查询

-- 聚合函数：
select sum(price) from 表名;
select avg(price) from 表名;
select count(*) from 表名;

-- 分组操作：
update product set cid='1';
update product set cid='2' where  pid in (5,6,7);
select cid, count(*) from tbl_user group by cid;# 根据cid字段分组，分组后统计记录的个数

-- 根据cid分组，分组统计每组商品的平均价格，并且平均价格大于20000元。
select cid, avg(price) from 表名 group by cid having avg(price) > 20000;
```

##### 查询总结

命令的排列顺序如下

- select  一般在的后面的内容都是要查询的字段
- from  要查询到表
- where
- group by
- having  分组后带有条件只能使用having
- order by 它必须放到最后面



***



### 数据库多表操作

```mysql
-- 关联两表操作
alter table 从表 add foreign key(从表外键) references 主表(主表主键)
eg: alter table product add foreign key(category_id) references category(cid)

-- 多表查询
select * from a,b;# a、b两表元素排列组合（一般不会使用）
select * from a inner join b on 条件;#内连接查询
eg: select * from category inner join product where category_id=cid; 
select * from a,b where 条件;#内连接查询（与上一条相同）
select * from a left outer join b on 条件;#外连接查询
select * from a right outer join b on 条件;#外连接查询

-- 子查询
eg: select * from product where category_id=(select cid from category where cid='c002');
```

注意：

- 当两张表没有建立任何关系的时候，那么可以随意删除其中任何一张表中的任何记录，但是一旦把两张表建立了关系(主外键约束)之后，那么不能删除主表中的数据(这些数据内容在从表中有关联关系的数据)
- 主表不能删除从表中已经引用的记录
- 从表不能添加主表中不存在的记录



***



### 子查询

查询orders表中id大于3的user_id：
```mysql
SELECT user_id 
FROM orders 
WHERE id>1;
```
查询前一步所列出user_id的所有username：
```mysql
SELECT username 
FROM user 
WHERE id IN (
    SELECT user_id 
    FROM orders 
    WHERE id>1);
```



***



### 联结

##### 内部联结或等值联结
查询客户姓名以及订单号以及创建时间
第一种写法：

```mysql
SELECT username, number, createtime
FROM user, orders
WHERE user.id=orders.user_id;
```

第二种写法：推荐
```mysql
SELECT username, number, createtime
FROM orders INNER JOIN user
ON user.id=orders.user_id
# 上下两个是一样的
SELECT username, number, createtime
FROM user INNER JOIN orders
ON user.id=orders.user_id
```

##### 自联结
订单号为1000010的订单有问题，想查询同一个人的其它订单是否也有问题
```mysql
# 子查询方式
SELECT number, createtime
FROM orders
WHERE user_id=(SELECT user_id 
               FROM orders 
               WHERE number='1000011')
# 自联结方式							 
SELECT o1.number, o1.createtime
FROM orders AS o1, orders AS o2
WHERE o1.user_id=o2.user_id 
AND o2.number='1000010';
```

##### 外联结
```mysql
# 左外联结，显示user的所有行
SELECT username, number, createtime
FROM user LEFT OUTER JOIN orders
ON user.id=orders.user_id
# 右外联结，显示orders的所有行
SELECT username, number, createtime
FROM user RIGHT OUTER JOIN orders
ON user.id=orders.user_id
```