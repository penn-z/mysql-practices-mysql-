### 接exercise1.md练习：
4. #### **<font color=blue>order by与limit练习(esc_goods数据表): </font>**
4.1 按价格由高到低排序：
```
select * from esc_goods order by shop_price desc;
```
4.2 按发布时间由早到晚排序：
```
select * from esc_goods order by goods_id;
```
4.3 按栏目由高到低排序，栏目内部按价格由高到低排序：
```
select * from esc_goods order by cat_id desc,shop_price desc; 
```
4.4 取出价格最高的前三名商品：
```
select * from esc_goods order by shop_price desc limit 3;
```
4.5 取出点击量第三名至第五名的商品：
```
select * from esc_goods order by click_count desc limit 2,3;
```
* * *

5. #### **<font color=blue>连接查询练习(esc_goods数据表): </font>**
5.1 取出所有商品的商品名、栏目名、价格
> inner join：
```
select a.goods_name,b.cat_name,a.shop_price from esc_goods as a inner join category as b on a.cat_id = b.cat_id;
```
> 等值连接：
```
select a.goods_name,b.cat_name,a.shop_price from esc_goods as a,category as b where a.cat_id = b.cat_id;
```
> natural join：
```
select a.goods_name,b.cat_name,a.shop_price from esc_goods as a natural join category as b;
```
5.2 取出第4个栏目下商品的商品名、栏目名、价格：
```
select a.goods_name,b.cat_name,shop_price from esc_goods as a inner join category as b on a.cat_id = b.cat_id where b.cat_id = 4;
```
5.3 根据给出的表结构按要求写出SQL语句

> m 赛程表(数据在data.md)
    
字段名称 字段类型 描述  
mid int 主键  
hid int 主队的ID  
gid int 客队的ID  
mres varchar(20) 比赛结果，如（2:0）  
matime date 比赛开始时间
> 数据表m数据如下：

| mid  | hid  | gid  | mres | matime     |
|------|------|------|------|------------|
|    1 |    1 |    2 | 2:0  | 2006-05-21 |
|    2 |    2 |    3 | 1:2  | 2006-06-21 |
|    3 |    3 |    1 | 2:5  | 2006-06-25 |
|    4 |    2 |    1 | 3:2  | 2006-07-21 |
  

> t 参赛队伍表(数据在data.md)  

字段名称 字段类型 描述  
teamID int 主键  
teamName varchar(20) 队伍名称  
> 数据表t数据如下：

| tid  | tname        |
|------|--------------|
|    1 | 国安         |
|    2 | 申花         |
|    3 | 慕课战队     |

表m的hid(主队id)与gid(客队id)都与表t中的tid(球队id)关联

查出 2006-6-1 到2006-7-1之间举行的所有比赛，并且用以下形式列出：  
拜仁 2：0 不来梅 2006-6-21
> 先找出2006-06-01 到2006-07-01的比赛：
```
select * from m where matime < '2006-07-01';
```
> 然后连接表t两次，表m一次进行查询：
```
select a.tid as '主队id',a.tname,m.mres,b.tname,b.tid as '客队id',m.matime as '比赛时间' from t as a,t as b,m where a.tid = m.hid and b.tid = m.gid and m.matime between '2006-06-01' and '2006-07-01';
```
`结果：`
| 主队id | tname | mres | tname | 客队id | 比赛时间 |
|-------:|:-----:|:----:|:-----:|:------:|:--------:|
| 2 | 申花 | 1:2 | 慕课战队 | 3 | 2006-06-21 |
| 3 | 慕课战队 | 2:5 | 国安 | 1 | 2006-06-25 |
> 或者用left join：
```
select hid,t1.tname as hname ,mres,gid,t2.tname as gname,matime
from
m left join t as t1
on m.hid = t1.tid
left join t as t2
on m.gid = t2.tid where matime between '2006-06-01' and '2006-07-01';
```
* * *

6. #### **<font color=blue>union查询练习： </font>**
A表：
| id | num |
|----|-----|
| a | 5 |
| b | 10 |
| c | 15 |
| d | 10 |
B表：
| id | num |
|----|-----|
| b | 5 |
| c | 15 |
| d | 20 |
| e | 99 |
要求查出以下效果（相应字母的值之和）：
| id | num |
|----|-----|
| a | 5 |
| b | 15 |
| c | 30 |
| d | 30 |
| e | 99 |
> 首先创建表和添加数据：
```
create table a (
id char(1),
num int
)engine myisam charset utf8;

insert into a values ('a',5),('b',10),('c',15),('d',10);

create table b (
id char(1),
num int
)engine myisam charset utf8;

insert into b values ('b',5),('c',15),('d',20),('e',99);
```
> 解题，先联表查询，注意此处应用union all，用union的话会去重：
```
select * from a union all select * from b;
```
| id   | num  |
|------|------|
| a    |    5 |
| b    |   10 |
| c    |   15 |
| d    |   10 |
| b    |    5 |
| c    |   15 |
| d    |   20 |
| e    |   99 |
> 利用sum求和,group by 分组,大功告成：
```
select id,sum(num) from (select * from a union all select * from b) as c group by id;
```
* * *

7. #### **<font color=blue>子查询练习： </font>**
7.1 查询出最新一行商品（以商品编号最大为最新，用子查询实现）：
```
select goods_id,goods_name,shop_price from esc_goods where goods_id = (select max(goods_id) from esc_goods);
```
> or：
```
select goods_id,goods_name,shop_price from esc_goods where goods_id = (select goods_id from esc_goods order by desc limit 1);
```
7.2 查询出编号为19的商品的栏目名称（用左连接查询和子查询分别实现）：
> left join：
```
select cat_name from category as a left join esc_goods as b on a.cat_id = b.cat_id where b.goods_id = 19;
```
> 子查询：
```
select cat_name from category where cat_id = (select cat_id from esc_goods where goods_id = 19);
```
7.3 用where型子查询把esc_goods表中的每个栏目下最新商品取出来：
```
select goods_id,goods_name,shop_price,cat_id from esc_goods where goods_id in (select max(goods_id) from esc_goods group by cat_id);
```
7.4 用from型子查询把ecs_goods表中的每个栏目下面最新的商品取出来：
```
select a.goods_id,a.goods_name,a.shop_price,a.cat_id from (select * from esc_goods order by goods_id desc) as a group by cat_id;
```
7.5 用exists型子查询，查出所有商品的栏目：
```
select c.cat_id from category as c where exists (select * from esc_goods where esc_goods.cat_id=c.cat_id);
```
