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
