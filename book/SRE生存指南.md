# 讨论框架

- 监控
- 事故处理
- 发布流程
- 容量规划

# 有趣的内容

- [59] SRE常用ICS(事故指挥系统). 第一规则: 谁先到现场谁负责
- [133] RDD (Readme Driven Development, 自述文件驱动开发) 
- [143] 软件描述的检查清单
  - 监控: 正在编写的代码有基本指标么? 他们正在被收集和存储么? 
  - 事故响应: 你正在编写关于如何对服务进行运维的文档么? 如果发生事故, 有没有报警机制? 任何人收到报警都知道怎么做么? 
  - 事后回顾报告: 当代码发生故障时, 你是否像对待其他发生故障的代码一样编写事后回顾报告呢? 
  - 测试和发布: 是否写了单元测试和集成测试? 是否有文档记录发布流程和回滚流程?
  - 容量规划: 是否记录了容量规划将如何影响系统的性能? 
