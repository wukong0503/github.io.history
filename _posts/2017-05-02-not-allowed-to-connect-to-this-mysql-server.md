---
layout: post
title:  "Host * is not allowed to connect to this MySQL server"
date:   2017-05-02 19:45:45
categories: MySQL
tags: MySQL
---

* content
{:toc}

## 原因
mysql默认用root账号连接数据库的地址只能是本地

## 解决方法
```
mysql -u root -p

mysql>use mysql;
mysql>update user set host =’%'where user =’root’;
mysql>flush privileges;

// 在本机登入mysql后，更改“mysql”数据库里的“user”表里的“host”项，从”localhost”改为'%'。
mysql>use mysql;
mysql>select 'host' from user where user='root';     
// 查看mysql库中的user表的host值（即可进行连接访问的主机/IP名称）
// 修改host值
mysql>update user set host = '%' where user ='root';
mysql>flush privileges;
mysql>select host,user from user where user='root';
mysql>quit
```
