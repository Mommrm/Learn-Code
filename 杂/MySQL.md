## 事务的四大特性
事务的四大特性，简称ACID，分别是：

原子性（Atomicity）：事务被视为一个不可分割的单位，事务中的所有操作要么全部执行成功，要么全部不执行，确保数据不会处于不一致的状态。
一致性（Consistency）：事务执行前后，数据库应保持合法的数据状态，即符合所有的预定义规则，比如外键约束、唯一性约束等。
隔离性（Isolation）：多个事务并发执行时，彼此间不应互相影响，仿佛每个事务都在单独执行。不同的隔离级别对此有不同的保障程度。
持久性（Durability）：一旦事务提交，其效果应该是永久的，即使系统发生故障也应该能够恢复到提交时的状态。

## 并发事务会带来的问题
并发事务可能会引发以下问题：

脏读（Dirty Read）：一个事务读取了另一个事务尚未提交的数据。
不可重复读（Non-Repeatable Read）：在同一个事务中，多次读取同一数据，得到的结果不一致，因为其他事务对该数据进行了修改并提交。
幻读（Phantom Read）：在同一个事务中，多次执行同样的查询，返回的结果集中出现了之前不存在的行，通常是由于其他事务插入了新行。

## 解决并发事务问题/事务的隔离级别
为了解决这些问题，数据库管理系统提供了不同的事务隔离级别，主要包括：

读未提交（Read Uncommitted）：最低级别，存在脏读、不可重复读和幻读问题。
读已提交（Read Committed）：解决了脏读问题，但仍有不可重复读和幻读。
可重复读（Repeatable Read）：解决了脏读和不可重复读，但幻读仍可能发生。
串行化（Serializable）：最高级别，通过串行执行事务来避免所有并发问题，但牺牲了并发性能。

## 脏读、不可重复读和幻读的区别
脏读：读取了其他事务未提交的数据。
不可重复读：在同一个事务内多次读取同一数据，结果不一致。
幻读：同一事务内多次执行相同的查询，因其他事务插入新记录，导致结果集中出现新行。

## 事务的ACID靠什么保证
原子性：通过日志（如undo log）记录事务中的操作，如果事务失败可以回滚。
一致性：通过数据库的约束、触发器和事务代码本身来保证。
隔离性：通过锁定机制（如行锁、表锁）和多版本并发控制（MVCC）来实现。
持久性：通过redo log（重做日志）保证，即使系统崩溃也能恢复数据。

## 事务的三大日志及其作用
Undo Log：用于事务的回滚，存储事务前的原始数据，确保事务的原子性。
Redo Log：用于事务的持久化，记录事务对数据库的修改操作，确保事务的持久性，即使系统崩溃也能恢复数据。
Bin Log（Binary Log）：主要用于数据的复制和恢复，记录了对数据库的所有更改操作，支持主从复制和数据备份。

## bin log 和 redo log的区别
Bin Log记录的是逻辑上的变化，比如SQL语句，主要用于数据复制和恢复。
Redo Log记录的是物理上的数据修改，如页的更改，主要用于崩溃恢复，确保事务的持久性。

## undo log 和 redo log的区别
Undo Log关注的是事务的回滚，存储的是数据的旧状态，以便在事务失败时撤销事务的影响。
Redo Log关注的是事务的持久化，记录的是数据的修改操作，确保即使系统崩溃也能根据日志恢复数据到最新状态。