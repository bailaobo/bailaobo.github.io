---
layout: post
title: Docker部署NodeJs项目
author: BaiLaobo
tags:
  - Docker
  - Node.js
categories: Docker
date: 2017-01-06 12:12:00
---

* content
{:toc}

## Debian系统准备

```bash
#docker pull debian:latest  #拉取debian最新镜像
#docker images 				#查看镜像列表
#docker run -i -t -v /data:/data  --privileged=true  debian /bin/bash #进入debian系统
```



## 安装nodejs

```bash
#wget https://nodejs.org/dist/v6.9.2/node-v6.9.2.tar.gz
#tar -zxvf node-v6.9.2.tar.gz
#cd node-v6.9.2
#./configure
#make 
#make install
```

## NPM安装插件

```bash
#vim ~/.profile #添加如下环境变量
export NODE_PATH=/usr/local/lib/node_modules
#source ~/.profile
#npm -gd install express serve-favicon morgan cookie-parser debug body-parser express-session less-middleware mysql jade
#forever  /data/vote/www  #看是否可以运行
```

## 提交镜像

```bash
#docker commit <cid>  deb-node1.0
#docker run -d -t -v /usr/local/src/vote_dev:/opt/software -p 8081:3000 --name vote_dev deb-node1.0 bash -c "source /root/.profile; /usr/local/bin/forever /data/vote/bin/www" 
#rsync -avz  --delete --exclude='.svn/' /svndir/vote /data/vote_dev/
```

## JEKINS 部署脚本

```bash
echo "start node_prod"
echo "copy db config file ..."
#copy the file to svn-update directory
cp -f /okdata/deploy/conf/vote/conf.db.js /okdata/deploy/.jenkins/workspace/VOTE_PROD/config/
echo "sync deploy dir ... "
rsync -av --delete --exclude='.svn/' /okdata/deploy/.jenkins/workspace/VOTE_PROD/ /usr/local/src/vote_prod/vote 
echo "restart docker ... "
docker restart vote_prod
echo "done!"
```
