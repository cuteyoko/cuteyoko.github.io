# TIDB学习记录

[引用自这里](https://book.tidb.io/session5/chapter1/a-brief-history-of-tidb.html)

本文动机是研探一款开源企业级数据架构及发展。

## TiDB概述

### 诞生背景

- 单机数据库存面对海量数据，存在扩展性问题

### 特性及优势

- 纯分布式架构
- 支持SQL
- 默认支持高可用
- 支持ACID
- 工具链生态丰富
### TiDB时间线


|时间 | 事件|
|--- |---|
|2015 年 04 月	| 写下第一行 TiDB 代码  |
|2015 年 09 月	| 在 GitHub 上开源，一个月 Star 数超过 2700  |
|2015 年 12 月	| TiDB 1.0 Alpha 发布，成为全球第一个开源的 Google F1 实现  |
|2016 年 04 月	| 独立研发的基于 Google Spanner 的下一代分布式存储引擎 TiKV 开源  |
|2017 年 10 月	| TiDB 1.0 GA  |
|2018 年 04 月	| TiDB 2.0 GA, TiDB + TiKV 16000+ Stars, 240+ Contributors  |
|2018 年 08 月	| TiDB Operator 开源  |
|2018 年 08 月	| CNCF 接纳 TiKV 作为 CNCF Sandbox 的云原生项目  |
|2018 年 12 月	| 面向社区人才培养的 Talent Plan 正式启动  |
|2019 年 01 月	| TiDB Lightning Toolset & TiDB Data Migration 正式开源  |
|2019 年 03 月	| TiDB 社区在线学习视频网站 PingCAP University 正式上线  |
|2019 年 05 月	| TiDB Binlog 正式开源  |
|2019 年 05 月	| CNCF 升级 TiKV 作为 CNCF Incubation 的云原生项目  |
|2019 年 06 月	| TiDB User Group 正式成立  |
|2019 年 06 月	| TiDB 3.0 GA，TiDB + TiKV 22000+ Stars, 390+ Contributors  |
|2019 年 06 月	| TiDB 社区问答网站 AskTUG 正式上线  |
|2019 年 06 月	| TiDB 社区在线学习视频网站 2.0 Courses 正式上线  |
|2019 年 07 月	| TiDB Operator 1.0 GA，并提供 AWS/GCP/阿里云一键部署方案  |
|2019 年 12 月	| 混沌测试平台 Chaos Mesh 正式开源  |
|2020 年 01 月	| 实时查询分析引擎 TiFlash Beta 版本发布  |
|2020 年 03 月	| TiDB 开源项目 Contributor 突破 400  |
|2020 年 03 月	| TiDB 4.0 RC  |
|2020 年 03 月	| 教大家从零到一写分布式数据库的 Talent Plan Courses 正式发布  |
|2020 年 05 月	| TiDB 4.0 GA（计划）  |

## TiDB架构

模块设计受  Spanner和 F1启发， 内核设计上将架构分为三个模块如下：
![架构](https://book.tidb.io/res/session1/chapter1/tidb-architecture/1.png)

通过相互通信组成完整TiDB系统。

![对应](https://book.tidb.io/res/session1/chapter1/tidb-architecture/2.png)

> TiDB (tidb-server, https://github.com/pingcap/tidb): SQL 层，对外暴露 MySQL 协议的连接 endpoint，负责接受客户端的连接，执行 SQL 解析和优化，最终生成分布式执行计划。TiDB 层本身是无状态的，实践中可以启动多个 TiDB 实例，客户端的连接可以均匀的分摊在多个 TiDB 实例上以达到负载均衡的效果。tidb-server 本身并不存储数据，只是解析 SQL，将实际的数据读取请求转发给底层的存储层 TiKV。

> TiKV (tikv-server, https://github.com/pingcap/tikv) : 分布式 KV 存储，类似 NoSQL 数据库，作为 TiDB 的默认分布式存储引擎，支持完全弹性的扩容和缩容，数据分布在多个 TiKV 存储节点中，系统会动态且自动地进行均衡，绝大多数情况下不需要人工介入。
> 与普通的 NoSQL 系统不一样的是，TiKV 的 API 能够在 KV 键值对层面提供对分布式事务的原生支持，默认提供了 SI （Snapshot Isolation）的隔离级别，这也是 TiDB 在 SQL 层面支持分布式事务的核心，上面提到的 TiDB SQL 层做完 SQL 解析后，会将 SQL 的执行计划转换为实际对 TiKV API 的调用。所以实际上数据都是存储在 TiKV 中。另外，TiKV 中的数据都会自动维护多副本（默认为 3），天然支持高可用和自动故障转移。
> TiFlash 是一类特殊的存储节点，和普通 TiKV 节点不一样的是，在 TiFlash 内部，数据是以列式的形式进行存储，主要的功能是为分析型的场景加速。后面的章节会详细介绍。

> Placement Driver (pd-server，简称 PD，https://github.com/pingcap/pd): 整个 TiDB 集群的元信息管理模块，负责存储每个 TiKV 节点实时的数据分布情况和集群的整体拓扑结构，提供 Dashboard 管控界面，并为分布式事务分配事务 ID。PD 不仅仅是单纯的元信息存储，同时 PD 会根据 TiKV 节点实时上报的数据分布状态，下发数据调度命令给具体的 TiKV 节点，可以说是整个集群的「大脑」，另外 PD 本身也是由至少 3 个对等节点构成，拥有高可用的能力。

### 存储层-TiKV

#### K-V模型
数据存储模型。 TiKV选择Key-Value模型，提供有序便利。
  - 巨大的Map，存储Key-Value Pairs
  - Key二进制有序，可迭代

#### 本地存储
rocksdb
可以简单的认为 RocksDB 是一个单机的持久化 Key-Value Map

#### Raft 协议

- Leader（主副本）选举
- 成员变更（如添加副本、删除副本、转移 Leader 等操作）
- 日志复制

> TiKV 利用 Raft 来做数据复制，每个数据变更都会落地为一条 Raft 日志，通过 Raft 的日志复制功能，将数据安全可靠地同步到复制组的每一个节点中。不过在实际写入中，根据 Raft 的协议，只需要同步复制到多数节点，即可安全地认为数据写入成功。

> 通过单机的 RocksDB，TiKV 可以将数据快速地存储在磁盘上；通过 Raft，将数据复制到多台机器上，以防单机失效。数据的写入是通过 Raft 这一层的接口写入，而不是直接写 RocksDB。通过实现 Raft，TiKV 变成了一个分布式的 Key-Value 存储，少数几台机器宕机也能通过原生的 Raft 协议自动把副本补全，可以做到对业务无感知。

#### Region
> 对于一个 KV 系统，将数据分散在多台机器上有两种比较典型的方案：
Hash：按照 Key 做 Hash，根据 Hash 值选择对应的存储节点
Range：按照 Key 分 Range，某一段连续的 Key 都保存在一个存储节点上
TiKV 选择了第二种方式，将整个 Key-Value 空间分成很多段，每一段是一系列连续的 Key，将每一段叫做一个 Region，并且会尽量保持每个 Region 中保存的数据不超过一定的大小，目前在 TiKV 中默认是 96MB。每一个 Region 都可以用 [StartKey，EndKey) 这样一个左闭右开区间来描述。

- 以 Region 为单位，将数据分散在集群中所有的节点上，并且尽量保证每个节点上服务的 Region 数量差不多
- 以 Region 为单位做 Raft 的复制和成员管理

![看图！](https://book.tidb.io/res/session1/chapter2/tidb-storage/3.png)

#### mvcc

多版本并发控制。带Version的key。Version大的放前面，小的放后面。定位到第一个大于等于该key的位置。

#### 分布式ACID

Percolator

```
tx = tikv.Begin()
    tx.Set(Key1, Value1)
    tx.Set(Key2, Value2)
    tx.Set(Key3, Value3)
tx.Commit()
```

### 计算层

TiDB在TiKV提供的分布式能力上构建计算引擎。
（无状态）

#### 表数据 映射 KV Pairs

OLTP 大批量单行CRUD/增删改查 ： key 对应唯一id
OLAP 全表扫描  ： 表中所有key编码到一个区间内

 如何构造Key?
- 为每个表分配一个表ID， TableID 集群内唯一整数 （生成函数，snowflake）
- 每行数据分配行ID RowID，表内唯一 （优化：整数型主键直接作为数据RowID）
- 每行按照`key: tablePrefix{TableID}_recordPrefixSep{RowID}` & `value: [col1, col2, col3, col4, ...]`组成键值对。

#### 





