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

## 3. MySQL索引、执行计划与存储过程（黑马程序员）
- 索引  
```
为特定column（大大）增加“读”的速度，但也会减缓“写”的速度
在存储引擎层实现，InnoDB为一个数据存储引擎
绝大多数B+Tree索引 
```
- 执行计划
```
慢查询：查询速度很慢
优化器->执行计划
EXPLAIN来查询语句的执行计划，并没有真正执行  
type表示连接类型：主键或唯一索引type将出现const；使用非唯一索引type为ref；all代表扫描全表
primary key与unique index：一个表只能有一个主键，可以有多个唯一性索引，主键就是唯一性索引；
Key代表实际使用的索引；
filter越大越好；
rows是认为需要执行的行数；
```

## 4. MySQL的其他知识（黑马程序员）
- auto-increment: 自动创建主键字段的值
- B-Tree（多路平衡查找树），学习网站：www.cs.usfca.edu
- B+ 叶子节点（形成双向链表）
- 索引的设计原则：
1. 数据量较大
2. 区分度较高的字段
3. 字符串使用前缀索引
4. 控制索引数量
5. 针对where、group by和order by设计索引
6. 多设计联合索引