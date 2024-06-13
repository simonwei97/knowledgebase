---
date: 2024-01-02
categories:
  - MySQL
search:
  boost: 2
hide:
  - footer
---

## MySQL 三大日志

- **undo log（回滚日志）**：是 Innodb 存储引擎层生成的日志，实现了事务中的 ==**原子性**==，主要用于 ==**事务回滚和 MVCC**==。
- **redo log（重做日志）**：是 Innodb 存储引擎层生成的日志，实现了事务中的 ==**持久性**==，主要用于 ==**掉电等故障恢复**==；
- **binlog （归档日志）**：是 Server 层生成的日志，主要用于 ==**数据备份和主从复制**==；

## undo log

每当 InnoDB 引擎对一条记录进行操作（修改、删除、新增）时，要把回滚时需要的信息都记录到 undo log 里。

- 在**插入**一条记录时，要把这条记录的主键值记下来，这样之后回滚时只需要把这个主键值对应的**记录删掉**就好了；
- 在**删除**一条记录时，要把这条记录中的内容都记下来，这样之后回滚时再把由这些内容组成的**记录插入到表中**就好了；
- 在**更新**一条记录时，要把被更新的列的旧值记下来，这样之后回滚时再把这些列**更新为旧值**就好了。

{==

在发生回滚时，就读取 undo log 里的数据，然后做原先相反操作。比如当 delete 一条记录时，undo log 中会把记录中的内容都记下来，然后执行回滚操作的时候，就读取 undo log 里的数据，然后进行 insert 操作。

==}

### 两大作用

- **实现事务回滚，保障事务的原子性**。事务处理过程中，如果出现了错误或者用户执 行了 ROLLBACK 语句，MySQL 可以利用 undo log 中的历史数据将数据恢复到事务开始之前的状态。
- **实现 MVCC（多版本并发控制）关键因素之一**。MVCC 是通过 ReadView + undo log 实现的。undo log 为每条记录保存多份历史数据，MySQL 在执行快照读（普通 select 语句）的时候，会根据事务的 Read View 里的信息，顺着 undo log 的版本链找到满足其可见性的记录。

!!!note "undo log 是如何刷盘（持久化到磁盘）的？"

    undo log 和数据页的刷盘策略是一样的，都需要通过 redo log 保证持久化。

    buffer pool 中有 undo 页，对 undo 页的修改也都会记录到 redo log。redo log 会每秒刷盘，提交事务时也会刷盘，数据页和 undo 页都是靠这个机制保证持久化的。

## Buffer Pool

Innodb 存储引擎设计了一个缓冲池（Buffer Pool），来提高数据库的读写性能。

有了 Buffer Poo 后：

- 当读取数据时，如果数据存在于 Buffer Pool 中，客户端就会直接读取 Buffer Pool 中的数据，否则再去磁盘中读取。
- 当修改数据时，如果数据存在于 Buffer Pool 中，那直接修改 Buffer Pool 中数据所在的页，然后将其页设置为脏页（该页的内存数据和磁盘上的数据已经不一致），为了减少磁盘 I/O，不会立即将脏页写入磁盘，后续由后台线程选择一个合适的时机将脏页写入到磁盘。

在 MySQL 启动的时候，InnoDB 会为 Buffer Pool 申请一片连续的内存空间，然后按照默认的 {++ **16KB** ++} 的大小划分出一个个的页， Buffer Pool 中的页就叫做缓存页。此时这些缓存页都是空闲的，之后随着程序的运行，才会有磁盘上的页被缓存到 Buffer Pool 中。

Buffer Pool 除了缓存「索引页」和「数据页」，还包括了 Undo 页，插入缓存、自适应哈希索引、锁信息等等。

!!!note "WAL （Write-Ahead Logging）技术"

    InnoDB 引擎会在适当的时候，由后台线程将缓存在 Buffer Pool 的脏页刷新到磁盘里，这就是 **WAL （Write-Ahead Logging）**技术。

    **WAL 技术指的是， MySQL 的写操作并不是立刻写到磁盘上，而是先写日志，然后在合适的时间再写到磁盘上。**

## redo log

!!!note "什么是 redo log？"

    redo log（重做日志）是InnoDB存储引擎独有的，它让MySQL拥有了崩溃恢复能力。

    redo log 是物理日志，记录了某个数据页做了什么修改，比如对 XXX 表空间中的 YYY 数据页 ZZZ 偏移量的地方做了 AAA 更新，每当执行一个事务就会产生这样的一条或者多条物理日志。

**在事务提交时，只要先将 redo log 持久化到磁盘即可**，可以不需要等到将缓存在 Buffer Pool 里的脏页数据持久化到磁盘。

当系统崩溃时，虽然脏页数据没有持久化，但是 redo log 已经持久化，接着 MySQL 重启后，可以根据 redo log 的内容，将所有数据恢复到最新的状态。

!!!note "为什么需要 redo log？"

    - **实现事务的持久性，让 MySQL 有 crash-safe 的能力**，能够保证 MySQL 在任何时间段突然崩溃，重启后之前已提交的记录都不会丢失；
    - **将写操作从「随机写」变成了「顺序写」**，提升 MySQL 写入磁盘的性能。

### redo log 和 undo log 区别在哪？

两种日志是属于 InnoDB 存储引擎的日志，它们的区别在于：

- redo log 记录了此次事务**「完成后」**的数据状态，记录的是更新之后的值；
- undo log 记录了此次事务**「开始前」**的数据状态，记录的是更新之前的值；

事务提交之前发生了崩溃，重启后会通过 undo log 回滚事务，事务提交之后发生了崩溃，重启后会通过 redo log 恢复事务

### redo log 是直接写入磁盘的吗？

==redo log 也有自己的缓存—— **redo log buffer**，每当产生一条 redo log 时，会先写入到 redo log buffer，后续在持久化到磁盘==。

### redo log 什么时候刷盘？

## binlog

MySQL 在完成一条更新操作后，Server 层还会生成一条 binlog，等之后事务提交的时候，会将该事物执行过程中产生的所有 binlog 统一写 入 binlog 文件。

**binlog 文件是记录了所有数据库表结构变更和表数据修改的日志，不会记录查询类的操作，比如 SELECT 和 SHOW 操作**。

### redo log 和 bin log 有什么区别？

1、适用对象不同：

- binlog 是 MySQL 的 Server 层实现的日志，所有存储引擎都可以使用；
- redo log 是 Innodb 存储引擎实现的日志；

2、文件格式不同：

- binlog 有 3 种格式类型，分别是 STATEMENT（默认格式）、ROW、 MIXED.
- redo log 是物理日志，记录的是在某个数据页做了什么修改，比如对 XXX 表空间中的 YYY 数据页 ZZZ 偏移量的地方做了 AAA 更新；

3、写入方式不同：

- binlog 是追加写，写满一个文件，就创建一个新的文件继续写，不会覆盖以前的日志，保存的是全量的日志。
- redo log 是循环写，日志空间大小是固定，全部写满就从头开始，保存未被刷入磁盘的脏页日志。

4、用途不同：

- binlog 用于备份恢复、主从复制；
- redo log 用于掉电等故障恢复。

### binlog 什么时候刷盘？

事务执行过程中，先把日志写到 binlog cache（Server 层的 cache），**事务提交的时候，再把 binlog cache 写到 binlog 文件中**。

## 为什么需要两阶段提交？

事务提交后，redo log 和 binlog 都要持久化到磁盘，但是这两个是独立的逻辑，可能出现半成功的状态，这样就造成两份日志之间的逻辑不一致。

在持久化 redo log 和 binlog 这两份日志的时候，如果出现半成功的状态，就会造成主从环境的数据不一致性。这是因为 redo log 影响主库的数据，binlog 影响从库的数据，所以 redo log 和 binlog 必须保持一致才能保证主从数据一致。

**MySQL 为了避免出现两份日志之间的逻辑不一致的问题，使用了「两阶段提交」来解决，两阶段提交其实是分布式事务一致性协议**，它可以保证多个逻辑操作要不全部成功，要不全部失败，不会出现半成功的状态。

**两阶段提交把单个事务的提交拆分成了 2 个阶段，分别是「准备（Prepare）阶段」和「提交（Commit）阶段」**，每个阶段都由协调者（Coordinator）和参与者（Participant）共同完成。

### 两阶段提交的过程

![](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/how_update/%E4%B8%A4%E9%98%B6%E6%AE%B5%E6%8F%90%E4%BA%A4.drawio.png?image_process=watermark,text_5YWs5LyX5Y-377ya5bCP5p6XY29kaW5n,type_ZnpsdHpoaw,x_10,y_10,g_se,size_20,color_0000CD,t_70,fill_0)

事务的提交过程有两个阶段，就是将 redo log 的写入拆成了两个步骤：prepare 和 commit，中间再穿插写入 binlog，具体如下：

- ==prepare 阶段==：将 XID（内部 XA 事务的 ID） 写入到 redo log，同时将 redo log 对应的事务状态设置为 prepare，然后将 redo log 持久化到磁盘（innodb_flush_log_at_trx_commit = 1 的作用）；
- ==commit 阶段==：把 XID 写入到 binlog，然后将 binlog 持久化到磁盘（sync_binlog = 1 的作用），接着调用引擎的提交事务接口，将 redo log 状态设置为 commit，此时该状态并不需要持久化到磁盘，只需要 write 到文件系统的 page cache 中就够了，因为只要 binlog 写磁盘成功，就算 redo log 的状态还是 prepare 也没有关系，一样会被认为事务已经执行成功；

{==

**两阶段提交是以 binlog 写成功为事务提交成功的标识，因为 binlog 写成功了，就意味着能在 binlog 中查找到与 redo log 相同的 XID。**

==}

> 事务没提交的时候，redo log 也是可能被持久化到磁盘的。

### 两阶段提交有什么问题？

两阶段提交虽然保证了两个日志文件的数据一致性，但是性能很差，主要有两个方面的影响：**磁盘 I/O 次数高（至少 2 次刷盘，一次 redo log 输盘，一次 bing log 落盘）** 和 **锁竞争激烈（多事务加锁保证日志提交顺序一致）**。

{==

MySQL 引入了** binlog 组提交（group commit）机制**，当有多个事务提交的时候，会**将多个 binlog 刷盘操作合并成一个，从而减少磁盘 I/O 的次数**。

==}

## MySQL 磁盘 I/O 很高，优化方法

- 设置组提交的两个参数： `binlog_group_commit_sync_delay` 和 `binlog_group_commit_sync_no_delay_count` 参数，延迟 binlog 刷盘的时机，从而减少 binlog 的刷盘次数。
- 将 sync_binlog 设置为大于 1 的值（比较常见是 100~1000），表示每次提交事务都 write，但累积 N 个事务后才 fsync，相当于延迟了 binlog 刷盘的时机。但是这样做的风险是，主机掉电时会丢 N 个事务的 binlog 日志。
- 将 `innodb_flush_log_at_trx_commit` 设置为 2。表示每次事务提交时，都只是缓存在 redo log buffer 里的 redo log 写到 redo log 文件。但是这样做的风险是，主机掉电的时候会丢数据。

- [小林 coding: 图解 MySQL-日志篇](https://xiaolincoding.com/mysql/log/how_update.html)
