# 分布式计算框架 MapReduce
Mapreduce是一种用于在大型商用硬件集群中(成千上万的结点)对海量数据(多个兆字节数据集)实施可靠的、高容错的分布式计算的框架，也是一种经典的并行计算模型。Mapreduce的基本原理是将一个复杂的问
题(数据集)分成若干个简单的子问题(数据块)进行解决(Map函数)；然后对子问题的结果进行合并( Reduce函数)，得到原有问题的解(结果)。
![](https://img-blog.csdnimg.cn/20210706132527298.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpYW41ODI3OTYxNDk=,size_16,color_FFFFFF,t_70)

## MapReduce 编程模型

### MapReduce 编程模型简介
MapReduce 是一种思想或是一种编程模型。Mapreduce编程模型主要由两个抽象类构成，即Mapr类和 Reducer类，Mper用以对切分过的原始数据进行处理，Reducer则对 Mapper的结果进行汇总，得到最后的输出结
果。根据其工作原理，可将MapReduce编程模型分为两类：MapReduce简单模型和MapReduce复杂模型。

### MapReduce 编程实例
实例地址：<https://blog.csdn.net/Mr_jokersun/article/details/107071022?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166322639516781432991753%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166322639516781432991753&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-6-107071022-null-null.142^v47^pc_rank_34_default_23,201^v3^control_2&utm_term=mapreduce%E7%9A%84wordcount%E4%BC%AA%E4%BB%A3%E7%A0%81&spm=1018.2226.3001.4187>

## MapReduce 数据流
从 Mapreduce的编程模型中可以发现，数据以不同的形式在不同结点之间流动，即经过本结点的分析处理，以另外一种形式进入下一个结点，从而得出最终结果。因此了解数据在各个结点之间的流入形式和流出形
式对我们开发系统很重要。wordcount的处理过程如下。
![](https://img-blog.csdnimg.cn/20200520170232673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsbGVuenln,size_16,color_FFFFFF,t_70)

### 分片、格式化数据源(InputFormat)
Inputformat主要有两个任务，一个是对源文件进行分片，并确定Mapper的数量：另一个对各分片进行格式化，处理成 <key,value>形式的数据流并传给Mapper。

### Map 过程
Mapper接收<key,value>形式的数据，并处理成<key,value>形式的数据，具体的处理过程可由用户定义。

### Combiner 过程
每一个map()都可能会产生大量的本地输出, Combiner()的作用就是对map()端的输出先做一次合并，以减少在Map和Reduce结点之间的数据传输量，提高网络I/O性能，是Mapreduce的一种优化手段之一。

### Shuffle 过程
Shuffle过程是指从 Mapper产生的直接输出结果，经过一系列的处理，成为最终的Reducer直接输入数据为止的整个过程，这一过程也是 Mapreduce的核心过程。整个 Shuffle过程可以分为两个阶段，Mapper端的 Shuffle和Reducer端的Shuffle。

### Reduce 过程
Reducer接收<key,{ value list}>形式的数据流，形成<key,value>形式的数据输出，输出数据直接写入HDFS，具体的处理过程可由用户定义。

## MapReduce 任务运行流程
### MRv2 基本组成
1)客户端( client):客户端用于向Yarn集群提交任务，是 Mapreduce用户和Yarn集群通信的唯一途径，它通过 Applicationclientprotocol协议(RPC协议的一个实现)与Yarn的Resourcemanager通信，通过客户
端，还可以对任务状态进行查询或杀死任务等。客户端还可以通过 Mrclientprotocol协议(RPC协议的一个实現)与 Mrappmaster(请看下一条)进行通信，从而直接监控和控制作业，以减轻 Resourcemanager的负担。
2) Mrappmaster: Mrappmaster为 Applicationmaster的一个实现，它监控和调度一整套MR任务流程，每个MR任务只产生一个 Mrappmaster。 Mrappmaster 只负责任务管理，并不负责资源的调配。
3) Map Task和 Reduce Task:用户定义的Map函数和 Reduce函数的实例化，在MRv2中,它们只能运行在Yarn给定的资源限制下，由 Mrappmaster和 Nodemanage协同管理和调度。

### Yarn 基本组成
Yarn是一个资源管理平台，它监控和调度整个集群资源，并负责管理集群所有任务的运行和任务资源的分配，它的基本组成如下。
1) Resource Manager(RM)：运行于 Namenode，为整个集群的资源调度器，它主要包括两个组件: Resource Schedule(资源调度器)和 Applications Manager(应用程序管理器)。
Resource Schedule：当有应用程序已经注册需要运行时，Applicationmaster会向它申请资源，而它会根据当时的资源和限制进行资源分配，它会产生一个 container资源描述(第4点)
Applications Manager:它负责管理整个集群运行的所有任务，包括应用程序的提交和 Resource Schedule协商启动和监控 Applicationmaster，并在 Applicationmaster任务失败时在其他结点重启它。
2) Nodemanager:运行于 Datanode，监控并管理单个结点的计算资源，并定时向RM汇报结点的资源使用情况，在结点上有任务时，还负责对 container进行创建、运行状态的监控及最终销毁。
3) Application Master(AM):负责对一个任务流程的调度、管理,包括任务注册、资源中请、以及和 Nodemanage通信以开启和杀掉任务等。
4) container:Yam架构下对运算资源的一种描述,它封装了某个结点的多维度资源,包括CPU、RAM、Disk、 Network等。当AM向RM申请资源时，RM分配的资源就是以container表示的, Map task和 Reduce Task只能在所分配的 container描述限制中运行。

### 任务流程
任务流程如下：
1) client向 Resourcemanager提交任务。
2) Resourcemanager分配该任务的第一个 container，并通知相应的 Nodemanager启动MRAppmaster。
3) Nodemanager接收命令后，开辟一个 container资源空间，并在 container中启动相应的Mrappmaster。
4) MR Appmaster启动之后，第一步会向 Resourcemanager注册，这样用户可以直接通过MR Appmaster监控任务的运行状态；之后则直接由 Mrappmaster调度任务运行，重复5)~8)，直到任务结東。
5) Mrappmaster以轮询的方式向 Resourcemanager申请任务运行所需的资源。
6)一旦 Resourcemanager配给了资源， Mrappmaster便会与相应的 Nodemanager通信，让它划分 Container并启动相应的任务( Map Task或 ReduceTask)。
7) Nodemanager 准备好运行环境，启动任务。
8)各任务运行，并定时通过RPC协议向 Mrappmaster汇报自己的运行状态和进度Mrappmaster也会实时地监控任务的运行，当发现某个Task假死或失败时,便杀死它重新启动任务。
9)任务完成， Mrappmaster向 Resourcemanager 通信，注销并关闭自己。

yarn中的mapreduce的任务运行流程如下：
![](https://img-blog.csdnimg.cn/482df49021964e41b8c8aed8b4ca634d.png)
