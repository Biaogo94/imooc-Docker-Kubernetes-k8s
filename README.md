# 模块
![1539670527771](assets/1539670527771.png)
## 用户服务

- 用户登录
- 用户注册
- 用户基本信息查询
- 无状态, 无session

## 课程服务
- 登录验证
- 课程的curd

## 信息服务
- 发送邮件
- 发送短信

## 用户edgeservice
## 课程edgeservice
## API GATEWAY

# Docker化
基本镜像
 ```bash
docker pull openjdk:8-jre
docker run -it --entrypoint bash openjdk:8-jre
 ```

把外部服务的配置设为参数
```
spring.datasource.url=jdbc:mysql://${mysql.address}:3307/db_user
```
内部服务则是换成服务名

```
thrift.user.ip=user-service
```



pom中添加插件

```xml
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```

然后进行`mvn clean package`．注意此时要先对父pom进行`mvn install`

写Dockerfile

```
FROM openjdk:8-jre
MAINTAINER nnkwrik nnkwrik@gmail.com

COPY target/user-thrift-service-1.0-SNAPSHOT.jar /user-service.jar

ENTRYPOINT ["java","-jar","/user-service.jar"]
```

命令行运行Docker

```
docker build -t user-service:lastest .
docker run -it user-service:latest --mysql.address=192.168.0.5
```

## docker-compose
```bash
docker-compose up -d
docker-compose up -d message-service // 重启某个服务
docker-compose down
```
## 镜像仓库
### 公有仓库

```bash
docker tag zookeeper:3.5  nnkwrik/zookeeper:3.5
docker login
docker push nnkwrik/zookeeper:3.5
docker pull nnkwrik/zookeeper:3.5
```

### 私有仓库

没有交互界面, 多台生产环境时不容易管理
```bash
docker pull registry:2 
docker run -d -p 5000:5000 registry:2 
docker tag zookeeper:3.5 localhost:5000/zookeeper:3.5  
docker push localhost:5000/zookeeper:3.5 
docker pull localhost:5000/zookeeper:3.5
```

### harbor

改harbor.cfg,后 `sudo ./install.sh`
```bash
hostname = hub.nnkwrik.com
```

/etc/hosts中添加

```
127.0.0.1		hub.nnkwrik.com
```

```
docker login hub.nnkwrik.com
docker tag openjdk:8-jre   hub.nnkwrik.co/micro-service/openjdk:8-jre
docker push hub.nnkwrik.com/micro-service/openjdk:8-jre
```

