- [9] 通过 TTL 或 Identification 判断网络中是否"中间商"
- [19] SACK机制: 在ACK中带上已经收到的包的编号, 使得发送端可一次性重发多个丢失的包 (具体作用在 Wireshark网络分析就这么简单 一书中有详细描述)
- [20] 过滤式, 用于找出 Delayed Ack 的包: `tcp.analysis.ack_rtt > 0.2 and tcp.len == 0`
- [30] 三板斧:
  - Statistics -> Summary
  - Analyze -> Expert Infos
  - Statistics -> TCP StreamGraph -> TCP Sequence Graph (Stevens)
- [33] Nagle 算法描述: 
  ```
  if 有新数据要发送
    if 数据量超过MSS (即一个TCP包所能携带的最大数据量)
      立即发送
    else
      if 之前发出去的数据尚未确认
        把新数据缓存起来, 凑够 MSS 或等确认到达后再发送
      else 
        立即发送
      endif
    endif
  endif
  ```
- [47] Delay Ack会导致 用3个包即可完成 TCP的断开操作
- [47] 不同机器发出的包, 在wireshark中进行排序, 包实际上不一定按标号顺序发送, 而是可能会因为包延迟而产生标记错误
- [70] 在数据接收方, 计算在途字节数没有意义; 在数据发送方才能计算在途字节数: `在途 = seq + len - ack`
- [54] Wireshark的提示信息详解
  - [Packet size limited during capture]
  - [TCP previous segment not captured]
  - [TCP ACKed unseen segment]
  - [TCP Out-of-ORder]
  - [TCP Dup ACK]
  - [TCP Fast Retransmission]
  - [TCP Retransmission]
  - [TCP zerowindow]
  - [TCP window Full]
  - [TCP segment of a reassembled PDU]
  - [Continuation to #]
  - [Time-to-live exceeded (Fragment reassembly time exceeded)]
- [71] 找到网络拥塞水位线的方法: 找到第一个重传包, 定位到其重传的原始包, 计算原始包的在途字节数
- [75] 625MB/s的网络流量大约需要耗费 5GHz 的CPU. 为了缓解CPU的压力, 将一些工作 外包(offload) 给网卡, 比如TCP的分段工作. LSO = Large Send Offload (将大包的发送工作外包给网卡, 由网卡进行分段?). 由于抓包是在CPU视角进行的, 所以可以抓到 `len > MSS` 的包.
- [82] 工具: Statistics -> Conversations
- [84] UDP包被网络层分片传输, 如果有些分片丢失, 接收方会放弃整个包, 并发送ICMP的超时消息
- [85] 与UDP相比, TCP自己有分段机制, 避免了被网络层分片, 重传效率要高得多
- [93] ping命令可以指定包大小, 以及是否允许分段
- [99] 多个网卡可绑定成一个NIC Teaming, 类型为Transmit Load Balancing (TLB), 收包只由一个网卡负责, 而发包工作则平摊给所有网卡.
- [107] Wireshark官网上可以下载示例包, 用于学习
- [107] 暂停帧用于流控, 其目标MAC地址是固定的 (01-80-C2-00-00-01)
- [112] [TCP window Full] 与 [TCP zerowindow]的不同: 
  - [TCP window Full]: 该包发送方意识到在途字节数已经达到的对方的接收窗欧酷大小
  - [TCP zero window]: 该包发送方意识到自己的缓存区已经满了, 无法接收更多数据
- [120] Window scale: TCP头不够表达窗口大小 (历史原因), 因此握手阶段需要协商一个系数
- [125] 通过对比正常与异常的TTL, 判断正常与异常的网络节点是否不是同一设备
- [148] HttpDNS的轶事 (HttpDNS没有得到使用)
- [157] 判断拦截的思路
  - TTL
  - Identification
  - 包大小 (第三方不知道TCP握手阶段协商的Window scale的系数)

