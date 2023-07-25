# mysql

## 1. 客户端

### 1.1. 安装

- [官网](https://dev.mysql.com/downloads/workbench/)下载mysql-workbench安装包；
- win系统可能需要安装vc运行环境，才能正常使用；

### 1.2. 配置

- 配置研发环境数据库访问配置；

### 1.3. 使用

- [操作文档](https://dev.mysql.com/doc/workbench/en/)；

#### 1.3.1. sql语句书写习惯

- 标准的sql语句编写格式如下，这样写层次更加清晰

```shell
select a.*,b.* 
from department a 
join employee b on b.department_id=a.id
where 1=1
```

## 2. 服务端

### 2.1. 安装

- 使用docker-compose运行mysql容器；

## 3. MySQL的相关知识点

注：相关知识可参考b站黑马程序员教程

### 3.1. 索引

- 会为特定column（大大）增加“读”的速度，但也会减缓“写”的速度
- 在存储引擎层实现（例如InnoDB为一个数据存储引擎）
- 绝大多数为B+Tree索引

### 3.2. 索引的设计原则

1. 数据量较大
2. 区分度较高的字段
3. 字符串使用前缀索引
4. 控制索引数量
5. 针对where、group by和order by设计索引
6. 多设计联合索引

### 3.3. 什么是执行计划

- MySQL如何执行一条SQL语句，包括SQL的查询顺序、是否使用索引等等

### 3.4. 索引性能与效率分析

- explain用于查询语句的执行计划，并没有真正执行；
- type表示连接类型。主键或唯一索引type将出现const；使用非唯一索引type为ref；all代表扫描全表；  
- primary key与unique index：一个表只能有一个主键，可以有多个唯一性索引。主键是唯一性索引；
- key代表实际使用的索引；
- filtered越大越好；
- rows是认为需要执行的行数；
- extra；
- 基本语句

``` SQL
explain select……
```

### 3.5. 其他知识

- auto-increment: 自动创建主键字段的值；
- BTree（多路平衡查找树）；
- [数据结构学习网站](https://cs.usfca.edu/~galles/visualization/Algorithms.html)；
- B+Tree 叶子节点（形成双向链表）；
- 反引号用于区分保留字和普通字符；

### 3.6. 实验

- 创建数据库world和一张表demo

```SQL
use world;
create table `demo` (
`id` int(11) not null auto_increment,
`name` varchar(128) not null,
`age` int(11),
primary key (`id`),
unique key `demo_idx_name` (`name`),
key `demo_idx_age` (`age`)
)engine=InnoDB charset=utf8mb4;
insert into `demo` 
(id, name, age)
values
(1, 'Tom', 13);
```

- 输入以下代码，观察结果

``` SQL
explain select * from `demo` where name = 'Tom';
```
