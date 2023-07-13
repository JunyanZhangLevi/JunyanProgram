# compose

[菜鸟教程](https://www.runoob.com/docker/docker-compose.html)

## 1. 简介

- 1. 概念：Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

## 2. 配置

## 3. 使用

### 3.1. cctc-deploy

[部署工程](http://10.1.14.6:5080/rd/deploy)；

#### 3.1.1. cctc-deploy项目目录简介

- appBaks 存放备份
- appBins 存放部署服务的配置文件
- appBins/basic/docker 存放不同服务器的docker-compose.yml文件
- appDatas 存放项目运行中产生的数据
- appLogs 存放项目日志

#### 3.1.2. 关于ctcc-deploy的简介

- 1. 目前公司采用的部署项目的方式是挂载的方式，所谓的挂载就是不再把代码打包拖进云服务器，而是通过gitlib与云服务器进行交互的方式，通过一些配置文件和指令，云服务器自动去gitlib上进行加载代码。
- 2. 这样即使我们修改了代码，我们只需要把新的代码提交到gitlib上，然后再在云服务器上输入一些指令，云服务器就会自动去gitlib上加载改变的部分代码块，这样就会方便很多，不用再来回删除和制作镜像。
- 3. ctcc_deploy就是一个配置文件的模板（即通过挂载去部署项目的配置文件的模板）。
- 4. 项目挂载发布的配置文件是以ctcc_deploy为模板的，只是部分配置文件发生了修改。
- 5. 提交到gitlib上的项目代码的目录和云服务器上/data目录下的文件目录是能够一一对应上的.但是云服务器上 /data 目录下多了一些其他目录和文件。如update.sh文件和ctcc_deploy目录
- 6. update.sh文件中是一些指令，这些指令就是gitlib和云服务器进行交互的关键，指令内容如下：

``` shell
[root@bogon data]# cat update.sh
cd ctcc-deploy
git pull
cd ..
# rm -rf appBins/
cp -rf ctcc-deploy/appBins .
cp ctcc-deploy/package-all.sh .
```

#### 3.1.3. docker-compose.yml介绍

- 1. docker-compose.yml配置文件里面存放各个服务的配置，可以把从image行到command行，当作传统部署的docker run命令

```shell
  mi-ms11:
     image: service/springboot:latest
     hostname: card.mi-ms11
     logging:
       driver: "json-file"
       options:
         max-size: "50m"
     ports:
       - 20001:8080
     volumes:
       - /data/appDatas/card/mi/mi-ms11:/root/volumns
       - /data/appBins/card/mi/mi-ms11/target:/root/target
       - /data/appLogs/card/mi/mi-ms11:/root/log
     command: sh -c 'chmod -R a+x /root && cd /root/target && java -jar -server -Xms128m -Xmx256m mi-ms11*.jar'
     deploy:
         replicas: 1
```

- 2. docker-compose可以理解为容器集合的管理器,是指令执行的依据，因为执行docker指令去管理容器过于复杂，所以才考虑使用docker-compose配置文件

### 3.2. 使用cctc-deploy部署项目

#### 3.2.1. 使用cctc-deploy部署小米mi-ms11项目实例

- 1. 在ctcc-deploy的appBins目录下新建card目录，在card目录下新建mi目录，然后在mi目录下新建mi-ms11目录（其实建的目录有点多了，是想和gitlib上的目录保持一致）
- 2. 建好目录后，把ctcc-deploy的appBins目录下的ctid目录下的local-service目录下的所有内容复制到mi-ms11目录，然后进行配置文件内容的修改
- 3. 首先修改application-dev.yml文件，把mi-ms11项目中的application-dev.yml复制过来即可（有可能需要修改数据库）
- 4. 修改package.sh文件,参照local-service目录下的package.sh文件，进行修改。mi-ms11的package.sh如下：
  
```shell
echo "backuping ..."
# -d "目录" 表示如果存在当前目录
if [ -d "/data/appBaks/card/mi/mi-ms11/jar/" ];then
  echo ""
  else
  mkdir -p /data/appBaks/card/mi/mi-ms11/jar/
fi

mv target/*.jar /data/appBaks/card/mi/mi-ms11/jar/

echo "done."
# 下面是把项目打成jar包
echo "packaging  ..."

mkdir -p workspace
cd workspace

# git_par="-b main" 表示main分支。$1表示占位符。-z 表示不存在。这一段指定拉取具体分支
git_par=$1
if [ -z "$git_par" ]; then
  git_par="-b main"
else
  git_par="-b $git_par"
fi
echo git执行参数:$git_par
# 拉取gitlib上的代码
git clone $git_par ssh://git@10.1.14.6:5022/rd/card/mi/mi-ms11.git
# 拉取之后，进入项目内部，对项目进行打包操作，并把生成的jar包给复制到 appBins/card/mi/mi-ms11/target目录下
cd mi-ms11/

mvn package -Dmaven.test.skip=true
cp target/mi-ms11*.jar ../../../target/
cd ../../../
rm -rf workspace
echo "done."
```

- 5. 在ctcc-deploy的appBins/basic/docker目录下的docker-compose.yml文件中添加配置文件，仍然复制local-service的相关配置进行修改

```shell
  mi-ms11:
     image: service/springboot:latest
     hostname: card.mi-ms11
     logging:
       driver: "json-file"
       options:
         max-size: "50m"
     ports:
       - 20001:8080
     volumes:
       - /data/appDatas/card/mi/mi-ms11:/root/volumns
       - /data/appBins/card/mi/mi-ms11/target:/root/target
       - /data/appLogs/card/mi/mi-ms11:/root/log
     command: sh -c 'chmod a+x /data/appBins/card/mi/mi-ms11/target && cd /data/appBins/card/mi/mi-ms11/target && java -jar -server -Xms128m -Xmx256m mi-ms11*.jar'
     deploy:
         replicas: 1
```

- 6. 这样所有的配置文件就都修改完成了，修改完成之后，记得提交到gitlib上。
- 7. 登陆10.1.14.14，首先进入/data目录，进入/data目录后首先执行"./update.sh",这个命令是云服务器重新拉取更新gitlib上的cctc-deploy代码。
- 8. 然后进入/data/appBins/card/mi/mi-ms11/target目录下，执行"./package.sh"。./package.sh命令是把我们的项目代码打包成jar包，如果我们的项目代码没有改变，就不用执行这个。
- 9. 最后再执行"compose up -d"。这个命令是重启配置文件或者代码发生变化的容器。

#### 3.2.2. 注意事项


