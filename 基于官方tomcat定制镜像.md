# 一、dockerfile定制tomcat镜像

## 1、命令行运行

```python
mkdir -p /data1/docker/tomcat/logs /data1/docker/tomcat/webapps

mkdir /data1/docker/tomcat/webapps/ROOT
cd /data1/docker/tomcat/webapps/ROOT 

cat >index.html<<\EOF
hello docker and tomcat
EOF

docker rm -f tomcat8

docker run -d -p 8080:8080 --name tomcat8 \
    -v /data1/docker/tomcat/logs:/usr/local/tomcat/logs \
    -v /data1/docker/tomcat/webapps:/usr/local/tomcat/webapps \
    tomcat:8.5

docker logs -f tomcat8
```

## 2、dockerfile方式

### a、编写dockerfile

```python
cat >dockerfile<<\EOF
#在这里用tomcat:8.5作为tomcat的基础镜像
FROM tomcat:8.5

#镜像维护者信息
MAINTAINER Lancger@qq.com

#这个环境变量用来表名该镜像模板的最后更新时间
ENV REFRESHED_AT 2020-12-17

#切换镜像目录，进入/usr/local/tomcat目录
WORKDIR /usr/local/tomcat

#覆盖相关文件
COPY server.xml /usr/local/tomcat/conf

#添加jvm参数
ENV JAVA_OPTS="$JAVA_OPTS -server -Xms4096m -Xmx4096m -Xmn2048m -Xss256K -XX:MetaspaceSize=1024m -XX:MaxMetaspaceSize=1024m -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+UseCMSInitiatingOccupancyOnly -Duser.timezone=GMT+08"

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

#启动tomcat shell执行程序
CMD ["catalina.sh", "run"] 
EOF
```

### b、构建镜像

```
# 构建镜像
docker build -t tomcat_base_4g:v1 -f dockerfile .
docker build -t viptime/tomcat8_base_4g:v1 -f dockerfile .

# 挂载配置目录启动容器
docker run -d -p 8080:8080 --name tomcat8 \
    -v /data1/docker/tomcat/logs:/usr/local/tomcat/logs \
    -v /data1/docker/tomcat/webapps:/usr/local/tomcat/webapps \
    tomcat8_base_4g:v1
    
# 进入容器
docker exec -it tomcat8 /bin/bash

# 查看日志
docker logs -f --tail=30 tomcat8

# 删除所有容器
docker rm -f $(docker ps -aq)
```

# 二、推送到 hub.docker.com

```
# 镜像打标签
docker tag tomcat_base:v1 viptime/tomcat8_base_4g:v1

# 登录hub.docker.com
docker login

# 推送镜像
docker push docker.io/viptime/tomcat8_base_4g:v1
```

# 三、将自定义war添加到官方Tomcat镜像

```bash
official

cat >dockerfile_official<<\EOF
#在这里用tomcat:8.5作为tomcat的基础镜像
FROM tomcat:8.5

#镜像维护者信息
MAINTAINER Lancger@qq.com

#这个环境变量用来表名该镜像模板的最后更新时间
ENV REFRESHED_AT 2020-12-17

#切换镜像目录，进入/usr/local/tomcat目录
WORKDIR /usr/local/tomcat

#覆盖相关文件
COPY server.xml /usr/local/tomcat/conf/

#将自己的war包放到对应的目录
ADD ROOT.war /usr/local/tomcat/webapps/

#添加jvm参数
ENV JAVA_OPTS="$JAVA_OPTS -server -Xms4096m -Xmx4096m -Xmn2048m -Xss256K -XX:MetaspaceSize=1024m -XX:MaxMetaspaceSize=1024m -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+UseCMSInitiatingOccupancyOnly -Duser.timezone=GMT+08"

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

#启动tomcat shell执行程序
CMD ["catalina.sh", "run"] 
EOF

docker build -t viptime/tomcat8_official_4g:v1 -f dockerfile_official .

docker push docker.io/viptime/tomcat8_official_4g:v1
```

参考资料：

https://www.coder.work/article/41481  docker - Docker将warfile添加到官方Tomcat镜像

https://yeasy.gitbook.io/docker_practice/image/build  使用 Dockerfile 定制镜像

https://segmentfault.com/a/1190000038340170 12个docker常用命令！以tomcat为例
