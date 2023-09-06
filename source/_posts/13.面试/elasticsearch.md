---
title: elasticsearch面试题
date: 2023-09-04
keywords:  elasticsearch面试题
description:  elasticsearch面试题
top: false
tags:
  -  面试
categories:
  -  面试
---

# elasticsearch 了解多少，说说你们公司 es 的集群架构，索引数据大小，分片有多少，以及一些调优手段 。
> 如： ES 集群架构 13 个节点，索引根据通道不同共 20+索引，根据日期，每日递增 20+，索引：10分片，每日递增 1 亿+数据，每个通道每天索引大小控制：150GB 之内。

> 目前es集群共有10个节点(3个网关节点，3个主节点，4个数据节点), 目前索引共14个，每日递增数据越50w。

# 倒排索引
每个文档对应一个文档ID，内容是关键词的集合。
如：文档1经过分词，提取了20个关键字每个关键字都会在文档中出现的次数和位置，倒排索引就是关键词 ----> ID 的映射。

# 倒排索引的结构
> B-Tree
叶子节点记录ID的位置，和MySQL中的MyISAM相似。


# ES的写数据过程
- 客户端选择node（协调节点）发送
- 协调节点对doc进行路由转发给对应的node（主分片）
- 实际的node对primary shard 处理请求数据进行同步。
- 协调节点发现primary shard 和replice node 完成，然后返回结果。

# ES

- 将数据写入内存缓存区
- 将数据写入tranlog缓存区
- 每个1s从 buffer的refresh 到file system cache 中 生成segment文件，一旦文件生成则可以通过索引查询到。
- refresh完成buffer清空。
- 每隔5s tranlog 从buffer flush 到磁盘中。
- 定期、定量从 file system cache 中结合tranlog 内容 flush index 到磁盘中。

# ES 读取数据过程

- 根据id 判断路由转发对应的node。
- 在primary shard 以及所有的replica中随机选取一个请求进行负载。
- 请求node 返回给协调节点
- 协调节点返回doc给客户端。

# ES 搜索过程。
- 协调节点，将请求转发给所有的shard 对应的primary shard 或者replica shard
- query phease 每个shard 将自己的结果（id集合）返回给协调节点。
- 由协调节点进行数据合并，排序，分页。
- fetch phase 有协调节点根据id去拉取doc返回客户端。


