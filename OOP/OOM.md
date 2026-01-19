| 图类型                                  | 用途                           |
| ------------------------------------ | ---------------------------- |
| **用例图（Use Case Diagram）**            | 描述系统功能与外部参与者（Actor）的交互       |
| **类图（Class Diagram）**                | 展示类、属性、方法及类间关系（关联、聚合、组合、继承等） |
| **对象图（Object Diagram）**              | 类图的实例化，展示某一时刻的对象及其链接         |
| **序列图（Sequence Diagram）**            | 描述对象间按时间顺序的消息交互              |
| **协作图 / 通信图（Communication Diagram）** | 强调对象间的结构关系与消息流               |
| **状态图（State Machine Diagram）**       | 描述对象在其生命周期中的状态变化             |
| **活动图（Activity Diagram）**            | 类似流程图，表示业务流程或算法逻辑            |
| **组件图（Component Diagram）**           | 描述系统模块化结构及依赖                 |
| **部署图（Deployment Diagram）**          | 表示软件在硬件环境中的部署情况              |
### 关系类型

- **关联（Association）**：对象之间的结构关系（如“学生选课”）。
- **聚合（Aggregation）**：“整体-部分”关系，部分可独立存在（如“大学-学院”）。
- **组合（Composition）**：强聚合，部分不能脱离整体存在（如“房屋-房间”）。
- **依赖（Dependency）**：一个类使用另一个类（临时关系，如方法参数）。
- **泛化（Generalization）**：继承关系（如“Dog is a Animal”）。
- **实现（Realization）**：类实现接口。

### 设计模式（Design Patterns）

- 面向对象建模常结合经典设计模式，如：
    - 创建型：单例（Singleton）、工厂（Factory）
    - 结构型：适配器（Adapter）、装饰器（Decorator）
    - 行为型：观察者（Observer）、策略（Strategy）



### SOLID 原则（由 Robert C. Martin 提出）

表格

| 原则    | 全称                                    | 说明                                      |
| ----- | ------------------------------------- | --------------------------------------- |
| **S** | Single Responsibility Principle (SRP) | 一个类应该只有一个引起它变化的原因（即只负责一项职责）。            |
| **O** | Open/Closed Principle (OCP)           | 软件实体应对扩展开放，对修改关闭。通过继承或组合扩展行为，而非修改源码。    |
| **L** | Liskov Substitution Principle (LSP)   | 子类必须能替换其父类而不破坏程序正确性。                    |
| **I** | Interface Segregation Principle (ISP) | 客户端不应依赖它不需要的接口；应使用小而专注的接口。              |
| **D** | Dependency Inversion Principle (DIP)  | 高层模块不应依赖低层模块，二者都应依赖抽象；抽象不应依赖细节，细节应依赖抽象。 |

- **DRY（Don’t Repeat Yourself）**：避免重复代码。
- **KISS（Keep It Simple, Stupid）**：设计应尽可能简单。
- **YAGNI（You Aren’t Gonna Need It）**：不要过早添加未明确需要的功能。
- **高内聚（High Cohesion）**：类内部元素紧密相关。
- **低耦合（Low Coupling）**：类之间依赖尽可能少且松散。