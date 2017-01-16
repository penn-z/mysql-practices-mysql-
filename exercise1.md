## 综合练习：
> #### 创建一个test的数据库
商品表:esc_goods  
goods_id　--主键,  
goods_name -- 商品名称  
cat_id -- 栏目id  
brand_id -- 品牌id  
goods_sn -- 货号  
goods_number -- 库存量  
shop_price -- 价格  
goods_desc　--商品详细描述  

> create esc_goods:
```MySQL
create table if not exists esc_goods(  
goods_id smallint(5) unsigned not null primary key auto_increment,  
goods_name varchar(255) not null,  
cat_id smallint(5) unsigned not null,  
brand_id smallint(5) unsigned not null,  
goods_sn int(15) unsigned not null,  
goods_number int(11) unsigned not null,  
shop_price decimal(10,2) unsigned not null, 
goods_desc text not null);
```
> create category:
```MySQL
create table if not exists category(  
cat_id smallint(5) unsigned not null primary key auto_increment,  
cat_name char(100) not null,  
parent_id smallint(5) unsigned not null);
```

建表完成后,作以下操作:  
删除esc_goods表的goods_desc 字段,及货号字段  
并增加字段:click_count -- 点击量  
在goods_name列上加唯一性索引  
在shop_price列上加普通索引  
在clcik_count列上加普通索引  
删除click_count列上的索引  

删除esc_goods表的goods_desc字段,及货号字段，并增加字段click_count 点击量：
```MySQL
alter table drop column goods_desc;  
alter table esc_goods add column click_count int(10) not null default 0;
```

对esc_goods插入data.md的goods表中的数据：（略）  
对category插入data.md的category表中的数据（略）

### 查询练习：
1. #### **<font color=blue>基础查询 where的练习: </font>**

1.1 主键为32的商品：
```
select * from esc_goods where goods_id = 32;
```
1.2 不属第3栏目的所有商品：
```
select * from esc_goods where cat_id != 3;
```
1.3 本店价格高于3000元的商品：
```
select * from esc_goods where shop_price > 3000;
```
1.4 本店价格低于或等于100元的商品：
```
select * from esc_goods where shop_price <= 100;
```
1.5 取出第4栏目或第11栏目的商品(不许用or)：
```
select * from esc_goods where cat_id in (4,11);
```
1.6 取出100<=价格<=500的商品(不许用and);
```
select * from esc_goods where shop_price between 100 and 500;
```
1.7 取出不属于第3栏目且不属于第11栏目的商品(and,或not in分别实现);
> and:
```
select * from esc_goods where cat_id = 3 and cat_id != 11;
```
> not in:
```
select * from esc_goods where cat_id not in (3,11);
```
1.8 取出价格大于100且小于300,或者大于4000且小于5000的商品：
```
select * from esc_goods where (shop_price between 100 and 300) or (shop_price between 4000 and 5000);
```
1.9 取出第3个栏目下面价格<1000或>3000,并且点击量>5的系列商品：
```
select * from esc_goods where cat_id = 3 and click_count > 5 and (shop_price < 1000 or shop_price >3000);
```
1.10 取出第1个栏目下面的商品(注意:1栏目下面没商品,但其子栏目下有)：
```
select * from esc_goods where cat_id in (select cat_id from category where parent_id = 1);
```
1.11 取出名字以"诺基亚"开头的商品：
```
select * from esc_goods where goods_name like "诺基亚%";
```
1.12 取出名字为"诺基亚nxx"的手机：
> 子查询：
```
select * from esc_goods where goods_name like "诺基亚n%" and cat_id in (select cat_id from category where parent_id = 1);
```
> 普通方法(利用'_'通配符)：
```
select * from esc_goods where goods_name like "诺基亚n__";
```
1.13 取出名字不以"诺基亚"开头的商品：
```
select * from esc_goods where goods_name not like "诺基亚%";
```
1.14 取出第3个栏目下面价格在1000到3000之间,并且点击量>5 "诺基亚"开头的系列商品：
```
select * from esc_goods where shop_price between 1000 and 3000 and click_count > 5 and goods_name like "诺基亚%" and cat_id = 3;
```
1.15 有如下表和数组  
把num值处于[20,29]之间,改为20  
num值处于[30,39]之间的,改为30  
> mian表  
  
| num |
|-----|
| 3 |
| 12 |
| 15 |
| 25 |
| 23 |
| 29 |
| 34 |
| 37 |
| 32 |
| 45 |
| 48 |
| 52 |
```
update mian set num = 20 where num >= 20 and num <= 29;
update mian set num = 30 where num >= 30 and num <= 39;
```
1.16 <font color=red>把esc_goods表中商品名为'诺基亚xxxx'的商品,改为'HTCxxxx'</font>  
提示:大胆的把列看成变量,参与运算,甚至调用函数来处理 .
substring(),concat()
```
update esc_goods set goods_name = concat("HTC", substring(goods_name, 4)) where goods_name like "诺基亚%";
```
* * *
2. #### **<font color=blue>分组group、聚合函数查询练习:</font>**
2.1 查出最贵的商品的价格：
> max()函数：
```
select max(shop_price) from esc_goods;
```
2.2 查出最大(最新)的商品编号：
```
select max(goods_sn) from esc_goods;
```
2.3 查出最便宜的商品的价格：
```
select min(shop_price) from esc_goods;
```
2.4 查出最旧(最小)的商品编号：
```
select min(goods_sn) from esc_goods;
```
2.5 查询该店所有商品的库存总量：
```
select sum(goods_number) from esc_goods;
```
2.6 查询所有商品的平均价：
```
select avg(shop_price) from esc_goods;
```
2.7 查询该店一共有多少种商品：
```
select count(*) from esc_goods;
```
2.8 查询每个栏目下面  
> 提示:(5个聚合函数,sum,avg,max,min,count与group综合运用)

最贵商品价格：
```
select cat_id,max(shop_price) from esc_goods group by cat_id;
```  
最低商品价格：
```
select cat_id,min(shop_price) from esc_goods group by cat_id;
```
商品平均价格：
```
select cat_id,avg(shop_price) from esc_goods group by cat_id;
```
商品库存量：
```
select cat_id,sum(goods_number) from esc_goods group by cat_id;
```
商品种类：
```  
select cat_id,count(goods_id) from esc_goods group by cat_id;
```  
* * *

3. #### **<font color=blue>having、group综合运用练习:</font>**
3.1 查询该店的商品比市场价所节省的价格：
```
select goods_id,goods_name,market_price - shop_price as '节省价格' from esc_goods;
```
3.2 查询每个商品所积压的货款(提示:库存*单价)：
```
select goods_id,goods_name,(shop_price * goods_number) as '该商品积压的货款' from esc_goods;
```
3.3 查询该店积压的总货款：
```
select sum(shop_price * goods_number) from esc_goods;
```
3.4 查询该店每个栏目下面积压的货款：
```
select cat_id,sum(shop_price * goods_number) from esc_goods group by cat_id;
```
3.5 查询比市场价省钱200元以上的商品及该商品所省的钱(where和having分别实现)：
> where实现：
```
select goods_id,goods_name,(market_price - shop_price) as '该商品省的钱' from esc_goods where (market_price - shop_price) > 200;
```
> <font color=orange>having实现：</font>
```
select goods_id,goods_name,(market_price - shop_price) as a from esc_goods having a > 200;
```
3.6 查询积压货款超过2W元的栏目,以及该栏目积压的货款：
```
select cat_id,sum(shop_price * goods_number) as a from esc_goods group by cat_id having a > 20000;
```
3.7 where-having-group综合练习题（数据已经在data.md给出)  
有如下表及数据  
| name | subject | score |    
|------:|:---------:|:-------|
| 张三 | 数学 | 90 |  
| 张三 | 语文 | 50 |  
| 张三 | 地理 | 40 |  
| 李四 | 语文 | 55 |  
| 李四 | 政治 | 45 |  
| 王五 | 政治 | 30 |  
要求:查询出2门及2门以上不及格者的平均成绩
> <font color=blue>正确做法：  </font>
```
select name,sum(score < 60) as a,avg(score) from result group by name having a >= 2;
```
<font color=red>我一开始的错误写法是：</font>
```
select name,avg(score) from result where score < 60 group by name having count(subject) >= 2;
```
| name   | avg(score) |
|-------:|:-----------|
| 张三   |    45.0000 |
| 李四   |    50.0000 |

where score < 60 虽然筛选了挂科科目，但是会导致挂科两门或以上的同学的不挂科科目成绩无法统计入平均分中。  
但若无score < 60的约束，又会把挂科少于2门的同学算上，同样不合理。  
<font color=blue>故而采取把 score < 60 写在select 后：</font>  
> 先查询每个同学的平均成绩：
```
select name,avg(score) from result group by name;
```
| name   | avg(score) |
|-------:|:-----------|
| 张三   |    60.0000 |
| 李四   |    50.0000 |
| 王五   |    30.0000 |
| 赵六   |    95.6667 |
> 查询每个同学的挂科情况：
```
select name,score < 60 from result;
```
| name   | score < 60 |
|-------:|:-----------|
| 张三   |          0 |
| 张三   |          1 |
| 张三   |          1 |
| 李四   |          1 |
| 李四   |          1 |
| 王五   |          1 |
| 赵六   |          0 |
| 赵六   |          0 |
| 赵六   |          0 |

> 查询每个同学的挂科情况：
```
select name,sum(score < 60) from result group by name;
```
| name   | sum(score < 60) |
|-------:|:----------------|
| 张三   |               2 |
| 李四   |               2 |
| 王五   |               1 |
| 赵六   |               0 |
> 计算每个人的平均分：
```
select name,sum(score < 60),avg(score) from result group by name;
```
| name   | sum(score < 60) | avg(score) |
|-------:|:---------------:|:-----------|
| 张三   |               2 |    60.0000 |
| 李四   |               2 |    50.0000 |
| 王五   |               1 |    30.0000 |
| 赵六   |               0 |    95.6667 |

> 利用having筛选挂科2门或以上的同学：
```
select name,sum(score < 60) as a,avg(score) from result group by name having a >= 2;
```
| name   | a    | avg(score) |
|-------:|:----:|:-----------|
| 张三   |    2 |    60.0000 |
| 李四   |    2 |    50.0000 |
