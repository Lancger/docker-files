# docker-files

```bash
# 构建镜像
docker build -t tomcat:v1 -f ./Dockerfile-java .

# 把build的镜像run起来：
docker run -itd --name tomcat -p 8080:8080 tomcat:v1

# 查看日志
docker logs -f tomcat
docker logs -f --tail=30 tomcat
```
