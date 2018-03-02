- MySQL
    - Performance
        1. [Wakeup Affinity/NUMA relates](https://mp.weixin.qq.com/s/DG1v8cUjcXpa0x2uvrRytA)

- Java
    - Flame Graph
        1. [JavaOne 2015 Java Mixed-Mode Flame Graphs](https://www.slideshare.net/brendangregg/javaone-2015-java-mixedmode-flame-graphs)
            1. Page fault Flame Graph
            2. Context switch Flame Graph
            3. Disk I/O request Flame Graph
            4. TCP event Flame Graph
            5. CPU cache missing Flame Graph

- Linux
    - Perf
        - [CPU pipeline front-end](https://software.intel.com/en-us/blogs/2011/11/22/pipeline-speak-learning-more-about-intel-microarchitecture-codename-sandy-bridge)
        ```
        the front-end does two main things - fetch instructions (from where program binaries are stored in memory or the caching system), and decode them into micro-operations. 
        ```
        - [CPU pipeline back-end](https://software.intel.com/en-us/blogs/2011/12/01/pipeline-speak-part-2-the-second-part-of-the-sandy-bridge-pipeline)
        ```
        The back-end of the pipeline is responsible for executing the micro-operations the front-end generates. 
        ```
        - [Perf examples](http://www.brendangregg.com/perf.html)
