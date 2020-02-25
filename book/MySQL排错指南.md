- [15] mysql --column-type-info, 可输出列信息
    ````
    master [localhost:18923] {msandbox} ((none)) > select 1 from dual;
    
    Field   1:  `1`
    Catalog:    `def`
    Database:   ``
    Table:      ``
    Org_table:  ``
    Type:       LONGLONG
    Collation:  binary (63)
    Length:     1
    Max_length: 1
    Decimals:   0
    Flags:      NOT_NULL BINARY NUM


    +---+
    | 1 |
    +---+
    | 1 |
    +---+
    1 row in set (0.00 sec)
    ```

- [54] Update带有sleep, 用于调试SQL
    ```
    update a set a=sleep(100) where a = 0;
    ```

- [124] 检查Select_scan (`The number of joins that did a full scan of the first table.`), Select_full_range_join (`The number of joins that used a range search on a reference table.`), 判断join是否足够多(??此处逻辑有点奇怪, 状态量并不会随join_buffer_size大小改变??), 判断是否需要增加 join_buffer_size

- [142] `vm.overcommit_ratio` 会影响进程划分的内存大小


