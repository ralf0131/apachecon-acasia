---
title: "字节跳动基于 Parquet 格式的降本增效实践"
date: "2023-08-18T16:15:00" 
track: "datalake"
presenters: "徐庆,王恩策"
stype: "中文演讲"
---
字节跳动离线数仓默认使用 Parquet 格式进行数据存储，但是在业务使用过程中我们遇到了小文件过多，数据存储成本高等相关问题。
针对小文件过多问题，现有技术方案一般是通过 Spark 读取多个 Parquet 小文件后，再将这些数据重新输出并合并到一个或多个大文件。对于存储成本过大问题目前离线数仓只有分区级的行级 TTL 方案，如果需要删除分区中不再使用且占比较大的明细字段数据（列级 TTL)，则需要通过 Spark 将数据读取出来并将需要删除的字段置为 NULL 的覆写方式来完成。
无论是小文件合并，列级 TTL，都存在对 Parquet 数据文件的大量覆写操作。由于 Parquet 格式有特殊的编码规则，需要经过特殊的（反）序列化、（解）压缩、（反）编码等一系列操作，才能实现对 Parquet 中数据的读写。在这一过程中，编解码、解压缩之类的操作是 CPU 密集型计算，会消耗大量计算资源。为了提高 Parquet 格式文件覆写效率，我们深入研究了 Parquet 文件格式定义，采用了二进制 copy 的方法优化数据覆写操作，跳过了普通覆写中编解码之类的多余操作，相比于传统方法大幅提高了文件覆写效率，性能是普通覆写方式的 10+ 倍。
为了提高易用性，我们同时提供了新的 SQL 语法来支持用户方便的完成小文件合并、列级 TTL 等操作。
 ### Speakers: 
 <img src="https://img.bagevent.com/resource/20230616/1450066050.jpg" width="200" /><br>徐庆: 字节跳动, 火山引擎LAS高级研发工程师。多年从事于Hive Metastore, SparkSQL, Hudi等大数据相关组件的研发工作。
 <br><br><img src="https://img.bagevent.com/resource/20230616/1521102550.jpeg" width="200" /><br>王恩策: 字节跳动, 火山引擎LAS高级研发工程师，负责字节跳动大数据分布式计算引擎的设计与研发，帮助公司在海量数据中挖掘出高价值信息。
 <br><br>