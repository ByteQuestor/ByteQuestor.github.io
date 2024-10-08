---
title: AWS学习记录
date: 2024-09-10 08:33:11
tags: 公有云
cover: https://raw.gitmirror.com/ByteQuestor/picture/main/AWS.png
description: 完整跟了一个AWS讲座课，于是有了这篇笔记
---

## AWS核心服务类别

+ 计算
+ 联网与内容分发
+ 存储
+ 数据库
+ 安全性、身份与合规性
+ 管理与监督

### 模块总结

三种云计算部署模型

云计算的六大优势

## 云中的计算

###  Amazon EC2

Amazon Elastic Compute Cloud(Amazon EC2) 

#### 客户端和服务器模型

（其实就是一个服务一个客户）

#### 咖啡店例子

比如：咖啡店，顾客发出请求，咖啡师满足顾客请求

在角色上，咖啡师会做咖啡所以能服务顾客，反正顾客肯定不能服务咖啡师

而咖啡师就是我们的服务器，需要提供算力，才能胜任服务器的角色

#### **AWS平台上的虚拟机**

就相当于我们在VMWare上运行的虚拟机，特点：

+   使用安全并且可以调整大小的计算容量
+   在几分钟内启动服务器实例
+   只需按实际使用量付费

#### **EC2  工作原理/流程**

1. 启动实例
2. 连接到该实例（通过网络连接，因为在云上）
3. 使用该实例

###  Amazon EC2 实例类型

#### 四个维度区分实例类型

+ 算力（VCPU）
+ 内存
+ 联网能力
+ 支持的存储的性能

#### 常见类型

+ 通用型 
  + 平衡计算、内存和联网资源
  + 适合各种工作负载
+ 计算优化型
  + 提供高性能处理器
  + 非常适合计算密集型应用程序和批处理工作负载
+ 内存优化型
  + 为内存密集型工作负载提供快速性能
  + 非常适合高性能数据库
+ 加速计算型（模型训练和模型推理大概就是选择这种）
  + 使用硬件加速器加快数据处理
  + 非常适合应用程序流和图形工作负载
+ 存储优化型
  + 提供低延迟和高IOPS存储（IOPS：每秒输入/输出操作数）
  + 适合分布式文件系统和数据仓库应用程序等工作负载

### Amazon EC2定价

#### 按需

+ 无预付成本 或 最低合同期限
+ 非常适合短期、不规律的工作负载

> **多用多付，少用少付，只要不开机就不计费**

#### Spot

+ 非常适合开始和结束时间灵活的工作负载
+ 与按需实例价格相比提供更多节省

> 临时用，因为云上资源有很多人用，实例开开关关，Spot就是为了利用**已经被购买但是暂时没有人利用的实例**，价格只有正常的十分之一，但是要优先保证正常付费的人使用，因此使用此计价方式**要容忍中断**，所以**可以跑无状态应用，不推荐跑数据库**等，如数据分析、模型训练。但是现在被中断的概率很低，，买一次可以用6个小时，六小时内被回收的概率只有5%左右，想继续用在时间到达之前续费

#### 预留

+ 在按需定价的基础上提供计费折扣
+ 要求做出 **1年期或3年期** 的使用承诺

> 相当于长租，长租预付有折扣，就是看到的那些$99/年，但是买了以后，规格什么的不允许换

#### Compute Savings Plan

+ 在计算使用量保持稳定的情况下，与按需实际成本相比，可实现高达66%的节省
+ 要求做出 **1年期或3年期** 的使用承诺

> 相对于**预留**方案，多了一个要求：需要承诺每小时的使用费用，而此方案给客户带来的好处是，买了以后，可以灵活的换规格，甚至可以跨系列，毕竟承诺的是每小时花多少钱，而不是针对某个类型的花费

#### 专用实例

+ 在VPC中的硬件上运行的EC2实例，为单个用户使用
+ 成本高于标准 Amazon EC2 实例

> 如果开10台实例，那么这10台实例是有可能跑在10个物理机上面的，如果不想每个实例都和别人share，那么就要求**专用实例**，这10台实例会跑在同一台宿主上（当然，这台宿主上还是要和别人share，如果**完全不想和别人share，看下面的专用主机**）

#### 专用主机

+ 具有EC2实例容量的物理服务器，为单个客户使用
+ 价格最高的 Amazon EC2 选项

> 物理主机，很贵

###   Amazon EC2  Auto Scanling

#### 咖啡店例子

比如：咖啡店，早上客流量少，一个咖啡师就能应付过来，当客流量多起来后，必须再喊一个咖啡师来帮忙

工作日流量少，可能一个实例能应付过来，但是一到节假日，流量飙升，那么就需要再安排一台实例来帮忙

当**计算需求改变**时，可以调整容量，使用**动态扩展**和**预测性扩展**

#### 如何配置

设置最小实例数量（1台实例）满足**低需求**，平时**中需求**可能需要2台实例，再**设置两台扩展，高需求的时候扩展顶上去**

###  ELB 负载均衡

Elastic Load Balancing（负载均衡）

#### 为什么需要负载均衡

比如店里来了顾客都喜欢去第一个店员那里排队，导致第二个店员没活干，这样就不均衡

ELB自动跨多个资源分配流量，为 **Auto Scaling** 组提供**单一联系点**，流量访问到ELB后，会被ELB均衡地分配到不同实例

收缩与扩展同理

### 亚马逊云科技消息收发服务

#### 应用程序架构

+ 整体式应用程序

> 如：组件1、组件2、组件3、组件4构成一个完整的应用，四个组件隶属于同一个应用程序
>
> 缺点：如果一个组件要修bug 或 加功能，那么会对整个应用程序造成挑战

+ 微服务

> 同样由 组件1、组件2、组件3、组件4 构成一个完整的应用，但是这四个组件不属于同一个程序，微服务之间通过API配合

使用微服务后，虽然满足了快速迭代的要求，但是交互变得更为复杂

以下两个服务解决了这一痛点（如何传递消息、如何处理事件）：

#### Amazon SNS

`Amazon Simple Notification Service`将消息发布到主题，订阅者会立即收到他们主题的消息，

以咖啡店为例，比如咖啡师知道有一个客人定了一个冰美式，直接丢到前台（topic）上，SNS通知订阅冰美式（冰美式topic）的顾客来取，那么就完成了交易（消息传递），又做了一个拿铁，直接丢到前台上，SNS通知订阅拿铁（拿铁topic）的顾客来取。也就是**一个生产者对应多个消费者**

+ 发布来自单个主题的更新
+ 发布来自多个主题的更新

#### Amazon SQS

`Amazon Simple Queue Service`在软件组件之间**发送、存储和接收消息**；**对消息进行排队**，而不需要提供其他服务

以咖啡店为例，顾客向收银员下单，收银员将单子贴在板子上，咖啡师根据贴上去的顺序一个一个地做，咖啡师只接收来自收银员的请求，根据板子上的需求制作咖啡，制作完毕后挨个递交给顾客。这样做的**好处是**，收银员将需求贴在板子上后，可以直接去服务下一个顾客

### 亚马逊云科技容器服务

容器相对虚拟机是一种更加轻量级的虚拟化，因为虚拟机承装了一个完整的操作系统，所以比较重

而且，如果做开发后，直接交付给测试团队可能无法直接跑起来，这个时候，就可以打包一个容器发过去，这是容器的灵活性。

容器和微服务架构就是天作之合

#### Amazon ECS

Amazon Elastic Container Service 运行和扩展容器化应用程序，使用简单的API调用来控制支持docker的应用程序

#### Amazon EKS

Amazon Elastic Kubernetes Service 运行和扩展kubernetes应用程序，使用新功能轻松更新应用程序

> ECS 比 EKS 容易上手（如果会用k8s可以优先选EKS）

#### Amazon Fargate

使用Amazon ECS 或 Amazon EKS 运行无服务器容器，仅需为使用的资源付费

### 无服务器计算服务

我们写一个应用，找一个虚拟机来部署，让客户访问到，但是需要维护虚拟机，即使代码写得再好，如果虚拟机挂了，那么客户就访问不了啦（容器同理）

现在我们不想进行维护，只想把精力花在写代码上

#### Amazon Lambda

运行代码而无需配置或管理服务器，仅为代码运行时的计算时间付费，使用其他亚马逊云科技服务自动触发代码

工作原理：

1. 将代码上传到Amazon Lambda
2. 将代码设置为从事件源触发
3. 代码只有在触发时才会运行
4. 只需按使用的计算时间付费

lambda的并发问题：如果同时指向了Lambda，那么Lambda会创建instance来处理请求

**Lambda是典型的无状态，非常适合事件处理，不过最多只能处理15分钟**

## 全球基础设施和可靠性

为了满足不同地区的客户的要求，必须要将服务扩展到不同的地区

### 了解AWS全球基础设施

+ 区域 [ region ]（每个区域有多个可用区）
+ 可用区（可用区包含入网点）

选择区域需要考虑**数据监管和法律要求、与客户的距离、定价、区域内的可用服务**

比如：北京和宁夏节点之间互通，但是和其他地方的不互通，其他地方的节点互通

### 离客户更近

#### Amazon CloudFront分发内容

如果说，某个region离客户太远，那么就搞一个CloudFront 做一个边缘站点，客户第一次访问会比较慢，以后再访问就会很快

### Amazon Outposts

### 与AWS交互

+ 亚马逊云科技管理控制台
+ Amazon Command Line Interface（Amazon CLI）其实就是命令行
+ 软件开发工具包（SDK）

### 本模块的内容

	+ 亚马逊云科技全球基础设施的三个方面
	+ 选择亚马逊云科技区域时要考虑的四个因素
	+ 与亚马逊云科技服务交互的三种方式

---

# 重开笔记：从题目角度记录主要模块

## 网络

+ 构建和连接到VPC
+ 使用网络访问控制列表和安全组保护VPC资源
+ 使用Amazon Route 53 和Amazon CloudFront分发内容（中国区不能用）

### VPC—虚拟隔离网络（Amazon Virtual Private Cloud）

场景：我们去咖啡店买咖啡，一般是告诉收银员我们想喝什么，而不是直接告诉咖啡师**收银员负责接收我们的请求，咖啡师只接收收银员的请求**

所谓**隔离**，就相当于，我们每个用户的都是隔离开的，就像连锁店的，都属于一个公司，但是每个店都有自己的老板和员工，互不关联

####  子网

**收银员就是公有子网，咖啡师就是私有子网，VPC就包含了公有子网和私有子网**

公有子网面向客户，面向互联网，接受请求（如：AmonzonEC2实例就可以接收公网请求）

私有子网只面向公有子网，（如：数据库不会直接接收公网请求）

#### 互联网网关（IGW）

客户端通过互联网向VPC发送请求，通过互联网网关进入VPC，然后就可以访问到公有子网了

#### 虚拟私有网关（VGW）

比如数据库，肯定是在私有子网里的，如果应用在本地，要把数据写入到数据库，那么就没法用互联网网关（为了安全），VPN网关就是VGW的一种，通过企业路由器转发VPN到VPN网关访问私有子网去和数据库交互

#### 专线（Amonzon Direct Connect）

需要拉线，功能上类似于虚拟私有网关，数据量巨大的时候需要用到

#### 网络控制访问列表和安全组

网络流量的路线：（访问一个部署在EC2上面的应用）

客户端发送数据包到通过互联网传输，由互联网网关进入到公有子网，成功访问到应用服务。

此时，在**互联网网关**后面放置一个防火墙**（网络访问控制列表）**，我们对流量进行筛选（某些类型的流量不允许进入），通过第一轮筛选后，我们再放置一个防护墙**（安全组）**对试图进入EC2的流量进行筛选

##### 网络访问控制列表（网络ACL）

子网的虚拟防火墙，**默认网络ACL**允许所有入站和出战流量，**自定义网络ACL**拒绝所有入站和出站流量，也就是黑名单机制（名单上的不允许通过）

网络ACL执行无状态数据包筛选，必须先根据出站规则对数据进行检查，数据包才能离开子网

##### 安全组

AmazonEC2实例的虚拟防火墙，默认情况下，安全组**拒绝所有入站流量**并**允许所有出战流量**，也就是白名单机制（名单上的才能通过）

安全组执行有状态数据包筛选，就是数据进来后，安全组有记录以前对传入数据包所做的决策

> 总结就是：**隔离、联通、管控**

### 域名系统（DNS）

就是解析IP的，后面有很多集群

## 数据库

主要分为关系型数据库和非关系型数据库

如：mysql库。平时用的就是这个，跟表格一样，有字段有行有列，每一行的数据都是按照字段的类型来写入的

非关系型数据库，比如第一行存放**[姓名、地址、爱好]**，**第二行存放[姓名、地址、生日]**，每一行称为一个`item`

### 关系型数据库

#### RDS

主要有`Amazon Aurora(高于RDS，定位是企业级数据库，对标Oracle)、PostgreSQL、MySQL、MariaDB、Oracle数据库、Microsoft SQL Server`

关系型数据库，跟传统的关系型数据库无异。**是托管数据库，而不是无服务器数据库**

**托管数据库**通常由云服务提供商完全管理，用户不需关心硬件或基础设施，主要关注数据库的配置和优化。

**无服务器数据库**则让用户更专注于应用逻辑，而数据库本身自动扩展，按需付费。

**托管数据库的成本**较为固定，适合需要稳定性和控制的应用；

**无服务器数据库**则因按需计费，可能在使用量波动较大的情况下更具成本效益。性能方面，托管数据库提供更高的定制性和优化机会，而无服务器数据库在处理流量变化时自动调整，以适应负载波动。

### 非关系型数据库

非关系型数据库使用行列以外的结构来组织数据

如：使用**键值对**，将数据组织到项目（键）中，且项目具有属性（值）

#### Amazon DynamoDB

无服务器键值数据库，会自动扩展以根据容量变化进行调整，同时保持一致的性能，每天可以处理超过10w亿个请求

#### DMS

Amazon Database Migration Service。**异构迁移**

**迁移** 关系数据库、非关系数据库和其他类型的数据存储

如：**MySql数据 **通过**DMS**迁移到**AmazonAurora数据库**，

### 其他数据库服务

+ Amoazon Redshift			- 跨数据仓库查询和分析数据
+ Amazon DocumentDB      - 在文档数据库服务中运行MongoDB工作负载
+ Amazon Neptune              - 运行使用高度互连的数据集的应用程序，比如图谱类的应用
+ Amazon QLDB                 - 查看应用程序数据的完整更改历史记录
+ Amazon Managed Blockchain -运行分散式分类账数据库（区块链）
+ Amazon ElastiCache - 添加缓存层以缩短数据库读取的时间
+ Amazon DynamoDB Accelerator - 将DynameDB响应时间从几毫秒缩短到几微秒

## 责任共担模式

### 云本身的安全性

亚马逊云科技主要负责

+ 数据中心的物理安全性
+ 网络基础设施
+ 硬件和软件基础设施
+ 虚拟化基础设施

客户主要负责

+ 实例操作系统
+ 基于主机的防火墙
+ 应用程序
+ 账户管理
+ 安全组

### 亚马逊云科技账户根账户

#### Amazon Identity and Access Management     — IAM

可用于管理对亚马逊云科技服务和资源的访问，**IAM用户**是代表**与亚马逊云科技服务和资源** 交互 **人员或应用程序的**身份

创建的亚马逊账户就是**根用户**，拿到根用户后创建一个IAM用户并为其授予创建其他用户的权限，并以新IAM用户身份登录，并继续创建其他用户，**根用户用来执行有限数量的任务**

##### IAM策略

用于允许或拒绝用户对亚马逊云科技服务和资源的权限的文档

##### IAM组

IAM组是IAM用户的集合

最佳实践：将IAM策略附加到IAM组，而不是附加到单个IAM用户，成员继承分配给组的策略

##### IAM角色

一种身份，可以通过担任这种身份来获得权限的临时访问权限

##### 多重身份验证

要登陆到亚马逊云科技网站，用户需要输入其**IAM用户ID和密码**，系统会提示用户提供**Amazon MFA设备**发出的身份验证响应内容，通过身份后可以请求云服务或资源

##### Amazon Organization

多账户模式下，可以帮助客户从一个中央位置整合并管理多个亚马逊云科技账户，使用**服务控制策略（SCP）**集中控制组织中各个账户的权限

### 合规性

由各地区的权威机构提供支持（比如保险）

#### Amazon Artifact

按需访问安全性与合规性报告和选择线上协议

## 应用程序安全性

### Amazon WAF

帮助保护Web应用程序和API免受常见的Web攻击

### Amazon Shield

使用**解决Dos和DDoS攻击**可防范分布式拒绝服务（DDoS）攻击

+ 保护应用程序免受DDoS攻击
+ 将 Amazon Shield Advanced 与其他亚马逊云科技服务集成
+ 使用**Amazon WAF**编写自定义Web ACL 规则以缓解复杂的 DDoS攻击

### Amazon Inspector

对应用程序自动执行安全评估，其实就是漏洞扫描

+ 自动执行应用程序安全性评估
+ 发现安全漏洞以及与最佳实践的偏差
+ 接收有关如何解决安全性问题的建议

## 其他安全服务

+ Amazon Key Management Service
  可以帮助客户通过使用加密密钥执行加密操作，可以为密钥选择所需的特定访问控制级别
+ Amazon GuardDuty
  智能威胁检测

## 监控和分析

+ Amazon CloudWatch
+ Amazon CloudTrail
+ Amazon Trusted Advisor

### Amazon CloudWatch

实时监控亚马逊云科技和本地基础设施和资源，从一个位置访问所有指标，根据指标自动提醒和操作

如下问题：

+ 如何知道应当合适启动更多的Amazon EC2 实例
+ 应用程序的性能或可用性是否受到容量不足的影响
+ 实际使用的基础设施有多少

Amazon CloudWatch 主要有三个主要组件组成**指标、警报、事件**

可以**跟踪并监控资源和应用程序的性能和运行状况**，它可以：

+ 跟踪资源和应用程序性能
+ 收集和监控日志文件
+ 警报触发时收到通知
+ 触发事件



### Amazon CloudTrail

跟踪整个亚马逊云科技基础设施中的用户活动和API请求，筛选API调用生成的日志，以帮助进行运营分析和故障排除，自动检测异常账户活动

### Amazon Trusted  Advisor

观察并给出改进建议（成本优化、性能、安全性、容错能力、服务限制）

## 、定价和支持

+ 亚马逊云科技定价 - 了解亚马逊云科技账单工具
+ 整合账单
+ 亚马逊云科技定价工具
+ Amazon Support计划
+ Amazon Marketplace

### 亚马逊云科技定价 - 了解亚马逊云科技账单工具

免费套餐主要有**永久免费、12个月免费、试用**

定价主要有**随用随付、预留容量，付费更少、阶梯计价（如水电,超过多少量会有优惠）**

计算工具			http://calculator.aws

###  整合账单

多账户环境下，每个账户去付款不合理（应该统一由财务去付款），因此，整合账单为所有亚马逊云科技账户提供单个账单，查看每个账户产生的逐项列明的费用，在企业的各个账户间共享成本节省

就是一个查看下属账单的工具，优化成本可以看一下

比如：张三要用2T的S3，李四要用5T的S3，王五要用7T的S3，那么此时管理账户就可以看到，直接去购买14个T的（多买肯定有折扣）

### 亚马逊云科技定价工具

#### Amazon Budgets

用于为亚马逊云科技服务使用量和成本设置阈值

比如：原本预算45元，但是结果发现，没用到这么多，就用这个工具看，哪个服务没用到，就关了，节省成本

#### Amazon Cost Explorer

可用于可视化、了解并管理一段时间内亚马逊云科技成本和使用情况

###  Amazon Support 计划

#### Basic Support

**Basic Support**对所有亚马逊云科技客户免费，并且可让他们访问以下资源：

+ 技术论文、文档、和支持社区
+ Amazon Personal Health Dashboard
+ 选择有限的 Amazon Trusted Advisor 检查

以下是付费支持（越往下越贵）

+ Developer
  + 最佳实践指导
  + 客户端诊断工具
  + 构建块架构支持
+ Business
  + 使用案例指导
  + 所有Amazon Trusted Advisor 检查
  + 对第三方软件的有限支持

以下更高级，会有一个**技术客户经理**

+ Enterprise On-Ramp
  + 应用程序架构指导
  + 基础设施事件管理
  + 多位技术客户经理（TAM）
+ Enterprise
  + 应用程序架构指导
  + 基础设施事件管理
  + 指定TAM

###  Amazon Marketplace

一个数字目录，其中列出了可在亚马逊云科技上运行的第三方软件

主要有以下类别

+ 业务应用程序
+ 数据和分析
+ DevOps
+ 基础设施软件
+ 物联网
+ Machine Learning
+ 迁移
+ 安全性











