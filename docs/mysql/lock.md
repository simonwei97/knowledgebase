---
date: 2024-01-02
categories:
  - MySQL
search:
  boost: 2
hide:
  - footer
---

## 死锁

死锁的四个必要条件：互**斥、占有且等待、不可强占用、循环等待**。只要系统发生死锁，这些条件必然成立，但是只要破坏任意一个条件就死锁就不会成立。

## 如何避免死锁？

在数据库层面，有两种策略

- **设置事务等待锁的超时时间**：当一个事务的等待时间超过该值后，就对这个事务进行回滚，于是锁就释放了，另一个事务就可以继续执行了。在 InnoDB 中，参数 `innodb_lock_wait_timeout` 是用来设置超时时间的，默认值时 50 秒。
- **开启主动死锁检测**：主动死锁检测在发现死锁后，主动回滚死锁链条中的某一个事务，让其他事务得以继续执行。将参数 innodb_deadlock_detect 设置为 on，表示开启这个逻辑，默认就开启。

```sql
-- 当发生超时后，就出现下面这个提示
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction

-- 当检测到死锁后，就会出现下面这个提示
ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting
```

## 死锁检查处理

1. 查看事务执行 SQL 过程中加了什么锁

```sql
select * from performance_schema.data_locks\G
```

2. 查看最近一个死锁情况

```sql
mysql> SHOW ENGINE INNODB STATUS;
```
