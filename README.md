# 一、docker-files

```bash
# 构建镜像
docker build -t tomcat:v1 -f ./Dockerfile-java .

# 把build的镜像run起来：
docker run -itd --name tomcat -p 8080:8080 tomcat:v1

# 查看日志
docker logs -f tomcat
docker logs -f --tail=30 tomcat
```

# 二、从hub.docker.com拉取镜像修改

```bash
docker pull viptime/tomcat8:base

推送镜像
docker push docker.io/viptime/tomcat8-4g:base
```

# 三、基于基础镜像封装

```bash
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
```
