---
date: 2024-01-02
categories:
  - General
---

##  Kafka

Kafka 是一个基于发布订阅模式的消息队列中间件。它由 Producer, Consumer, Broker 和 Partition 几个组成。

Kafka 里面的每一个消息都属于一个主题，每一个主题都有多个 Partition。Partition 又可以使用主从复制模式，即 Partition 之间组成主从模式。这些 Partition 均匀分布在 Broker 上，以保证高可用。（这里点到了高可用，引导面试官探讨 Kafka 高可用）。每一个 Partition 内消息是有序的，即分区顺序性。（这一句是为了引出后面如何保证消息有序性）

Producer 依据负载均衡设置，将消息发送到 Topic 的特定 Partition 下；（后面面试官可能会问负载均衡策略）


Consumer 之间组成了 Consumer Group，可以有多个 Consumer Group 消费同一个 Topic，互相之间不会有影响。Kafka 强制要求每个 Partition 只能有一个 Consumer，并且 Consumer 采取拉模式，消费完一批消息之后再拉取一批（尝试引出来后面的拉模型的讨论）；

一个 Kafka 集群由多个 Broker 组成，每个 Broker 上存放着不同 Topic 的 Partition；
