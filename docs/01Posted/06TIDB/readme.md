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

### 





