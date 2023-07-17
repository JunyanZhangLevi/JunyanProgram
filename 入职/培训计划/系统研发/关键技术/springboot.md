# springboot

## 1. 创建工程

- 准备好所有安装环境；
- 命令窗口查找maven，选择Spring Initializr，创建代码工程；

## 2. 配置

- 导入依赖
- properties配置文件
- .gitignore文件
- VSCode插件

## 3. 使用

### 3.1. 三种工程类型

#### 3.1.1. 网关工程

- netty，nio，例如api-gateway工程；

#### 3.1.2. 依赖工程

- 输出可用于pom文件添加的依赖jar，发布到内网nexus依赖库，例如rsp-common工程；

>操作说明

1. 创建依赖maven工程；
2. 参考以往依赖工程（例如rsp-common），修改pom.xml，删除启动类和测试类；
3. 使用mvn deploy指令，发布依赖工程；
4. 业务工程pom.xml加入依赖工程发布版本的依赖配置，例如：

    ```xml
        ...
        <dependency>
            <groupId>com.bhz</groupId>
            <artifactId>rsp-common</artifactId>
            <version>0.0.1</version>
        </dependency>
        ...
    ```

5. 将业务工程和依赖工程放入vscode的一个workspace下；
6. 拖拽业务工程的一个或者多个文件到依赖工程的指定目录下，此时业务工程会提示将自动修改关联文件，操作保存修改；
7. 使用mvn deploy指令，再次发布依赖工程；
8. 最后，分别提交业务工程和依赖工程代码即可；

>>PS

- 若上述操作后，建议更新依赖工程的版本号，然后同步修改业务工程的依赖版本号，方可获取指定版本的依赖工程的更新内容；

#### 3.1.3. 业务工程
