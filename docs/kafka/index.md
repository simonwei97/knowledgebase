---
date: 2024-01-02
categories:
  - Kafka
search:
  boost: 2
hide:
  - footer
---

## Kafka 简介

Kafka 是一个基于发布订阅模式的消息队列中间件。它由 Producer, Consumer, Broker 和 Partition 几个组成。

Kafka 是最初由 Linkedin 公司开发，是一个分布式、支持分区的（partition）、多副本的（replica），基于 zookeeper 协调的分布式消息系统，它的最大的特性就是可以实时的处理大量数据以满足各种需求场景：比如基于 hadoop 的批处理系统、低延迟的实时系统、Storm/Spark 流式处理引擎，web/nginx 日志、访问日志，消息服务等等，用 scala 语言编写，Linkedin 于 2010 年贡献给了 Apache 基金会并成为顶级开源项目。

## Kafka 的使用场景

- **日志聚合**：可收集各种服务的日志写入 kafka 的消息队列进行存储
- **消息系统**：广泛用于消息中间件
- **系统解耦**：在重要操作完成后，发送消息，由别的服务系统来完成其他操作
- **流量削峰**：一般用于秒杀或抢购活动中，来缓冲网站短时间内高流量带来的压力
- **异步处理**：通过异步处理机制，可以把一个消息放入队列中，但不立即处理它，在需要的时候再进行处理

## Kafka 基本概念

kafka 是一个分布式的，分区的消息(官方称之为 commit log)服务。它提供一个消息系统应该具备的功能，但是确有着独特的设计。可以这样来说，**Kafka 借鉴了 JMS 规范的思想，但是确并没有完全遵循 JMS 规范**。

| 名称            | 解释                                                                                                                                                    |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Broker          | 消息中间件处理节点，一个 Kafka 节点就是一个 broker，一个或者多个 Broker 可以组成一个 Kafka 集群(Kafka Cluster)                                          |
| Topic           | Kafka 根据 topic 对消息进行归类，发布到 Kafka 集群的每条消息都需要指定一个 topic                                                                        |
| Producer        | 消息生产者，向 Broker 发送消息的客户端                                                                                                                  |
| Consumer        | 消息消费者，从 Broker 读取消息的客户端                                                                                                                  |
| ConsumerGroup   | 每个 Consumer 属于一个特定的 Consumer Group，一条消息可以被多个不同的 Consumer Group 消费，但是一个 Consumer Group 中只能有一个 Consumer 能够消费该消息 |
| Partition       | 物理上的概念，一个 topic 可以分为多个 partition，每个 partition 内部消息是有序的                                                                        |
| Replica（副本） | 一个 topic 的每个分区都有若干个副本，一个 Leader 和若干个 Follower                                                                                      |
| Leader          | 每个分区多个副本的“主”，生产者发送数据的对象，以及消费者消费数 据的对象都是 Leader                                                                      |
| Follower        | 每个分区多个副本中的“从”，实时从 Leader 中同步数据，保持和 Leader 数据的同步。Leader 发生故障时，某个 Follower 会成为新的 Leader。                      |

- Producer 将消息发送到特定的主题，Consumer 通过订阅特定的 Topic(主题) 来消费消息。
- Partition 属于 Topic 的一部分。一个 Topic 可以有多个 Partition ，并且同一 Topic 下的 Partition 可以分布在不同的 Broker 上，这也就表明一个 Topic 可以横跨多个 Broker 。
- 服务端(brokers)和客户端(producer、consumer)之间通信通过 TCP 协议来完成。

## Kafka 的多副本机制

## Kafka 消息的消费模式

!!!note "采用 Pull 模式。"

Consumer 可以自主决定是否批量的从 Broker 拉取数据。Pull 模式有个缺点是，如果 Broker 没有可供消费的消息，将导致 Consumer 不断在循环中轮询，直到新消息到达。为了避免这点，Kafka 有个参数可以让 Consumer 阻塞直到新消息到达。

## Kafka 如何实现负载均衡与故障转移

**负载均衡**

- Kakfa 的负载均衡就是每个 Broker 都有均等的机会为 Kafka 的客户端（生产者与消费者）提供服务，可以负载分散到所有集群中的机器上。Kafka 通过智能化的分区领导者选举来实现负载均衡，提供智能化的 Leader 选举算法，可在集群的所有机器上均匀分散各个 Partition 的 Leader，从而整体上实现负载均衡。

**故障转移**

- Kafka 的故障转移是通过使用会话机制实现的，每台 Kafka 服务器启动后会以会话的形式把自己注册到 Zookeeper 服务器上。一旦服务器运转出现问题，就会导致与 Zookeeper 的会话不能维持从而超时断连，此时 Kafka 集群会选举出另一台服务器来完全替代这台服务器继续提供服务。

## Kafka 中 Zookeeper 的作用

ZooKeeper 主要为 Kafka 提供元数据的管理的功能。

- Kafka 是一个使用 Zookeeper 构建的分布式系统。Kafka 的各 Broker 在启动时都要在 Zookeeper 上注册，由 Zookeeper 统一协调管理。
- 如果任何节点失败，可通过 Zookeeper 从先前提交的偏移量中恢复，因为它会**做周期性提交偏移量工作**。
- 同一个 Topic 的消息会被分成多个分区并将其分布在多个 Broker 上，这些分区信息及与 Broker 的对应关系也是 Zookeeper 在维护。

还执行其他活动，如: eader 检测、分布式同步、配置管理、识别新节点何时离开或连接、集群、节点实时状态等。

## Kafka 的 ACK 机制

| ACK    | 说明                                                                                                                                               | 优缺点                                                                                   |
| :----- | :------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------- |
| **0**  | 相当于异步操作，Producer 不需要 Leader 给予回复，发送完就认为成功，继续发送下一条（批）Message                                                     | **此机制具有最低延迟，但是持久性可靠性也最差，当服务器发生故障时，很可能发生数据丢失**。 |
| **1**  | Kafka 默认的设置。表示 Producer 要 Leader 确认已成功接收数据才发送下一条（批）Message。不过 Leader 宕机，Follower 尚未复制的情况下，数据就会丢失。 | **此机制提供了较好的持久性和较低的延迟性**。                                             |
| **-1** | Leader 接收到消息之后，还必须要求 ISR 列表里跟 Leader 保持同步的那些 Follower 都确认消息已同步，Producer 才发送下一条（批）Message。               | **此机制持久性可靠性最好，但延时性最差**。                                               |


