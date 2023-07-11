# 1.概述
索引 是提高MySQL查询性能的非常有用的一个工具，当我们对数据库中的某些字段建立了索引，那么怎么查看在执行的SQL查询的过程中是否用到了这些索引呢？
查询SQL语句的执行情况通常通过关键字 explain 来进行

# 2.实践

## 2.1建表
例如，如下的数据表
```sql
use test;

drop table if exists `student`;
create table `student`
(
  `id`      int not null auto_increment,
  `name`    varchar(50) not null,
  `number`  varchar(20) not null,
  `address` varchar(100),
  `age`     int default 0,
  primary key (`id`)
)Engine=InnoDB DEFAULT CHARSET=utf8;

insert into student (`name`, `number`, `address`, `age`)
values
("马云", "18000001", "浙江省杭州市余杭区", 55),
("马化腾", "18000002", "广东省深圳市南山区", 50),
("张一鸣", "18000003", "北京市海淀区", 38),
("王兴", "18000004", "北京市朝阳区", 40),
("李彦宏", "18000005", "北京市海淀区", 45),
("程维", "18000006", "北京市海淀区", 42),
("雷军", "18000007", "北京市朝阳区", 54),
("刘备", "18000008", "四川省成都市青羊区", 60),
("诸葛亮", "18000009", "四川省成都市武侯区", 43),
("关羽", "18000010", "湖北省荆州市荆州区", 58),
("张飞", "18000011", "四川省阆中市", 56),
("曹操", "18000012", "河南省洛阳市老城区", 63),
("孙权", "18000013", "江苏省南京市建邺区", 49),
("李世民", "18000014", "陕西省西安市长安区", 38),
("李隆基", "18000015", "陕西省西安市长安区", 28),
("朱元璋", "18000016", "江苏省南京市玄武区", 61),
("朱棣", "18000017", "北京市东城区", 39);
```

## 2.2对比加索引前后的SQL查询情况
explain 关键字查看SQL查询过程的情况：
SQL查询 address 为北京市的人：
```sql
explain select name, address from student where address like "北京市%";
```
[[编程开发/SQL/assets/e480798e54009ba463ff7981825f370c_MD5.png|Open: Pasted image 20230711100209.png]]
![[编程开发/SQL/assets/e480798e54009ba463ff7981825f370c_MD5.png]]
在 student 表上加索引:
```sql
alter table student add index (address(9));
```
[[编程开发/SQL/assets/ff0230705d3c38800de1aaa345d74011_MD5.png|Open: Pasted image 20230711100023.png]]
![[编程开发/SQL/assets/ff0230705d3c38800de1aaa345d74011_MD5.png]]
为了加快索引，我们采用前缀索引，因为一个汉字在UTF-8编码下占3个字节，因此选择对address字段的前9位加索引。
再次执行上面的SQL查询语句：
[[编程开发/SQL/assets/13bd93911facda171ed1c005d2ad5667_MD5.png|Open: Pasted image 20230711100054.png]]
![[编程开发/SQL/assets/13bd93911facda171ed1c005d2ad5667_MD5.png]]

## 2.3解释Explain得到的结果
### 2.3.1 type 反应查询语句的性能
我们主需要注意一个最重要的的 type 的信息很明显地体现出是否用到了索引：
`type` 结果值从好到坏依次是：

`system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL`

一般来说，得保证查询至少达到 range 级别，最好能达到 ref 级别，否则就可能出现性能问题。

### 2.3.2 possible_keys: SQL查询时用到的索引。
可以看到，没加索引时，possible_keys 的值为 NULL，加了索引后的值为 address，即用到了索引address(索引默认为(column_list)中的第一个列的名字

### 2.3.3 key 显示SQL实际决定查询结果使用的键(索引)。如果没有使用索引，值为NULL
可以看到，没加索引时，key 的值为 NULL，加了索引后的值为 address，即决定查询结果用到了索引address

### 2.3.4 rows 显示MySQL认为它执行查询时必须检查的行数
可以看到，没加索引时，rows 的值为17，即数据表student中所有数据，说明没加索引时的SQL查询是全表扫描；
加了索引后，rows 的值为6，数据库表中address以“北京市”开头的一共也就6条，SQL在执行查询操作时，一共也检查了6行，不必进行全表扫描查询，可以很容易得出结论：加索引的SQL查询性能远高于不加索引的情况。

# 3.总结
通过在SQL查询语句前面添加关键字 explain 就可以分析SQL查询语句的性能了.