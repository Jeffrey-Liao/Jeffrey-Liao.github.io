# 数据库重点知识单项选择题

> 本试卷共50题，涵盖《重点.md》中全部核心知识点，包括：存储结构、数据字典、文件组织（Heap/Sorted/Hashed）、Bitmap、B+树、分区表、查询代价、Codd 12律、锁机制、日志系统（Redo/Undo/Checkpoint）、冲突可串行化、索引与哈希对比等。

---

## 一、存储结构与物理存储

1. 在Oracle体系结构中，Table Space直接包含的逻辑存储单元是？
- [ ] A. Data Blocks
- [ ] B. Extents
- [ ] C. Segments
- [ ] D. Pages

2. 关于Segment的描述，正确的是？
- [ ] A. Segment可跨多个Table Space分配
- [ ] B. 每个表对应一个Segment存放其全部数据
- [ ] C. Segment是I/O操作的最小单位
- [ ] D. Segment仅用于存储索引对象

3. Extent的物理存储约束是？
- [ ] A. 必须属于同一Table Space
- [ ] B. 必须属于同一Segment
- [ ] C. 必须位于单个Data File内
- [ ] D. 可跨越多个Data Files

4. Data Block在物理层面通常与什么一一对应？
- [ ] A. Page
- [ ] B. Sector
- [ ] C. Disk Block
- [ ] D. Cluster

5. Oracle标准物理存储层级关系是？
- [ ] A. Table Space → Segment → Extent → Data Block
- [ ] B. Segment → Table Space → Extent → Data Block
- [ ] C. Data Block → Extent → Segment → Table Space
- [ ] D. Extent → Segment → Table Space → Data Block

## 二、数据字典视图

6. ALL_ 视图反映的信息范围是？
- [ ] A. 当前用户拥有的全部对象
- [ ] B. 当前用户有权访问的所有对象
- [ ] C. 数据库全局所有对象
- [ ] D. 系统预定义的内置对象

7. USER_ 与 ALL_ 视图的核心区别在于？
- [ ] A. USER_ 包含更多元数据列
- [ ] B. USER_ 限于自有对象，ALL_ 包含授权访问对象
- [ ] C. ALL_ 视图默认不可读
- [ ] D. USER_ 需显式授权才能查询

8. 访问DBA_ 视图通常要求？
- [ ] A. SELECT ANY TABLE权限
- [ ] B. CREATE SESSION权限
- [ ] C. DBA角色或同等高权限
- [ ] D. CONNECT角色

9. 查询当前用户所建表的列定义，应优先使用？
- [ ] A. DBA_TAB_COLUMNS
- [ ] B. ALL_TAB_COLUMNS
- [ ] C. USER_TAB_COLUMNS
- [ ] D. V$TABLES

10. 不属于Oracle标准数据字典视图前缀的是？
- [ ] A. ALL_
- [ ] B. USER_
- [ ] C. SYS_
- [ ] D. DBA_

## 三、数据文件组织方式

11. Heap File最本质的组织特征是？
- [ ] A. 记录按主键升序排列
- [ ] B. 记录按哈希值聚类存储
- [ ] C. 数据追加写入，无序无索引
- [ ] D. 所有记录存于同一Block

12. Heap File插入新记录的时间复杂度为？
- [ ] A. O(n)
- [ ] B. O(log n)
- [ ] C. O(1)
- [ ] D. O(n²)

13. Heap File中等值查询的平均I/O成本约为？
- [ ] A. 1次读取
- [ ] B. 一半页数读取
- [ ] C. 全部页数读取
- [ ] D. log₂(页数)次读取

14. Sorted File中，决定记录物理顺序的字段称为？
- [ ] A. 哈希键
- [ ] B. 排序键字段
- [ ] C. 排序字段
- [ ] D. 主键

15. 若Sorted File按Empno排序且Empno唯一，则Empno是？
- [ ] A. 哈希键
- [ ] B. 排序键字段
- [ ] C. 外键
- [ ] D. 索引字段

16. Sorted File支持高效范围查询的根本原因是？
- [ ] A. 内置B+树加速定位
- [ ] B. 记录物理有序，支持顺序扫描
- [ ] C. 位图索引辅助过滤
- [ ] D. 数据自动分区存储

17. Hashed File中定位记录依赖的关键机制是？
- [ ] A. 二分查找算法
- [ ] B. 哈希函数计算桶号
- [ ] C. 线性遍历搜索
- [ ] D. B+树路径导航

18. 多个记录映射到同一桶的现象称为？
- [ ] A. 冲突（Collision）
- [ ] B. 溢出（Overflow）
- [ ] C. 分裂（Split）
- [ ] D. 合并（Merge）

19. Hashed File冲突处理方法中，不包括？
- [ ] A. 使用溢出页
- [ ] B. 构建溢出链表
- [ ] C. 动态重哈希
- [ ] D. 二分查找定位

20. Hashed File最适合的查询类型是？
- [ ] A. WHERE salary BETWEEN 3000 AND 5000
- [ ] B. WHERE name LIKE 'A%'
- [ ] C. WHERE empno = 101
- [ ] D. ORDER BY name

## 四、Bitmap（位图）

21. Bitmap索引适用的数据列特征是？
- [ ] A. 高基数（如主键）
- [ ] B. 低基数（如性别）
- [ ] C. 大文本字段
- [ ] D. 数值型主键

22. Bitmap中对两条件执行AND操作，等价于？
- [ ] A. 位图OR运算
- [ ] B. 位图XOR运算
- [ ] C. 位图AND运算
- [ ] D. 位图NOT运算

23. REGION='east'位图[1,0,0,0,0,0]与REGION='central'[0,1,1,0,1,1]做OR运算结果是？
- [ ] A. [1,1,1,0,1,1]
- [ ] B. [0,0,0,0,0,0]
- [ ] C. [1,0,0,0,0,0]
- [ ] D. [0,1,1,0,1,1]

24. Bitmap索引在OLAP中广泛应用的主因是？
- [ ] A. 支持高频DML操作
- [ ] B. 空间紧凑且布尔运算高效
- [ ] C. 原生支持范围查询
- [ ] D. 适合高并发事务处理

25. Bitmap索引效率最低的操作是？
- [ ] A. WHERE status = 'single'
- [ ] B. WHERE status IN ('single','married')
- [ ] C. WHERE status = 'single' AND region = 'east'
- [ ] D. UPDATE SET status = 'divorced'

## 五、B+树索引

26. B+树所有叶子节点位于？
- [ ] A. 不同深度层次
- [ ] B. 同一最高层
- [ ] C. 同一最低层
- [ ] D. 随机分布层级

27. B+树叶节点指针直接指向？
- [ ] A. 父节点地址
- [ ] B. 子节点地址
- [ ] C. 数据记录或ROWID
- [ ] D. 下一叶节点地址

28. 若非叶节点最大键数为n，其最小指针数为？
- [ ] A. ⌊(n+1)/2⌋
- [ ] B. ⌈(n+1)/2⌉
- [ ] C. n
- [ ] D. 1

29. 若叶节点最大键数为n，其最小键数为？
- [ ] A. ⌊(n+1)/2⌋
- [ ] B. ⌈(n+1)/2⌉ - 1
- [ ] C. ⌊(n+1)/2⌋
- [ ] D. 1

30. B+树分裂时，提升至父节点的键是？
- [ ] A. 最小键值
- [ ] B. 最大键值
- [ ] C. 中间位置键值
- [ ] D. 随机选取键值

31. B+树删除后叶节点键数不足时，首选策略是？
- [ ] A. 直接删除该节点
- [ ] B. 与兄弟节点合并
- [ ] C. 向兄弟节点借键
- [ ] D. 提升父节点键值

32. B+树非叶节点指针数与键数的关系是？
- [ ] A. 指针数 = 键数
- [ ] B. 指针数 = 键数 + 1
- [ ] C. 指针数 = 键数 - 1
- [ ] D. 无确定关系

33. B+树与B树的本质区别是？
- [ ] A. B+树内部节点存数据，B树只存索引
- [ ] B. B+树数据全在叶子，B树数据可分布于内部节点
- [ ] C. B+树不支持范围查询
- [ ] D. B树更适合磁盘I/O

34. “INDEX RANGE SCAN”适用于？
- [ ] A. 唯一索引等值查询
- [ ] B. 范围查询或非唯一索引等值查询
- [ ] C. 全表扫描操作
- [ ] D. 位图索引扫描

35. 执行计划出现“INDEX UNIQUE SCAN”表明？
- [ ] A. 使用非唯一索引等值查询
- [ ] B. 使用唯一索引等值查询，结果≤1行
- [ ] C. 使用位图索引
- [ ] D. 使用哈希索引

## 六、表类型与分区

36. Ordinary table的数据组织方式是？
- [ ] A. 按主键排序存储
- [ ] B. 按哈希值分布存储
- [ ] C. 无序集合（heap）
- [ ] D. 按时间分区存储

37. Partitioned table的优势不包括？
- [ ] A. 提升查询性能
- [ ] B. 增强管理便利性
- [ ] C. 减少磁盘空间占用
- [ ] D. 支持并行操作

38. 基于列值范围的分区类型是？
- [ ] A. Hash
- [ ] B. List
- [ ] C. Range
- [ ] D. Composite

39. Hash分区实现行均匀分布的机制是？
- [ ] A. 排序算法
- [ ] B. 哈希函数
- [ ] C. 位图算法
- [ ] D. B+树算法

40. List分区适用的典型场景是？
- [ ] A. 连续日期范围
- [ ] B. 离散地区代码（如EU, NA）
- [ ] C. 高频更新列
- [ ] D. 大文本字段

## 七、查询开销与优化器

41. $V(R,A)$在查询代价模型中表示？
- [ ] A. R中A属性的总记录数
- [ ] B. R中A属性的平均长度
- [ ] C. R中A属性的唯一值数量
- [ ] D. R中A属性的空值数量

42. 若T(R)=1000，V(R,A)=10，A为非键属性，则SC(R,A)≈？
- [ ] A. 1
- [ ] B. 10
- [ ] C. 100
- [ ] D. 1000

43. Nested Loop Join的最优I/O代价是？
- [ ] A. $T_R * B_S + B_R$
- [ ] B. $B_R + B_S$
- [ ] C. $B_R * B_S + B_R$
- [ ] D. $2*(B_R+B_S) + (B_R+B_S)$

44. Sort-Merge Join的总代价公式是？
- [ ] A. $B_R + B_S$
- [ ] B. $cost\:of\:sorting + B_S + B_R$
- [ ] C. $B_R + T_R * c$
- [ ] D. $2*(B_R+B_S)$

45. CBO选择执行计划的核心依据是？
- [ ] A. 预定义规则集
- [ ] B. SQL语句长度
- [ ] C. 统计信息（statistics）
- [ ] D. 索引数量

## 八、Codd 12律

46. Information Rule强调的核心原则是？
- [ ] A. 所有数据（含元数据）必须以表形式存储
- [ ] B. 所有数据必须加密存储
- [ ] C. 所有数据必须有备份副本
- [ ] D. 所有数据必须建立索引

47. Guaranteed Access Rule要求的访问三元组是？
- [ ] A. 表名 + 外键 + 列名
- [ ] B. 表名 + 主键（行值） + 属性名（列值）
- [ ] C. 数据库名 + 用户名 + 表名
- [ ] D. 索引名 + 键值 + 页号

48. NULL值必须被如何处理？
- [ ] A. 全部忽略
- [ ] B. 系统化且统一处理
- [ ] C. 强制转换为0
- [ ] D. 强制转换为空字符串

49. Active Online Catalog要求结构元数据存储于？
- [ ] A. 操作系统文件
- [ ] B. 数据字典（data dictionary）
- [ ] C. 应用程序配置文件
- [ ] D. 备份归档文件

50. Non-Subversion Rule禁止的行为是？
- [ ] A. 低级接口绕过安全与完整性约束
- [ ] B. 用户不使用SQL访问数据库
- [ ] C. 索引未设置唯一性约束
- [ ] D. 日志文件未加密存储

---

## 答案与解析

### 一、存储结构与物理存储

1. **C** — Table Space直接包含Segments。原文：“Table Space包含若干个Segments”。
2. **B** — Segment与表一一对应，存放其全部数据。原文：“每个表对应一个段（Segment），用于存放该表的所有数据”。
3. **C** — Extent必须位于单个Data File内。原文：“每个 extent 必须位于一个 data file 内（不能跨文件）”。
4. **C** — Data Block与Disk Block一一对应。原文：“Data Block 一般与Disk Block一一对应”。
5. **A** — 标准层级：Table Space → Segment → Extent → Data Block。

### 二、数据字典视图

6. **B** — ALL_视图覆盖当前用户有权访问的所有对象。原文：“包含当前用户有权限访问的所有对象的信息”。
7. **B** — USER_限于自有对象，ALL_扩展至授权访问对象。原文明确区分二者范围。
8. **C** — DBA_视图需DBA角色等高权限。原文：“访问这些视图通常需要额外的权限，如DBA角色”。
9. **C** — USER_TAB_COLUMNS专用于当前用户表的列定义查询，最精准。
10. **C** — SYS_非标准数据字典前缀；标准为ALL_/USER_/DBA_。

### 三、数据文件组织方式

11. **C** — Heap File本质：追加写入、无序、无索引。原文：“数据直接追加到文件末尾，无排序、无索引”。
12. **C** — 插入为O(1)，因直接追加。原文：“新记录直接插入到文件末尾...插入操作非常快（O(1)）”。
13. **B** — 等值查询平均读取一半页。原文：“平均读取一半的页才能找到目标记录”。
14. **C** — 决定物理顺序的字段称排序字段（Ordering Field）。原文定义。
15. **B** — 排序字段若为唯一键，则称排序键字段（Ordering Key Field）。原文举例明确。
16. **B** — 物理有序使范围查询可顺序扫描起始位置后连续页。
17. **B** — 定位依赖哈希函数计算桶号。原文：“通过哈希函数将记录分配到不同的桶中”。
18. **A** — 多记录哈希到同一桶即冲突（collision）。原文：“当多个记录哈希到同一个桶时，会发生冲突”。
19. **D** — 二分查找属Sorted File特性，非Hashed File冲突解法。
20. **C** — Hashed File专为等值查询优化，平均O(1)。原文：“查找效率非常高（平均时间复杂度接近 O(1)）”。

### 四、Bitmap（位图）

21. **B** — Bitmap索引适合低基数列（如性别、状态）。原文表格及上下文均体现此点。
22. **C** — AND操作对应位图按位AND。原文表格示例展示AND获得where结果。
23. **A** — OR运算：[1,0,0,0,0,0] ∨ [0,1,1,0,1,1] = [1,1,1,0,1,1]。
24. **B** — Bitmap索引空间小、布尔运算快，适合OLAP分析。原文隐含此优势。
25. **D** — UPDATE需更新多个位图块，效率极低，属公认短板。

### 五、B+树索引

26. **C** — 所有叶子节点位于同一最低层。原文：“All leaves at same lowest level”。
27. **C** — 叶节点指针指向数据记录或ROWID。原文：“Pointers in leaves point to records”。
28. **B** — 非叶节点最小指针数 = ⌈(n+1)/2⌉。原文：“非叶节点：⌈(n+1)/2⌉ pointers”。
29. **A** — 叶节点最小键数量 = ⌊(n+1)/2⌋。原文表格明确标注“最小键数量”列为⌊(n+1)/2⌋。
30. **C** — 分裂时取中间键（下标⌊n/2⌋）提升至父节点。原文：“找$n/2$下标的键提到父节点中”。
31. **C** — 删除后键数不足，首选向兄弟借键（re-distribution）。原文：“删除后需要借节点”。
32. **B** — 非叶节点指针数 = 键数 + 1。原文：“度(n)：为节点的最大键数量；指针数量：n+1”。
33. **B** — B+树数据全在叶子节点，B树数据可分布于内部节点。根本区别。
34. **B** — INDEX RANGE SCAN用于范围查询或非唯一索引等值查询。原文表格明确对比。
35. **B** — INDEX UNIQUE SCAN用于唯一索引等值查询，结果≤1行。原文：“等值查询（=）”、“最多1行”。

### 六、表类型与分区

36. **C** — Ordinary table即heap-organized，数据以无序集合方式存储。原文表格第一行。
37. **C** — 分区不减少磁盘空间，反而可能增加元数据开销；其优势是性能、管理性、并行性。
38. **C** — Range分区基于列值范围。原文表格明确。
39. **B** — Hash分区使用哈希函数均匀分布行。原文：“Uses hash function to distribute rows evenly”。
40. **B** — List分区用于离散值映射，如region IN ('EU', 'NA')。原文表格明确。

### 七、查询开销与优化器

41. **C** — $V(R,A)$定义为R中A属性的唯一值数量。原文：“$V(R,A)$是A中唯一的数据的数量”。
42. **C** — $SC(R,A) = T_R / V(R,A) = 1000 / 10 = 100$。原文公式：“SC(R, A)=$T_R / V(R,A)$”。
43. **B** — Nested Loop Join最优代价为$B_R + B_S$。原文：“Best: $B_R+B_S$”。
44. **B** — Sort-Merge代价 = 排序代价 + $B_S + B_R$。原文公式明确。
45. **C** — CBO基于统计信息估算代价。原文：“基于统计信息（statistics）估算不同执行计划的‘代价’（cost）”。

### 八、Codd 12律

46. **A** — 第一定律：所有数据（含元数据）必须以表形式存储。原文：“Everything in a database must be stored in a table format”。
47. **B** — 第二定律：通过表名、主键（行值）、属性名（列值）保证访问。原文：“combination of *table-name*, *primary-key* (row value), and *attribute-name* (column value)”。
48. **B** — 第三定律：NULL必须系统化、统一处理。原文：“must be given a systematic and uniform treatment”。
49. **B** — 第四定律：结构描述存储于数据字典（data dictionary）。原文：“stored in an online catalog, known as data dictionary”。
50. **A** — 第十二定律：低级接口不能绕过安全与完整性约束。原文：“the interface must not be able to subvert the system and bypass security and integrity constraints”。