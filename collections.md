- MySQL
    - Performance
        1. [Wakeup Affinity/NUMA relates](https://mp.weixin.qq.com/s/DG1v8cUjcXpa0x2uvrRytA)
    - Deadlock
        1. [Replace into deadlock analysis](https://www.kancloud.cn/taobaomysql/monthly/67177)
    - Checkpoint
        1. [MySQL checkpoint 深入分析](https://www.cnblogs.com/geaozhang/p/7341333.html)
    - Group Commit
        1. [WL#5223: Group Commit of Binary Log
](https://dev.mysql.com/worklog/task/?id=5223) 
        ```
        列举了原始commit模型的5个缺点
        ```
    - InnoDB
        1. [InnoDB diagrams](https://github.com/jeremycole/innodb_diagrams)
        2. [The basics of InnoDB space file layout](https://blog.jcole.us/2013/01/03/the-basics-of-innodb-space-file-layout/)
        
    - AHI
        1. [AHI performance](https://www.percona.com/blog/2016/04/12/is-adaptive-hash-index-in-innodb-right-for-my-workload/)
        2. [InnoDB Adaptive hash index介绍](http://mysql.taobao.org/monthly/2015/09/01/)
        ```
        包括代码分析和观察手段
        ```
        3. [InnoDB Adaptive Hash Index调研总结 by 何登成](http://hedengcheng.com/?p=458)

- Java
    - Flame Graph
        1. [JavaOne 2015 Java Mixed-Mode Flame Graphs](https://www.slideshare.net/brendangregg/javaone-2015-java-mixedmode-flame-graphs)
            1. Page fault Flame Graph
            2. Context switch Flame Graph
            3. Disk I/O request Flame Graph
            4. TCP event Flame Graph
            5. CPU cache missing Flame Graph

- Linux
    - Performance Tracing
        - [Linux Tracing Systems](https://jvns.ca/blog/2017/07/05/linux-tracing-systems/)
        - Perf
            1. [Perf examples](http://www.brendangregg.com/perf.html)

    - CPU Microarchitecture
        - [CPU pipeline front-end](https://software.intel.com/en-us/blogs/2011/11/22/pipeline-speak-learning-more-about-intel-microarchitecture-codename-sandy-bridge)
        ```
        the front-end does two main things - fetch instructions (from where program binaries are stored in memory or the caching system), and decode them into micro-operations. 
        ```
        - [CPU pipeline back-end](https://software.intel.com/en-us/blogs/2011/12/01/pipeline-speak-part-2-the-second-part-of-the-sandy-bridge-pipeline)
        ```
        The back-end of the pipeline is responsible for executing the micro-operations the front-end generates. 
        ```
        - [Tuning Applications Using a Top-down Microarchitecture Analysis Method](https://software.intel.com/en-us/vtune-amplifier-help-tuning-applications-using-a-top-down-microarchitecture-analysis-method)
        ```
        A specific microarchitecture may make available hundreds of events through its PMU.
        ...
        you can benefit from using predefined events and metrics, and the top-down characterization method to convert the data into actionable information
        ```
        - [andikleen/pmu-tools](https://github.com/andikleen/pmu-tools)
        ```
        pmu tools is a collection of tools for profile collection and performance analysis on Intel CPUs on top of Linux perf. 
        ```
        - [Performance Analysis in Modern Multicores](http://cs.haifa.ac.il/~yosi/PARC/yasin.pdf)
        ```
        Introduction of CPU-microarchitecture/Vtune/Top-down
        ```

    - Assembly Instructions
        - Instructions (https://www.aldeid.com/wiki/X86-assembly/Instructions)
        
    - Storage
        - [Linux Storage Stack Diagram](https://www.thomas-krenn.com/en/wiki/Linux_Storage_Stack_Diagram)
        - [深入分析diskstats](http://ykrocku.github.io/blog/2014/04/11/diskstats/)
    - Network
        - [The method to epoll’s madness](https://medium.com/@copyconstruct/the-method-to-epolls-madness-d9d2d6378642)
        - [Wireshark Display Filter Reference](https://www.wireshark.org/docs/dfref/)
        - [How to forcibly kill an established TCP connection in Linux](http://rtomaszewski.blogspot.com/2012/11/how-to-forcibly-kill-established-tcp.html)
            * tcpkill
            ```
            tcpkill listens on the incoming traffic, learns about the SEQ and ACK numbers and generate its RST packet to desynchronize and kill the TCP session
            ```
            * killcx
            ```
            killcx spoofs a SYN packet on behalf of the client and sent it to the server. The server than replays with a valid TCP packet revealing the ACK and SEQ numbers. As soon as this this is on a wire the killcx sniffs this up and sent RST to kill the active session.
            ```
        - [Monitoring and Tuning the Linux Networking Stack: Sending Data](https://blog.packagecloud.io/eng/2017/02/06/monitoring-tuning-linux-networking-stack-sending-data)
        - [Monitoring and Tuning the Linux Networking Stack: Receiving Data](https://blog.packagecloud.io/eng/2016/06/22/monitoring-tuning-linux-networking-stack-receiving-data/)
        
    - Usage
        - [Configure Postfix to Send Mail Using an External SMTP Server](https://www.linode.com/docs/email/postfix/postfix-smtp-debian7/)
        
    - Linux Capability
        - [Linux Capability golang library](https://github.com/elastic/go-seccomp-bpf)
    - Profs
        - [A month of /proc](https://ops.tips/blog/a-month-of-proc/)
        - [How is /proc able to list process IDs?](https://ops.tips/blog/how-is-proc-able-to-list-pids/)
       
- Go
    - Memory
        - [Go Memory Management](https://povilasv.me/go-memory-management/)

- Machine Learning
    - [data visualisation guide](https://www.kaggle.com/learn/data-visualisation)
    - [Understanding Boxplots](https://towardsdatascience.com/understanding-boxplots-5e2df7bcbd51)

- Collections
    - [System Design Primer](https://github.com/donnemartin/system-design-primer)
    
- Monitor
    - Prometheus
        - [Prometheus 不完全避坑指南](https://aleiwu.com/post/prometheus-bp/)
   
- 篆刻
    - [宝典福书册.北京故宫博物院藏.清乾隆时期缂丝](http://b.gmzm.org/2018/%E9%87%91%E7%9F%B3%E4%B9%A6%E6%B3%95/%E5%AE%9D%E5%85%B8%E7%A6%8F%E4%B9%A6%E5%86%8C/index.asp?page=5)
