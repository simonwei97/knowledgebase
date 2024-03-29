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

==采用 Pull 模式。==

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

- **0**： 相当于异步操作，Producer 不需要 Leader 给予回复，发送完就认为成功，继续发送下一条（批）Message。**此机制具有最低延迟，但是持久性可靠性也最差，当服务器发生故障时，很可能发生数据丢失**。
- **1**： Kafka 默认的设置。表示 Producer 要 Leader 确认已成功接收数据才发送下一条（批）Message。不过 Leader 宕机，Follower 尚未复制的情况下，数据就会丢失。**此机制提供了较好的持久性和较低的延迟性**。
- **-1**： Leader 接收到消息之后，还必须要求 ISR 列表里跟 Leader 保持同步的那些 Follower 都确认消息已同步，Producer 才发送下一条（批）Message。**此机制持久性可靠性最好，但延时性最差**。

## Kafka 的高性能是如何保证的？

1. **零拷贝**。在 <u>Linux</u> 上 Kafka 使用了两种手段，**mmap** 和 **sendfile**，前者用于解决 Producer 写入数据，后者用于 Consumer 读取数据；
2. **顺序写**：Kafka 的数据，可以看做是 AOF （append only file），它只允许追加数据，而不允许修改已有的数据。（后面是亮点）该手段也在数据库如 MySQL，Redis 上很常见，这也是为什么我们一般说 Kafka 用机械硬盘就可以了。有人做过实验（的确有，你们可以找找，我已经找不到链接了），机械磁盘 Kafka 和 SSD Kafka 在性能上差距不大；
3. **Page Cache**：Kafka 允许落盘的时候，是写到 Page Cache 的时候就返回，还是一定要刷新到磁盘（主要就是 mmap 之后要不要强制刷新磁盘），类似的机制在 MySQL, Redis 上也是常见，（简要评价一下两种方式的区别）如果写到 Page Cache 就返回，那么会存在数据丢失的可能。
4. **批量操作**：包括 Producer 批量发送，也包括 Broker 批量落盘。批量能够放大顺序写的优势，比如说 Producer 还没攒够一批数据发送就宕机，就会导致数据丢失；
5. **数据压缩**：Kafka 提供了数据压缩选项，采用数据压缩能减少数据传输量，提高效率；
6. **日志分段存储**：Kafka 将日志分成不同的段，只有最新的段可以写，别的段都只能读。同时为每一个段保存了偏移量索引文件和时间戳索引文件，采用二分法查找数据，效率极高。同时 Kafka 会确保索引文件能够全部装入内存，以避免读取索引引发磁盘 IO。（这里有一点很有意思，就是在 MySQL 上，我们也会尽量说把索引大小控制住，能够在内存装下，在讨论数据库磁盘 IO 的时候，我们很少会计算索引无法装入内存引发的磁盘 IO，而是只计算读取数据的磁盘 IO）

!!!note "批量操作+压缩的亮点"

    - 批量发送和数据压缩，在处理大数据的中间件中比较常见。比如说分布式追踪系统 CAT 和 skywalking 都有类似的技术。代价就是存在数据丢失的风险；
    - 数据压缩虽然能够减少数据传输，但是会消耗更过 CPU。不过在 IO 密集型的应用里面，这不会有什么问题；

## Kafka 高可用

1. **数据复制（Replication）**： Kafka 使用副本机制来实现数据的冗余存储和容错。每个分区都可以配置多个副本，其中一个作为领导者（Leader），其他副本作为追随者（Follower）。领导者负责处理所有的读写请求，而追随者则通过复制领导者的数据来提供冗余备份和故障恢复。当领导者发生故障时，追随者中的一个会被选举为新的领导者。
2. **ISR（In-Sync Replica）机制**： 在 Kafka 中，追随者的副本需要与领导者保持同步才能提供可靠的数据复制。Kafka 使用 ISR 机制来确定一组处于同步状态的副本。只有属于 ISR 中的副本才能参与数据的复制和读取操作。如果某个副本无法及时复制领导者的数据或发生故障，它将从 ISR 中移除，直到与领导者保持同步为止。
3. **Controller 选举**： Kafka 集群中有一个专门的节点称为 Controller，负责管理分区和副本的状态。当集群中的 Controller 发生故障时，会自动进行 Controller 的选举，确保集群能够继续正常运行。选举完成后，新选举出的 Controller 负责维护和管理分区和副本的状态。
4. **故障恢复**： 当领导者或副本发生故障时，Kafka 会自动进行故障恢复。追随者中的一个副本会被选举为新的领导者，并从 ISR 中添加新的副本，以确保数据的可靠性和高可用性。一旦故障副本恢复正常，它将再次加入 ISR，参与数据的复制和读取。
5. **数据持久化**： Kafka 使用磁盘作为主要的数据存储介质，消息和日志被持久化写入到磁盘中。这样即使发生节点故障，数据也不会丢失。Kafka 还支持数据的压缩和数据段的分割，以优化存储和提高读写性能。
