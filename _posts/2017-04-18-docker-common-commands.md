---
layout: post
title:  "docker常用命令"
categories: docker
tags:  docker
author: wukong
---

* content
{:toc}

## 删除REPOSITORY和TAG为none的image
首先删除所有停止的containers
```
docker rm $(docker ps -a -q) -f
```
然后删除images，Windows下一个一个删除
```
docker rmi $(docker images | grep '^<none>' | awk '{print $3}')
```
