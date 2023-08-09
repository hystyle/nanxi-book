---
description: 从数据库的三范式开始吧！
---

# 数据库基础

## 数据库的三范式

数据库的三范式是一种关系数据库设计原则，旨在消除冗余数据并提高数据存储和查询的效率。以下是数据库的三范式的简要说明：

1. 第一范式（1NF）：确保每个数据表中的每个字段都是原子的，不可再分的。每个字段应该只包含单个值，不允许多个值或重复的数据。
2. 第二范式（2NF）：在满足第一范式的基础上，确保表中的每个非主键字段都完全依赖于主键。换句话说，每个非主键字段都与主键字段直接相关，而不是间接相关。
3. 第三范式（3NF）：在满足第二范式的基础上，确保表中的每个非主键字段都不依赖于其他非主键字段。任何与其他非主键字段无关的数据应该被移动到单独的表中，并通过外键关联。

遵循三范式的设计原则可以带来以下好处：

* 减少数据冗余：避免在不同记录中重复存储相同的数据，节省存储空间。
* 提高数据更新的一致性：通过分解数据结构，确保数据的一致性更新。
* 提高查询的性能：通过优化表的结构，减少表之间的冗余和连接，加快数据的检索。

\