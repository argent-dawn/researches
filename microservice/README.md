# 微服务架构与实践技术概览

## 1 系统架构

架构设计包含以下三个部分：

1. 业务架构：根据业务需求设计业务模块及交互关系
2. 系统架构：根据业务需求设计系统和子系统的模块
3. 技术架构：根据业务需求决定采用的技术及框架

其中系统架构负责定义系统包含哪些应用以及应用之间如何进行分工与合作，本质上是通过定义系统的拆分和协作方式来平衡功能复杂度与实现成本，以最优化满足业务架构的需求。

系统分工主要基于两个方向：

1. 水平方向：按照技术维度拆分应用（前端/中端/后端）
2. 垂直方向，按照业务维度拆分应用

系统合作主要包含两个方面：

1. 通讯机制（同步调用/异步消息/共享 DB 访问）
2. 数据格式（文本/XML/JSON/二进制等）

应用架构需要实现的三个目标：

1. 提高敏捷性——及时响应业务需求，促进企业发展
2. 提升用户体验——提升客户体验，减少客户流失
3. 降低成本——降低增加产品、客户或业务方案的成本

## 2 扩展方式

![扩展模型](https://www.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part1-3_scale-cube.png)

这张图从三个维度概括了一个系统的扩展方式：

1. x 轴：水平副本，即通过负载均衡服务器调用多个完全相同的应用副本
2. z 轴：数据拆分，即分库分表
3. y 轴：功能分解，即将应用按不同功能进行拆分

## 3 单一应用架构

![单一应用模型](https://www.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part1-1_monolithic-architecture.png)

### 3.1 技术优势

  1. 结构和机制简单，前期较易于开发、测试、部署和维护
  2. 整体开发周期较短

### 3.2 技术劣势

  1. 体积和复杂度容易失控，后期将难以理解，不利于长期开发和维护
  2. 不利于持续集成、测试和部署
  3. 无法有效利用运行环境资源
  4. 整体可用性较差
  5. 不利于重构和技术更新/优化
  6. 不利于扩展，且通常仅支持 x 方向扩展
  7. 不利于并行开发

### 3.3 优化方式

  1. 水平切分：前/中/后端分离
  2. 垂直切分：分布式 / SOA 架构
  3. 网格切分：微服务架构

## 4 微服务架构

![微服务模型](https://www.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part1-2_microservices-architecture.png)

### 4.1 基本特点/原则

1. 以一组相互关联的分布式服务的形式划分和实现业务逻辑
2. 各服务保持独立的生命周期（设计、开发、部署和维护）、底层数据和技术实现
3. 服务即产品，实现持续集成与部署
4. 各服务保持独立的开发和运维团队
5. 各服务实例在运行期间相互隔离
6. 各服务实例以轻量级通信方式互联/调用
7. 基础设施（测试、集成和部署相关）自动化

### 4.2 与 SOA 的关系和区别

两者都属于分布式架构，微服务是 SOA 的一种特殊实现方式：

1. 目标不同：SOA 以整合已有资源为目标；微服务以提供高可用性和扩展性为目标
2. 关注点不同：SOA 关注资源整合；微服务关注系统分解
3. 实现重点不同：SOA 采用中心化管理，重点实现 ESB 和相关规范；微服务采用去中心化管理，重点实现服务间的通信和协同机制

### 4.3 技术优势

1. 在代码层面有效限制单个服务的体积和复杂度，保持其易于理解，有利于长期开发和维护
2. 有利于持续集成、测试和部署
3. 能够最大限度利用运行环境资源
4. 整体可用性较高
5. 有利于重构和技术更新/优化
6. 有利于扩展，可支持 x/y/z 全方向扩展
7. 有利于并行开发
8. 有利于快速建设和扩大开发/维护团队

### 4.4 技术劣势

1. 需要具备较强的整体架构和规划能力，准确把握使用微服务架构的条件和时机
2. 服务拆分过程要求较高，必须对相关业务和技术具有深刻了解
3. 分布式系统本身具有较高的复杂性，例如服务间的通信/协作机制和容错机制
4. IDE 支持程度相比单一应用架构较低
5. 调用链路长，服务间通信机制存在一定性能损耗
6. 为避免同步耦合，服务间通常存在一定程度代码冗余
7. 分布式环境下无法保证数据一致性，通常只能采取较复杂的最终一致性方案
8. 整体测试、部署、维护的成本和难度显著提高，严重依赖自动化工具与基础设施
9. 对于跨多个服务的交叉业务，其修改、部署的成本和难度显著提高，通常需要根据依赖关系进行协调和排序
10. 服务治理相对复杂，对开发和运维人员的要求显著提高

### 4.5 架构取舍

常见应用类型在采用微服务架构后的收益：

1. 记录型系统：指以对特定领域信息进行增删改查作为应用核心能力（如 CRM、ERP、OA 等）的传统系统，该类系统通常使用传统的经典技术构建，具有较高的集成度，将从微服务架构中得到最大收益
2. 交互型系统：指以与用户交互作为应用核心能力（如各种移动应用、微信、微博等等）的新型系统，该类系统更多地会使用现代的新型技术构建，要求高度的敏捷度和持续开发/集成，将从微服务架构中得到较高收益
3. 分析型系统：将得到有限收益，其他架构模式如管道或过滤模式可能更加适合

引入微服务系统时，还需考虑：

1. 是否有适合的项目和团队，避免为了 “微服务” 而 “微服务”
2. 认清架构复杂性所带来的挑战，做好相应的准备和应对措施
3. 开始时可以考虑引入部分合适的微服务架构原则对已有系统进行改造或新建微服务应用，逐步探索及积累微服务架构经验，而非全盘实施微服务架构

### 4.6 常见反模式

1. 服务过度内聚/分解
2. 不重视自动化
3. 不使用版本控制
4. 不使用服务网关

## 5 关键问题

在设计和实施微服务架构的系统时，需要重点关注和思考以下问题：

* 服务划分
  * 领域驱动设计方法（DDD）
* 服务通信
* 服务数据
  * 数据存储
  * 数据管理
* 服务测试
* 服务部署
* 服务治理
  * 服务发现
  * 服务路由
  * 负载均衡
  * 服务容错
  * 服务监控
  * 服务鉴权
  * 服务编排
* 服务运维
* 架构实施
  * 技术储备
  * 重构策略
  * 团队结构
  * 工具使用
  * 技术规范

### 5.1 服务划分

服务划分是实施微服务架构过程中最关键的步骤，将直接影响后续所有开发/维护工作及系统整体可用性。

* 服务划分要求实施者必须：
  1. 对相关业务和技术具有深刻了解
  2. 具备较强的整体架构能力、规划能力和判断能力
* 基本原则：
  1. 明确架构的根本目标不仅仅是压缩单个服务的大小，而是解决单一架构应用在业务急剧增长时遇到的问题，避免由于过分关注服务大小而导致过度拆解
  2. 单一职责：服务应局限于特定范围内，通常为一组数量有限的强相关功能集合
  3. 共同封闭：任何针对单一服务的修改，其影响范围应限制在服务内部
  4. 松散耦合：服务统一以接口形式对外暴露，隐藏内部实现逻辑和相关数据
  5. 相互协作：服务能够通过相互调用完成复杂业务逻辑，参考 Unix 命令行工具设计
* 常用方法：
  1. 按照动词或用例划分：每个服务实现一个特定动作/行为（action）
  2. 按照名词或资源划分：每个服务实现针对特定资源的全部相关操作 （operations）
  3. 按照领域驱动设计方法（DDD）的子域（Subdomain）划分：每个服务实现一个对应的子域
  4. 按照业务流程划分：每个服务实现一个对应的业务流程

### 5.2 服务划分 —— 领域驱动设计方法（DDD）

* 基本原则
  1. DDD 的核心诉求是让业务架构和系统架构间形成绑定关系，任何业务架构的调整（为了响应频繁的业务需求变化）都必然牵动系统架构同时发生改变，因此本质上 DDD 是一个贯穿系统生命周期的，不断重构、持续完善领域模型的过程；
  2. 使用 DDD 的必要前提是创造能够让业务和系统两种不同认知模型逐步统一的环境。因此如果不能建立一个跨业务和技术的新型架构设计小组，贸然实践 DDD 和微服务架构可能会适得其反；
  3. 采用 DDD 的系统通常以微服务架构的方式进行实现，包括各服务独立对应一个限界上下文、以聚合根为依据拆分服务、在集成服务时采用防腐层（Anti-Corruption Layer，ACL）等。但 DDD 与微服务架构间并没有强制绑定，因此通常推荐使用大粒度的服务来包含业务分析过程中发现的可疑点，避免在贸然拆分后由于变化过度频繁导致重构成本激增；
  4. 在领域建模时，各领域对象间的关联的设计应遵循如下原则：
     * 保持精简：对象之间的复杂关联容易形成关系网，不利于理解和维护单个对象、划分对象之间的边界，以及实现对象之间的遍历；
     * 保持单向：关联应尽量保持单向；
     * 关注性能：对多的关联在业务上较为常见，实现时通常会采用集合来表示 1：N 的关系，尤其是当集合内元素较多的时候，甚至需要通过单独的查询来获取关联的集合信息；
     * 关注限制：需要深入分析关联上是否存一定的限制条件，如果存在就必须有所体现，这样通常能够有效简化关联（例如可以将 M：N 简化为 1：N，或将 1：N 简化为 1：1）；
* 技术优势
  1. 随着业务增长和项目推进，系统后续开发和维护的难度将保持平滑上升；
  2. 在设计阶段从业务角度思考问题，能够确保系统的有效性；
  3. 有助于明确业务主次关系；
  4. 有助于系统的实现保持简洁；
* 技术劣势
  1. 相对传统设计方式，采用 DDD 的项目在初期就处于难以上手的位置；
  2. 门槛较高，没有深厚的面向对象架构和开发能力几乎不可能实践成功；

### 5.3 服务通信

服务间的跨进程通信机制（IPC）可以按以下两个维度进行划分：

<table>
    <tr>
	    <th></th>
	    <th>One-to-One</th>
	    <th>One-to-Many</th>  
	</tr >
    <tr>
        <td>Synchronous</td>
        <td>Request/response</td>
        <td>—</td>
    </tr>
    <tr>
        <td rowspan="2">Asynchronous</td>
        <td>Notification</td>
        <td>Publish/subscribe</td>
    </tr>
    <tr>
        <td>Request/async response</td>
        <td>Publish/async responses</td>
    </tr>
</table>

常见的通信机制包括基于消息的异步机制和基于请求/响应的同步机制，各服务应根据实际需求组合使用不同的通信机制。

* 重点关注
  1. 服务接口设计：一切活动均应以接口为中心
  2. 接口更新策略：确保向后兼容
  3. 服务容错机制：防止出现雪崩效应

#### 5.3.1 基于消息的异步机制

* 技术优势
  1. 通信双方高度解耦
  2. 具有缓冲机制，通信限制较少
  3. 高度灵活，可支持全部通信机制
  4.特征明显，不易在开发时造成混淆或被忽略
* 技术劣势
  1. 消息处理系统作为独立服务将带来额外的开发和维护负担
  2. 实现请求/响应机制时较复杂
* 技术/框架
  1. JMS
  2. RabbitMQ
  3. Apache Kafka
  4. Apache ActiveMQ

#### 5.3.2 RESTFul（基于请求/响应的同步机制）

* 技术优势
  1. Http 协议相对熟悉简单
  2. 易于测试
  3. 原生支持请求/响应机制
  4. 对防火墙友好
  5. 不需要引入额外的服务，易于实现
* 技术劣势
  1. 只支持请求/响应一种通信机制
  2. 必须实时交互，缺乏缓冲机制，通信限制较多
  3. 依赖服务发现机制
* 技术/框架
  1. RAML / Swagger
  2. JSON / XML

#### 5.3.5 RPC（基于请求/响应的同步机制）

* 技术优势
  1. 通过选择通信协议和数据格式最大程度优化性能
* 技术劣势
  1. 实现相对复杂，需要引入额外的框架
* 技术/框架
  1. Thrift / Dubbo
  2. TCP
  3. JSON / XML / Binary

### 5.4 服务数据——数据存储

微服务架构中的数据存储方式可以按数据所有权划分为以下两类：

1. 独立存储：各服务独立存储和维护相关数据，并不允许跨服务访问
2. 共享存储：各服务共同存储和维护相关数据，并允许自由访问

为保证松散耦合，原则上各服务一律使用独立存储的方式，只有在特定场景下，如服务间高度共享一组数据时，才考虑多服务共享存储的方式。

* 重点关注
  1. 独立性：服务间必须是松散耦合的，各服务可独立开发和部署
  2. 扩展性：服务可能会在数据库层面进行扩展，如分库、分表等
  3. 需求差异：相关技术选型必须满足服务需求

#### 5.4.1 独立存储

独立存储方式在实现时包括以下几个层级：

1. Table：各服务独立使用一组有限的 Table，通常使用在 I/O 负载或整体规模较小的系统中
2. Schema：各服务独立使用一个 Schema，通常用来以较低开销实现数据隔离
3. Database Server：各服务独立使用一个 Database Server，通常使用在 I/O 负载或整体规模较大的系统中

* 技术优势
  1. 确保服务间松散耦合，任何服务的数据库修改不会影响其他服务
  2. 确保各服务可针对需求采用最适合的存储技术
* 技术劣势
  1. 分布式事务难以实现，通常只能采用较复杂的最终一致性方案
  2. 跨服务查询数据较难实现

#### 5.4.2 共享存储

* 技术优势
  1. 能够使用简单熟悉的本地 ACID 事务实现数据一致性
  2. 相关数据操作易于实现
* 技术劣势
  1. 开发耦合，数据库变更可能影响多个服务的实现
  2. 运行耦合，服务间可能互相抢占资源，影响整体可用性
  3. 存储技术选型受限，可能无法最优化满足各服务需求

### 5.5 服务数据——数据管理

微服务系统在数据管理层面有以下特点：

1. 各服务独立维护与自身业务相关的数据，外部只能以API形式进行访问
2. 各服务可根据实际需求使用不同的的数据库技术和部署方式

这种分布式的数据管理机制存在以下问题：

1. 由于可用性要求以及各数据库的支持程度不同，数据更新操作通常无法实现分布式事务（ACID），只能采取较复杂的最终一致性方案（BASE）
2. 跨服务/联表查询的实现可能非常复杂，且难以保证可用性

大部分微服务系统采用事件驱动架构作为解决方案，包括：

1. 最终一致性：各服务在更新数据实体时抛出对应事件，同时监听其他服务抛出的事件并以此更新相关的数据实体
2. 跨服务/联表查询：统一监听相关服务抛出的事件并以此更新对应的物化视图，通过查询物化视图代替跨服务/联表查询（针对简单的跨表查询，可使用服务编排的方式依次调用相关服务接口后对数据进行整合）

* 重点关注
  1. 实现原子性：更新实体时必须确保抛出对应事件，避免由于系统崩溃或局部错误导致数据不一致
* 技术优势
  1. 能够以跨服务事务的形式实现数据的最终一致性
  2. 支持跨服务物化视图
  3. 使用异步队列代替并发锁机制，能够实现较高的IO性能
* 技术劣势
  1. 相比传统的本地 ACID 事务，事件驱动机制在实现时较复杂
  2. 必须实现相关的事物补偿机制
  3. 由于事件机制的异步特性，系统必须能够妥善处理非一致数据
  4. 订阅方必须能够识别和忽略重复事件
* 技术/框架
  1. API Composition
  2. CQRS
  3. Saga pattern
  4. Event Sourcing
  5. Transaction Log Tailing
  6. MySQL Binlog：Canal

#### 5.5.1 实现原子性——捆绑本地事务

修改数据库实体时利用本地事务额外增加一个对应的 event 实体，通过独立线程或服务定时扫描 event 表，根据各 event 实体的状态决定是否发布更新事件。

* 技术优势
  1. 确保更新事件的发布不依赖 2PC
  2. 直接生成并发送业务级事件，不需要再进行额外的处理
* 技术劣势
  1. 易出现人为错误，开发人员必须在代码层面确保 event 实体正确插入
  2. 受限于数据库对事务和查询的支持能力

#### 5.5.2 实现原子性——监听事务日志（Transaction Log Tailing）

通过独立线程或服务定时扫描数据库的事务日志，根据日志内容决定是否发布更新事件。

* 技术优势
  1. 确保更新事件的发布不依赖 2PC
  2. 基于数据库原生实现的机制，减轻开发负担和人为出错的几率
* 技术劣势
  1. 数据库事务日志的格式受限于具体的数据库厂商和版本
  2. 从底层数据库事件中逆向分析高层业务事件的逻辑可能非常复杂

#### 5.5.3 实现原子性——事件溯源（Event Sourcing）

事件溯源是一种数据持久化方式，与传统方式保存数据的最终状态不同，事件溯源将保存针对特定实体的每一个事件。当需要重建实体时，通过回溯这些事件（即让这些事件重新发生）让实体恢复到某个特定时刻的状态。

* 技术优势
  1. 确保相关事件的发布不依赖 2PC
  2. 存储时使用事件取代领域模型，有效避免阻抗失配问题
  3. 能够审计并准确还原相关实体在任意时间的状态
  4. 业务逻辑处理的实体是高度解耦的事件，更利于实现微服务架构
* 技术劣势
  1. 陡峭的学习曲线和漫长的适应过程
  2. 事件数据库只支持主键查询，因此实际查询时必须使用 CQRS

### 5.6 服务测试（待完善）

* 重点关注
  1. 自动化
* 技术/框架
  1. 持续集成：Jenkins
  2. 内存数据库：H2
  3. 测试框架：JUnit/Spring MockMvc

### 5.7 服务部署

微服务系统的部署操作有以下几个特点：

1. 实例数量庞大，可能在几十到几百之间
2. 各实例使用独立的技术、语言或框架进行开发
3. 各实例具有不同的部署、资源、监视和扩展要求
4. 部署操作必须保持迅速、可靠和高性价比

常见的服务部署方式包括：

1. 单主机多服务实例
2. 单虚拟机单服务实例
3. 单容器单服务实例

原则上各服务一律使用单容器单服务实例的方式，仅在特殊场景下才考虑其他方式。

* 重点关注
  1. 自动化
  2. 可靠性
  3. 扩展性

#### 5.7.1 单主机多服务实例

* 技术优势
  1. 资源利用率相对较高
  2. 部署和启动实例的过程相对较快
* 技术劣势
  1. 缺少必要的隔离措施，无法有效监控和限制各实例的资源占用
  2. 运维团队必须熟悉每一个实例的相关技术细节，部署难度的提高将带来额外风险

#### 5.7.2 单虚拟机单服务实例

* 技术优势
  1. 各实例间实现完全隔离，能够有效监控和限制实例的资源占用
  2. 可有效利用现有云服务的基础设施，如 AWS 提供的负载均衡和自动扩展
  3. 封装相关技术细节，降低部署难度和风险
* 技术劣势
  1. 资源利用率较低
  2. 部署和启动实例的过程相对较慢（Boxfuse 例外）
  3. 依赖相关的管理工具
* 技术/框架
  1. Aminator / Packer.io
  2. VirtualBox / VMware
  3. Boxfuse

#### 5.7.3 单容器单服务实例

* 技术优势
  1. 各实例间实现完全隔离，能够有效监控和限制实例的资源占用
  2. 部署和启动实例的过程相对较快
  3. 封装相关技术细节，降低部署难度和风险
* 技术劣势
  1. 相关基础设置的成熟度相比虚拟机较差
  2. 安全性相比虚拟机较差
  3. 依赖相关的管理工具
* 技术/框架
  1. 容器级方案：Docker
  2. 环境级方案：Kubernetes

### 5.8 服务治理——服务发现

在对各服务实例的地址和端口信息进行管理时存在以下问题：

1. 实例数量可能非常庞大
2. 各实例的地址和端口信息可能动态分配
3. 各实例可能随时出现崩溃、升级或其他不可用情况

通常使用服务发现机制自动对各服务实例的地址、端口及可用性信息进行统一的维护和管理

在对各服务实例的地址和端口信息进行管理时存在以下问题：

1. 实例数量可能非常庞大
2. 各实例的地址和端口信息可能动态分配
3. 各实例可能随时出现崩溃、升级或其他不可用情况

通常使用服务发现机制自动对各服务实例的地址、端口及可用性信息进行统一的维护和管理。

* 重点关注
  1. 可用性
* 技术/框架
  1. 服务注册中心：Netflix Eureka / Etcd / Consul / Apache Zookeeper
  2. 第三方注册：Netflix Prana
  3. 环境级方案：Kubernetes

#### 5.8.1 发现机制——客户端发现

* 技术优势
  1. 不引入额外服务，开发负担较低
  2. 有利于针对业务逻辑实现最优化的负载均衡等控制处理
* 技术劣势
  1. 服务发现与客户端紧耦合，每个客户端都必须实现服务发现机制

#### 5.8.2 发现机制——服务端发现

* 技术优势
  1. 服务发现与客户端高度解耦，不需要在每个客户端中实现服务发现机制
  2. 有利于使用部署环境原生提供的服务发现功能
* 技术劣势
  1. 服务发现作为独立服务将带来额外的开发和维护负担

#### 5.8.3 注册机制——自我注册

* 技术优势
  1. 不引入额外服务，开发负担较低
* 技术劣势
  1. 服务注册与服务实例紧耦合，每个实例都必须实现服务注册机制

#### 5.8.4 注册机制——第三方注册

* 技术优势
  1. 服务注册与服务实例高度解耦，不需要在每个实例中实现服务注册机制
* 技术劣势
  1. 服务注册作为独立服务将带来额外的开发和维护负担

### 5.9 服务治理——服务网关

在微服务架构中，客户端直接访问微服务接口会导致以下问题：

1. 客户端支持的数据粒度/格式/协议与各服务接口提供的数据粒度/格式/协议不一致
2. 大量请求将给客户端带来严重的性能损失和逻辑复杂度
3. 客户端与服务间耦合度过高，不利于服务更新和迭代
4. 在客户端直接暴露各服务实例的内部地址或服务注册中心地址存在安全隐患

常见的解决方案是各服务以服务网关（API Gateway）形式对外暴露接口，服务网关通过访问服务注册中心统一实现包括服务路由、协议转换、服务容错等在内的业务无关的基础功能，大部分情况下，负载均衡和服务鉴权也会一并在服务网关中实现。

在系统整体规模较小的情况下，服务网关也经常额外承载包括请求分解、服务编排、数据整合等一系列业务相关的辅助功能。

* 重点关注
  1. 运行效率
  2. 可扩展性，尤其针对实现服务编排的场景
  3. 通信机制兼容性
  4. 服务发现
  5. 服务容错
* 技术优势
  1. 在客户端和服务实例之间实现解耦
  2. 减轻乐客户端的逻辑复杂度和性能压力
  3. 提高通信效率和整体性能
  4. 提高系统安全性
* 技术劣势
  1. 服务网关作为独立服务将带来额外的开发和维护负担
  2. 服务网关可能成为系统瓶颈
  3. 在实现业务相关的辅助功能时，一旦客户端需求或服务实现发生变更，服务网关也将被迫更新
* 技术/框架
  1. 异步响应/非阻塞 I/O 机制
  2. 响应式开发
  3. 服务网关：Netflix Zuul
  4. 负载均衡：Netflix Ribbon

### 5.10 服务治理——服务容错

分布式系统下的局部错误可能会引发雪崩效应，导致系统整体瘫痪。为确保高可用性，通常会采取以下容错机制：

1. 请求超时处理
2. 限制未完成请求数量
3. 熔断处理
4. 服务降级
5. 服务缓存

* 技术/框架
  1. 熔断器：Netflix Hystrix

### 5.11 服务治理——服务监控

服务监控的主要目标是：

1. 事故预警：如基于阀值对比的实时事件预警等
2. 故障定位：如静态点的异常日志收集、调用链追踪等
3. 优化决策：如分析用户行为数据和服务拓补关系优化系统瓶颈等

完整的服务监控体系需要实现：

1. 数据采集
2. 数据分析
3. 数据展示
4. 自动化报/预警

通常按监控对象不同可分为以下几个层级：

1. 主机监控
2. 容器监控
3. 服务监控
4. 客户端监控

监控过程主要处理的信息包括：

1. 硬件设备或系统的基本配置
2. 硬件设备或系统的实时运行状态
3. 系统日志
4. 系统自定义埋点的回传信息

常见的数据采集方式有：

1. 命令行：如在 linux 系统中通过 shell 脚本使用 top/vmstat/netstat 等命令进行数据采集，再把结果存储在文本文件中进行后续处理
2. 嵌入式：通过在进程中运行 agent 来获取应用状态，如目前的APM产品都是通过将监控工具嵌入到应用内部进行数据采集
3. 主动输出：提前在应用中埋点，各应用主动上报相关信息，如应用系统在日志中主动输出需要采集的业务状态
4. 旁路式：通过外部获取的方式采集数据，如调用监控 API、模拟业务报文 ，Ping 服务器，监控流量等
5. 远程接入：通过调用应用进程接口获取相关状态，如使用 JMX 方式连接到 Java 进程并对其状态进行采集
6. 入侵式：类似于嵌入式，区别在于入侵式 agent 运行在独立进程而非应用进程中

各层级监控的主要参考指标为：

1. 主机：
   * cpu/内存/网络/磁盘等资源使用情况
   * 登录/访问记录
2. 容器：
   * cpu/内存/网络/磁盘等资源使用情况
   * 名称/体积/版本等基本信息
3. 服务：
   * 服务地址/端口/名称/版本等基本信息
   * 服务状态/日志/异常/埋点记录等运行信息
   * 服务响应时间/流量/吞吐率/调用次数/失败次数/异常次数等性能信息
   * 服务拓补关系/调用链/调用来源
4. 客户端：
   * 用户操作/日志/异常/埋点记录等运行信息

* 重点关注
  1. 自动化
  2. 标准化日志
  3. 高效采集：不能影响监控对象的性能
  4. 传输/存储机制：涉及的数据量可能非常庞大
* 技术/框架
  1. 环境级方案：Kubernetes
  2. 容器监控：Cadvisor / Heapster
  3. 时序数据库：InfluxDB
  4. 可视化：Grafana
  5. 日志收集：Fluentd / Logstash / Sentry
  6. 日志存储：Hadoop / Strom / ElasticSearch / Solr
  7. 日志分析：Spark
  8. 服务器监控：Nagios / Zabbix
  9. 埋点：Selenium
  10. 数据缓冲：kafka
  11. 调用链跟踪：Zipkin / Spring Cloud Sleuth
  12. 服务监控：Spring Boot Actuator

### 5.12 服务治理——服务鉴权（待完善）

* 技术/框架
  1. 环境级方案：Kubernetes
  2. Spring Security / Shiro

### 5.13 服务治理——服务编排

在微服务架构中，业务逻辑的实现存在以下问题：

1. 客户端支持的数据粒度/格式/协议与各服务接口提供的数据粒度/格式/协议不一致
2. 大部分业务逻辑会同时涉及多个服务，且服务之间可能有严格的调用次序，需要相应的跨服务事务支持以确保数据的最终一致性
3. 相比单个服务，业务逻辑的变化或调整通常更加频繁

因此，业务逻辑通常以工作流形式实现，包含对相关各服务的协调、调度、整合和处理等功能，在具体实现机制上分为以下两类：

1. 服务编制（Orchestration）：面向流程，基于集中化的可执行中心流程
2. 服务编排（Choreography）：面向合作，基于去中心化的异步消息序列

#### 5.13.1 服务编制

在一个独立进程中实现集中化的可执行中心流程，对涉及到的服务进行统一协调。中心流程与服务间保持主从关系，具体工作流和处理细节对各服务完全透明。中心流程负责处理相关的服务调度和协调工作，包括规定服务调用次序、服务请求失败重试等。

服务编制需要在一个相对集中的位置建立可执行中心流程，常见的处理方式包括：

1. 聚合服务：封装成独立的聚合服务，作为底层服务与客户端之间的中间层，提供包括业务和数据两方面的聚合功能
2. 服务网关：在规模较小或业务逻辑相对简单的系统中，服务网关可以承载相应的服务编制功能

原则上不建议将服务编制逻辑实现在服务网关中，因为：

1. 服务编制的更新频率通常远大于服务网关
2. 服务网关和服务编制可能采用不同的开发技术
3. 服务网关和服务编制可能有不同的扩展需求

* 技术优势
  1. 实现相对简单
  2. 业务逻辑独立保存，易于理解和调试
  3. 可支持同步请求和异步事件两种通信方式
  4. 服务间耦合度较低
  5. 易于掌控工作流执行进度
* 技术劣势
  1. 服务编制作为独立进程可能成为系统瓶颈
  2. 中心流程可能过度实现，影响服务功能划分，导致各服务失去其应有价值
  3. 中心流程的复杂度容易失控
  4. 服务与中心流程之间可能出现较高耦合
* 技术/框架
  1. 可视化编制引擎：Netflix Conductor
  2. Apache Camel
  3. Saga

#### 5.13.2 服务编排

服务间通过去中心化的，基于异步事件的交互行为自治实现具体业务流程。各服务独立负责自身在业务流程中所扮演的角色，包括相关的业务逻辑和处理细节。在交互行为中，各服务保持平等，不存在主从关系，通过相互倾听，在接收到与自身业务相关的事件后尽快执行对应逻辑，并在执行完毕后通过广播通知其他服务进行后续处理。

* 技术优势
  1. 系统高度自治
  2. 服务间的协作相对灵活高效
  3. 不引入额外的造成系统瓶颈的因素
  4. 更符合领域驱动设计方法中的充血模型
* 技术劣势
  1. 实现相对复杂，特别是跨服务事务中的补偿（Compensating）处理
  2. 业务逻辑分散在各服务中，难以理解、调试和维护
  3. 仅支持基于异步事件的通信方式，依赖消息处理系统
  4. 跨服务事务中的补偿（Compensating）处理可能导致服务间出现较高耦合
  5. 难以掌控工作流执行进度，通常只能进行被动监控

### 5.14 服务运维（待完善）

* 重点关注
  1. 自动化
* 技术/框架
  1. 环境级方案：Kubernetes
  2. 容器级方案：Docker

### 5.15 架构实施——技术储备（暂缺）

### 5.16 架构实施——重构策略

通常对单一架构应用进行重构时应遵循以下步骤：

1. 停止增加新功能：所有新功能以微服务形式单独开发，通过额外的胶水代码与原系统进行
2. 系统前后端分离
3. 拆分系统服务/数据

在拆分系统服务时，通常优先考虑满足以下条件的模块：

1. 与系统其它部分耦合度较低的模块
2. 频繁变化的模块
3. 与系统其它部分资源需求差异较大的模块

具体拆分过程为：

1. 定义待拆分模块与系统间的双向接口
2. 使用 IPC 机制调用接口实现模块与系统间的通信
3. 将待拆分模块实现为独立服务

* 重点关注
  1. 避免推翻式重构，应采取循序渐进的方式
  2. 服务拆分顺序
  3. 使用胶水代码确保服务的独立性

### 5.17 架构实施——团队结构

企业的组织结构通常满足康威定律，即对于一个设计系统的组织，其产出的设计等同于这个组织之内和组织之间的沟通结构。

因此对于开发传统单一应用的团队，其组织通常结构如下：

![Conway's Law in action](https://martinfowler.com/articles/microservices/images/conways-law.png)

针对微服务架构独立生命周期和运行隔离的特点，可考虑按服务重新划分和组织团队：

![Service boundaries reinforced by team boundaries](https://martinfowler.com/articles/microservices/images/PreferFunctionalStaffOrganization.png)

### 5.18 架构实施——工具使用（暂缺）

### 5.19 架构实施——技术规范（暂缺）

必须对相关的技术进行规范性管理，避免出现技术失控情况

## 参考文档

* [Nginx Blog: Microservices](https://www.nginx.com/blog/introduction-to-microservices/)
* [Microservices: Decomposing Applications for Deployability and Scalability](https://www.infoq.com/articles/microservices-intro)
* [Microservices: a definition of this new architectural term](https://martinfowler.com/articles/microservices.html)
* [Microservice Architecture](http://microservices.io/index.html)
* [Seven Microservices Anti-patterns](https://www.infoq.com/articles/seven-uservices-antipatterns)
* [解析微服务架构](https://www.ibm.com/developerworks/community/blogs/3302cc3b-074e-44da-90b1-5055f1dc0d9c/entry/%E8%A7%A3%E6%9E%90%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84_%E4%B8%80_%E4%BB%80%E4%B9%88%E6%98%AF%E5%BE%AE%E6%9C%8D%E5%8A%A1?lang=es)
* [从源头入手，一分钟秒懂为什么要搞微服务架构？](http://developer.51cto.com/art/201707/545735.htm)
* [在首席架构师手里，应用架构如此设计](http://geek.csdn.net/news/detail/73332)
* [基于微服务的软件架构模式](http://www.jianshu.com/p/546ef242b6a3)
* [面向服务与微服务架构](http://blog.csdn.net/mindfloating/article/details/24583369)
* [多研究些架构，少谈些框架](http://newtech.club/archives/)
* [微服务的4个设计原则和19个解决方案](http://p.primeton.com/articles/59b0f9244be8e61fea00be67)
* [微服务架构的理论基础 - 康威定律](https://yq.aliyun.com/articles/8611)
* [SOA和微服务架构的区别？](https://www.zhihu.com/question/37808426)
* [从分布式一致性谈到CAP理论、BASE理论](http://www.cnblogs.com/szlbm/p/5588543.html)
* [普通视图和物化视图的区别](https://www.cnblogs.com/xuezhen-huang/p/4651118.html)
* [“推倒重来”的讲究](http://www.luanxiang.org/blog/archives/2176.html)
* [来自京东、唯品会对微服务编排、API网关、持续集成的实践分享](http://blog.csdn.net/Myl12/article/details/54613529)
* [美团点评服务治理框架](https://www.cnblogs.com/Leo_wl/p/7513531.html)
* [微服务来了，监控怎么办？](http://blog.csdn.net/guwei9111986/article/details/51798394)
* [微服务监控案例之一](https://www.cnblogs.com/wintersun/p/6747355.html)
* [微服务架构下，如何实现分布式跟踪？](http://blog.csdn.net/solitary1984/article/details/52163871)
* [Kubernetes主机和容器的监控方案](http://blog.csdn.net/qq_34463875/article/details/77866005)
* [Docker 微服务的性能监控与日志收集](https://baijia.baidu.com/s?old_id=478661)
* [利用Zipkin对Spring Cloud应用进行服务追踪分析](https://yq.aliyun.com/articles/60165)
* [领域驱动设计之领域模型](http://www.cnblogs.com/netfocus/archive/2011/10/10/2204949.html)
* [看完这篇，你就会了解什么是 DDD](https://zhuanlan.zhihu.com/p/20839480)
* [DDD & Microservices](http://insights.thoughtworks.cn/ddd-and-microservices/)
* [初探领域驱动设计：为复杂业务而生](http://developer.51cto.com/art/201409/450967.htm)