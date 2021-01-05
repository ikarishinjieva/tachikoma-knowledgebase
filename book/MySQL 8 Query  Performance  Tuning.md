
- [21] dim_STAT 经常作为压力测试中的监控 (与Oracle官方报告中的性能图非常类似)
- [22] TPC-E是TPC-C的复杂版本, TPC-H测试的是优化器表现
- [43] 图3-2: sysbench的各个自定义函数接口的流程图, 包括: do_prepare(), create_table(), cleanup()等
- [88] Performance_schema: 
  - Event type (由大往小)
    - transactions
    - statements
    - stages
    - waits
  - Event Scope
    - current: 正在进行的事件, 或idle线程的最后一个事件. ([92] 主键: thread_id + event_id)
    - history: 保留每个线程的最后10个事件. ([92] 主键: thread_id + event_id)
    - history_long: 保留所有事件的最后10000个事件. (无主键)
    
- [106] sys.format_time可见pico time可读化
- [122] information_schema中, 有与buffer pool和LRU分布相关的表, 但访问代价巨大, 谨慎使用
- [129] information_schema中, 与引擎相关的统计值, 由于获取成本高, 增加了缓存
  - information_schema_stats_expiry 控制了其缓存策略
  - 表7-5, 列出了那些表/列受到缓存影响
- [159] log_slow_extra, 仅对file格式的慢日志有效, 多记录了一些handler的统计信息

- [354] innodb索引统计信息的计算, 相当于使用RUC隔离级别访问数据, 会受到正在进行的事务的影响
- [354] 有一些情况可以禁用innodb索引统计信息, 比如: 用表模拟物化视图
- [362] innodb索引统计信息分为 可持久化的(persistent) 和 瞬时的 (transient) (区别? 设计原理?)

- [375] 用CTE快速插入记录
```
  CREATE TABLE chapter_15.t1 (
id int unsigned NOT NULL auto_increment,
val varchar(36) NOT NULL,
PRIMARY KEY (id)
) STATS_PERSISTENT=1,
STATS_SAMPLE_PAGES=50,
STATS_AUTO_RECALC=1;

SET SESSION cte_max_recursion_depth = 1000000;

START TRANSACTION;

 INSERT INTO chapter_15.t1 (val)
WITH RECURSIVE seq (i) AS (
SELECT 1
UNION ALL
SELECT i + 1
FROM seq WHERE i < 1000000
)
SELECT UUID()
FROM seq;

COMMIT;
```

- [414] 用统计直方图影响执行计划的例子

- [422] 表17-1, 在没有直方图/索引的情况下, 各类操作默认的过滤度是多少
  - ALL: 100%
  - Equality: 10% (name = 'Sydney')
  - Not Equality: 90% (name <> 'Sydney')
  - Inequality: 33.3% (Population > 4000000)
  - Between: 11.1% (Population between 1000000 and 4000000)
  - In: min(#items * 10, 50)% (Name IN ('a', 'b'))
  
- [425] 状态量 Last_query_cost: 上一个SQL的执行计划预估成本, 与Explain analyze不同 (Explain analyze是实际成本)

- [436] BNL算法: 使用join_buffer作为外层表的结果缓存

- [436] Hash join算法: 使用join_buffer作为哈希表的内存部分, 超出后, 使用磁盘作为扩展

- [438] Hash join
  - 8.0.20前, 仅支持inner join
  - 8.0.20后, 支持anti/semi/outer join
  
- [442] Hash join 遇到limit不能短路退出

- [444] Index Merge: 将多个索引合并使用, 可支持的算法: 
  - Intersection (AND)
  - Union (OR)
  - Sort-Union (OR with ranges)

- [450] 优化器很难决策使用 Index merge是否更优, 需要人为hint干预

- [452] MRR = Multi-range-read: 从辅助索引中扫描, 然后按主键排序, 再向聚簇索引回表

- [454] MRR 排序使用read_rnd_buffer

- [454] BKA = Batched key access: 相当于BNL + MRR, 适用于大量磁盘操作的SQL, BKA需要手工开启

- [462] 优化器配置: Engine cost, 可配置磁盘IO与内存IO的代价比例, 影响优化器的计算
  - [465] server_cost: 包括临时表代价, 行扫描代价, 等
  
- [489] MySQL锁分类:
  - User-level lock
  - Flush locks
  - Metadata lock
  - Explicit table locks
    - lock tables
    - flush table with read lock
  - Implicit table locks
  - Record locks
  - Gap lock / Next-key lock / Predicate lock(Spatial Index)
  - Insert intention locks
  - Auto-increment locks
  - Backup locks (8.0)
    - 以防在在线备份时出现以下情况, 破坏备份一致性
      - 对文件有创建/销毁的SQL: create table...
      - ACL
      - 不在redo log中记录的DDL
  - Log locks (8.0)
    - 访问log的元信息时, 需要使用log locks, 包括访问p_s.log_status
    

- [510] innodb_autoinc_lock_mode的三种模式, 是在 性能 v.s. 连续性保障 中取得平衡

- [528] p_s中监控锁的表: 
  - data_locks
  - data_lock_waits
  - metadata_locks
  - table_handles: 表锁
  
- [547] p_s中的sample query列, 采样被取代的条件:
  - 首次出现
  - 出现 更大的timer_wait
  - 受 p_s.max_digest_sample_age 控制
  
- [548] p_s中的信息列的常见使用方法: 
  - examined row 远大于返回行数: 索引使用不力
  - No index used / No good index used 高: 索引需加强
  - Full join 高: 
    - 优化索引
    - join缺失条件
  - range check高: 优化索引
  - 分位数劣化严重: 时间相关的压力过载
  - 磁盘临时表使用量高: 
    - 使用聚合的索引优化
    - 临时表内存不足
  - sort merge高: 生成新的排序索引
  - error / warning 量大
  
- [551] prepare statement的p_s统计不在上述表中, 在p_s.prepare_statement_instances中

- [559] p_s.table_io_waits_summary_by_index_usage 可用于评价索引的使用程度, NULL代表不使用索引

- [581] sys.statement_analyser的使用流程

- [603] explain analyze 会执行SQL并采集信息

- [628] 对explain的个字段的解释:
  - [631] select type
  - [632] access type
  - [639] extra info
  
- [665] sys.ps_trace_thread 可追踪某线程的p_s event, 而不占用太多空间 (使用polling策略, 将生成的event及时转移走)
  - [669] 使用举例
  
- [675] p_s.trace_statement_digest 用于分析某digest的语句, 包括追踪执行, 列出stage, 执行计划等

- [685] undo log的长度评估
  - history list length是undo log未清理的事务数, 其越长, MVCC倒查的成本越高
  
- [687] innodb_max_purge_lag 当history list length超过该值, 在DML中加入延迟以缓解undo log的剧烈增长

- [690] innodb_trx 表的各列含义, 其中包括事务修改的行数

- [697] innodb mointor的内容, 包括: 
  - RW/RO的事务统计
  - 等待redo log的程度
  - undo log的统计
  
- [706] 用于找到某事务最后10条命令的p_s SQL

```
SELECT EVENT_ID, SQL_TEXT,
FORMAT_PICO_TIME(TIMER_WAIT) AS Latency,
IF(END_EVENT_ID IS NULL, 'YES', 'NO') AS IsCurrent
FROM ((SELECT EVENT_ID, END_EVENT_ID,
TIMER_WAIT,
SQL_TEXT, NESTING_EVENT_ID,
NESTING_EVENT_TYPE
FROM performance_schema.events_statements_current
WHERE THREAD_ID = @thread_id
) UNION (
SELECT EVENT_ID, END_EVENT_ID,
TIMER_WAIT,
SQL_TEXT, NESTING_EVENT_ID,
NESTING_EVENT_TYPE
FROM performance_schema.events_statements_history
WHERE THREAD_ID = @thread_id
)
) events
WHERE (NESTING_EVENT_TYPE = 'TRANSACTION'
AND NESTING_EVENT_ID = @event_id)
OR EVENT_ID = @nesting_event_id
ORDER BY EVENT_ID DESC\G
```

- [726] sys.schema_table_lock_waits的使用举例

- [774] buffer pool dump, 对每个页只记录页位置索引, 使用8个字节

- [777] buffer pool 分为新旧两个区, page先进入旧区, 待足一定时间, 且再次被访问到, 才移入新区
  - page made young: 移入新区的比例
  - page not made young: 留在旧区的比例

- [777] buffer pool 中页的淘汰机制, 与新/旧区的关系
  - https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html, 图15-2

- [778] innodb_io_capability: 异步正常刷盘时的IOPS极限. innodb_io_capability_max: 日志落后过多时, 刷盘的IOPS极限.

- [778] O_DIRECT_NO_FSYNC: 在8.0.14后, 可以安全使用? 如果redo log与表空间文件在不同的文件系统, 应使用O_DIRECT?? (原理??)

- [781] redo log file刷盘的策略: 
  - 75%以下: 周期性刷盘
  - 75% - 90%: 触发异步刷盘
  - 90以上%: 触发同步刷盘
  - 刷盘落后程度的公式: [782]
  
- [791] 8.0.14 引入并行读线程, 一个Query的读操作可以并行度, 参数: innodb_parallel_read_threads

- [808] MySQL 5.7 -> 8.0升级, utf8mb4的校验集不同

- [843] select ... skip locked: 可以跳过已有锁的行. 使用场景: 任务分配系统

- [844] eq_range_index_dive_limit: OR/IN条件小于该值时, 对每个值进行评估, 超过该值时, 认为条件在全表中均匀分布

- [856] DDL创建/重建二级索引, 需要使用innodb_sort_buffer_size

- [857] DROP TABLE前, 建议关闭AHI

- [866] 图25-1, innodb文件LSN新旧热力图, 顺序插入数据比随机插入数据的文件更紧凑

- [878] buffer pool越大, 乱序插入越快: buffer pool缓存了乱序页, 使其尽量顺序写.
  - 同理: 导数据前, 建议移除二次索引
  
- [886] MySQL shell, util.import_table可切分且并行导入数据文件


