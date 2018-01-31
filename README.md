# Code-young HIDS 
----------
**Code young HIDS**是一款由 `Agent`，`Collector`和`Analyser`三大部分组成，集异常检测、监控管理为一体，拥有异常行为发现、快速阻断、高级分析等功能，可从多个维度行为信息中发现入侵行为。

**Agent**为采集者角色，收集服务器信息、开机启动项、计划任务、监听端口、服务、登录日志、用户列表，实时监控文件操作行为、网络连接、执行命令，初步筛选整理后通过RPC协议传输到Server节点。

**Collector**为系统的中间物理节点，定期同步analyser下发给agent的指令及更新信息供agent取用。

**Analyser**为整套系统的大脑，支持agent宿主机信息查询，agent模块梯度热更新，分组，任务关联，告警提示，对从各Agent接收到的信息和行为进行分析检测和保存，可从各个维度的信息中发现异常登录行为、异常网络连接行为、异常命令调用行为等，从而实现对入侵行为实时预警。


## 文档

* [部署文档](./docs/)
* [使用帮助](./docs/)
* [规则编写](./docs/)
* [编译指南](./docs/)

## 功能特点（待完善）


- 实时监控、秒级响应
- 静默部署、信息收集
- 横向对比、可发现未知威胁。
- 高级分析功能，可溯源
- 全局快速阻断（进程、文件、网络）


> 相比其他同类应用，维护使用此系统需要有一定的安全判断分析能力。


## 整体架构图
![](./docs/lc.png)


## TODO
- 配置精细化
- 基线核查
- rootkit检查
- 实时进程及文件监控
- Aent与Server通信的单双向验证（SSL）
- 幻影蜜罐（基于真实环境虚化出来的动态蜜罐，无需独立部署）
- 支持多场景（办公环境、线上环境、Docker）
- 机器学习判断可疑行为


## 进程结构
```
├─agent 
│  ├─agent //守护进程
│  ├─agent //指令接受、状态反馈、模块更新
│  ├─sysdetect //信息收集（文件监控、特性信息上报）
│  ├─syscheck  //信息收集（软件信息、进程信息、用户信息、组信息、自启动信息、服务信息、端口信息、防火墙信息、内核模块信息、配置文件信息）
│  └─logstash  //日志转发（RPC、队列缓存、离线sqlite3缓存）
│  
├─collector // Collector工程
│  ├─collector //守护进程
│  └─collector //转发agent通知、同步analyser指令、统计agent状态，上报agent心跳
│
├─analyser
│  ├─analyser//守护进程
│  └─analyser//控制agent更新、分析agent状态、统计agent日志
│
└─consumer//消费kafka-topic更新本地静态日志库
```

## 源码结构
```
├─public
│  ├─network //TSL传输
│  ├─storage //mysql封装
│  └─task //核心任务注册调度
│
├─agent // Agent工程
│  ├─dlfunc //检测信息收集
│  ├─logstash //RPC 日志缓存转发
│  ├─report //agent通知上报
│  ├─storage //sqlite3本地信息缓存
│  └─task //agent 核心任务注册及消息处理
│
├─collector // Collector工程
│  ├─collector//核心任务注册及消息处理
│  └─put_cgi //agent 请求reply处理
│
├─analyser
│  ├─analyser//核心任务注册及消息处理
│  ├─security-env//安全运行环境
│  ├─consumer//libkafka消费日志
│  └─put_cgi //Collector 请求reply处理
│
├─web // Web控制台项目
│  ├─home // 配置文件
│  ├─主机管理
│  ├─Collector管理
│  ├─任务模板
│  ├─日志管理
│  └─系统管理
│
└─end
```

## 参与者及致谢

参与者：杨学良

参考：
ossec
osquery
netdata
nicstat
nicstat
kmod
iptables
sysinitd
systemd
iotop
tiger


致谢列表：
