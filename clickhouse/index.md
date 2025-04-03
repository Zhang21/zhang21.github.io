# ClickHouse文档


ClickHouse 文档总结。

<br/>

<!--more-->

<br/>

参考：

- [ClockHouse docs](https://clickhouse.com/docs)

---

<br/>

# 介绍

<br/>
<br/>

## 什么是ClickHouse

ClickHouse 是一种高性能、面向列的 SQL 数据库管理系统（DBMS），用于联机分析处理（OLAP, Online Analytical processing）。

<br/>
<br/>

### 什么是分析

分析（又称 OLAP），是指对海量数据进行复杂计算的 SQL 查询。分析查询通常要处理数十亿甚至数万亿行数据。

事务查询（OLTP, Online Transaction Processing），每次查询只读写几行，因此只需几毫秒。

在许多使用案例中，分析查询必须是实时的，即在一秒内返回数据。

<br/>
<br/>

### 面向行和面向列的存储

数据库可存储面向行或面向列的数据。

在面向行的数据库中，连续的表行一个接一个按顺序地存储。这种布局允许快速检索行，因为每一行的列值都存储在一起。

CH 是一种面向列的数据库。在此系统中，表格是作为列的集合存储的，即每列的值都是按顺序一个接一个地存储的。这种布局会增加恢复单行的难度（因为行之间存在间隙），但列操作（如筛选和聚合）的速度要比面向行的数据库快得多。

<br/>
<br/>

### 数据复制和完整性

CH 采取异步多主复制方案，确保数据在多个节点上冗余存储。数据写入任何副本后，所有剩余副本都会在后台检索其副本。系统会在不同的副本上维护相同的数据。大多数故障后，恢复都是自动进行的，在复杂情况下则是半自动恢复。

<br/>
<br/>

### 安装

<br/>
<br/>

### 安装建议

- 与时钟频率较高而核数较少相比，时钟频率较低额内核较多的情况下，CH 的工作效率往往更高。
- 至少 4GB 内存来执行非繁琐查询。CH 服务器可在内存更小的情况下运行，但查询经常会中止。
  - 查询复杂性。
  - 查询中处理的数据量。
  - GROUP BY, DISTINCT, JOIN 和其他操作的临时数据大小。
- 建议禁用 SWAP
- 对于分布式 CH 集群，建议使用至少 10G 网络连接。

<br/>

---

<br/>

## 为什么CH速度如此快

从架构角度看，数据库至少由存储层和查询处理层组成。存储层负责保存、加载和维护表数据，而查询处理层则执行用户查询。与其他数据库相比，CH 在这两层都进行了创新，从而实现了极快的插入和选择查询。

<br/>
<br/>

### 存储层-并发插入是相互隔离的

在 CH 中，每个表都由多个表部分（table parts）组成。每当用户向表中插入数据时，就会创建一部分。查询总是针对查询开始时存在的所有表部分执行。

为了避免累积过多部分，CH 在后台运行合并操作，不断将多个（小）部分合并为一个更大的部分。

优点：

- 单个插入是本地的，不需要更新全局数据结构，即每个表的数据结构。
- 因此，多个同时插入的数据不需要相互同步，也不需要与现有表数据同步，因此插入操作几乎可以以磁盘 IO 的速度运行。

<br/>
<br/>

### 存储层-并发插入和查询时隔离的

另一方面，合并表部分是用户看不到的后台操作，不会影响 SELECT 查询。事实上，这种结构能有效地隔离插入和查询，因此许多数据库都采用了这种架构。

<br/>
<br/>

### 存储层-合并时间计算

CH 还能在合并操作过程中执行额外的数据转换。

- 替换合并
- 汇总合并
- TTL 合并：根据某些基于时间的规则压缩、移动或删除行。

<br/>
<br/>

### 存储层-数据修剪

重复运行相同和类似的查询，可添加所有或重新组织数据，使频繁查询能更快地访问这些数据。此方法也称为数据修剪（data purning）。CH 为此提供了三种技术：

- 主键索引
- 表投影：表的另一种内部版本，存储相同的数据，但按不同的主键排序。
- 跳转索引：将额外的数据统计嵌入列中，如最大值、最小值等。

这三种技术的目的都是在全列读取过程中尽可能多地跳过行，因为读取数据的最快方法就是根本不读取数据。

<br/>
<br/>

### 存储层-数据压缩

CH 的存储层还使用不同的编码器对原始表格数据进行额外压缩。

列式存储尤其适合这种压缩，因为相同类型和数据分布的值被放在一起。

数据压缩不仅能减少数据库表的存储空间，而且在很多情况下还能提高查询性能，因此本地磁盘和网络 ID 通常受限于较低的吞吐量。

<br/>
<br/>

### 查询处理层

CH 利用矢量化查询处理层，尽可能并行执行查询，以最大限度地利用所有资源，提高速度和效率。

矢量化是指查询计划操作员成批传递中间结果行，而不是单行。更好地利用 CPU 缓存。

为了充分利用多个核心，CH 将查询计划展开为多个通道（通常每个核心一个通道）。每个通道处理表数据的不连续范围。这样，数据库的性能就会随着核心数而扩展。

如果单个节点太小，无法容纳表数据，可以添加更多节点形成集群。表格可以拆分（分片）并分布在各个节点上。CH 将在存储表数据的所有节点上运行查询，从而根据可用节点的数量进行横向扩展。

<br/>

---

<br/>

## 什么是OLAP

<br/>

---

<br/>

## ClickHouse的独特功能

- 真正面向列的数据库管理系统
- 数据压缩
- 磁盘存储数据
- 在多个核心上并行处理
- 在多个服务器上分布式处理
- 支持 SQL
- 矢量计算引擎
- 实时数据插入
- 主索引
- 次级索引
- 适合在线查询
- 支持近似计算
- 自适应join算法
- 数据副本和完整性支持
- 基于角色的访问控制

<br/>

---

<br/>

# 增删查改

<br/>
<br/>

## 创建表

```sql
CREATE DATABASE IF NOT EXISTS helloworld

-- 如果不指定 db，则默认为 default
CREATE TABLE helloworld.my_first_table
(
    user_id UInt32,
    message String,
    timestamp DateTime,
    metric Float32
)
ENGINE = MergeTree()
PRIMARY KEY (user_id, timestamp)
```

<br/>

主键在 CH 中的工作原理：

- 在 CH 中，表中每一行的主键都不是唯一的。
- 主键也是排序键。

<br/>
<br/>

## 插入数据

```sql
INSERT INTO helloworld.my_first_table (user_id, message, timestamp, metric) VALUES
    (101, 'Hello, ClickHouse!',                                 now(),       -1.0    ),
    (102, 'Insert a lot of rows per batch',                     yesterday(), 1.41421 ),
    (102, 'Sort your data based on your commonly-used queries', today(),     2.718   ),
    (101, 'Granules are the smallest chunks of data read',      now() + 5,   3.14159 )
```

<br/>
<br/>

### ClickHouse与OLTP数据库的插入比较

作为一个 OLAP 数据库，CH 针对高性能和可扩展性进行了优化，每秒可插入数百万行，但在即时一致性方面有妥协。

OLTP 数据库（如 Postgres）专门针对事务插入进行了优化，完全符合 ACID 标准，可确保强大的一致性和可靠性保证。可靠性限制了插入性能，因此会产生相当大的开销。

<br/>
<br/>

### 插入最佳做法

- 大批量插入
- 确保空闲重试的批次一致
- 插入到MergeTree或分布式表
- 为小批量使用异步插入
- 使用 ClickHouse 官方客户端
- 首选原生格式
- 使用 HTTP 接口

<br/>
<br/>

## 查询数据

```sql
SELECT *
FROM helloworld.my_first_table
ORDER BY timestamp

```

<br/>
<br/>

## 更新和删除数据

```sql
/*
更新
ALTER TABLE [<database>.]<table> UPDATE <column> = <expression> WHERE <filter_expr>
*/
ALTER TABLE website.clicks
UPDATE visitor_id = getDict('visitors', 'new_visitor_id', visitor_id)
WHERE visit_date < '2022-01-01'

/*
删除
ALTER TABLE [<database>.]<table> DELETE WHERE <filter_expr>
*/
ALTER TABLE website.clicks DELETE WHERE visitor_id in (253, 1002, 4277)

```

<br/>

---

<br/>

# 核心概念

<br/>
<br/>

## 表部分

表部分（table parts）

CH MergeTree 引擎系列中每个表的数据在磁盘上都是不可变数据部分（data parts）的集合。

下面是一个示例：

```sql
CREATE TABLE uk_price_paid
(
    date Date,
    town LowCardinality(String),
    street LowCardinality(String),
    price UInt32
)
ENGINE = MergeTree
ORDER BY (town, street);

```

<br/>

每当向表中插入一组行时，就会创建一个数据部分。

![数据部分](https://raw.githubusercontent.com/zhang21/images/master/cs/database/clickhouse/data_part.png)

<br/>
<br/>

## 主索引

主索引（Primary Indexes）

<br/>

---

<br/>

# 管理和部署

docs: <https://clickhouse.com/docs/en/architecture/introduction>

<br/>
<br/>

## 部署和扩展

术语：

- 副本
- 分片
- 分布式协调：CH Keeper 协调系统与 ZK 兼容。

<br/>
<br/>

## 扩展

<br/>
<br/>

## 复制

<br/>
<br/>

## 集群部署



