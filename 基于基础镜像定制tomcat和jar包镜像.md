# 1、tomcat-dockerfile文件

```bash
#基于基础镜像封装成jenkins镜像
FROM tomcat:8.5.60-jdk8

#镜像维护者信息
MAINTAINER Lancger@qq.com

#这个环境变量用来表名该镜像模板的最后更新时间
ENV REFRESHED_AT 2020-09-13

#切换镜像目录，进入/usr目录
WORKDIR /usr/local

RUN rm -rf ./tomcat/webapps/*

#拷贝代码包
COPY ./ROOT.war /usr/local/tomcat/webapps/ROOT.war

#公开端口
EXPOSE 8080

#设置启动命令
ENTRYPOINT ["/usr/local/tomcat/bin/catalina.sh", "run"]
```

# 2、jar-dockerfile机器人文件

```bash
#基于基础镜像封装成jar镜像
FROM  ascdc/jdk8:latest

#镜像维护者信息
MAINTAINER Lancger@qq.com

#这个环境变量用来表名该镜像模板的最后更新时间
ENV REFRESHED_AT 2020-09-23

RUN mkdir -p /app/war/pkg/
ADD kline_robot.jar /app/war/pkg/
WORKDIR /app/war/pkg/

#设置JAVA_OPTS参数
ENV JAVA_OPTS="\
-server \
-Xmx1024m \
-Xms1024m \
-Xmn512m \
-Xss256K \
-XX:MetaspaceSize=256m \
-XX:MaxMetaspaceSize=512m \
-XX:SurvivorRatio=4 \
-XX:+UseParallelGC \
-XX:ParallelGCThreads=4 \
-XX:+UseParallelOldGC \
-XX:+UseAdaptiveSizePolicy \
-XX:+HeapDumpOnOutOfMemoryError \
-XX:HeapDumpPath=/tmp \
-XX:+PrintGCDetails \
-XX:+PrintTenuringDistribution \
-XX:+PrintGCTimeStamps \
-Xloggc:/tmp/gc.log \
-XX:+UseGCLogFileRotation \
-XX:NumberOfGCLogFiles=5 \
-XX:GCLogFileSize=10M"

#公开端口
EXPOSE 8080

ENTRYPOINT java ${JAVA_OPTS} -Dfastjson.parser.autoTypeSupport=true -Dfile.encoding=UTF-8 -Dsun.jnu.encoding=UTF-8  -Djava.security.egd=file:/dev/./urandom -Duser.timezone=GMT+08 -Dspring.profiles.active=dev -jar kline_robot.jar --server.port=8080
```

# 3、exchange_api-dockerfile文件

```bash
#基于基础镜像封装成jar镜像
FROM  ascdc/jdk8:latest

#镜像维护者信息
MAINTAINER Lancger@qq.com

#这个环境变量用来表名该镜像模板的最后更新时间
ENV REFRESHED_AT 2020-09-23

RUN mkdir -p /app/war/pkg/
ADD exchange_api.jar /app/war/pkg/
WORKDIR /app/war/pkg/

#设置JAVA_OPTS参数
ENV JAVA_OPTS="\
-server \
-Xmx1024m \
-Xms1024m \
-Xmn512m \
-Xss256K \
-XX:MetaspaceSize=256m \
-XX:MaxMetaspaceSize=512m \
-XX:SurvivorRatio=4 \
-XX:+UseParallelGC \
-XX:ParallelGCThreads=4 \
-XX:+UseParallelOldGC \
-XX:+UseAdaptiveSizePolicy \
-XX:+HeapDumpOnOutOfMemoryError \
-XX:HeapDumpPath=/tmp \
-XX:+PrintGCDetails \
-XX:+PrintTenuringDistribution \
-XX:+PrintGCTimeStamps \
-Xloggc:/tmp/gc.log \
-XX:+UseGCLogFileRotation \
-XX:NumberOfGCLogFiles=5 \
-XX:GCLogFileSize=10M"

#公开端口
EXPOSE 8080

ENTRYPOINT java ${JAVA_OPTS} -Dfastjson.parser.autoTypeSupport=true -Dfile.encoding=UTF-8 -Djava.security.egd=file:/dev/./urandom -Duser.timezone=GMT+08 -Dspring.profiles.active=dev -jar exchange_api.jar --server.port=8080
```

