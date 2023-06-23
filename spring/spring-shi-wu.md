---
description: 从事务的隔离级别开始吧！
---

# Spring事务

## Spring事务的隔离级别

Spring事务管理器支持以下五个隔离级别：

1. DEFAULT（默认）：使用底层数据库的默认隔离级别。对于大多数数据库来说，通常是READ\_COMMITTED（读已提交）。
2. READ\_UNCOMMITTED（读未提交）：允许事务读取未提交的数据变更。这个隔离级别存在脏读、不可重复读和幻读的问题，一般情况下不推荐使用。
3. READ\_COMMITTED（读已提交）：保证一个事务提交后才能被另一个事务读取。避免了脏读的问题，但可能出现不可重复读和幻读的问题。
4. REPEATABLE\_READ（可重复读）：保证一个事务执行期间多次读取同一数据结果一致，即使其他事务进行了修改。避免了脏读和不可重复读的问题，但可能出现幻读的问题。
5. SERIALIZABLE（串行化）：最高的隔离级别，确保事务序列化执行，避免了脏读、不可重复读和幻读的问题。但由于串行化执行的性能较低，一般情况下很少使用。

可以通过在@Transactional注解中设置isolation属性来指定事务的隔离级别，例如：

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
public void myMethod() {
    // 事务处理的代码
}
```

需要根据具体的业务场景和数据访问需求选择合适的隔离级别。

## Spring事务的传播方式

Spring事务管理器支持以下七种传播行为：

1. REQUIRED（默认）：如果当前存在事务，则加入该事务，如果不存在事务，则创建一个新的事务。
2. SUPPORTS：如果当前存在事务，则加入该事务，如果不存在事务，则以非事务方式执行。
3. MANDATORY：要求当前存在事务，如果不存在事务，则抛出异常。
4. REQUIRES\_NEW：创建一个新的事务，如果当前存在事务，则将当前事务挂起。
5. NOT\_SUPPORTED：以非事务方式执行，如果当前存在事务，则将当前事务挂起。
6. NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。
7. NESTED：如果当前存在事务，则在嵌套事务内执行，如果不存在事务，则执行与REQUIRED相同的操作。

可以通过在@Transactional注解中设置propagation属性来指定事务的传播行为，例如：

```java
@Transactional(propagation = Propagation.REQUIRED)
public void myMethod() {
    // 事务处理的代码
}
```

需要根据具体的业务需求和调用关系选择合适的传播行为。

## 多场景事务回滚

推荐《[Spring事务传播行为详解](https://segmentfault.com/a/1190000013341344)》



