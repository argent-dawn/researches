# 六边形架构

## 架构
1. 各 **限界上下文** 统一采用 **六边形架构**，其中 **领域模型** 保存在 `domain/model` 路径下，**领域服务** 保存在 `domain/service` 路径下，**应用程序** 保存在 `application` 路径下，**端口/适配器** 保存在 `infrastructure` 路径下；
1. **六边形架构** 中，外层对象可以访问内层对象，但反之内层对象禁止访问外层对象；
1. 统一使用 **开放主机服务**（`REST`）和 **领域事件** 的方式进行 **限界上下文** 集成；
1. **开放主机服务** 采用 **防腐层** 架构，相关接口可根据实际情况视为 **领域服务**（默认）或 **应用服务**，但接口实现统一视为 **端口/适配器** [p417-427]；

### domain
1. 每个 **聚合** 唯一对应一个 **模块** 和一个 **资源库**，每个 **模块** 唯一对应一个 **包**（package），彼此紧密相关的 **聚合** 统一采用父子 **模块** 结构；
1. 所有 **实体** 统一继承 `Entity` 类，主键固定命名为 `id`，并以 **值对象** 形式实现；
1. 所有 **值对象** 统一继承 `ValueObject` 类（枚举类除外）；
1. **实体** 与 **值对象** 的无参构造函数统一为 `private` 类型，不允许外部调用，用于初始化各属性默认值；
1. **实体** 与 **值对象** 的有参构造函数统一为 `public` 或 `protected` 类型（取决于是否有 `Factory` 方法），可用于外部调用，该方法先通过调用无参构造函数完成初始化，再调用 `setter` 方法设置实际属性；
1. **实体** 与 **值对象** 的 `setter` 方法统一为 `private` 类型，并作为 **守卫方法** 提供相关的校验逻辑，所有对属性的设置操作必须通过 `setter` 方法进行；
1. **实体** 与 **值对象** 的 `public` 方法统一采用 **行为展现接口** 风格，且不允许在方法中直接获取属性（`getter` 方法除外）；
1. 所有 **实体** 和 **值对象** 必须重写 `hashCode` 和 `equals` 方法；

### application
1. **聚合根** 与 **应用服务** 间保持一一对应关系，每个 **应用服务** 唯一对应一个文件；
1. 统一使用 `DTO` 对象的 `public` 方法实现与 **领域对象** 之间的转换（部分 `DTO` 仅提供单向转换），`DTO` 对象保存在 `application/data` 路径下；
1. 为隔离 **领域模型** 和解耦服务输出，所有 **应用服务** 和 **工厂方法** 只接受基本类型变量、`DTO` 对象（为简化开发视同 **命令对象**）及其它 **非领域对象**（如日期类）作为参数，并且只返回 `DTO` 对象 [p479-480，p483-485]；

### infrastructure
1. 所有 REST 相关 **端口** 统一保存在 `infrastructure/rest` 路径下，所有持久化相关 **端口** 统一保存在 `infrastructure/persistence` 路径下，所有定时器相关 **端口** 统一保存在 `infrastructure/timer` 路径下，每个 **适配器** 唯一对应一个文件；

## 命名
1. **模块**、变量、方法、类等命名统一遵循在表达清楚的前提下尽量简化的原则；
1. 接口使用的词缀统一参考 github rest api，包括但不限于：`page`、`size`、`sort`、`direction`、`since`、`until`；

## 其它约束
1. 统一使用 `org.springframework.util.Assert` 实现 **守卫方法**；
1. 所有 **命令方法** 原则上返回 `void`；
1. 所有日期类统一使用 Java8 提供的 `LocalDate` 或 `LocalDateTime`；

## 参考文献
* [Layers, ports & adapters - Part 1, Foreword](https://matthiasnoback.nl/2017/07/layers-ports-and-adapters-part-1-introduction/)
* [Layers, ports & adapters - Part 2, Layers](https://matthiasnoback.nl/2017/08/layers-ports-and-adapters-part-2-layers/)
* [Layers, ports & adapters - Part 3, Ports & Adapters](https://matthiasnoback.nl/2017/08/layers-ports-and-adapters-part-3-ports-and-adapters/)
* [Ports and Adapter Pattern / Hexagonal Architecture](https://codingcanvas.com/hexagonal-architecture/)
* [Ports and Adapters Architecture](https://www.thinktocode.com/2018/07/19/ports-and-adapters-architecture/)
* [HEXAGONAL ARCHITECTURE](https://apiumhub.com/tech-blog-barcelona/hexagonal-architecture/)
