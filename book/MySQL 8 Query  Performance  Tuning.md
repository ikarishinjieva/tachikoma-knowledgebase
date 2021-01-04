
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

