## Drools系统架构

示例一：

1.单个Drools控制器（Business Central）管理多个KIE Excution Server，常规架构

![kie server simple architecture](assets/kie-server-simple-architecture.png)

2.使用Zookeeper同步的分布式Drools控制器（Business Central）架构

![kieæå¡å¨æ¶æ](assets/kie-server-architecture.png)

示例二：

1.基于KIE Workbench维护规则工程

![img](assets/13416824-6e69672a1bc8c1cf.png)

2.自建后台集成 KIE Workbench

![img](assets/13416824-9c9694a4e3da1f2b.png)

3.自建后台网站集成KIE Excution Server 的 REST API

![

img](assets/13416824-f6738b193fd343c6.png) 