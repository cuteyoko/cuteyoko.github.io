# TiDB

[简介看这里](https://pingcap.com/products/tidb)

## TiDB整体架构

![TiDB架构](https://download.pingcap.com/images/docs-cn/tidb-architecture-v3.1.png)

特点简单来说 分布式/SQL支持/高可用/ACID事务支持/生态工具链丰富

架构分三层: SQL层TiDB Server,无状态，解析SQL  PD层，元信息管理，负责节点分布和集群拓扑结构，管控面，分布式事务ID,分布式组成。
