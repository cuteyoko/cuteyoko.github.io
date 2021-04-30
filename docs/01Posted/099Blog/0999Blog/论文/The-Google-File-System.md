# The Google File System
[【Ref】](https://static.googleusercontent.com/media/research.google.com/zh-CN//archive/gfs-sosp2003.pdf)

## 0. ABSTRACT

谷歌搞了一个分布式的文件系统
- 扩展性好
- 分布式部署在廉价机器上
- 提供容错机制
- 为多个client提供高性能服务

## 1. INTRODUCTION

- components failure是常态而非异常（千百台机器内存、cpu、程序、操作系统blabla的错误）
- 文件超大！ 【？ 多大算大】
- AppendOnlyNewFile 大多数文件通过追加修改，append性能优化重要
- 简化接口，联合设计，原子化Append

## 2. DESIGN OVERVIEW

### 2.1 预设前提

- 辣鸡机器来组合
- 存超大文件，小文件支持，但性能随缘吧
- 两种主要读负载 大带宽顺序读， 小随机读，可以重排合并减少随机
- 大带宽顺序写，写了很少变动
- 多client高效并发写同一文件， 生产者消费者队列、多路合并
- 带宽比时延重要

### 2.2 接口

- create
- delete
- open
- close
- read
- write files.

- snapshot 低消耗创建复制
- record append 原子性append

### 2.3 架构

- single master
- multi chunk-server
- multi client

**chunk**
- 64bit-chunk unique handle 
- 按handle + range索引读写
- 可靠性，三副本

**master**
- 维护所有元数据: namespace, access control, the mapping from file to chunks, current location of chunks
- 维护chunk lease, gc, orphaned chunks,  chunk migration
- 定时与chunk server心跳、下发指令、收集状态

**client**
- 向Server提供FileSystem API
- 与master/chunk server沟通，读写数据

client 与 chunkserver 不缓存数据:client流式/太大（而且不缓存的设计简单，不需要考虑cache同步)， chunk server利用了Linux本身的buffer cache机制。


### 2.4 SingleMaster
- 只有一个master节点，简化设计，利用全局知识（无同步代价）来进行chunk placement
- 只有一个，可能成为瓶颈。 => 文件读写数据不经过master, 仅访问元数据。缓存部分信息，供短期后续信息使用。


```seqdiag
-> application: file req
    -> client: Transelate(name + byteoffset) -> chunk index : 如何映射
        -> master:GetChunkHandle&Replicas (fileName, chunkIndex)
    -> client: Cache ChunkInfos(finleName + index)
    -> client: reqToClosestChunkServer(replicas)
    -> clinet: (till cache expires/ file reopend) ： 如何过时？如何file reopened.
    -> client: May Ask extra next metadata to cache. 减少master代价
```

## 2.5 chunk size

- 关键参数： 分块大小
- 预置： 64MB
- 减少元数据查询交互，顺序阅读场景尤其有效减少元数据，随机小读也可以cache 几TB数据的
- 减少网络（overhead）保持较长链接
- 减少元数据大小

缺点

- 空间浪费
- 小文件热点？（多个client同时对小文件多次操作） 低频变高频

解决：（不是主要问题，鸵鸟）
- 提高小文件副本
- client可以从其他client读取数据

## 2.6 MetaData

- file-chunk namespace
- mapping from files to chunks
- location of each chunk replicas

全缓存
