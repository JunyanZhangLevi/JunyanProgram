# ssh

## 1. 安装

## 2. 配置

## 3. 使用

### 3.1. 常用指令

- ssh-keygen：

```bash
# 生成公私钥对（注意将邮箱改为自己的邮箱）
ssh-keygen -t rsa -C "chencan@bhz.com.cn"
```

- ssh-copy-id：

```bash
# 将本机公钥存入目标服务器，win系统使用git-bash来执行（注意用户名不需要更改，使用root即可）
ssh-copy-id root@10.1.14.13
```
