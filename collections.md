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
        
        
    - Storage
        - [Linux Storage Stack Diagram](https://www.thomas-krenn.com/en/wiki/Linux_Storage_Stack_Diagram)
        - [深入分析diskstats](http://ykrocku.github.io/blog/2014/04/11/diskstats/)
    - Network
        - [The method to epoll’s madness](https://medium.com/@copyconstruct/the-method-to-epolls-madness-d9d2d6378642)
        - [Wireshark Display Filter Reference](https://www.wireshark.org/docs/dfref/)
       
- Go
    - Memory
        - [Go Memory Management](https://povilasv.me/go-memory-management/)
   
