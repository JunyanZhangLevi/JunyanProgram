# git

## 1. 安装  

- [官网](https://git-scm.com/downloads)下载安装包；
- git安装前，建议提前安装vscode，并在安装选项中选择使用vscode作为编辑器；
- 安装过程中，注意勾选git bash到path，以方便terminal识别；

## 2. 配置

### 2.1. 客户端

- 设置全局邮箱和用户名；
- macos系统若出现代码合并问题，建议执行：git config --global --add pull.rebase false；

### 2.2. gitlab

- 将ssh公钥填入gitlab，建议使用工程ssh地址进行交互，无需每次输入密码验证；
- 建议将默认视图改为your projects' activity；

## 3. 使用

### 3.1. 常用命令

- git clone 下载
- git init 初始化本地版本库
- git status 查看状态
- add commit pull push 提交文件修改
- git log 查看提交历史(英文状态下q退出)  
