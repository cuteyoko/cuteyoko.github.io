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

### 设计参考

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


