# 一、docker-files

注意有大文件，需要使用git-lfs

```bash
remote: error: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.github.com.

yum install git-lfs -y

git lfs install
git lfs track "jdk-8u251-linux-x64.tar.gz"
git add .gitattributes
git add *
git commit -m "大文件上传"
git push

git push origin master 

https://www.jianshu.com/p/3f25cd20e392  Github如何上传超过100M的大文件
https://github.com/Lancger/opslinux
```

```bash
# 构建镜像
docker build -t tomcat_base_4g:v1 -f ./Dockerfile-java-4g .
docker build -t viptime/tomcat_base_4g:v1 -f ./Dockerfile-java-4g .

# 把build的镜像run起来：
docker run -itd --name tomcat -p 8080:8080 tomcat_base_4g:v1

# 查看日志
docker logs -f tomcat
docker logs -f --tail=30 tomcat
```

# 二、从hub.docker.com拉取镜像修改

```bash
拉取镜像
docker pull viptime/tomcat_base_4g:v1

推送镜像
docker push docker.io/viptime/tomcat_base_4g:v1
```

# 三、基于基础镜像封装

```bash
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
```
