---
date: 2024-01-02
categories:
  - General
search:
  boost: 2
---


##  Kafka

Kafka 是一个基于发布订阅模式的消息队列中间件。它由 Producer, Consumer, Broker 和 Partition 几个组成。

Kafka 里面的每一个消息都属于一个主题，每一个主题都有多个 Partition。Partition 又可以使用主从复制模式，即 Partition 之间组成主从模式。这些 Partition 均匀分布在 Broker 上，以保证高可用。（这里点到了高可用，引导面试官探讨 Kafka 高可用）。每一个 Partition 内消息是有序的，即分区顺序性。（这一句是为了引出后面如何保证消息有序性）

Producer 依据负载均衡设置，将消息发送到 Topic 的特定 Partition 下；（后面面试官可能会问负载均衡策略）


Consumer 之间组成了 Consumer Group，可以有多个 Consumer Group 消费同一个 Topic，互相之间不会有影响。Kafka 强制要求每个 Partition 只能有一个 Consumer，并且 Consumer 采取拉模式，消费完一批消息之后再拉取一批（尝试引出来后面的拉模型的讨论）；

一个 Kafka 集群由多个 Broker 组成，每个 Broker 上存放着不同 Topic 的 Partition；

## Kafka 的高性能是如何保证的？

1. **零拷贝**。在 <u>Linux</u> 上 Kafka 使用了两种手段，**mmap** 和 **sendfile**，前者用于解决 Producer 写入数据，后者用于 Consumer 读取数据；
2. **顺序写**：Kafka 的数据，可以看做是 AOF （append only file），它只允许追加数据，而不允许修改已有的数据。（后面是亮点）该手段也在数据库如 MySQL，Redis上很常见，这也是为什么我们一般说 Kafka 用机械硬盘就可以了。有人做过实验（的确有，你们可以找找，我已经找不到链接了），机械磁盘 Kafka 和 SSD Kafka 在性能上差距不大；
3. **Page Cache**：Kafka 允许落盘的时候，是写到 Page Cache的时候就返回，还是一定要刷新到磁盘（主要就是mmap之后要不要强制刷新磁盘），类似的机制在 MySQL, Redis上也是常见，（简要评价一下两种方式的区别）如果写到 Page Cache 就返回，那么会存在数据丢失的可能。
4. **批量操作**：包括 Producer 批量发送，也包括 Broker 批量落盘。批量能够放大顺序写的优势，比如说 Producer 还没攒够一批数据发送就宕机，就会导致数据丢失；
5. **数据压缩**：Kafka 提供了数据压缩选项，采用数据压缩能减少数据传输量，提高效率；
6. **日志分段存储**：Kafka 将日志分成不同的段，只有最新的段可以写，别的段都只能读。同时为每一个段保存了偏移量索引文件和时间戳索引文件，采用二分法查找数据，效率极高。同时 Kafka 会确保索引文件能够全部装入内存，以避免读取索引引发磁盘 IO。（这里有一点很有意思，就是在 MySQL 上，我们也会尽量说把索引大小控制住，能够在内存装下，在讨论数据库磁盘 IO 的时候，我们很少会计算索引无法装入内存引发的磁盘 IO，而是只计算读取数据的磁盘 IO）

!!!note "批量操作+压缩的亮点"
    - 批量发送和数据压缩，在处理大数据的中间件中比较常见。比如说分布式追踪系统 CAT 和 skywalking 都有类似的技术。代价就是存在数据丢失的风险；
    - 数据压缩虽然能够减少数据传输，但是会消耗更过 CPU。不过在 IO 密集型的应用里面，这不会有什么问题；
