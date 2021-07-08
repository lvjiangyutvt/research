# 第1章 Hadoop_概论

## 1.1 大数据概念

​	大数据（Big Data）：指无法在一定时间范围内用常规软件工具进行捕捉、管理和处理的数据集合，是需要新处理模式才能具有更强的决策力、洞察发现力和流程优化能力的海量、高增长率和多样化的信息资产。

​	大数据主要解决，海量数据的采集、存储和分析计算问题。

## 1.2 大数据特点(4V)

- Volume（大量）
- Velocity（高速）
- Variety（多样）
- Value（低价值密度）

# 第2章 Hadoop_入门

## 2.1 Hadoop概念

### 2.1.1 Hadoop是什么

- Hadoop是一个由Apache基金会所开发的分布式系统基础架构。
- 主要解决，海量数据的存储和海量数据的分析计算问题。
- 广义上来说，Hadoop通常是指一个更广泛的概念——Hadoop生态圈

### 2.1.2 Hadoop发展历史（了解）

- Hadoop创始人Doug Cutting，为了实现与Google类似的全文搜索功能，他在Lucene框架基础上进行优化升级，查询引擎和索引引擎
- 2001年年底Lucene成为Apache基金会的一个子项目。
- 对于海量数据的场景，Lucene框架面对与Google同样的困难，存储海量数据困难，检索海量速度慢。
- 学习和模仿Google解决这些问题的办法：微型版Nutch。
- 可以说Google是Hadoop的思想之源（Google在大数据方面的三篇论文）

> GFS-->HDFS
>
> Map-Reduce-->MR
>
> BigTable-->HBase

- 2003-2004年公开了部分GFS和MapReduce思想的细节，以此为基础Doug Cutting等人用了2年业余时间实现了DFS和MapReduce机制，使Nutch性能飙升。
- 2005年Hadoop作为Lucene的子项目Nutch的一部分正式引入Apache基金会。
- 2006年3月份，Map-Reduce和Nutch Distributed File System（NDFS）分别被纳入到Hadoop项目中，Hadoop就此正式诞生，标志着大数据时代来临。
- 名字来源于Doug Cutting儿子的玩具大象

### 2.1.3 Hadoop三大发行版本（了解）

​	Hadoop三大发行版本：Apache、Cloudera、Hortonworks。

- ​		Apache版本最原始（最基础）的版本，对于入门学习最好。2006
- ​		Cloudera内部集成了很多大数据框架，对应产品CDH。2008
- ​		Hortonworks文档较好，对应产品HDP。2011
- ​		Hortonworks现在已经被Cloudera公司收购，推出新的品牌CDP。

### 2.1.4 Hadoop优势（四高）

- 高可靠性：Hadoop底层维护多个数据副本，所以即使Hadoop某个计算元素或存储出现故障，也不会导致数据的丢失。
- 高扩展性：在集群间分配任务数据，可方便的扩展数以千计的节点。（例如双11、618可以动态增加服务器）
- 高效性：在MapReduce的思想下，Hadoop是并行工作的，以加快任务处理速度。
- 高容错性：能够自动将失败的任务重新分配。

### 2.1.5 Hadoop组成（面试重点）

![Hadoop版本组成区别](.\noteimages\Hadoop版本组成区别.png)

#### 2.1.5.1 HDFS概述

​	Hadoop distributed File System，简称HDFS，是一个分布式文件系统。

- 架构概述

  1）NameNode（nn）：存储文件的元数据，如文件名，文件目录结构，文件属性（生成时间、副本数和文件权限），以及每个文件的块列表和块所在的DataNode等。

  2）DataNode（dn）：在本地文件系统存储文件块数据，以及块数据的校验和。

  3）Secondary NameNode（2nn）：一是镜像备份，二是日志与镜像的定期合并。两个过程同时进行，称为checkpoint。镜像备份的作用：备份fsimage镜像（fsimage是元数据发送检查点时写入文件）；日志与镜像的定期合并的作用：将Namenode中edits日志和fsimage合并，防止集群二次耗时过长。（如果Namenode节点故障，namenode下次启动的时候，会把fsimage加载到内存中，应用edit log，edit log往往很大，导致操作很耗时。）

#### 2.1.5.2 YARN概述

​	Yet Another Resource Negotiator 简称YARN，另一种资源协调者，是Hadoop的资源管理器。

- 架构概述

  ​	1）ResourceManager（RM）：整个集群资源（内存、CPU等）的老大

  ​	2）NodeManager（NM）：单个节点服务器资源老大

  ​	3）ApplicationMaster（AM）：单个任务运行的老大

  ​	4）Container：容器，相当一台独立的服务器，里面封装了任务运行所需要的资源，如内存、CPU、磁盘、网络等。

说明1：客户端可以有多个。

说明2：集群上可以运行多个ApplicationMaster。

说明3：每个NodeManager上可以有多个Container。

#### 2.1.5.3 MapReduce概述 

​	MapReduce将计算过程分为两个阶段：Map和Reduce

​	1）Map阶段并行处理输入数据。

​	2）Reduce阶段对Map结果进行汇总。

#### 2.1.5.4 HDFS、YARN、MapReduce三者关系

​	当客户提交一个任务时，ResourceManager就会找一个节点开启Container，然后把任务放在这，名曰ApplicationMaster。ApplicationMaster会和ResourceManager申请这个任务需要运行多少资源，有对应资源的节点开启MapTask。每一个Maptask独立工作返回一个结果。最后将所有MapTask结果汇总写到HDFS上，这就是ReduceTask。将结果写到HDFS，NameNode又进行了一个记账操作，DataNode负责存储操作，Secondary NameNode也记录一部分数据，只是有一点时间偏差。

