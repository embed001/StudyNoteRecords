

大数据学习笔记
1.	数据存储中 hadoop和mpp之间的核心区别点有哪些
条目	Hadoop	MPP
设计目标与数据模型	设计初衷是为了处理海量的非结构化和半结构化数据，如文本、日志、图像、视频等。其核心组件包括Hadoop Distributed File System (HDFS) 和 MapReduce（或更现代的计算框架如 Apache Spark）。HDFS 提供了一个高度容错的分布式文件系统，用于存储大文件，并将文件切分成块分布在整个集群中。数据在Hadoop中通常是原始的、未经预处理的，需要通过应用程序或框架（如Hive、Pig、Spark SQL等）进行进一步的结构化处理和分析。	主要针对结构化数据，尤其是用于在线分析处理（OLAP）和数据仓库场景。MPP系统本质上是一种分布式数据库，它设计为高效地执行复杂的SQL查询。MPP数据库将数据分布到多个节点上，每个节点包含部分完整的数据库副本，并具有独立的处理能力。数据在MPP系统中通常是预先清洗、集成和模式化的，可以直接用SQL进行查询。
数据存储格式与访问方式	HDFS上的数据通常以二进制格式存储，如CSV、JSON、Avro、Parquet等，可以根据应用需求选择合适的格式。数据访问通常需要编写MapReduce作业、Spark程序或使用Hive等SQL-like查询引擎，这些工具会将高级查询转换为对HDFS上数据的操作。	数据以表格形式存储在关系型数据库中，遵循严格的schema定义，支持标准SQL查询。MPP数据库内部可能使用特定的列式存储、压缩和其他优化技术来提高查询性能。用户可以直接使用SQL语句进行交互式查询，无需编写复杂的编程代码。
数据分区与索引策略	HDFS本身并不提供数据分区和索引机制，而是依赖于上层应用（如Hive的表分区、HBase的键值存储）来实现数据组织和快速访问。这些策略通常需要根据业务逻辑手动设置，并且在处理复杂查询时可能需要全表扫描或大量数据移动。	MPP数据库内置了高级的数据分区和索引功能，可以根据数据属性自动或手动划分数据，优化数据分布和查询路由。这使得MPP系统能够更高效地处理复杂的联接查询和聚合操作，减少数据移动和I/O开销。
数据一致性与事务支持	HDFS提供强一致性保证，即一旦一个文件块被成功写入，后续读取一定能获取到最新版本。然而，Hadoop生态系统中的数据处理框架（如MapReduce、Spark）通常不支持实时的ACID事务，对于需要保证数据一致性和隔离性的复杂更新操作，通常需要额外的工具（如Apache HBase、Apache Flink）或设计特定的工作流来实现。	MPP数据库通常支持完整的ACID事务特性，确保在并发环境下的数据一致性、隔离性和持久性。这对于需要复杂更新操作、多表关联更新或实时数据分析的场景至关重要。
系统管理和优化	由于其开放、模块化的设计，Hadoop集群的管理和优化往往涉及到众多组件的配置、监控和调优，如HDFS、YARN、各个计算框架等。这需要专业的运维知识和工具链支持，但同时也提供了高度的灵活性和定制化能力。	MPP数据库作为一个一体化的解决方案，通常提供更为直观和集中的系统管理界面，包括性能监控、查询优化、自动统计收集等功能。虽然可能牺牲了一定的定制自由度，但通常简化了运维工作，更适合企业级数据仓库的部署和管理。

2.	联邦数据架构
联邦数据架构主要关注如何在分布式、异构且可能自治的数据环境中实现数据的集成、管理和分析。其核心知识要点包括：
2.1.	数据分布与异构性
2.1.1.	分布性
联邦数据架构涉及多个地理位置分散、管理上独立的数据源（如数据库、数据仓库、API等），这些数据源通常由不同的组织或部门所有，各自拥有独特的数据集。
2.1.2.	异构性
数据源在技术栈、数据模型、数据格式、语义定义等方面可能存在显著差异。异构性不仅包括结构异构（如关系型与非关系型数据库），还包括语义异构（即相同概念在不同数据源中有不同的表示）。
2.2.	逻辑集成与虚拟化
2.2.1.	数据联邦
通过逻辑层将分散的数据源统一视作一个整体，提供单一的访问接口。用户或应用程序可以通过联邦查询语言（FQL）对多个数据源进行联合查询，仿佛它们是一个单一的数据库。
2.2.2.	元数据管理
维护全局的元数据目录，记录各个数据源的位置、结构、更新频率等信息，为联邦查询解析、路由和优化提供支持。
2.2.3.	数据虚拟化
在不移动或复制原始数据的情况下，通过中间件创建逻辑视图或数据服务，屏蔽底层数据源的复杂性和差异性。
2.3.	自治性与协同
2.3.1.	自治性
各成员数据源保持自身的管理权限和操作规则，可以独立进行更新、备份和维护，无需完全服从中央控制。
2.3.2.	协同机制
联邦数据架构通过协议、标准和中间件协调跨数据源的操作，如事务处理、并发控制、数据一致性保证等。
2.4.	安全性与隐私保护
2.4.1.	访问控制
实施细粒度的身份验证、授权和审计机制，确保只有合法用户和应用程序能够访问特定数据源。
2.4.2.	数据脱敏与匿名化
在必要时对敏感数据进行处理，减少直接暴露个人身份信息的风险。
2.4.3.	隐私增强技术
如差分隐私、同态加密、安全多方计算（MPC）等，用于在数据共享和联合分析过程中保护数据隐私。
2.5.	性能与可扩展性
2.5.1.	查询优化
联邦查询处理器需要考虑数据分布、数据量、网络延迟等因素，进行智能路由、局部查询优化和结果合并。
2.5.2.	缓存与预计算
利用缓存技术存储常用查询的结果，或预先计算聚合数据以加速响应时间。
2.5.3.	弹性扩展
随着数据规模和查询负载的增长，联邦架构应能动态调整资源分配，适应新的数据源或增加处理能力。


联邦数据中心提供paas+daas+daap，标准工具，标准中心数据，标准数据管理服务，分布式数据中心提供daas+daap
联邦数据中心，提供管理能力，统筹能力，治理能力等
分布式数据中心，提供业务数据，授权管理，数据产品，数据服务
数据管理，最开始是分散式数据中心，后来是集中式的中台，现在是联邦式分布式平台
中台的人做了个对比汇报，领导给了联邦管理概念
结合datamesh,datafabric的理念，建设分布式逻辑数据平台
倡导noetl，nomkt的理念
分布式逻辑平台为纽带，实现数据管理，数据治理，数据认责，数据授权，管理数据的开发过程和生命周期
他们说有点像边缘计算，有点像领域驱动
为了提升数据价值和数据寻址，提出数据市场概念，实现daap理念，让领域专家治理和管理领域数据，对外提供成熟稳定数据产品
在数据市场形成标准和竞争
通过领域驱动，市场驱动，动态完善数据体系，数据治理
核心是主动治理与逻辑平台
金融行业，从业资格证，还有一些高级的cfa，frm资格证书，稍微了解下。
从业资格证，一级的讲的是金融基础+金融法律


3.	大数据训练集中营

3.1.	HDFS2.0比HDFS1.0的更正
 
3.2.	HA设计之脑裂的解决
 

QJM，Quorum Journal Manager，由JournalNode(JN)组成,一般是奇数点结点组成。
  
 
 


 
3.3.	HDFS Federation  HDFS联邦
   
 
 

3.4.	Hadoop集群安装
3.4.1.	Docker镜像方式运行
个人电脑上安装Docker App
https://docs.docker.com/get-dockder

拉取hadoop-docker镜像
docker pull sequencyiq/Hadoop-docker

启动Container
Docker run –p 50070:50070 –p 9000:9000 –p 8088:8088 -it sequenceiq/Hadoop-docker  /etc/bootstrap.sh  -bash



在Hadoop上跑一些作业
Cd $HADOOP_PREEFIX
bin/Hadoop jar share/Hadoop/mapreduce/Hadoop-mapreduce-examples-2.7.0.jar grep input output ‘dfs[a-z]+’
bin/hadfs dfs –cat output/*
 
 
 
3.4.2.	CDH方式安装
https://docs.cloudera.com/documentation/enterprise/latest/topics/installation.html
https://www.jianshu.com/p/610cce9f9026
图形化模式，界面操作。

3.5.	HDFS常用命令总结
hdfs dfs -ls /
hdfs dfs -ls -R /
hdfs dfs -lsr /
hdfs dfs -ls -R /tmp/
hdfs dfs -lsr /tmp/
hdfs dfs -mkdir /test/test2
hdfs dfs -mkdir hdfs://49.2.1.1/test/test123/
hdfs dfs -mkdir -p /test/test3/test4/
hdfs dfs -moveFromLocal /opt/hadoop/servers/test/hellow.txt /test/test123
hdfs dfs -moveToLocal /test/test123/hellow.txt /opt/hadoop/servers/
moveToLocal: Option ‘-moveToLocal’ is not implemented yet
hdfs dfs -mv /test/test123/ /test/test2/
hdfs dfs -put /opt/hadoop/servers/test/ /tmp/
hdfs dfs -cat /test/test2/test123/hellow.txt
hdfs dfs -appendToFile /opt/hadoop/servers/test/aa.txt /test/test2/test123/hellow.txt
hdfs dfs -appendToFile /opt/hadoop/servers/test/dd.txt /opt/hadoop/servers/test/cc.txt /test/test2/test123/hellow.txt
hdfs dfs -cp /test/test2/test123/hellow.txt /test/
hdfs dfs -rm /test/test2/test123/hellow.txt
hdfs dfs -rm -r /test
hdfs dfs -chmod -R -777 /
格式   hdfs dfs  -get [-ignorecrc ]  [-crc]  <src> <localdst>
hdfs dfs  -get   /install.log  /export/servers
格式: hdfs dfs -chmod [-R] URI[URI …]
hdfs  dfs  -chown  -R hadoop:hadoop  /install.log



1.查看hdfs下根目录下的文件
hdfs dfs -ls /

2.查看hdfs某个目录下的所有文件结构：
如：查看根目录所有文件结构
hdfs dfs -ls -R /
hdfs dfs -lsr /

如：查看根文件tmp下的所有文件列表
hdfs dfs -ls -R /tmp/
hdfs dfs -lsr /tmp/

3.创建文件夹
如：在根文件的test目录下，创建test2
hdfs dfs -mkdir /test/test2
或者：
hdfs dfs -mkdir hdfs://49.2.1.1/test/test123/

4.创建文件夹 - 递归创建文件夹
hdfs dfs -mkdir -p /test/test3/test4/

5.本地文件移动上传hdfs某个目录：
如：
hdfs dfs -moveFromLocal /opt/hadoop/servers/test/hellow.txt /test/test123

6.hdfs文件移动到本地：
如：
hdfs dfs -moveToLocal /test/test123/hellow.txt /opt/hadoop/servers/
出现了一下问题：
moveToLocal: Option ‘-moveToLocal’ is not implemented yet

7.hdfs内部进行文件移动
hdfs dfs -mv /test/test123/ /test/test2/

8.将本地文件放到hdfs某个目录：

hdfs dfs -put /opt/hadoop/servers/test/ /tmp/

9.查看hdfs上某个文件的内容：
hdfs dfs -cat /test/test2/test123/hellow.txt

10.追加一个或者多个文件到hdfs指定文件中.也可以从命令行读取输入
如：追加本地aa.txt 到hdfs 上的 hellow.txt中
hdfs dfs -appendToFile /opt/hadoop/servers/test/aa.txt /test/test2/test123/hellow.txt

如：追加本地bb.txt cc.txt 到hdfs 上的 hellow.txt中：
hdfs dfs -appendToFile /opt/hadoop/servers/test/dd.txt /opt/hadoop/servers/test/cc.txt /test/test2/test123/hellow.txt

11.hdfs间文件拷贝：复制文件(夹)，可以覆盖，可以保留原有权限信息
hdfs dfs -cp /test/test2/test123/hellow.txt /test/

12.hdfs删除某个文件
hdfs dfs -rm /test/test2/test123/hellow.txt

13.hfds递归删除
hdfs dfs -rm -r /test

14.hdfs赋予文件夹权限
hdfs dfs -chmod -R -777 /

get
格式   hdfs dfs  -get [-ignorecrc ]  [-crc]  <src> <localdst>

作用：将文件拷贝到本地文件系统。 CRC 校验失败的文件通过-ignorecrc选项拷贝。 文件和CRC校验和可以通过-CRC选项拷贝

hdfs dfs  -get   /install.log  /export/servers

15.chown
格式: hdfs dfs -chmod [-R] URI[URI …]
作用： 改变文件的所属用户和用户组。如果使用 -R 选项，则对整个目录有效递归执行。使用这一命令的用户必须是文件的所属用户，或者超级用户。

hdfs  dfs  -chown  -R hadoop:hadoop  /install.log

3.6.	JavaAPI

 
Http://hadoop.apache.org/docs/current/api/org/apache/hadoop/fs/FileSystem.html

3.7.	HDFS高级功能
3.7.1.	RBF
RBF（(Router-based Federation，基于路由的Federation方案)。
 
主要组件介绍
Router（无状态）
一个系统中可以包含多个 Router，每个 Router 包含两个作用：
• 为客户端提供单个全局的 NameNode 接口，并将客户端的请求转发到正确
子集群中的 Active NameNode 上。
• 收集 NameNode 的心跳信息，报告给 State Store，这样 State Store 维护
的信息是实时更新的。

State Store（ 分布式）
在 State Store 里面主要维护以下几方面的信息：
• 子集群的状态，包括块访问负载、可用磁盘空间、HA 状态等；
• 文件夹/文件和子集群之间的映射，即远程挂载表；
• Rebalancer 操作的状态；
• Routers 的状态。

RBF 访问流程
• 客户端向集群中任意一个 Router 发出某个文件的读写请求操作；
• Router 从 State Store 里面的 Mount Table 查询哪个子集群包含这个文件，并从 State Store 里面的
Membership table 里面获取正确的 NN；
• Router 获取到正确的 NN 后，会将客户端的请求转发到 NN 上，然后也会给客户端一个请求告诉它需要请
求哪个子集群；
• 此后，客户端就可以直接访问对应子集群的 DN，并进行读写相关的操作。
 
3.7.2.	异构存储
对需要频繁访问的数据我们称之为“热”数据，反之我们称之为“冷”数据，而处于中间 的数据我们称之为“温”数据。 那么如何定义数据为冷热呢？eBay 内部根据数据年龄和 使用频率来定义：
 
Hadoop 从 2.6.0 版本开始支持异构存储：
 
 
3.8.	MapReduce的编程模式
 
3.8.1.	MRv1
3.8.1.1.	数据切分，Split 
主要用于确定 InputSplit 的个数以及每个 InputSplit 对应的数据段。
• 一个大文件会被切分成若干个 InputSplit
• 对文件的切分是按照“固定”大小进行的，这个大小就是 split size
• splitSize=max{ minSize, min{ totalSize / numSplits, blockSize } }
• numSplits 为用户设定的 Map Task 个数，默认情况下是 1。
• minSize 为 Split 的最小值，由配置参数确定，默认是 1。
• blockSize 为 HDFS 中的 block 大小 ，默认是 64MB。
• 一旦确定 splitSize 值后，将文件依次切成大小为 splitSize 的 InputSplit
• 最后剩下不足 splitSize 的数据块单独成为一个 InputSplit

3.8.1.2.	Host选择算法
• InputSplit 对象包含 4 个属性：文件名、起始位置、Split 长度、节点列表，构成一个四元组<file, start, length, hosts>。
• 其中，节点列表是关键，关系到任务的本地性（locality）。本地性即优先让空闲资源处理本节点上的数据，如果节点上没有可处理的数据，则处理同一个机架上的数据，最差情况是处理其他机架上的数据。
• Hadoop 将数据本地性按照代价划分成三个等级：Node、Rack、Any。

3.8.1.3.	排序Sort
MapReduce 的 Sort 分为两种：
• Map Task 中 Spill 数据的排序
• 数据写入本地磁盘之前，先要对数据进行一次本地排序
• 快排算法
• 先按分区编号 partition 进行排序，然后按 key 进行排序。
经过排序后，数据以分区为单位聚集在一起，且同一分区
内所有数据按照 key 有序
• ReduceTask 中数据排序
• Reduce Task 对所有数据进行排序
• 归并排序算法
• 小顶堆
• Sort 和 Reduce 可并行进行

3.8.2.	MapReduce的计算框架
MapReduce 分布式计算框架：
• JobTracker：
• 负责集群资源监控和作业调度
• 通过心跳监控所有 TaskTracker 的健康状况
• 监控 Job 的运行情况、执行进度、资源使用，交由任务调度器负责资源分配
• 任务调度器可插拔：FIFO Scheduler、Capacity Scheduler、Fair Scheduler
• TaskTracker：
• 具体执行 Task 的单元
• 以 Slot 为单位等量划分本节点的资源，分为 Map Slot 和 Reduce Slot
• 通过心跳周期性向 JobTracker 汇报本节点的资源使用情况和任务运行进度
• 接收 JobTracker 的命令执行相应的操作（启动新任务、杀死任务等）
• Client：
• 提交用户编写的程序到集群
• 查看 Job 运行状态

3.8.3.1.	MapReduce原理概述
 
• 作业提交与初始化
• 首先 JobClient 将作业的相关文件上传到 HDFS
• 然后 JobClient 通知 JobTracker
• JobTracker 的作业调度模块对作业进行初始化（ JobInProgress 和 TaskInProgress）
• 任务调度与监控
• JobTracker 的任务调度器（TaskScheduler）按照一定策略，将 task 调度到空闲的
TaskTracker
• 任务 JVM 启动
• TaskTracker 下载任务所需的文件，并为每个 Task 启动一个独立的 JVM
• 任务执行
• TaskTracker 启动 Task，Task 通过 RPC 将其状态汇报给 TaskTracker，再由
TaskTracker 汇报给 JobTracker
• 完成作业
• 数据写到 HDFS
3.8.3.2.	JobTracker 核心功能
— 资源管理
JobTracker 不断接收各个 TaskTracker 周期性发送过来的资源量和任务状态等信息，为 TaskTracker 分配最合适的任务。
Hadoop 引入了“slot”概念表示各个节点上的计算资源。为了简化资源管理，Hadoop 将各个节点上 的资源(CPU、内存和磁盘等)等量切分成若干份，每一份用一个 slot 表示，同时规定一个 Task 可根据 实际需要占用多个 slot。
三级调度模型： • 选择一个队列 • 选择一个作业 • 选择一个任务
3.8.3.3.	TaskTracker 核心功能
TaskTracker 核心功能介绍 — 心跳机制
• 心跳是 Jobtracker 和 Tasktracker 的桥梁，它实际上是一个 RPC 函数，Tasktracker 周期性的调用
该函数汇报节点和任务状态信息，从而形成心跳。
• 在 Hadoop 中，心跳主要有三个作用：
• 判断 Tasktracker 是否活着
• 及时让 Jobtracker 获取各个节点上的资源使用情况和任务运行状态
• 为 Tasktracker 分配任务
• Tasktracker 周期性的调用 RPC 函数 heartbeat 向 Jobtracker 汇报信息和领取任务，函数定义是：
HeartbeatResponse heartbeat(TaskTrackerStatus status, boolean restarted, boolean initialContact, boolean acceptNewTasks, short responseId)

3.8.3.4.	Hadoop1.0 到 Hadoop2.0
 
2013 年，Hadoop 2.0 发布，引入 YARN、HDFS HA、Federation。

3.9.	Yarn
3.9.1.	YARN 的基本设计思想
3.9.1.1.	YARN 的基本组成
ResourceManager：全局的资源管理器，负责整个系统的资源管理和分配
• 处理客户端请求
• 启动/监控 ApplicationMaster
• 监控 NodeManager
• 资源分配和调度
NodeManager：驻留在一个 YARN 集群中的每个节点上的代理
• 单个节点的资源管理
• 处理来自 ResourceManger 的命令
• 处理来自 ApplicationMaster 的命令
ApplicationMaster：应用程序管理器，负责系统中所有应用程序的管理工作
• 数据切分
• 为应用程序申请资源，并进行分配
• 任务监控和容错
 

3.9.1.2.	Hadoop 2.0 技术栈
 
3.9.2.	资源调度器
3.9.2.1.	资源调度算法
FIFO 先进先出调度（ FIFO）
SJF 短任务优先（Shortest Job First，SJF）
RR  时间片轮转算法（Round Robin，RR）
最大最小公平调度（Min-Max Fair）
加权最大最小公平调度（Weighted Min-Max Fair）
容量调度（Capacity）
3.9.2.2.	YARN的三种调度器
FIFO Scheduler（先进先出调度器）
Capacity Scheduler（容量调度器）
Fair Scheduler（公平调度器）
3.9.3.	Yarn的高级特性
3.9.3.1.	Node Label
目前只有 Capacity Scheduler 支持该功能。Fair Scheduler 开发进行中（YARN-2497）
• Node Label 有两种类型：
• 节点分区（Node Partition）
• 一个节点只属于一个分区
• 和资源计划有关
• 节点限制 （Node Constraints）
• 一个节点可以分配多个限制条件
• 和资源计划无关
3.9.3.2.	Yarn HA
RM 存在单点故障问题。YARN 的 HA 架构和 HDFS HA 类似，需要启动两个ResourceManager，这两个 ResourceManager 会向 ZooKeeper 集群注册，通过 ZooKeeper 管理它们的状态（Active 或 Standby）并进行自动故障转移。
   
3.9.3.3.	Yarn Federation
对于 HDFS 的扩展性问题来说，我们讲过了 HDFS federation 的几种方案，通过横向扩展命名空间的做法来延展其扩展性。 随着集群规模的扩张，不仅仅存储系统会有性能瓶颈问题，计算系统也会存在这样的问题。YARN 的 ResourceManager 在管理这上千甚至上万个 NodeManager 节点时，会面临着许多性能问题，此外，大量在跑的应用会产生大量的event，也同样加重了 RM 的性能问题。一般这种情况，我们的一个简单直接的方案是再搭建一个新的 YARN 集群。
对于不方便增加一个 YARN 集群的情况来说，目前社区提供了一套类似 HDFS
Federation 的方案：
• YARN Federation
• 涉及的主要有以下两大模块:
• 多集群状态信息收集，存储（State Store）
让众多独立小集群变为逻辑意义上的一个超大资源池
• 客户端请求路由服务实现（Proxy Store）
对于客户端来说，它面对的将不是众多具体的独立小集群
   
3.10.	Hbase
3.10.1.	HBase 概述
HBase是Hadoop的一部分
 
3.10.1.1.	什么是分片分组
分片(sharding)：分片解决扩展性问题，属于水平拆分。
 
分组(group)：分组解决可用性问题，分组通常通过主从复制（replication）的方式实现。
 
分片+分组：互联网公司数据库的实际软件架构（大数据量下）
 
3.10.1.2.	什么是分库分表
分表
分表从表面意思看就是把一张表分成多个小表，分表解决的是数据量过大的问题。关系型数据库在大于一定数据量的情况下检索性能会急剧下降。在面对互联网海量数据情况时，所有数据都存于一张表，显然会轻易超过数据库单表可承受的数据量阈值。这个单表可承受的数据量阈值，需根据数据库和并发量的差异，通过实际测试获得。
分库
分库解决的是数据库性能瓶颈的问题。
单纯的分表虽然可以解决数据量过大导致检索变慢的问题，但无法解决过多并发请求访问同一个库，导致数据库响应变慢的问题。所以通常水平拆分都至少要采用分库的方式，用于一并解决大数据量和高并发的问题。这也是部分开源的分片数据库中间件只支持分库的原因。
3.10.1.3.	分库分表带来的问题
分布式事务：
但分表也有不可替代的适用场景。最常见的分表需求是事务问题。同在一个库则不需考虑分布式事务，善于使用同库不同表可有效避免分布式事务带来的麻烦。目前强一致性的分布式事务由于性能问题，导致使用起来并不一定比不分库分表快。目前采用最终一致性的柔性事务居多。
跨库跨表的 join 问题：
原本一次查询能够完成的业务，可能需要多次查询才能完成。

Google 的分布式数据库之路
 

HBase 和 RDBMS 的不同
 
3.10.2.	HBase 的逻辑视图
 
行键+列键+时间戳
3.10.3.	HBase 的物理视图
Key-Value
 
逻辑VS物理
 
3.10.4.	HBase 整体架构
功能组件
 
HBase Master服务器
 
HBase Region服务器
 
 

HMaster
每台 Region Server 都会与 Master 进行通信，HMaster 的主要任务就是告诉 RegionServer 它需要维护哪些 Region，具体功能如下：
1.管理用户对表的增删改查操作；
2.管理 Region Server 的负载均衡，动态调整 Region 分布；
3.在 Region 分裂后，负责新的 Region 的分配；
4.在 Region Server 停机后，负责失效 Region Server 上的 Region 的迁移；

Region Server：由多个 Store 组成，HBase 使用表存储数据集，当表的大小超过设定的值时，HBase 会自动将表划分为不同的 Region，它是 HBase 集群上分布式存储和负载均衡的最小单位。
Store：由两部分组成：MemStore 和 StoreFile。首先用户写入的数据存放到MemStore 中，当 MemStore 满了后刷入 StoreFile。

预写日志，HLog:Write ahead log(WAL*)是数据库系统中常见的一种手段，用于保证数据操作的原子性和持久性。
 

HFile
 

 
   

3.10.5.	HBase API 和实验
 
 
 

下载 Docker 镜像
docker pull harisekhon/hbase
在 Docker 中启动 HBase
docker run -d -p 2181:2181 -p 8080:8080 -p 8085:8085 -p 9090:9090 -p 9095:9095 –p 16000:16000 -p 16010:16010 -p 16201:16201 -p 16301:16301 -p 16030:16030 –p 16020:16020 --name jikeshijian_hbase harisekhon/hbase
测试启动效果
访问http://localhost:16010
进入 HBase 的 Docker容器
docker exec -it jikeshijian_hbase bash
进入 HBase Shell
hbase shell

HBase操作练习，Shell
HBase（HBase shell 进入）：
（1）创建命名空间：create_namespace 'jinlantao_test'
（2）创建表：create 'test1', {NAME => 'f1', VERSION => 2}
（3）查询命名空间：list_namespace
（4）列出表：list
（5）创建表：create 'jinlantao_test:test1', 'cf1'
（6）描述表：describe 'jinlantao_test:test1'
（7）插入数据：put 'jinlantao_test:test1', 'r1', 'cf1:c1', 'value'
（8）获取数据：get 'jinlantao_test:test1', 'r1'
（9）扫描表：scan 'jinlantao_test:test1'
（10）统计总数：count 'jinlantao_test:test1'
（11）删除数据：delete 'jinlantao_test:test1', 'r1', 'cf1:c1'
（12）禁止表：disable 'jinlantao_test:test1'
（13）删除表：drop 'jinlantao_test:test1'

API实践：
【代码文件】BaseTest.java
源文件github地址： 
https://github.com/LantaoJin/HbaseTest/blob/master/src/main/java/org/geekbang/bigdata/hbase/BaseTest.java

package org.geekbang.bigdata.hbase;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.*;
import org.apache.hadoop.hbase.client.*;
import org.apache.hadoop.hbase.util.Bytes;

import java.io.IOException;
public class BaseTest {
    public static void main(String[] args) throws IOException {
        // 建立连接
        Configuration configuration = HBaseConfiguration.create();
        configuration.set("hbase.zookeeper.quorum", "127.0.0.1");
        configuration.set("hbase.zookeeper.property.clientPort", "2181");
        configuration.set("hbase.master", "127.0.0.1:60000");
        Connection conn = ConnectionFactory.createConnection(configuration);
        Admin admin = conn.getAdmin();
        TableName tableName = TableName.valueOf("user_t");
        String colFamily = "User";
        int rowKey = 1;
        // 建表
        if (admin.tableExists(tableName)) {
            System.out.println("Table already exists");
        } else {
            HTableDescriptor hTableDescriptor = new HTableDescriptor(tableName);
            HColumnDescriptor hColumnDescriptor = new HColumnDescriptor(colFamily);
            hTableDescriptor.addFamily(hColumnDescriptor);
            admin.createTable(hTableDescriptor);
            System.out.println("Table create successful");
        }
        // 插入数据
        Put put = new Put(Bytes.toBytes(rowKey)); // row key
        put.addColumn(Bytes.toBytes(colFamily), Bytes.toBytes("uid"), Bytes.toBytes(001)); // col1
        put.addColumn(Bytes.toBytes(colFamily), Bytes.toBytes("name"), Bytes.toBytes("Tom")); // col2
        conn.getTable(tableName).put(put);
        System.out.println("Data insert success");
        // 查看数据
        Get get = new Get(Bytes.toBytes(rowKey));
        if (!get.isCheckExistenceOnly()) {
            Result result = conn.getTable(tableName).get(get);
            for (Cell cell : result.rawCells()) {
                String colName = Bytes.toString(cell.getQualifierArray(), cell.getQualifierOffset(), cell.getQualifierLength());
                String value = Bytes.toString(cell.getValueArray(), cell.getValueOffset(), cell.getValueLength());
                System.out.println("Data get success, colName: " + colName + ", value: " + value);
            }
        }
        // 删除数据
        Delete delete = new Delete(Bytes.toBytes(rowKey));      // 指定rowKey
        conn.getTable(tableName).delete(delete);
        System.out.println("Delete Success");
        // 删除表
        if (admin.tableExists(tableName)) {
            admin.disableTable(tableName);
            admin.deleteTable(tableName);
            System.out.println("Table Delete Successful");
        } else {
            System.out.println("Table does not exist!");
        }
    }
}
 
Java API流程
 

3.10.6.	HBase Compaction
为什么需要 Compaction
前面提到，HBase 的 MemStore 在满足阈值的情况下会将内存中的数据刷写成 HFile，一个MemStore 刷写就会形成一个 HFile。随着时间的推移，同一个 Store 下的 HFile 会越来越多，文件太多会影响 HBase 查询性能，主要体现在查询数据的 I/O 次数增加。为了优化查询性能，HBase 会合并小的 HFile 以减少文件数量，这种合并 HFile 的操作称为Compaction，这也是为什么要进行 Compaction 的主要原因。
1. 将多个小的 HFile 合并成一个更大的 HFile 以增加查询性能。
2. 在合并过程中对过期的数据（超过 TTL，被删除，超过最大版本号）进行真正的删除。

Major Compaction 和 Minor Compaction
• Minor Compaction ：会将邻近的若干个 HFile 合并，在合并过程中会清理 TTL 的数据，但不会清理被删除的数据。
• Major Compaction：会将一个 store 下的所有 HFile 进行合并，并且会清理掉过期的和被删除的数据，即在 Major Compaction 会删除全部需要删除的数据。值得
注意的是，一般情况下，Major Compaction时间会持续比较长，整个过程会消耗大量系统资源，对上层业务有比较大的影响。因此，生产环境下通常关闭自动触发Major Compaction 功能，改为手动在业务低峰期触发。

Compaction示意图：
 

如何决定哪些 HFile 需要 Minor Compaction
首先内存中维护着一个 filesToCompact（合并队列），在该队列中的 HFile 将会被 Minor 合并。当有新的 HFile 文件产生时，如果同一个列簇下的文件数大于等于hbase.hstore.compaction.min 时，就会将符合合并规则的文件放入合并队列，合并规则如下：
• 如果该文件小于 hbase.hstore.compaction.min.size， 则一定会被添加到合并队列中。
• 如果该文件大于 hbase.hstore.compaction.max.size，则一定会从队列中被排除。
• 如果该文件小于它后面 hbase.hstore.compaction.max（默认为 10）个文件之和乘hbase.hstore.compaction.ratio（默认为 1.2），则该文件也将加入到合并队列中。

HBase 调优
GC 调优
• GC 算法选择
• 参数调整
存储调优（HDFS）
• Linux系统参数（网络，内存，I/O）
• Short-Circuit Read
• Data Locality
表结构调优
• Row Key 设计
• 列族设计
 

 


HBase RIT
Region-In-Trasition 是 HBase 的一种变迁机制。
RIT 问题是指例如在 Region 状态的变迁过程中（merge、split、assign、unssign 等操作），出现了问题。然后导致 region 的状态一直保持在 RIT， HBase 出现异常。一般遇到 HBase table 进入 RIT 怎么解决：
1. 当在 HBase webui看到某个表某个 region 进入 RIT 时，可以重启该 region 所在节点进行恢复。
2. 停止 HBase 集群删除 ZooKeeper 上的 /hbase 节点，重启集群进行恢复。
3. 重启不能恢复时，就需要查看 HBase日志了，检查 HDFS 文件是否异常，修复 HDFS 文件异常，通过 HBasehbck 命令进行修复。
4. Region Server 内存太小也会导致 table 进入 RIT，加大 Region Server 内存解决，测试环境就碰到过这个问题。
5. 暴力删除异常 table 或 table 部分受损的数据分区，通过删除 HDFS上 /hbase 下的目录文件，修复 HBasemeta，这种方式会丢失数据。



3.10.7.	HBase 的高可用性和灾备
 

Region Server 故障恢复
发现：
HBase 检测宕机是通过 ZooKeeper 实现的， 正常情况下 Region Server 会周期性向ZooKeeper 发送心跳，一旦发生宕机，心跳就会停止，超过一定时间（SessionTimeout）ZooKeeper 就会认为 Region Server 宕机离线，并将该消息通知给 Master。
HLog 切分：
一台 Region Server 只有一个 HLog 文件，即所有 Region 的日志都是混合写入该HLog 的，然而，回放日志是以 Region 为单元进行的，因此在回放之前首先需要将HLog 按照 Region 进行分组，这个分组的过程就称为 HLog 切分。
HLog 回放：
重新回放 HLog，写入 MemStore，实际上就是 HBase 写入的过程。
 

HMaster HA
很少有人提 HMaster 发生故障时如何恢复，其实 HMaster 是有 HA 的，即主备模式。同一时间只有一个 HMaster 能成功在 ZooKeeper 中注册 /hbase/master 节点，为Active 提供服务。因为每台 HMaster 都和 ZooKeeper 之间存在着心跳保持，当 Active HMaster 发生故障时，ZooKeeper 中的 /hbase/master 节点自动删除，其他 HMaster 此时如果成功注册该节点，则变为新的 Active。成为 Active 的 HMaster 需要从 ZooKeeper中加载完相应的数据到内存，就可以提供服务。
 

3.10.8.	HBase 2.x
HBase Read HA
由上图可知，Region 将不再只保存在某一单独的 Region Server 上，而是选择其他的两个 Region Server 分别存储该 Region 的两个备份，这样某台 Region Server 挂掉时，客户端仍然可以从其它 Region Server 上备份的 Region 中读到数据，如此保证了 HBase 的读高可用，可用性达到了 99.99%。
 

In-Memory Compaction
In-Memory Compaction 是 HBase2.0 中的重要特性之一，通过在内存中引入 LSM*结构，减少多余数据，实现降低 flush 频率和减小写放大的效果。
在 2.0 版本中，MemStore 中的数据先 flush 成一个 Immutable 的 Segment，多个Immutable Segments 可以在内存中进行 Compaction，当达到一定阈值以后才将内存中的数据持久化成 HDFS 中的 HFile 文件。
 
堆外内存优化：
 

3.11.	Hive
3.11.1.	Hive 概述
 
Hive 的产生
• Hive 是基于 Hadoop 的一个数据仓库工具；
• 可以将结构化的数据文件映射为一张数据库表，并提供简单的类 SQL（HQL）查询功能，可以将 HQL 语句转换为 MapReduce 任务进行运行；
• 学习成本低，可以通过类 SQL 语句快速实现简单的 MapReduce 统计，不必开发专门的
MapReduce 应用；
• 适合数据仓库的 ETL 和统计分析；
• 由 Facebook 开发并开源，贡献给 Apache 基金会。

Hive 的特点
• 简单易用
• 基于 SQL 表达式语法，兼容大部分 SQL-92 语义和部分 SQL-2003 扩展语义。
• 可扩展
• Hive 基于 Hadoop 实现，可以自由的扩展集群的规模，一般情况下不需要重启服务。
• 延展性
• Hive 支持用户自定义函数，用户可以根据自己的需求来实现自己的函数。
• 容错性
• Hadoop 良好的容错性，节点出现问题 SQL 仍可完成执行。

Hive 适用场景
• 最佳使用场合
• 大数据集的批处理作业，例如：网络日志分析。
• 不适用于
• 不能在大规模数据集上实现低延迟快速的查询，例如：Hive 在几百 MB 的数据集上执行查
询一般有分钟级的时间延迟。
• 不支持联机事务处理（OLTP）
• Hive 不提供基于行级的数据更新操作（2.0 版本开始支持 Update）。

 

Docker-hive 配置
https://hub.docker.com/r/nagasuga/docker-hive
• docker pull nagasuga/docker-hive
• docker run -i -t nagasuga/docker-hive /bin/bash -c 'cd /usr/local/hive && ./bin/hive'



https://github.com/big-data-europe/docker-hive
git clone https://github.com/big-data-europe/docker-hive.git
• docker-compose up -d
• docker-compose exec hive-server bash
• # /opt/hive/bin/beeline -u jdbc:hive2://localhost:10000
• > CREATE TABLE pokes (foo INT, bar STRING);
• > LOAD DATA LOCAL INPATH '/opt/hive/examples/files/kv1.txt' OVERWRITE INTO
TABLE pokes;
• docker-compose down -v

带截图如下：
• git clone https://github.com/big-data-europe/docker-hive.git
（入到docker-hive 目录下，/Users/bytedance/git/github/docker-hive目录下）
• docker-compose up –d
 
• docker-compose exec hive-server bash
• # /opt/hive/bin/beeline -u jdbc:hive2://localhost:10000   （进入到docker后执行）
 
• > CREATE TABLE pokes (foo INT, bar STRING);
• > LOAD DATA LOCAL INPATH '/opt/hive/examples/files/kv1.txt' OVERWRITE INTO
TABLE pokes;
 
• docker-compose down -v
 Hive命令：
 

Hive参数
 
 
3.11.2.	Hive 的基本原理-
 
	
元数据存储
• Hive 将元数据存储在数据库中，如 MySQL、Oracle、Derby。
• Hive 中的元数据包括表的名字、表的列和分区及其属性、表的属性（是否为外部表等）、表的数据所在目录等

驱动器（Driver）
• 编译器
• 完成词法分析、语法分析，将 HQL 查询解析成 AST。
• AST 生成逻辑执行计划。
• 逻辑执行计划生成物理 MR 执行计划。
• 优化器
• 对逻辑执行计划进行优化。
• 对物理执行计划进行优化。
• 执行器
• 生成的物理执行计划转变成 MR Job。
• 提交到 Hadoop 上面执行。


与传统数据库对比
 

部署方式
内嵌模式
• CLI Shell/Driver/MetaStoreCLI 均在一个 JVM 内部。
 

HiveServer2
HiveServer2
• Driver/MetaStoreCLI 均在 ThriftServer 端。
• 提供 JDBC/ODBC 协议。
• 做 Session 隔离，每个 Session 内部共享一个 Driver
 

Remote MetaStoreServer
• MetaStore 作为一个独立的 Thrift 服务，而不是每个 Driver 自己维护一个瘦 MetaStore 访问 DB。
 

Thrift是Facebook于2007年开发的跨语言的rpc服框架，提供多语言的编译功能，并提供多种服务器工作模式；用户通过Thrift的IDL（接口定义语言）来描述接口函数及数据类型，然后通过Thrift的编译环境生成各种语言类型的接口文件，用户可以根据自己的需要采用不同的语言开发客户端代码和服务器端代码。

3.11.3.	HiveQL 详解
这两个需要进一步搞清楚， Join原理， Groupby原理。
3.11.3.1.	Join,Groupby,District实现原理
   

3.11.3.2.	SQL转化为MapReduce 的过程
1. 词法语法解析：Antlr 定义 SQL 的语法规则，完成 SQL 词法，语法解析，将 SQL 转化为抽象语法
树（AST Tree）。
2. 语义解析：遍历 AST Tree，抽象出查询的基本组成单元查询块（QueryBlock）。
3. 生成逻辑执行计划：遍历查询块，翻译为逻辑执行计划，Hive 用操作树（OperatorTree）表示。
4. 优化逻辑执行计划：对操作树进行变换，合并不必要的操作，减少 shuffle 数据量，得到优化过的逻辑执行计划。
5. 生成物理执行计划：遍历操作树，翻译为 MapReduce 任务，即物理执行计划。
6. 优化物理执行计划：继续对物理执行计划进行变换，生成最终的 MapReduce 任务。

3.11.3.3.	Hive编译器
Parser：
1.	将 SQL 转换成抽象语法树。
语法解析器：
2. 将抽象语法树转换成查询块。
逻辑计划生成器：
3. 将查询块转换成逻辑计划
逻辑计划优化器：
4. 优化逻辑计划。
物理计划生成器：
5. 将逻辑计划转换成物理计划
物理计划优化器：
6. 物理计划优化策略
 

3.11.3.4.	Hive内部操作符：
 

3.11.3.5.	SQL 解析细节
简单的讲，SQL 在分析执行时经历如下 4 步：
1. 语法解析
2. 元数据绑定
3. 优化执行策略
4. 交付执行
 

3.11.3.6.	语法解析
语法解析之后，会形成一棵语法树，如下图所示。树中的每个节点是执行的 rule，整棵树称之为执行策略。
 
3.11.3.7.	元数据绑定
QueryBlock 中的 ProductID，Name 等标识符需要在 Catalog 里进行元数据绑定，并识别是否有效。
 
策略优化
形成上述的执行策略树还只是第一步，因为这个执行策略可以进行优化，所谓的优化就是对树中节点进行合并或是进行顺序上的调整。
以大家熟悉的 Join 操作为例，下图给出一个 Join 优化的示例。A JOIN B 等同于 B JOIN A，但是顺序的调整可能给执行的性能带来极大的影响，下图就是调整前后的对比图。 

Hive 中的逻辑查询优化可以大致分为以下几类：
• 投影修剪
• 推导传递谓词
• 谓词下推
• 将 Select-Select，Filter-Filter 合并为单个操作
• 多路 Join
• 查询重写以适应某些列值的 Join 倾斜
	
3.11.3.8.	Hive数值类型
 

字符类型
Hive 支持的字符类型为 String，对应 Java 的 String； 使用引号（双引号或单引号）引用。
VARCHAR
• 变长字符串序列。
• 必须指定一个最大长度，如 VARCHAR(255)，如果超过最大长度，字符串将被截取。
CHAR
• 固定长度字符串序列。
• 比如指定一个固定长度，最大 255. 如 CHAR(10)，如果不足长度，则用空字符补齐。 示例：
• ‘this is a string’
• “this is a string”
• Select count(*) from table where city =“shanghai”

日期时间类型
Hive 支持日期类型，并进行比较计算：
• TIMESTAMP， 兼容 java.sql.Timestamp 格式。
示例：
• 1327882394（Unix 纪元秒），对应 Java Int 类型；
• 1327882394.123456789（Unix 纪元秒和纳秒），对应 Java Float 类型；
• “2016-10-25 12:34:56.123456789”，对应 Java String 类型。

BOOLEAN
• 相当于 Java 的 Boolean 类型
• true/false
BINARY
• 任意字节序列，类似于传统 RDBMS 中的 VARBINARY
• BINARY 字段仅作为二进制字节序列，不被 Hive 解析为基本类型
复合数据类型
• ARRAY/MAP/STRUCT/UNIONTYPE

3.11.3.9.	Hive数据模型
Hive 通过以下模型来组织 HDFS 上的数据 • 数据库（Database）
• 表（Table）
• 分区（Partition）
• 桶（Bucket）

3.11.3.10.	Table 管理表和外表
• Hive 中的表和关系型数据库中的表在概念上很类似。
• 每个表在 HDFS 中都有相应的目录用来存储表的数据。
• 根据数据是否受 Hive 管理，分为：
• Managed Table（管理表）
• External Table（外表）
• 区别：
• Managed Table：
• HDFS 存储数据受 Hive 管理，在统一的路径下:
${hive.metastore.warehouse.dir}/{database_name}.db/{tablename}
• Hive 对表的删除操作影响实际数据的删除。
• External Table：
• HDFS 存储路径不受 Hive 管理，只是 Hive 元数据与 HDFS 数据路径的一个映射。
• Hive 对表的删除操作仅仅删除元数据，实际数据不受影响。

3.11.3.11.	Table 永久表和临时表
• Permanent Table 是指永久存储在 HDFS 之上的表，默认创建表为永久表。
• Temporary Table 是指仅当前 Session 有效的表，数据临时存放在用户的临时目录
下，当前 Session 退出后即删除。
• 临时表比较适合于比较复杂的 SQL 逻辑中拆分逻辑块，或者临时测试。
• 注意：
• 如果创建临时表时，存在与之同名的永久表，则临时表的可见性高于永久表，即对
表的操作是临时表的，用永久表无效；
• 临时表不支持分区。

3.11.3.12.	Partition
• 基于用户指定的分区列的值对数据表进行分区。
• 表的每一个分区对应表下的相应目录，所有分区的数据都是存储在对应的目录中：
• ${hive.metastore.warehouse.dir}/{database_name}.db/{tablename}/{partit
ionkey}={value}
• 分区的优点：
• 分区从物理上分目录划分不同列的数据；
• 用于查询的剪枝，提升查询的效率。
• 可以多级 Partition，即指定多个 Partition 字段，但所有 Partition 的数据不可无限
扩展（多级目录造成 HDFS 小文件过多影响性能）。

3.11.3.13.	Bucket
• 桶作为另一种数据组织方式，弥补 Partition 的短板（不是所有的列都可以作为
Partition Key）。
• 通过 Bucket 列的值进行 Hash 散列到相应的文件中，重新组织数据，每一个桶对应
一个文件。
• 桶的优点：
• 有利于查询优化；
• 对于抽样非常有效。
• 桶的数量一旦定义后，如果更改，只会修改 Hive 元数据，实际数据不会重新组织。

3.11.3.14.	DDL操作
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.] table_name 创建表
SHOW TABLES [IN database_name] [‘identifier_with_wildcards’]; // 列出表
SHOW CREATE TABLE ([db_name.]table_name|view_name); //列出表创建语句
SHOW PARTITIONS table_name; //列出表的所有分区
SHOW COLUMNS (FROM|IN) table_name [(FROM|IN) db_name]; //列出表的所有字段
DESCRIBE [EXTENDED|FORMATTED] table_name //描述表定义
DESCRIBE [EXTENDED|FORMATTED] table_name PARTITION partition_spec; //描述分区定义
DROP TABLE [IF EXISTS] table_name [PURGE]; //删除表
TRUNCATE TABLE table_name [PARTITION partition_spec]; //清空表数据
ALTER TABLE table_name RENAME TO new_table_name; //重命名表
ALTER TABLE table_name SET TBLPROPERTIES table_properties; //修改表属性
ALTER TABLE table_name ADD [IF NOT EXISTS] PARTITION //增加分区
partition_spec [LOCATION ‘location1’] partition_spec [LOCATION ‘location2’] ...;
ALTER TABLE table_name
[PARTITION partition_spec]
ADD|REPLACE COLUMNS (col_name data_type [COMMENT col_comment], ...) [CASCADE|RESTRICT] //增加/替换表字段

hive常用语法大全
https://blog.csdn.net/qq_45396429/article/details/115307726

hive命令大全，实战常运用的sql集合
https://blog.csdn.net/weixin_43992119/article/details/131696411

Hive基本语法和使用
https://blog.csdn.net/qq_40178533/article/details/106436852

3.11.4.	常用存储格式
列存和行存
文件格式如下：
 
RCFile 是 Hive 推出的一种专门面向列的数据格式。它遵循“先按列划分，再垂直划分”的设计理念。当查询过程中，针对它并不关心的列时，它会在 IO 上跳过这些列。

ORC（OptimizedRC File）存储源自于RCFile 这种存储格式。• ORC 在压缩编码，查询性能方面相比RCFile 做了很多优化。• Metadata 用 protobuf 存储，支持schema 的变动，如新增或者删除字段。

Parquet源自于 Google Dremel 系统，Parquet 相当于Google Dremel 中的数据存储引擎。
Apache Parquet 最初的设计动机是存储嵌套式数据，比如 Protocolbuffer，thrift、json 等，将这类数据存储成列式格式，以方便对其高效压缩和编码，且使用更少的 IO 操作取出需要的数据，这也是Parquet 相比于 ORC 的优势，它能够透明地将Protobuf 和 thrift 类型的数据进行列式存储。存储 metadata，支持 schema 变更。

Avro 是一种用于支持数据密集型的二进制文件格式。它的文件格式更为紧凑，若要读取大量数据时，Avro 能够提供更好的序列化和反序列化性能。并且 Avro 数据文件天生是带 Schema 定义的，所以它不需要开发者在API 级别实现自己的 Writable 对象。最近多个 Hadoop 子项目都支持 Avro 数据格式，如 Pig、Hive、Flume、Sqoop 和 HCatalog。

自定义文件格式。
通过继承 InputFormat 和 OutputFormat 来自定义文件格式：
参考 Base64InputFormat 和 Base64OutputFormat 的实现。
https://github.com/cloudera/hive/tree/cdh5.8.0-release/contrib/src/java/org/apache/hadoop/hive/contrib/fileformat/base64。
创建表时指定 InputFormat 和 OutputFormat，来读取 Hive 中的数据。

3.11.5.	高级 SQL 解析

3.11.6.	Hive 性能优化
3.11.3.15.	优化思路
• 编译器优化器优化
采用合理的优化策略，生成高效的物理计划。
• MapReduce 执行层优化
通过 MR 参数优化，提升 Job 运行效率。
• HDFS 存储层优化
采用合理的存储格式和合理的 Schema 设计，降低 IO 瓶颈。

3.11.3.16.	SQL 层优化
• 不必要的 shuffle
• NestLoopJoin
• Window 函数
• 全局排序
• 谓词下推异常
• 数据倾斜
• Join 顺序
• Join 膨胀
• MapJoin
• 特殊 UDF（percentile）

3.11.3.17.	MapReduce 执行层优化
• 并发度控制
• Num_Map_tasks = $inputsize / max($mapred.min.split.size, min($dfs.block.size,
$mapred.max.split.size))
• Num_Reduce_tasks = min($hive.exec.reducers.max ,
$inputsize/$hive.exec.reducers.bytes.per.reducer)
• Job 并行执行
• set hive.exec.parallel=true;
• Task 内存优化
• 本地执行
• set hive.exec.mode.local.auto=true;
• hive.exec.mode.local.auto.inputbytes.max（默认 128MB）
• hive.exec.mode.local.auto.input.files.max（默认 4）
• JVM 重用
• set mapred.job.reuse.jvm.num.tasks=10 //每个 jvm 运行 10 个 task

3.11.3.18.	存储层优化
• 列存储，高压缩比，列剪枝，过滤无用字段 IO
• Orc
• Parquet
• 分区分桶
• 合并输入小文件
• 如果 Job 输入有很多小文件，造成 Map 数太多，影响效率
• set hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat
• 小文件合并
• set hive.merge.mapfiles=true; // map only job 结束时合并小文件
• set hive.merge.mapredfiles=true; // 合并 reduce 输出的小文件
• set hive.merge.smallfiles.avgsize=256000000; //当输出文件平均大小小于该值，启动新 Job 合并文件
• set hive.merge.size.per.task=64000000; //合并之后的每个文件大小

3.11.7.	UDF 和 UADF
函数 /UDF
• 输入一行记录，输出一行记录
• 示例：upper/lower/length
聚集函数 /UDAF
• 输入多行记录，输出一行记录
• 示例：sum/count/avg
表生成函数 /UDTF
• 输入一行记录，输出多行记录
• 示例：explode

查看函数
SHOW FUNCTIONS;
DESCRIBE FUNCTION <function_name>; DESCRIBE FUNCTION EXTENDED <function_name>;

3.11.7.1.	Hive 内置函数——数学计算相关
 

Hive 内置函数——集合相关
 

Hive 内置函数——日期相关
 

Hive 内置函数——字符串处理函数
 

Hive 内置聚集函数
 

Hive 内置表生成函数
 

用户自定义函数
虽然 Hive 已经提供了很多内存的函数，但是还是不能，满足用户的需求，因此有提供了 自定义函数供用户自己开发函数来满足自己的需求。
Java 开发，生成 Jar 包。 使用方式：
• ADD JAR /full/path/to/your jar;
• CREATE TEMPORARY FUNCTION func_name AS ‘udf.class.name';
• DROP TEMPORARY FUNCTION IF EXISTS func_name;

3.11.8.	Hcatalog 技术
 

 

3.11.9.	Hive on Tez/Spark

Hive TeZ
 

Hive on Spark
• 优势：
• HQL 不需要做任何变动，无缝的提供了另一种执行引擎支持；
• 有利于与 Spark 的其他模块如 Mllib/Spark Streaming/GragphX 等结合 ；
• 提升了执行效率 。
• 如何使用?
• hive.execution.engine=spark; //使用 Spark 作为执行引擎
• spark.master 默认提交到 YARN
• spark.executor.memory
• spark.executor.cores
• spark.yarn.executor.memoryOverhead
• spark.executor.instances

3.11.10.	构建 Hive 表与数据处理


3.11.11.	Hive 数仓
 
构建数据仓库平台需要考虑的几点
仓库建模：
• ODS、事实表、维度表、数据集市、Cube
• 星型模型、雪花模型
报表展示：
• 如何快速查询？
• 结果如何反哺线上服务？
调度、主数据、权限……

Hive 仓库 ETL 流程：
 
 

3.12.	Spark
3.11.12.	Spark 发展历程和现状
 
目前最新版本为3.2

Spark生态圈：
 
什么是 Spark
Apache Spark™ is a unified analytics engine for large-scale data processing.
• 该项目发起于 2009 年 UC Berkeley
• 贡献给了 Apache 开源基金会（Apache 2.0）
• 最新的版本是：v3.1.2 （2021/06/01）
• 80 万行代码（73% Scala）
• 由来自 355 个组织的超过 1600 名贡献者
• 目前后面支持的商业公司是 Databricks。
 

多部署方式：
 
3.11.13.	RDD 编程基础
 
 
RDD：Resilient Distributed Dataset
DAG：Directed Acyclic Graph，有向无环图
 
 
 

Flume API
val textFile = sc.textFile(”hdfs://data.txt")
val topWordCount = textFile.flatMap(line => line.split(" ")) . map(word => (word, 1))
.reduceByKey(_+_) .collect()

 

3.11.14.	Spark Core 架构和原理
 
 
     

代码源码解释
 
 
3.11.15.	Spark 任务调度


3.11.16.	开发第一个 Spark 程序


3.11.17.	Spark 流式计算基础


3.11.18.	Structured Streaming
 
 
Micro Batch 模式
• 接收实时输入数据流并将数据分成批处理，然后由 Spark 引擎进行处理，以分批生成
最终结果流。
• 称为 DStream Model。
DStream API
高层抽象表示连续的数据流。DStream 中的每个 RDD 都包含来自特定间隔的数据。 应用于 DStream 的任何操作都转换为对基础 RDD的操作。
DStream
pageViews = readStream("http://...", "1s")
ones = pageViews.map(event => (event.url, 1))
counts = ones.runningReduce((a, b) => a + b)
Exactly Once
• 实时计算有三种语义：At-most-once、At-least-once、Exactly-once。
• Exactly-Once 不是指对输入的数据只处理一次，指的是：在流计算引擎中, 算子给下游的结果是 Exactly-Once 的（即给下游的结果有且仅有一个，且不重复、不少算）。
• 在 Spark Streaming 处理过程中，从一个算子（Operator）到另一个算子（Operator），可
能会因为各种不可抗力如机器挂掉等原因，导致某些 Task 处理失败，Spark 内部会基于Lineage 或 Checkpoint 启动重试 Task 去重新处理同样的数据。因不可抗力的存在，流处理引擎内部不可能做到一条数据仅被处理一次。所以，当流处理引擎声称提供 Exactly-Once 语义时，指的是从一个 Operator 到另一个 Operator，同样的数据，无论重复处理多少次，最终的结果状态是 Exactly-Once。
Exactly Once
• Spark 执行单元
• 任务(即一批数据)
• 一批数据全部成功/全部失败
• Task 重做
• 失败重做：task 重做、stage 重做
• 推测执行：另一个节点同时做
• Committer：任务唯一成功
• 其它系统
• Storm：at-most-once、at-least-once
• MapReduce：exactly-once

E2E Exactly Once
同时满足 3 个条件：
• Source 支持 Replay。
• 流计算引擎本身处理能保证 Exactly-Once。
• Sink 支持幂等或事务更新。
具体到 Spark Streaming 流处理程序，包含 3 个步骤：
• 接收数据：从 Source 中接收数据。
• 转换数据：用 DStream 和 RDD 算子转换。
• 储存数据：将结果保存至外部系统。
E2E Exactly Once -接收数据
• 不同的数据源提供不同的保证。
• 如 HDFS 中的数据源，支持 Exactly-Once 语义。
• 如基于 Kafka Direct API 从 Kafka 获取数据，也能保证 Exactly-Once。
E2E Exactly Once - 转换数据
Spark Streaming 内部是天然支持 Exactly-once 语义的。任务失败不论重试多少次，一个算子给另一个算子的结果有且仅有一个，不重不丢。
E2E Exactly Once - 存储数据
Spark Streaming 中的输出操作 foreachRDD 默认具有 At-Least Once 语义，因此当任务失败时会重试多次输出，这样就会重复多次写入外部存储。如果储存数据想实现 Exactly-once，有两种途径：
• 幂等输出：即同样的数据输出多次，结果一样。一般需要借助外部存储中的唯一键实现。具体步骤:
• 将 Kafka 参数 enable.auto.commit 设置为 false。
• 打开 Spark Streaming 的 Checkpoint 特性，用于存放 Kafka 偏移量。
• 有的时候 Checkpoint 无法保存时，需要手动提交 Kafka 偏移量 offset。
• 事务输出：即数据输出和 Kafka Offset 提交在同一原子性事务中。具体步骤:
• 将 Kafka 参数 enable.auto.commit 设置为 false。
• 在使用事务型写入时，我们需要生成一个唯一 ID，这个 ID 可以使用当前批次的时间、分区号或是 Kafka 偏移量 Offset 来生成。
• 结果存储与 ID/Offset 提交在同一事务中原子执行，并写入数据库。

Structured Streaming API
• 区分 Processing Time 和 Event Time。
• 增量查询 API。
• 提供的 connector 和 sink 保障 E2E 语义。
• 批流代码统一。
Event Time
在滑动的 event time 窗口上的聚合对于结构化流是简单的，非常类似于分组聚合。在分 组聚合中，聚合的值对分组的列保持唯一的。在基于窗口的聚合中，聚合的值对每个窗 口的 event time 保持唯一。

 .
 
所以在 Structured Streaming 中，支持 end-to-end exactly once 语义，要求：
• Offset tracking in WAL
• State management
• Fault-tolerant sources and sinks

3.11.19.	Continuous Processing

 
（基于2.4版本）
在连续模式下仅支持 dataset/dataframe 的类似于 map 的操作，即支持 projection
（select、map、flatMap、mapPartitions 等）和 selection（where、filter 等）。
除了聚合函数（因为尚不支持聚合）、current_timestamp（）和 current_date（）
（使用时间的确定性计算具有挑战性）之外，支持所有 SQL 函数。

3.11.20.	Spark Shuffle
Shuffle过程
 

Shuffle的实现
Shuffle读写
Shuffle 分为 2 个阶段：
• Write 阶段（map side） 的任务个数是根据 RDD 的分区数决定的。
假设从 HDFS 中读取数据，那么 RDD 分区个数由该数据集的 block 数决定，也就是一个 split 对应生成 RDD 的一个 partition。
• Read 阶段（reduce side）的任务个数是通过配置 spark.sql.shuffle.partitions 决定的。
• Shuffle 中间的数据交互
• Write 阶段（map side） 会将状态以及 Shuffle 文件的位置等信息封装到 MapStatue 对象中，然后发送给 Driver。
• Read 阶段（reduce side）会从 Driver 拉取 MapStatue，解析后开始执行 reduce 操作。
• Spark1.2 前使用 HashShuffle 算法，1.2 之后主要使用 SortShuffle。

HashShuffle
Shuffle read 阶段，从各个节点上通过网络拉取到 reduce 任务所在的节点，然后进行 key 的聚合或连接等操作。 一般来说，拉取 Shuffle 中间结果的过程是一边拉取一边聚合的。每个 shuffle read task 都会有一个自己的 buffer 缓冲区，每次只能拉取与 buffer 缓冲区相同大小的数据，然后在内存中进行聚合。聚合完一批数据后，再拉取下一批数据，直到最后将所有数据到拉取完，得到最终的结果。
Shuffle write 阶段，每个 task 根据记录的 Key 进行哈希取模操作（hash(key) % reduceNum），
相同结果的记录会写到同一个磁盘文件中。会先将数据写入内存缓冲区，当内存缓冲填满之后，才会溢写（spill）到磁盘文件中。

 
 
将 spark.shuffle.consolidateFiles 设为 true，shuffle write 阶段并不会为每个 task 创建 reduceNum 个文件，而是一个 cpu core 具有一个逻辑上 shuffleFileGroup，每个Group 会生成 reduceNum 个文件，这样大量减少了 shuffle 中间文件个数。

 
Task 的数据会先写入一个内存数据结构中，当内存满了之后，会根据Key 进行排序，然后分批溢写到本地磁盘（示例图演示为 3 批次）。溢写过程只会产生 2 个磁盘文件，一个是数据文件，一个是索引文件（其中标识了各个 task 的数据在文件中的 start offset 与 end offset）
 
BypassSortShuffle 的触发条件为：
1. shuffle map task 数量小于 spark.shuffle.sort.bypassMergeThreshold（默认200）
2. 不是聚合类的 shuffle 算子（比如 reduceByKey）。
此时 task 会创建 reduceNum 个临时磁盘文件，并将数据按 key 进行 hash 取模，写入对应的磁盘文件。类似 HashShuffle，写入磁盘文件时也是先写入内存缓冲，缓冲写满之后再溢写到磁盘文件的。最后，将所有临时磁盘文件都合并成一个磁盘文件，并创建一个单独的索引文件。该 Shuffle 会生成大量中间文件，虽然最后都合并了。且不需要对数据进行排序。
 

3.11.21.	数据倾斜及其优化
数据倾斜的问题
• 绝大多数 task 执行得都非常快，但个别 task 执行极慢。比如，总共有 1000 个 task
，997 个 task 都在 1 分钟之内执行完了，但是剩余两三个 task 却要一两个小时。
• 原本能够正常执行的 Spark 作业，某天突然报出 OOM（内存溢出）异常。反复执行
几次都在某一个 task 报出 OOM 错误，此时可能出现了数据倾斜，作业无法正常运行。

数据倾斜的原因
• 80% 的流量来自 20% 的任务，大多数的数据都符合二八原则。
• 大多数大数据引擎默认的哈希分区算法都无法使数据分散均匀。
• 某些记录存在异常，例如 null。
• 真正决定作业执行快慢的是最长的任务。

数据倾斜的定位
经验法：
数据倾斜一般都发生在shuffle过程中。可能会触发shuffle操作的算子有：distinct、 groupByKey、reduceByKey、aggregateByKey、join、cogroup、repartition等。
页面法：

解决数据倾斜的思路
• Map 端聚合
groupByKey -> reduceByKey • 聚合多个列
select ... from ... group by city_id，street_id，area_id • 聚合随机值
• 增加 reduce 个数
• 没有从根本上改变数据倾斜的本质。
• 但非常容易，可避免 OOM。
• Map Join
• 彻底避免了 shuffle
• 只适合小表，并非所有 join 都能走 map join
• 获取倾斜的记录进行两轮 join • 单个或几个 key 倾斜
• 扩容后进行 join
• 多个 key 倾斜
• 未知 key 倾斜
• 只对倾斜的 key 打上随机数

Spark如何处理数据倾斜
对倾斜端切更多的 task
 

3.11.22.	11. Spark 内存管理
Executro内存：
 

 
  

RDD 的缓存和持久化
RDD 的持久化具体实现由 Driver端和 Executor 端的 Storage 模块构成了主从式的架构，即 Driver端的 BlockManager 为 Master，Executor 端的 BlockManager 为Slave。逻辑上以 Block 为基本存储单位，RDD 的每个 Partition 唯一对应一个 Block（BlockId 的格 式为 rdd_rddId_partitionId）。Master 负责元数据信息的管理，而 Slave 需要将 Block 的状态上报到 Master，同时接收 Master的 命 令 ， 例 如 新 增 或 删 除 一 个RDD。

RDD 的缓存
• RDD 的缓存就是将 RDD 数据放在 Storage 内存中的。
• 从缓存到 Storage 内存之间，Partition 中的数据以迭代器（iterator）来访问。
• 将 Partition 由不连续的存储空间转换为连续存储空间的过程，Spark 称之为“展开”（Unroll）。


3.11.23.	12. Spark 消息通讯机制
Akka
Spark RPC 在 2.0 之前是使用 Akka 类库实现的，Akka 用 Scala 语言开发，基于 Actor 并发模型实现，Akka 具有高可靠、高性能、可扩展等特点，使用 Akka 可以轻松实现分 布式 RPC 功能。

拓展 Scala Actor
Actor 是计算机科学领域中的一个并行计算模型，它把 actors 当做通用的并行计算原语：一个 actor对接收到的消息做出响应，进行本地决策，可以创建更多的 actor，或者发送更多的消息，同时准备接收下一条消息。
一个 Actor 指的是一个最基本的计算单元，它能接收一个消息并且基于其执行计算。

Netty
Spark2.0 使用 Netty 作为 master 与 worker 的通信框架。Netty 是一个基于 JAVA NIO 类库的异步通信框架，它的架构特点是：异步非阻塞、基于事件驱动、高性能、高可靠性和高可定制性。
 
Spark RPC
• RpcEndpoint：Spark 的每个节点（Client/Master/Worker）都有一个 RpcEndpoint。一个 RpcEndpoint 经历的过程依次是：构建 → onStart → receive → onStop。其中onStart 在接收任务消息前调用，receive 和 receiveAndReply 分别用来接收另一个RpcEndpoint 的 send 和 ask 过来的消息。
• RpcEndpointRef：是对远程 RpcEndpoint 的一个引用。当我们需要向一个具体的RpcEndpoint 发送消息时，一般我们需要获取到该 RpcEndpoint 的引用，然后通过该应用发送消息。
• RpcAddress: 表示远程的 RpcEndpointRef 的地址，Host:Port。
• RpcEnv：RPC 上下文环境，每个 RpcEndPoint 运行时依赖的上下文环境称之为 RpcEnv。RpcEnv 负责 RpcEndpoint 整个生命周期的管理。
• Dispatcher：消息分发器。

 

3.13.	SparkSQL
3.13.1.	Spark SQL 的基本架构
Spark 1.0 的主要问题
• 内存计算带来了大量内存管理问题。
• IO 的性能瓶颈开始偏向 CPU 性能瓶颈。
• Shark 对 Hive 的依赖太大，迫切的 SQL 优化需求。
• Micro Batch 的流式计算接口不通用。

Spark 2.0 做了什么？
Tungsten
SQL&DataFrame
Structured Streaming
   

调和大师 Spark SQL
用户
• 数据开发工程师、数据科学家。
场景
• Hive 的替代品，Presto 的替代品。
优势
• 完全胜任 ETL 工作，并能获得更好的性能。
• Join 性能强，交互延迟可接受。
• 兼容性好（BI 工具，标准 SQL）。
• 可用于机器学习。
• Spark 一站式平台。

SparkSQL特点
功能：
• Spark 社区非常强大。
• 可以跑在 YARN 等多种调度器上。
• 开发者可使用多种语言：Scala、Python、R&SQL。
• 强大的 SQL 特性。
• 强大的 SQL 优化器 Catalyst。
• 号称比 MapReduce 快100倍。
• 支持 HiveQL，可以利用 Hive metastore。
• JDBC、OBDC 等
  
Catalyst
 




3.13.2.	结构化数据和 DataFrame
  

API历史
    

3.13.3.	Spark SQL 中的“树”
3.13.3.1.	Hive
 
3.13.3.2.	Spark SQL Tree
Spark SQL 对 SQL 语句的处理和关系型数据库对 SQL 语句的处理采用了类似的方法，首先会将 SQL 语句进行解析（Parse），然后形成一个 Tree，在后续的如绑定、优化等处理过程都是对 Tree 的操作，而操作的方法是采用 Rule，通过模式匹配，对不同类型的节点采用不同的操作。
 
如上图所示，箭头左边表达式有3种数据类型（Literal 表示字面量、Attribute 表示变量、Add 表示运算），表示 x+(1+2)。映射到右边树状结构后，每一种数据类型就会变成一个节点。另外，Tree 还有一个非常重要的特性，可以通过一定的规则进行等价变换。

 
TreeNode 体系
TreeNode 是 Spark SQL 中所有树节点的基类，定义了通用集合操作和树遍历接口。
 

3.13.3.3.	Expression优化规则
 

 

3.13.4.	SQL 编译器和 ANTLR
 

3.13.5.	Catalog 和 HiveCatalog
数据绑定
• Unresolved LogicalPlan 仅仅是一种数据结构，不包含任何数据信息，比如不知道
数据源、数据类型、不同的列来自于哪张表等。接下来我们需要对其进行“数据绑
定”。
• 数据绑定需要用到 Catalog。
• Catalog 是一种数据库用语。 在英文原意中为编目的意思，但是在数据库中完全可以
不用翻译。Catalog 主要用于各种函数资源信息和元数据信息（数据库、数据表、数据视图、数据分区与函数等）的统一管理。
• Spark 的 Catalog 管理类叫 SessionCatalog，此类管理着临时表、view、函数及外部依赖元数据（如 hive metastore），是 Analyzer 进行绑定的桥梁。
 
HiveExternalCatalog
SessionCatalog 实际上只是接口，具体的 Catalog 实现为 ExternalCatalog，目前有两种实现，一个是 InMemoryCatalog，一个是 HiveExternalCatalog。前者用于测试，而生产环境一般用后者： HiveExternalCatalog 是实际存储与操作数据的类。
 

Analyzer 是如何结合 Catalog 的呢？
Analyzer 会使用事先定义好的 Rule 以及 SessionCatalog 对 Unresolved LogicalPlan 进行
transform 操作。
• Rule 是定义在 Analyzer 里面的。
• 多个性质类似的 Rule 组成一个 Batch，多个 Batch 构成一个 Batches。
• Batches 由 RuleExecutor 执行，执行顺序如下图：

3.13.6.	逻辑计划树和优化器
3.13.6.1.	逻辑计划生命周期
 
逻辑计划树的分类
LeafNode
• RunnableCommand
UnaryNode
• RedistributeData
• basicLogicalOperators
BinaryNode
• Join
• CoGroup

3.13.6.2.	Optimizer
• 优化器是整个 Catalyst 的核心，优化器分为基于规则优化和基于代价优化两种。
• 基于规则的优化策略实际上就是对语法树进行一次遍历，对模式匹配能够满足特定规则的节点进行
相应的等价转换。因此，基于规则优化说到底就是一棵树等价地转换为另一棵树。
• SQL 中经典的优化规则有很多，下文结合示例介绍三种比较常见的规则：谓词下推（将过滤尽可
能地下沉到数据源端）、常量累加（比如1 + 2事先计算好）和列剪枝（减少读取不必要的列）。
1. Sql text 经过 SqlParser 解析成 Unresolved LogicalPlan
2. Analyzer 模块结合 Catalog 进行绑定，生成 Resolved LogicalPlan
3. Optimizer 模块对 Resolved LogicalPlan 进行优化，生成 Optimized LogicalPlan
3.13.6.3.	谓词下推
谓词下推是由 PushDownPredicate 规则实现，这个过程主要将过滤条件尽可能地下推到底层，最好是数据源。
例如，语法树中两个表先做 join，之后再使用 age>10 对结果进行过滤。join 算子通常是一个非常耗时的算子，耗时多少一般取决于参与 join 的两个表的大小，如果能够减少参与 join 两表的大小，就可以大大降低 join 算子所需时间。
谓词下推能将过滤条件下推到 join 之前进行，如图中过滤条件 age>0 以及 id!=null 两个条件就分别下推到了 join 之前。这样系统在扫描数据的时候就对数据进行了过滤，参与 join 的数据量将会显著减少，join 耗时必然也会降低。

 
3.13.6.4.	常量累加（Constant Folding）
常量累加其实很简单，就是上文中提到的规则 x+(1+2) -> x+3。
示例如果没有进行优化的话，每一条结果都需要执行一次 100+80 的操作，然后再与变量 math_score 以及 english_score 相加，而优化后就不需要再执行 100+80 操作。

 
3.13.6.5.	列剪枝（Column Pruning）
列值裁剪是另一个经典的规则，示例中对于 people 表来说，并不需要扫描它的所有列值，而只需要 id 列，所以在扫描 people 之后需要将其他列进行裁剪，只留下列 id。这个优化一方面大幅度减少了网络、内存数据量消耗，另一方面对于列存格式（Parquet）来说大大提高了扫描效率。
 
 
 
3.13.7.	物理计划树和策略器
3.13.7.1.	物理计划树
• 经过 Optimizer 优化后的逻辑计划并不知道如何执行，例如算子节点 Relation（实际上是LogicalRelation）虽然代表本次查询会从一张确定的表中获取数据，如 marketing.buyers，但是这张表是什么类型的表（Hive 还是 HBase），如何获取（JDBC 还是读 HDFS 文件），数据分布是什么样的（Bucketed 还是 HashDistributed）此时并不清楚。
• 需要将逻辑计划树转换成物理计划树，以获取真实的物理属性。例如，Relation 算子变为
FileSourceScanExec，Join 算子变为 SortMergeJoinExec。
• 一般的，物理算子以 Exec 结尾。
3.13.7.2.	SparkPlan = PhysicalPlan
SparkPlan 实际上就是我们所说的物理计划，它是所有物理计划抽象类。有了 SparkPlan Tree，才能将其转换成 RDD 的 DAG。
SparkPlan 也有四类：
• LeafExecNode 叶子节点 主要和数据源相关，用户创建 RDD。
• UnaryExecNode 一元节点 主要是针对 RDD的转换操作。
• BinaryExecNode 二元节点 join 操作就属于这类。
• 其他类型的节点。
在物理算子树中， LeafExecNode 是创建一个 RDD 开始，遍历 Tree 过程中每个非叶子节点做一次Transformation，通过 execute 函数转换成新的 RDD，最终会执行 Action 算子把结果返回给用户。
3.13.7.3.	SparkPlanner
1. Sql text 经过 SqlParser 解析成 Unresolved LogicalPlan
2. Analyzer 模块结合 Catalog 进行绑定，生成 Resolved LogicalPlan
3. Optimizer 模块对 resolved LogicalPlan 进行优化,生成 Optimized LogicalPlan
4. SparkPlanner 将 LogicalPlan 转换成 PhysicalPlan（SparkPlan）
3.13.7.4.	Strategies
• SparkPlanner 也是类似 Analyzer和Optimizer，使用类似基于规则（Rules 和 Batches）的方式对逻辑计划树进行转换，这里的规则称为策略（Strategy）。
• SparkPlanner 通过在 Optimizer LogicalPlan 树上应用策略（Strategy），从而生成 SparkPlan列表，即Iterator[PhysicalPlan]。
• SparkPlanner 中定义了一组 Strategy，称为Strategies，类似生成逻辑执行计划中的 Batches。
 
3.13.7.5.	Join Selection
 
3.13.7.6.	build table 的选择
Spark SQL 主要有三种实现 join 的策略，分别是 broadcast hash join、shuffle hash join、sort merge join。对应物理算子就是 BroadcastHashJoinExec、ShuffledHashJoinExec、SortMergeJoinExec。join 的两边分别是流式表（streamed side）和构建表（build side）。
构建表被作为查找表数据结构，流式表作为顺序遍历的数据结构。通常通过一条条迭代流式表中数据，并在构建表中 查找与当前流式表数据 join 键值相同的数据来实现两表的 join。
JoinSelection 的第一步就是 build side 的选择。
• Hash join 将两表之中较小的那一个构建哈希表，这个小表就是 build table。大表叫做 probe table。
• 当 join 类型为 inner-like（包含 inner join 与 cross join 两种）或 right outer join 时，左表才有可能作为
build table。而在 join 类型为 inner-like 或者 left outer/semi/anti join 时，右表有可能作为 build table。

3.13.7.7.	join 策略的选择
策略的选择会按照效率从高到低的优先级来排：
1. broadcast hash join
a. 先根据 broadcast hint 来判断。
b. 其次是广播阈值。
2. hash join
a. spark.sql.join.preferSortMergeJoin 配置项为 false。
b. 右表能够作为 build table，构建本地 HashMap（先右后左）。
c. 右表的数据量比左表小很多（3倍）。
3. sort merge join
a. 如果上面两种策略都不符合，并且参与 join 的 key 是可以排序的。
 
 
 
3.13.7.8.	PrepareForExecution
 
1. SQL text 经过 SqlParser 解析成 Unresolved LogicalPlan。
2. Analyzer 模块结合 Catalog 进行绑定，生成 Resolved LogicalPlan。
3. Optimizer 模块对 resolved LogicalPlan 进行优化，生成 Optimized LogicalPlan。
4. SparkPlanner 将 LogicalPlan 转换成 PhysicalPlan（SparkPlan）。
5. prepareForExecution() 将 PhysicalPlan 转换成可执行物理计划。
• 经过 SparkPlanner 和 Strategies，相当于生成了物理计划数组，之后获取第一条便是计算需要的物理计划树了，但是在真正提交之前，还有一步 prepareForExecution。
• prepareForExecution 的主要的目的是为了优化物理计划，使之满足 shuffle 数据分布，数据排序和内部行格式等。

3.13.7.9.	EnsureRequirements
1. 添加 ExChange 节点，遍历子节点，会依次判断子节点的分区方式（partitioning）是否满足所需的数据分布（distribution）。如果不满足，则考虑是否能以广播的形式来满足，如果不行的话就添加 ShuffleExChangeExec 节点，之后会查看所要求的子节点输出
（requiredChildDistributions），是否有特殊需求，并且要求有相同的分区数，针对这类对
子节点有特殊需求的情况，则会查看每个子节点的输出分区数目，如果匹配不做改变，不然会添加 ShuffleExchangeExec 节点。
2. 查看 requiredChildOrderings 针对排序有特殊需求的添加 SortExec 节点。

3.13.7.10.	Execution
 
 
Execute()
1. Sql text 经过 SqlParser 解析成 Unresolved LogicalPlan。
2. Analyzer 模块结合 Catalog 进行绑定，生成 Resolved LogicalPlan。
3. Optimizer 模块对 resolved LogicalPlan 进行优化，生成 Optimized LogicalPlan。
4. SparkPlanner 将 LogicalPlan 转换成 PhysicalPlan（SparkPlan）。
5. prepareForExecution() 将 PhysicalPlan 转换成可执行物理计划。
6. 使用 execute() 执行可执行物理计划

 

3.13.8.	Spark Join 的各种实现

3.13.9.	一个例子
 select a.customerId
from
(select customerId , amountPaid as amount from sales where 1 = '1’ ) a
where amount=500.0
3.13.9.1.	Parsed Plan
 
3.13.9.2.	ResolvedRelation Rules
• 这条规则用于解析 plan 中所有的 relations（tables）。
• 当发现一个 unresolved relation 时，就会通过访问 Catalog 来解析该 relation。
• 如果发现表不存在，就会抛出 table not exists 异常。
3.13.9.3.	Resolved Relation Logical Plan
 
ResolvedReference Rules
• 这条规则用于解析 plan 中当 references （columns）。
• 所有别名（aliases）和列名（column）都有一个唯一的编号，用于解析器定位和识别。
• 这个唯一的编号可以用在删除子查询等优化中，以达到更好的优化效果。
3.13.9.4.	Resolved Reference Logical Plan
 
Promote String Rules
• 这条规则允许分析器将字符串转换成合适的数据类型。
• 在本查询中，Filter( 1=’1’) 是用 double 类型和 string 类型进行比较。
• 这条规则会添加一个 cast，将 string 转换成 double，以获得更好的比较语义。
 
3.13.9.5.	Eliminate Subqueries Rules
• 该规则允许分析器消除多余的子查询。
• 正是由于每个引用都有一个唯一 ID，我们才能做到这一点。
• 删除子查询允许我们在后续步骤中进行更深入的优化。
 

3.13.9.6.	Constant Folding Rules
• 简化那些可以变成常量的表达式。
• 在本例中，Filter(1=1) 总是返回 true。
• 所有常量折叠规则将其替换成 true。
 
3.13.9.7.	Simplify Filters Rules
• 该规则用于简化过滤器 Filter 算子：
• 移除始终为 true 的过滤器；
• 如果过滤器为 false，可以删除整个子树。
• 在本例中, 始终为 true 的过滤器算子被删除。
• 通过简化过滤器规则，我们可以避免数据的多次无效迭代。
 
3.13.9.8.	PushPredicateThrough Rules
• 为了更好地优化，可以将过滤器尽量靠近数据源。
• 该规则将过滤器下推到靠近 JsonRelation 的地方。
• 此外，当我们重新排列树节点时，我们需要确保重写的规则与列名匹配。
• 在我们的示例中，过滤器规则被重写为使用别名 amountPaid 而不是 amount。
 
3.13.9.9.	Project Collapsing Rules
• 该规则用于删除不必要的 projects（投影）。
• 在本例中，我们不需要第二个投影，即（customerId, amountPaid），因为我们只需要投影
customerId。
• 所有应用该规则消去了第二个投影。
• 完成本优化后，我们得到了最优的计划。
 
3.13.9.10.	Generating Physical Rules
• 最终 Catalyst 将 logical plan 转成 physical plan（也叫 Spark plan）。
• 在 queryExecutor 中，有一个惰性的变量 executedPlan，它用于生成物理计划。
• 在物理计划上，我们可以调用 executeCollect 或 executeTake 来 evaluating 计划，生成
RDD。

3.13.10.	自定义 Catalyst 规则
 
 
 

3.13.11.	代码生成技术
3.13.11.1.	Tungsten 项目
致力于提升 Spark 程序对内存和 CPU 的利用率，使性能达到硬件的极限，主要工作包含以下三个方面：
• 内存管理和二进制数据编码：off-heap 管理内存，降低对象的开销和消除 JVM GC 带来的延时。
• 向量化、缓存感知计算：优化存储，提升 CPU L1/ L2/L3 缓存命中率。
• 代码生成计算：优化 Spark SQL 的代码生成部分，提升 CPU 利用率。

3.13.11.2.	Tungsten 内存管理和数据编码
在相当多的场景中 IO 经常作为大数据开发的瓶颈，我们批处理是基于列存、分区甚至是倒排索引，这一 切的努力都是在解决磁盘的 IO 瓶颈。但是如果数据完全放入了内存之后，我们面临的新问题是什么呢？
CPU 不够用。
对于大数据的场景，其实我们没有把 CPU 的资源用在刀刃上。面对海量数据，CPU 第一件事情就是序列 化与反序列化和压缩与解压数据。在 Spark 这样的分布式计算模型下，需要大量的数据 shuffle，必然需 要将 Java 对象在不同的进程和机器间挪动。
CPU 的第二件事情就是创建对象，在海量数据情况下，CPU 需要把海量对象写回到内存中。 CPU 的第三件事情就是Java垃圾回收了。


3.13.12.	向量化技术

3.13.13.	Thrift Server 实现

3.13.14.	Spark AQE 和 DPP 加速

3.13.15.	Spark3.x 介绍和展望

3.13.16.	Spark SQL 优化详解
3.15.15.1.	• CBO 技术

3.15.15.2.	• 索引技术

3.15.15.3.	• 物化视图的重写技术




3.14.	Presto （待修改）
Presto是一款Facebook开源的MPP架构的OLAP查询引擎，可针对不同数据源执行大容量数据集的一款分布式SQL执行引擎，数据量支持GB到PB字节，主要用来处理秒级查询的场景。Presto 本身并不存储数据，，但是可以接入多种数据源，并且支持跨数据源的级联查询，而且基于内存运算，速度很快，实时性高。
注意：虽然Presto可以解析SQL，但它不是一个标准的数据库。不是MySQL、Oracle的代替品，也不能用来处理在线事务 (OLTP)。
适合：PB级海量数据复杂分析，交互式SQL查询，⽀持跨数据源进行数据查询和分析。 不像hive，只能从hdfs中读取数据。

Presto的介绍、使用和原理架构
https://blog.csdn.net/qq_44766883/article/details/131232308

3.15.	Kylin（待修改）
Apache Kylin是一个开源的分布式分析引擎，提供Hadoop/Spark之上的SQL查询接口及多维分析（OLAP）能力以支持超大规模数据，最初由eBay Inc开发并贡献至开源社区。它能在亚秒内查询巨大的Hive表。

Kylin的介绍、使用和原理架构(Kylin3.0和Kylin4.0，Cube，去重原理，性能优化，MDX For Kylin，BI工具集成)
https://blog.csdn.net/qq_44766883/article/details/131344487

3.16.	Clickhouse（待修改）
ClickHouse是一个高性能分布式列式数据库管理系统，用于快速处理大规模数据集。它采用了一种分布式、可扩展的架构，具有高吞吐量和低延迟的特点。

ClickHouse 部署架构图 clickhouse架构原理
https://blog.csdn.net/u011250186/article/details/135963478
ClickHouse的核心组件分为三层：Client、Server和Storage。

Client层：Client层是与用户交互的接口，用户可以通过各种客户端工具（如ClickHouse-CLI、ClickHouse-JDBC等）连接到ClickHouse服务器，并发送查询请求和接收查询结果。
Server层：Server层是ClickHouse的核心计算引擎，负责接收和处理客户端的查询请求。它包含了多个服务组件，如Query Parser、Query Optimizer、Query Executor等。
Storage层：Storage层是ClickHouse的数据存储和管理组件，负责数据的存储和读写操作。它支持多种存储引擎，如MergeTree、ReplicatedMergeTree、Distributed等。

3.17.	Flink （待修改）
Apache Flink是由Apache软件基金会开发的开源流处理框架，其核心是用Java和Scala编写的分布式流数据流引擎。Flink以数据并行和流水线方式执行任意流数据程序，Flink的流水线运行时系统可以执行批处理和流处理程序。此外，Flink的运行时本身也支持迭代算法的执行。

一文带你全方位(架构，原理及代码实现)了解Flink(3.2W字建议收藏)
https://blog.csdn.net/zuochang_liu/article/details/113618345


