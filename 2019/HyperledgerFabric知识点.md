# Hyperledger Fabric知识点

##  Hyperledger Fabric状态数据库有什么用？
> 状态数据库仅仅是有序交易日志的快照，因此在任何时候都可以根据交易日志重新生成。

##Hyperledger Fabric kafka部署的时候机器数量选择？

> kafka的配置数量应该是4台，zk应该是3，5，7台

> 从性能角度来看，Kafka和Zookeeper（ZK）不会成为瓶颈。部署的ZK节点和Kafka代理有多少将取决于您的弹性模型/要求，Zookeeper的容错基于2f + 1模型，其中f表示您愿意容忍的故障节点的数量。

> Kafka比较麻烦，它支持是N-1服务器故障，N是集群数量。

## Hyperledger Fabric 成员服务管理控制？

##  Hyperledger Fabric 节点部署方式？
> peer节点不能和order节点部署在一台机器上，而enduring peers和committing peers可以部署在同一台机器上，这种设计主要是为了系统架构的解耦，提高扩展性，以及通过主机隔离提高安全性。

## Hyperledger Fabric 什么是MSP？

> 联盟链成员的证书管理，它定义了哪些RCA以及ICA在链里是可信任的，包括定义了channel上的合作者。

##  Hyperledger Fabric 怎么解决双花？
> 客户端APP通过SDK将验证后的交易信息发送给OSN，然后，OSN对消息做初步校验后，封装成Kafka消息格式，发送到Kafka集群，对交易信息统一排序。

## Hyperledger Fabric kafka和Raft选择？

>  |      kafka       |     raft     |
| :--------------: | :----------: |
| 不易部署，依赖zk |   方便部署   |
|    比较中心化    | 更加去中心化 |
|    额外的组件    |   原生支持   |

## Hyperledger Fabric 系统链码？
> 生命周期系统链码（LSCC ）：处理生命周期管理。
配置系统链码（CSCC）：处理在Peer节点上的通道配置。
查询系统链码（QSCC）：提供账本的查询API，例如获取区块以及交易。
背书系统链码（ESCC）：通过对交易提案响应进行签名来处理背书过程。
验证系统链码（VSCC）：处理交易验证，包括检查背书策略以及多进程并发控制。

## Hyperledger Fabric 为什么用k8s部署方式？

> Fabric 的组件都经过容器封装好的，很方便部署在 K8s 这类容器平台上，Fabric 是分布式系统，根据应用的具体需求，集群的各个组件数会有不同，需要灵活地配置和调整， K8s 具备了多租户的能力，可在同一个平台中运行多个互相隔离的 Fabric 实例，方便开发测试，比如一个实例作为开发用，另一个实例作为测试用。

## Hyperledger Fabric solo共识和kafka共识区别？
> solo模式指整个fabric网络依赖于一个orderer节点，而kafka模式依赖于一个kafka集群

## Fabric的账本数据结构
> 区块链数据(Blockchain Data)
> 状态数据(State Database)
> 索引数据(Index Database)

**区块链数据**又称账本数据，就是我们通常所说的由一个一个的区块(Block)连接而成的链式数据结构。每一个区块中都存储有一条或一组有序的且不可篡改的记录。

**状态数据**，也称为世界状态(World State)，它不是链式数据结构的一部分，而是链式数据结构综合而成的一个结果。这部分数据是保存在Level DB数据库中的，是可以修改的。状态数据只是保存所有交易完成之后的最新结果，全部都是以Key/Value数据对的形式存在，并不会保存交易过程。

**索引数据库**是为了查询区块链数据的时候，可以快速定位，加快查询速度。在索引数据中，保存了两类数据，一类是对于每一个Key的历史记录的追踪信息，另一类是对于block的相关信息，包括区块hash，区块号，交易号等等。










