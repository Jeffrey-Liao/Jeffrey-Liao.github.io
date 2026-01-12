# 数据库知识测验

## 说明
本测验包含80道单项选择题，涵盖数据库核心知识点，特别侧重于锁机制和B+树相关内容。每道题有4个选项，只有一个正确答案。

请仔细阅读题目和选项，选择您认为正确的答案。

---

## 题目

### 存储结构

1. 在Oracle数据库中，Table Space包含什么？
A. 若干个Data Blocks
B. 若干个Segments
C. 若干个Extents
D. 若干个Rows

2. 每个表在Oracle中对应什么？
A. 一个Extent
B. 一个Data Block
C. 一个Segment
D. 一个Table Space

3. 关于Extents的描述，哪一项是正确的？
A. Extents可以跨多个data file存储
B. Extents由若干个Segments组成
C. 每个extent必须位于一个data file内
D. Extents是数据库中最小的存储单位

4. Data Block通常与什么一一对应？
A. Memory Page
B. Disk Block
C. Cache Line
D. Buffer Pool

### 数据字典视图

5. `ALL_`视图包含什么信息？
A. 当前用户拥有的所有对象
B. 数据库中所有对象
C. 当前用户有权限访问的所有对象
D. 系统管理员拥有的所有对象

6. `USER_`视图提供什么视角？
A. 全局视角
B. 管理员视角
C. 用户自有资源视角
D. 系统级视角

7. 访问`DBA_`视图通常需要什么权限？
A. SELECT ANY TABLE
B. DBA角色
C. CONNECT角色
D. RESOURCE角色

### 数据文件类型

8. Heap File的特点是什么？
A. 记录按某个字段有序存储
B. 使用哈希函数分配记录
C. 数据直接追加到文件末尾，无排序、无索引
D. 使用B+树索引组织数据

9. Heap File的插入操作时间复杂度是多少？
A. O(log n)
B. O(n)
C. O(1)
D. O(n log n)

10. Heap File的等值查询平均需要读取多少页？
A. 所有页
B. 四分之一的页
C. 一半的页
D. 一个页

11. Sorted files的查找时间复杂度是多少？
A. O(1)
B. O(log n)
C. O(n)
D. O(n log n)

12. Sorted files最适合哪种场景？
A. 频繁插入、少查询
B. 频繁查询、少更新
C. 频繁删除、少插入
D. 频繁更新、少查询

13. Hashed files使用什么方法将记录分配到不同的桶中？
A. 二分查找
B. B+树导航
C. 哈希函数
D. 线性搜索

14. Hashed files的查找时间复杂度接近多少？
A. O(1)
B. O(log n)
C. O(n)
D. O(n²)

15. 当多个记录哈希到同一个桶时，会发生什么？
A. 自动创建新桶
B. 记录被拒绝插入
C. 冲突（collision）
D. 系统崩溃

16. 解决Hashed files冲突的方法不包括以下哪项？
A. 使用溢出页（Overflow Page）
B. 链接溢出链表
C. 重新哈希整个文件
D. 使用线性探测

### Bitmap索引

17. Bitmap索引主要用于哪种查询条件？
A. 范围查询
B. 等值查询
C. 模糊查询
D. 连接查询

18. Bitmap索引在处理WHERE条件时，如何获得结果？
A. 对位图进行OR运算
B. 对位图进行AND运算
C. 对位图进行XOR运算
D. 对位图进行NOT运算

### B+树

19. B+树中，所有叶子节点位于什么位置？
A. 不同的层级
B. 同一最低层级
C. 根节点层级
D. 随机层级

20. B+树叶子节点的指针指向什么？
A. 父节点
B. 兄弟节点
C. 记录
D. 索引键

21. 对于B+树非叶非根节点，如果最大键数量为n，则最小键数量是多少？
A. ⌊n/2⌋
B. ⌈(n+1)/2⌉-1
C. ⌈n/2⌉
D. ⌊(n+1)/2⌋

22. 对于B+树叶节点，如果最大键数量为n，则最小键数量是多少？
A. ⌊n/2⌋
B. ⌈(n+1)/2⌉-1
C. ⌈n/2⌉
D. ⌊(n+1)/2⌋

23. B+树根节点的最小键数量是多少？
A. 0
B. 1
C. 2
D. n

24. 当B+树节点键数量大于n时，会进行什么操作？
A. 合并
B. 删除
C. 分裂
D. 重组

25. B+树插入时，分裂节点时，第n/2下标的键会被提到哪里？
A. 子节点
B. 兄弟节点
C. 父节点
D. 根节点

26. B+树删除键值后，如果节点键数小于最小键数，首先尝试什么操作？
A. 合并节点
B. 借用兄弟节点的键
C. 删除父节点
D. 重建索引

27. B+树删除操作中，当叶子节点键数不足时，重新分配后父节点需要做什么？
A. 更新分隔键
B. 删除分隔键
C. 添加新键
D. 保持不变

28. B+树中，非叶节点的指针数量与键数量的关系是什么？
A. 指针数量 = 键数量
B. 指针数量 = 键数量 + 1
C. 指针数量 = 键数量 - 1
D. 指针数量 = 2 × 键数量

29. B+树中，叶子节点的指针数量与键数量的关系是什么？
A. 指针数量 = 键数量
B. 指针数量 = 键数量 + 1
C. 指针数量 = 键数量 - 1
D. 指针数量 = 2 × 键数量

30. B+树的"sequence pointer"指的是什么？
A. 指向父节点的指针
B. 指向兄弟节点的指针
C. 指向下一个叶子节点的指针
D. 指向根节点的指针

### 表格类型

31. Ordinary (heap-organized) table的数据存储方式是什么？
A. 按B-tree索引结构排序
B. 按分区规则分布
C. 以无序集合（heap）方式存储
D. 按相关数据物理上存储在一起

32. Partitioned table的主要目的是什么？
A. 提高安全性
B. 改善性能和可管理性
C. 减少存储空间
D. 简化SQL语法

33. Index-organized table (IOT)的数据存储特点是什么？
A. 数据以无序集合方式存储
B. 数据按B-tree索引结构排序存储
C. 相关数据物理上存储在一起
D. 数据按分区规则分布

34. Clustered table的特点是什么？
A. 来自多个表的相关数据物理上存储在一起
B. 数据按B-tree索引结构排序存储
C. 数据以无序集合方式存储
D. 数据按分区规则分布

### 分区规则

35. Range分区基于什么？
A. 哈希函数分布
B. 离散值的显式映射
C. 列值的范围
D. 随机分布

36. Hash分区使用什么方法来分布行？
A. 二分查找
B. 哈希函数
C. 线性搜索
D. B+树导航

37. List分区基于什么？
A. 列值的范围
B. 哈希函数分布
C. 离散值的显式映射
D. 随机分布

### 查询开销计算

38. Nested loop join的最佳情况开销是多少？
A. B_R + B_S
B. T_R * B_S + B_R
C. B_R * B_S + B_R
D. 2*(B_R+B_S) + (B_R+B_S)

39. Nested loop join的最坏情况开销是多少？
A. B_R + B_S
B. T_R * B_S + B_R
C. B_R * B_S + B_R
D. 2*(B_R+B_S) + (B_R+B_S)

40. Block nested loop join的最坏情况开销是多少？
A. B_R + B_S
B. T_R * B_S + B_R
C. B_R * B_S + B_R
D. 2*(B_R+B_S) + (B_R+B_S)

41. Sort-Merge join的开销公式是什么？
A. B_R + B_S
B. cost of sorting + B_S + B_R
C. B_R * B_S + B_R
D. 2*(B_R+B_S) + (B_R+B_S)

42. Indexed nested loop join的开销公式是什么？
A. B_R + B_S
B. B_R + T_R * c
C. B_R * B_S + B_R
D. 2*(B_R+B_S) + (B_R+B_S)

43. Hash join的开销公式是什么？
A. B_R + B_S
B. B_R + T_R * c
C. B_R * B_S + B_R
D. 2*(B_R+B_S) + (B_R+B_S)

### 结果大小估计

44. 当关系运算W=R*S时，总元组数量T(W)的计算公式是什么？
A. T(R) + T(S)
B. T(R) * T(S)
C. T(R) - T(S)
D. max(T(R), T(S))

45. 当关系运算W=R*S时，新单条记录的长度L(W)的计算公式是什么？
A. L(R) + L(S)
B. L(R) * L(S)
C. L(R) - L(S)
D. max(L(R), L(S))

### 优化器

46. 当前Oracle数据库默认且唯一支持的优化器是什么？
A. Rule-Based Optimizer (RBO)
B. Cost-Based Optimizer (CBO)
C. Heuristic Optimizer
D. Statistics-Based Optimizer

47. Cost-Based Optimizer (CBO)基于什么来估算执行计划的代价？
A. 预定义规则
B. 统计信息
C. 查询历史
D. 系统负载

### 执行计划

48. `TABLE ACCESS FULL`表示什么？
A. 通过索引定位
B. 通过ROWID访问表
C. 全表扫描
D. 索引唯一扫描

49. `INDEX UNIQUE SCAN`适用于哪种查询类型？
A. 范围查询
B. 等值查询
C. 模糊查询
D. 连接查询

50. `INDEX RANGE SCAN`适用于哪种查询类型？
A. 等值查询（=）
B. 范围查询（>, <, BETWEEN, LIKE）或非唯一索引等值
C. 全表扫描
D. 索引唯一扫描

51. `SORT order by`出现在执行计划中表示什么？
A. 使用了ORDER BY
B. 使用了GROUP BY
C. 使用了HAVING
D. 使用了JOIN

52. `SORT group by`出现在执行计划中表示什么？
A. 使用了ORDER BY
B. 使用了GROUP BY
C. 使用了HAVING
D. 使用了JOIN

53. `FILTER`出现在执行计划中表示什么？
A. 使用了ORDER BY
B. 使用了GROUP BY
C. 使用了HAVING
D. 使用了JOIN

54. `NESTED LOOPS`出现在执行计划中表示什么？
A. 单表查询
B. 多表连接
C. 全表扫描
D. 索引扫描

### Codd12律

55. Information Rule规定数据库中的所有数据必须存储为什么格式？
A. XML格式
B. JSON格式
C. 表格式
D. 文本格式

56. Guaranteed Access Rule规定数据可以通过什么组合逻辑访问？
A. 表名、主键、属性名
B. 表名、索引名、列名
C. 数据库名、表名、列名
D. 用户名、表名、列名

57. Systematic Treatment of NULL Values要求NULL值必须被如何处理？
A. 忽略
B. 系统且统一地处理
C. 随机处理
D. 由应用程序处理

58. Active Online Catalog要求数据库结构描述存储在哪里？
A. 本地文件
B. 在线目录（数据字典）
C. 应用程序配置
D. 系统日志

59. Comprehensive Data Sub-Language Rule要求语言必须支持哪些功能？
A. 仅数据查询
B. 仅数据定义
C. 数据定义、视图定义、数据操纵、完整性约束、授权、事务边界
D. 仅数据操纵

60. View Updating Rule要求什么？
A. 视图只能读取
B. 理论上可更新的视图必须系统支持更新
C. 视图不能更新
D. 视图更新需要特殊权限

61. Physical Data Independence要求什么？
A. 逻辑数据独立于用户视图
B. 物理数据独立于应用程序
C. 数据独立于网络
D. 数据独立于操作系统

62. Logical Data Independence要求什么？
A. 物理数据独立于应用程序
B. 逻辑数据独立于用户视图
C. 数据独立于网络
D. 数据独立于操作系统

63. Integrity Independence要求完整性约束必须定义在哪里？
A. 应用程序代码中
B. 数据目录（catalog）中
C. 系统配置中
D. 用户文档中

64. Distribution Independence要求最终用户不能察觉什么？
A. 数据安全措施
B. 数据分布在多个地理位置
C. 数据备份策略
D. 数据加密方式

65. Non-Subversion Rule要求低级别记录访问接口不能做什么？
A. 访问数据
B. 绕过安全和完整性约束
C. 读取日志
D. 修改配置

### 锁机制

66. S锁（Shared lock）用于什么操作？
A. 写操作
B. 读操作
C. 删除操作
D. 更新操作

67. X锁（Exclusive lock）用于什么操作？
A. 读操作
B. 写操作
C. 查询操作
D. 排序操作

68. IS锁（Intent Shared）表示什么？
A. 后续可能对子节点加S锁
B. 后续可能对子节点加X锁
C. 当前读，但打算后续写
D. 共享锁

69. IX锁（Intent Exclusive）表示什么？
A. 后续可能对子节点加S锁
B. 后续可能对子节点加X锁
C. 当前读，但打算后续写
D. 排他锁

70. SIX锁（Shared with Intent to eXclusive）表示什么？
A. 后续可能对子节点加S锁
B. 后续可能对子节点加X锁
C. 当前读，但打算后续写
D. 共享锁

71. IS锁与哪种锁不相容？
A. S锁
B. X锁
C. IX锁
D. SIX锁

72. IX锁与哪种锁不相容？
A. S锁
B. X锁
C. SIX锁
D. 以上都是

73. S锁与哪种锁不相容？
A. IX锁
B. SIX锁
C. X锁
D. 以上都是

74. SIX锁与除哪种锁外的所有锁都不相容？
A. S锁
B. X锁
C. IS锁
D. IX锁

75. X锁与哪种锁不相容？
A. S锁
B. IS锁
C. IX锁
D. 所有锁

### Log文件

76. `<T, START>`日志记录表示什么？
A. 事务T已完成
B. 事务T已开始
C. 事务T已中止
D. 事务T已回滚

77. `<T, COMMIT>`日志记录表示什么？
A. 事务T已开始
B. 事务T已完成成功
C. 事务T已中止
D. 事务T已回滚

78. `<T, ABORT>`日志记录表示什么？
A. 事务T已完成成功
B. 事务T已开始
C. 事务T无法成功完成
D. 事务T已提交

79. `<T, X, v>`日志记录表示什么？
A. 事务T已开始
B. 事务T已完成成功
C. 事务T已更改数据库元素X，其原值为v
D. 事务T已中止

80. Redo Log处理的是什么类型的事务？
A. 所有事务
B. 未完成的事务
C. 已提交的事务
D. 已中止的事务

81. Undo Log处理的是什么类型的事务？
A. 所有事务
B. 未完成的事务
C. 已提交的事务
D. 已中止的事务

82. 在Redo Log中，当遇到`<END CKPT>`记录时，redo操作不需要查看哪个记录之前的内容？
A. `<START CKPT>`
B. `<START Ti>`
C. `<T, START>`
D. `<T, COMMIT>`

83. 在Undo Log中，当遇到`<END CKPT>`记录时，undo操作需要查看哪个记录之前的内容？
A. `<START CKPT>`
B. `<START Ti>`
C. `<T, START>`
D. `<T, COMMIT>`

84. non-quiescent checkpoint的第一步是什么？
A. 写入`<END CKPT>`记录
B. 将脏缓冲区写入磁盘
C. 写入`<START CKPT(T1, …Tk)>`记录
D. 刷新日志

85. non-quiescent checkpoint的第二步（Redo Log）是什么？
A. 将所有脏缓冲区写入磁盘
B. 将已提交事务的脏缓冲区写入磁盘
C. 写入`<END CKPT>`记录
D. 刷新日志

86. non-quiescent checkpoint的第二步（Undo Log）是什么？
A. 将所有脏缓冲区写入磁盘
B. 将已提交事务的脏缓冲区写入磁盘
C. 写入`<END CKPT>`记录
D. 刷新日志

### 并行原理

87. Conflict equivalent schedules的定义是什么？
A. 可以通过一系列非冲突的相邻操作交换转换
B. 具有相同执行顺序的调度
C. 具有相同结果的调度
D. 具有相同事务的调度

88. Conflict serializable调度的定义是什么？
A. 与某个串行调度冲突等价
B. 与某个并行调度冲突等价
C. 与某个并发调度冲突等价
D. 与某个序列调度冲突等价

89. 如果一个调度是冲突可串行化的，那么它一定是：
A. 可串行化的
B. 不可串行化的
C. 部分可串行化的
D. 条件可串行化的

### Hashing and Indexing

90. Hashing最适合哪种操作？
A. 范围查询
B. 等值查询
C. 模糊查询
D. 连接查询

91. INDEXING（包括B树）最适合哪种操作？
A. 等值查询
B. 范围查询
C. 模糊查询
D. 连接查询

92. Hashing主要用于什么？
A. WHERE过滤
B. 等值连接（Equi-Join）
C. ORDER BY排序
D. GROUP BY分组

93. Indexing主要用于什么？
A. 等值连接（Equi-Join）
B. WHERE过滤
C. HASH GROUP BY
D. 内存哈希表构建

94. Hashing能否用于`<`, `>`, `LIKE`等操作？
A. 可以
B. 部分可以
C. 不能
D. 取决于哈希函数

95. Indexing是否需要DBA显式创建？
A. 不需要
B. 需要
C. 有时需要
D. 从不需要

96. Hashing是否需要预先存在？
A. 需要
B. 不需要
C. 有时需要
D. 从不需要

97. Linear hashing的核心思想是什么？
A. 使用高位比特进行哈希
B. 使用低位比特进行哈希
C. 使用随机比特进行哈希
D. 使用全部比特进行哈希

98. Linear hashing的文件增长方式是什么？
A. 指数增长
B. 线性增长
C. 对数增长
D. 随机增长

99. Extensible hashing与Linear hashing相比，主要优势是什么？
A. 更简单的实现
B. 更好的空间利用率
C. 更快的插入速度
D. 更少的哈希冲突

100. B+树中，非叶节点的度(n)指的是什么？
A. 最小键数量
B. 最大键数量
C. 指针数量
D. 节点高度