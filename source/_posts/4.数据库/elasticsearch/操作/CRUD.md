---
title: elasticsearch基本操作
date: 2023-08-27
keywords:  elasticsearch基本操作
description:  elasticsearch基本操作
top: false
tags:
  -  elasticsearch
categories:
  -  elasticsearch
---

#  文档的基本 CRUD 与批量操作

## 创建自动生成 _id

```shell
POST weather1/_doc/rAEhe4AB1GDKafqqvVdM
{
  	"id" : 11111,
    "city" : "北京",
    "city_id" : 22222,
    "wether":"beijing",
    "comment":"2019-04-15T14:12:12"
}

GET weather1/_search
```


## 创建自动生成指定id

```shell
POST weather1/_doc/1
{
  	"id" : 11111,
    "city" : "北京",
    "city_id" : 22222,
    "wether":"beijing",
    "comment":"2019-04-15T14:12:12"
}
```


## 修改

```shell
POST weather1/_doc/1
{
  	"id" : 11111,
    "city" : "北京",
    "city_id" : 33333,
    "wether":"beijing",
    "comment":"2019-04-15T14:12:12"
}

GET weather1/_search
```


## 创建文档指定Id。操作为创建，如果不加操作则认为是修改。如果id已经存在，报错

```shell
POST weather1/_doc/rAEhe4AB1GDKafqqvVdM?op_type=create
{
  	"id" : 11111,
    "city" : "北京",
    "city_id" : 22222,
    "wether":"beijing",
    "comment":"2019-04-15T14:12:12"
}

GET weather1/_doc/rAEhe4AB1GDKafqqvVdM
```

## 删除索引

```shell
DELETE weather1/_doc/1
```

# 查询

