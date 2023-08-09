---
description: 从threadLocal的使用场景开始吧！
---

# ThreadLocal

## ThreadLocal的使用场景

ThreadLocal是Java中的一个类，它的作用是为每个线程提供一个独立的变量副本，解决了变量并发访问的冲突问题。ThreadLocal常用于以下场景：

1. 在进行对象跨层传递的时候，使用ThreadLocal可以避免多次传递，打破层次间的约束。
2. 线程间数据隔离。
3. 进行事务操作，用于存储线程事务信息。
4. 数据库连接，Session会话管理。

## ThreadLocal的实现原理

每一个Thread维护一个ThreadLocalMap，ThreadLocalMap是由一个个Entry构成，而Entry继承了弱引用，key为使用弱引用的ThreadLocal对象，value为线程变量的副本。

```java
public class ThreadLocal<T> {
    // 省略其他代码...

    // 初始化 ThreadLocalMap 对象，每个线程的 ThreadLocalMap 存储在线程的 ThreadLocal.ThreadLocalMap 属性中
    static class ThreadLocalMap {
        // 省略其他代码...

        // Entry 对象，用于存储 ThreadLocal 对象和对应的值
        static class Entry extends WeakReference<ThreadLocal<?>> {
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }

        // 省略其他代码...
    }

    // 省略其他代码...

    // 获取当前线程的 ThreadLocalMap 对象
    ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }

    // 设置当前线程的 ThreadLocalMap 对象
    void setMap(Thread t, ThreadLocalMap map) {
        t.threadLocals = map;
    }

    // 获取当前线程的 ThreadLocalMap.Entry 对象
    ThreadLocalMap.Entry getEntry(ThreadLocalMap map) {
        return map == null ? null : map.getEntry(this);
    }

    // 设置当前线程的 ThreadLocalMap.Entry 对象
    void setEntry(ThreadLocalMap map, ThreadLocalMap.Entry entry) {
        map.setEntry(this, entry);
    }

    // 省略其他代码...

    // 获取 ThreadLocal 对象在当前线程中存储的值
    public T get() {
        Thread currentThread = Thread.currentThread();
        ThreadLocalMap map = getMap(currentThread);
        if (map != null) {
            ThreadLocalMap.Entry entry = getEntry(map);
            if (entry != null) {
                @SuppressWarnings("unchecked")
                T value = (T) entry.value;
                return value;
            }
        }
        return setInitialValue();
    }

    // 设置当前线程中 ThreadLocal 对象的值
    public void set(T value) {
        Thread currentThread = Thread.currentThread();
        ThreadLocalMap map = getMap(currentThread);
        if (map != null)
            map.set(this, value);
        else
            createMap(currentThread, value);
    }

    // 省略其他代码...
}

```

## ThreadLocal的使用问题

在使用ThreadLocal时，需要及时清理不再需要的ThreadLocal对象，避免内存泄漏问题。可以通过调用ThreadLocal的remove()方法来清理当前线程的ThreadLocalMap中对应的键值对。

原因：`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用，而 value 是强引用。所以，如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。
