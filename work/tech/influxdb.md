### 简介
InfluxDB是一个由InfluxData开发的开源时序型数据。它由Go写成，着力于高性能地查询与存储时序型数据。InfluxDB被广泛应用于存储系统的监控数据，IoT行业的实时数据等场景。

InfluxDB（时序数据库）（influx，[ˈɪnflʌks]，流入，涌入），常用的一种使用场景：监控数据统计。每毫秒记录一下电脑内存的使用情况，然后就可以根据统计的数据，利用图形化界面（InfluxDB V1一般配合Grafana）制作内存使用情况的折线图；可以理解为按时间记录一些数据（常用的监控数据、埋点统计数据等），然后制作图表做统计；

InfluxDB自带的各种特殊函数如求标准差，随机取样数据，统计数据变化比等，使数据统计和实时分析变得十分方便，适合用于包括DevOps监控，应用程序指标，物联网传感器数据和实时分析的后端存储。类似的数据库有Elasticsearch、Graphite等。

InfluxDB有三大特性：
- Time Series （时间序列）：你可以使用与时间有关的相关函数（如最大，最小，求和等）
- Metrics（度量）：你可以实时对大量数据进行计算
- Eevents（事件）：它支持任意的事件数据

特点

- 为时间序列数据专门编写的自定义高性能数据存储。 TSM引擎具有高性能的写入和数据压缩
- Golang编写，没有其它的依赖
- 提供简单、高性能的写入、查询 http api，Native HTTP API, 内置http支持，使用http读写
- 插件支持其它数据写入协议，例如 graphite、collectd、OpenTSDB
- 支持类sql查询语句
- tags可以索引序列化，提供快速有效的查询
- Retention policies自动处理过期数据
- Continuous queries自动聚合，提高查询效率
- schemaless(无结构)，可以是任意数量的列
- Scalable可拓展
- min, max, sum, count, mean,median 一系列函数，方便统计
- Built-in Explorer 自带管理工具

对常见关系型数据库（MySQL）的基础概念对比

| 概念     | Mysql | InfluxDB |
|--------|-------|----------|
| 数据库（同） |   database    |     database     |
| 表（不同）  |   table    |     measurement（测量; 度量）     |
| 列（不同）  |    column   |      tag(带索引的，非必须)、field(不带索引)、timestemp(唯一主键)    |

- database：数据库；
- measurement：数据库中的表；
- points：表里面的一行数据。
- influxDB中独有的一些概念：Point由时间戳（time）、数据（field）和标签（tags）组成。

Point相当于传统数据库里的一行数据，如下表所示：

| Point属性    |    传统数据库中的概念    |
|-----|--------|
|  fields（字段、数据）   |    各种记录值（没有索引的属性）也就是记录的值：温度， 湿度    |
|  time（时间戳）   |    每个数据记录时间，是数据库中的主索引(会自动生成)    |
|  tags（标签）   |   各种有索引的属性：地区，海拔     |