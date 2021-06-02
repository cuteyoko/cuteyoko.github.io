# TiDB

[简介看这里](https://pingcap.com/products/tidb)

## TiDB整体架构

![TiDB架构](https://download.pingcap.com/images/docs-cn/tidb-architecture-v3.1.png)

特点：简单来说 分布式/SQL支持/高可用/ACID事务支持/生态工具链丰富

架构：分三层，SQL层TiDB Server,无状态，解析SQL;  PD层，元信息管理，负责节点分布和集群拓扑结构，管控面，分布式事务ID,分布式组成; 存储节点TiKV/TiFlash，分布式的提供事务的K-V引擎，region分段，快照隔离级别，支持分布式事务，TiFlash列式存储。

## 存储

![存储视图](https://download.pingcap.com/images/docs-cn/tidb-storage-architecture.png)

- KV对，从SQL到KV模型映射
- RocksDB，持久化KVMap
- Raft,数据复制同步，数据接口
- Region,KV空间分段映射方式
- mvcc,快照
- 分布式ACID事务， Percolator

![数据分布](https://download.pingcap.com/images/docs-cn/tidb-storage-3.png)
