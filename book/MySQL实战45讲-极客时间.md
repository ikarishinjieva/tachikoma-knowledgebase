本笔记版权声明: 极客时间版权所有: https://time.geekbang.org/column/article/82865

# 07 | 行锁功过：怎么减少行锁对性能的影响？

当备库用–single-transaction 做逻辑备份的时候，如果从主库的 binlog 传来一个 DDL 语句会怎么样?

```
Q1:SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
Q2:START TRANSACTION  WITH CONSISTENT SNAPSHOT；
/* other tables */
Q3:SAVEPOINT sp;
/* 时刻 1 */
Q4:show create table `t1`;
/* 时刻 2 */
Q5:SELECT * FROM `t1`;
/* 时刻 3 */
Q6:ROLLBACK TO SAVEPOINT sp;
/* 时刻 4 */
/* other tables */
```

show create 是为了拿到表结构 (Q4)，然后正式导数据 （Q5），回滚到 SAVEPOINT sp，在这里的作用是释放 t1 的 MDL 锁 （Q6）。

# 12 | 为什么我的MySQL会“抖”一下？

innodb_io_capacity 建议设置成磁盘的IOPS

# 16 | “order by”是怎么工作的？

你可以用下面介绍的方法，来确定一个排序语句是否使用了临时文件。

```
/* 打开 optimizer_trace，只对本线程有效 */
SET optimizer_trace='enabled=on'; 

/* @a 保存 Innodb_rows_read 的初始值 */
select VARIABLE_VALUE into @a from  performance_schema.session_status where variable_name = 'Innodb_rows_read';

/* 执行语句 */
select city, name,age from t where city='杭州' order by name limit 1000; 

/* 查看 OPTIMIZER_TRACE 输出 */
SELECT * FROM `information_schema`.`OPTIMIZER_TRACE`\G

/* @b 保存 Innodb_rows_read 的当前值 */
select VARIABLE_VALUE into @b from performance_schema.session_status where variable_name = 'Innodb_rows_read';

/* 计算 Innodb_rows_read 差值 */
select @b-@a;

```

---

max_length_for_sort_data，是 MySQL 中专门控制用于排序的行数据的长度的一个参数。它的意思是，如果单行的长度超过这个值，MySQL 就认为单行太大，要换一个算法。

# 18 | 为什么这些SQL语句逻辑相同，性能却差异巨大？

```
mysql> select * from trade_detail where tradeid=$L2.tradeid.value; 
```

其中，$L2.tradeid.value 的字符集是 utf8mb4。

... 因此， 在执行上面这个语句的时候，需要将被驱动数据表里的字段一个个地转换成 utf8mb4，再跟 L2 做比较。

相当于:

```
select * from trade_detail  where CONVERT(traideid USING utf8mb4)=$L2.tradeid.value; 
```

导致全表扫描

如果改成以下形式

```
select operator from tradelog  where traideid =CONVERT($R4.tradeid.value USING utf8mb4); 
```

你看，这里的 CONVERT 函数是加在输入参数上的，这样就可以用上被驱动表的 traideid 索引。

# 19 | 为什么我只查一行的语句，也执行这么慢？

通过查询 sys.schema_table_lock_waits 这张表，我们就可以直接找出造成阻塞的 process id，把这个连接用 kill 命令断开即可。

https://static001.geekbang.org/resource/image/74/01/74fb24ba3826e3831eeeff1670990c01.png

# 21 | 为什么我只改一行的语句，锁这么多？

需要注意，在这个例子中，lock in share mode 只锁覆盖索引，但是如果是 for update 就不一样了。 执行 for update 时，系统会认为你接下来要更新数据，因此会顺便给主键索引上满足条件的行加上行锁。

这个例子说明，锁是加在索引上的；同时，它给我们的指导是，如果你要用 lock in share mode 来给行加读锁避免数据被更新的话，就必须得绕过覆盖索引的优化，在查询字段中加入索引中不存在的字段。比如，将 session A 的查询语句改成 select d from t where c=5 lock in share mode。你可以自己验证一下效果。

---

(非常重要)

我总结的加锁规则里面，包含了两个“原则”、两个“优化”和一个“bug”。

- 原则 1：加锁的基本单位是 next-key lock。希望你还记得，next-key lock 是前开后闭区间。
- 原则 2：查找过程中访问到的对象才会加锁。
- 优化 1：索引上的等值查询，给唯一索引加锁的时候，next-key lock 退化为行锁。
- 优化 2：索引上的等值查询，向右遍历时且最后一个值不满足等值条件的时候，next-key lock 退化为间隙锁。
- 一个 bug：唯一索引上的范围查询会访问到不满足条件的第一个值为止。

---

next-key lock 是分成间隙锁和行锁两段来执行的

# 22 | MySQL有哪些“饮鸩止渴”提高性能的方法？

图 3 从 information_schema.innodb_trx 查询事务状态

这个结果里，trx_mysql_thread_id=4，表示 id=4 的线程还处在事务中。

通过下面的流程, 我们可以预先发现慢查询导致的性能问题: 

1. 上线前，在测试环境，把慢查询日志（slow log）打开，并且把 long_query_time 设置成 0，确保每个语句都会被记录入慢查询日志；
2. 在测试表里插入模拟线上的数据，做一遍回归测试；
3. 观察慢查询日志里每类语句的输出，特别留意 Rows_examined 字段是否与预期一致。（我们在前面文章中已经多次用到过 Rows_examined 方法了，相信你已经动手尝试过了。如果还有不明白的，欢迎给我留言，我们一起讨论）。

# 23 | MySQL是怎么保证数据不丢的？

写日志过程细化: 

1. redo log prepare: write
2. binlog: write
3. redo log prepare: fsync
4. binlog: fsync
5. redo log commit: write

# 24 | MySQL是怎么保证主备一致的？

图2是update语句的主从复制流程, 包括undo log/redo log/binlog

# 26 | 备库为什么会延迟好几个小时？

如图4例: 多线程回放时, 除了主键, 需要考虑唯一键冲突的情况. 如果只按照主键分配worker, 会导致唯一键冲突使得并发失败.

# 30 | 答疑文章（二）：用动态的观点看加锁

(非常重要) 如何看死锁

# 35 | join语句怎么优化？ (非常重要)

MRR 能够提升性能的核心, 在于，这条查询语句在索引 a 上做的是一个范围查询（也就是说，这是一个多值查询），可以得到足够多的主键 id。这样通过排序以后，再去主键索引查数据，才能体现出“顺序性”的优势。

# 40 | insert语句的锁为什么这么多？

至于这个语句的执行为什么需要临时表，原因是这类一边遍历数据，一边更新数据的情况，如果读出来的数据直接写回原表，就可能在遍历过程中，读到刚刚插入的记录，新插入的记录如果参与计算逻辑，就跟语义不符。

# 44 | 答疑文章（三）：说一说这些好问题

BNL 算法的执行逻辑是：
1. 首先，将驱动表的数据全部读入内存 join_buffer 中，这里 join_buffer 是无序数组；
2. 然后，顺序遍历被驱动表的所有行，每一行数据都跟 join_buffer 中的数据进行匹配，匹配成功则作为结果集的一部分返回。

Simple Nested Loop Join 算法的执行逻辑是：顺序取出驱动表中的每一行数据，到被驱动表去做全表扫描匹配，匹配成功则作为结果集的一部分返回。

这两位同学的疑问是，Simple Nested Loop Join 算法，其实也是把数据读到内存里，然后按照匹配条件进行判断，为什么性能差距会这么大呢？

解释这个问题，需要用到 MySQL 中索引结构和 Buffer Pool 的相关知识点：

1. 在对被驱动表做全表扫描的时候，如果数据没有在 Buffer Pool 中，就需要等待这部分数据从磁盘读入；
从磁盘读入数据到内存中，会影响正常业务的 Buffer Pool 命中率，而且这个算法天然会对被驱动表的数据做多次访问，更容易将这些数据页放到 Buffer Pool 的头部（请参考第 35 篇文章)
2. 即使被驱动表数据都在内存中，每次查找“下一个记录的操作”，都是类似指针操作。而 join_buffer 中是数组，遍历的成本更低。

所以说，BNL 算法的性能会更好。

# 技巧
- 通过 `show status like '%Innodb_rows_read%'`, 查看InnoDB扫描了多少行
- 如何看死锁
- `information_schema.innodb_trx 查询事务状态` 查询事务状态
- 加锁规则里面，包含了两个“原则”、两个“优化”和一个“bug”
- optimizer_trace



