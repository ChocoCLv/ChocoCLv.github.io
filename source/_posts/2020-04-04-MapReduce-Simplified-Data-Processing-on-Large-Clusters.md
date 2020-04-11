---
title: 'MapReduce: Simplified Data Processing on Large Clusters'
comments: true
typora-root-url: ../../source/
date: 2020-04-04 16:52:56
tags:
- 经典论文
- MapReduce
---

MapReduce论文阅读

https://pdos.csail.mit.edu/6.824/papers/mapreduce.pdf

<!--more-->

# 摘要

# 引言

# 基本编程模型及示例

# Google的实现

## 工作流

![MapReduce workflow](/imgs/image-20200404200106604.png)

1. 用户侧的`MapReduce`库先将输入文件分成M片，每片大小为16~64MB（具体大小可控制）。然后在集群机器上的程序副本开始执行。
2. 集群中的一个机器为`master`角色，其余机器为`worker`，`worker`由`master`分配任务。存在`M`个`Map`任务和`R`个`Reduce`任务需要分配。`master`为空闲的`worker`分配`map`任务或`reduce`任务。
3. 执行`map`任务的`worker`从对应的输入分片中读取内容，从中解析出`kv`对并对每个`kv`对调用一次用户定义的`Map function`。`Map function`产生的中间`kv`对在内存中缓存。
4. 将缓存的`kv`对周期性的写入磁盘，并使用`partition`函数分成`R`份。将写入磁盘后的文件名发送给`master`。
5. 当一个执行`reduce`任务的`worker`获得中间文件名时，它使用`rpc`读取从`map worker`的本地磁盘中读取数据。当一个`reduce worker`读取了所有的中间数据，根据中间`key`对其排序。因为有许多不同key的数据由同一个reduce任务处理，所以需要排序。如果中间数据量太大，可能还需要外部排序。
6. `reduce`任务将每个key和对应的values作为`Reduce function`的输入，`Reduce function`的一次输出对应该`reduce`任务文件的一行。

## master的数据结构

- 每个`map`任务和每个`reduce`任务的`state(idle, in-progress, completed)`，以及`non-idle`任务对应机器的标识符。
- 存储每个已完成的map任务的中间文件及其大小。

## 容错

### worker failure

- `master`周期性ping每个`worker`。在一定时间内无响应的`worker`认为failed。
- `failed worker`上执行的任务会被置为idle，从而被重新调度。
- 在发生故障时，会重新执行已完成的map任务【包括执行成功的历史任务】，因为它们的输出存储在故障机器的本地磁盘上，因此无法访问。完成的reduce任务不需要重新执行，因为它们的输出存储在全局文件系统中。
- 当map任务首先由`worker a`执行，然后由`worker B`执行(因为a失败了)时，所有执行`reduce`任务的`worker`都会得到重新执行的通知。任何尚未从 `worker A`读取数据的`reduce`任务都将从`worker B`读取数据。
- MapReduce对大规模工人故障有弹性。例如，在一次MapReduce操作中，运行中的集群上的网络维护导致一次80台机器的组在几分钟内无法访问。MapReduce主服务器只是重新执行无法到达的工作机器所做的工作，并继续前进，最终完成MapReduce操作。

### master failure

如果master终止，可以从最后一个检查点状态启动一个新的副本。然而，考虑到只有一个master，它的失败是不太可能的.因此，如果master失败，我们当前的实现将中止MapReduce计算。客户端可以检查此条件并根据需要重试MapReduce操作。

### Semantics in the Presence of Failures

- map reduce的输出需要是确定性的，幂等的。
- 保障map reduce任务提交的原子性、持久性。即没有部分完成，完成后持久化到文件。
- 原子性：reduce任务在全部完成前将输出写入到临时文件，完成后将临时文件重命名到final name。【一个map任务输出R个文件，一个reduce任务输出一个文件】。如果同时有多个reduce任务在执行，并同时rename，此时只能通过文件系统的原子性rename保证。
- master忽略重复的map完成信息。