---
title: Zookeeper核心知识点
typora-root-url: ../../source/
date: 2019-10-28 13:58:35
tags:
- zookeeper
- 分布式
---

 Zookeeper 是一个分布式协调服务，可用于服务发现，分布式锁，分布式领导选举，配置管理等。 Zookeeper 提供了一个类似于 Linux 文件系统的树形结构（可认为是轻量级的内存文件系统，但 只适合存少量信息，完全不适合存储大量文件或者大文件），同时提供了对于每个节点的监控与 通知机制。 

<!--more-->

# Zookeeper

## 集群Server角色

Zookeeper集群是一个基于主从复制的高可用集群，每个服务器承担下面三种角色中的一种：Leader、Follower和Observer。

### Leader

- 同一时间只有一个实际工作的Leader；负责发起并维护与Follower和Observer间的心跳。
- 所有写操作由Follower和Observer转发到Leader完成，再由Leader将写操作广播给其他Follower，超过半数（所以一般Follower数量为奇数）写入成功后即认为成功。

### Follower

- 可能存在多个Follower
- 可直接处理客户端的读请求，将客户端的写请求转发到Leader
- 对Leader的写请求进行投票

### Observer

- 与Follower类型，但没有投票权。

Zookeeper为了保证高可用和强一致性，为了支持更多的客户端，需要增加更多Server。但是过多的Follower会导致投票延迟大，写性能差。加入Observer节点可以提供伸缩性，不影响吞吐率，**减小投票延迟**。

## ZAB

Zookeeper使用ZAB协议保证集群Server的一致性。类似的还有分布式事务中的2PC、3PC、PAXOS、Raft等一致性协议。

### zxid

- ZAB使用zxid对事务进行编号，zxid单调递增。follower只会接受zxid比自己的lastZxid大的提议。
- zxid是一个64位数字，高32位代表Leader周期epoch的编号，每当选出一个新的Leader，Leader从本地日志最大事务的zxid，读取epoch并加一，作为新的epoch，同时将低32位归零。
- 针对客户端每一个事务请求，低32位加1。

### 协议四阶段

- 选举阶段（Leader election）：选举**准Leader**（得到超半数节点票数）。
- 发现阶段（Discovery）：Follower与准Leader通信，同步最新的事务提议，**准Leader生成新的epoch**。一个Follower只会连接一个Leader.
- 同步阶段（Synchronization）:同步集群中所有的副本,超半数节点同步完成后,**准Leader成位真正的Leader**.
- 广播阶段（Broadcast）:集群证实对外提供事务服务,**Leader可以进行消息广播**.

### 投票机制

如何选出准Leader

1. Server启动后询问其他的Server要投票给谁,Server每次根据自己的状态回复自己推荐的leader id和上一次处理事务的zxid.(系统启动时每个server都会推荐自己)
2. 收到所有server回复以后,将zxid最大的server置为下次投票的server
3. 票数最多的server为获胜者,票数超半数则该server被选为Leader.否则重复1~3
4. Leader等待server连接.
5. Follower连接leader并发送最大的zxid
6. Leader根据收到的zxid确定同步点,至此选举阶段完成