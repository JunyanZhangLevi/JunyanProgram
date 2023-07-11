# maven

## 安装

- 官网下载，解压。

## 配置

- 将本机公钥存入目标服务器 ssh-copy-id root@10.1.14.13

- 配置链接内网仓库 scp root@10.1.14.13:/root/.m2/settings.xml .
  
- 验证： mvn -v

## 使用

- clean
- install
- compile
- package
- deploy
- pom.xml配置
