---
description: 从Redis的线程模型开始吧！
---

# 基础

## Redis的线程模型

Redis采用的是单线程模型。具体来说，Redis服务器使用一个主线程来处理所有的客户端请求和数据操作。主线程通过事件循环（event loop）的方式监听并处理客户端的连接和操作请求。

Redis使用了事件驱动的异步 I/O 模型（I/O多路复用），底层使用了高性能的事件库，如epoll、kqueue等，以实现非阻塞的网络通信。通过事件驱动模型，主线程可以同时处理多个客户端连接和请求，提高了系统的并发处理能力。

由于Redis是单线程的，所以在处理客户端请求时不需要进行线程切换和上下文切换，避免了线程切换带来的开销和资源竞争。这使得Redis在处理大量并发请求时表现出色，并能快速响应客户端。

需要注意的是，虽然Redis的主线程是单线程的，但Redis内部使用了多个工作线程来处理后台任务，如持久化、主从同步等，以提高系统的性能和可靠性。

总的来说，Redis的单线程模型保证了其简单高效的特性，适用于处理高并发读写的场景，并且通过异步 I/O 和事件驱动的方式提供了良好的性能和可扩展性。

## Redis为什么是单线程？

Redis选择采用单线程模型的设计是出于以下几个考虑：

1. 避免竞争条件：在多线程环境下，需要考虑线程间的资源竞争和同步问题，包括锁的使用、线程安全的数据结构等。单线程模型可以避免这些潜在的竞争条件，简化了系统的设计和实现。
2. 避免线程切换开销：线程的切换需要保存和恢复线程的上下文，涉及到寄存器状态的保存和恢复、内核态与用户态之间的切换等开销。单线程模型避免了这些线程切换的开销，提高了系统的响应速度。
3. 更好的内存利用率：多线程模型下，每个线程都需要维护自己的栈空间，而且栈空间大小是固定的，导致内存的浪费。单线程模型下只需要一个线程栈，节省了内存空间。
4. 简化并发控制：在单线程模型中，不需要关注线程间的同步和并发控制问题，减少了开发者需要处理的复杂性，简化了系统的设计和调试。

虽然Redis采用单线程模型，但通过异步 I/O、非阻塞操作和事件驱动等技术，使得Redis能够高效地处理大量的并发请求。此外，Redis还可以通过多实例、主从复制和集群等方式来提高系统的可靠性和扩展性。

## Redis常用的数据类型

Redis常用的基础数据类型包括：

1. 字符串（String）：存储字符串类型的值，可以是普通字符串、整数或浮点数。
2. 列表（List）：按照插入顺序存储一组字符串元素，可以在列表的两端进行元素的添加和删除操作，支持按索引访问和范围获取。
3. 哈希（Hash）：类似于关联数组，存储字段和值的映射关系，适合存储对象的属性。
4. 集合（Set）：无序且唯一的字符串集合，支持添加、删除和判断元素是否存在的操作，还支持交集、并集、差集等集合运算。
5. 有序集合（Sorted Set）：与集合类似，每个元素关联一个分数（score），按照分数排序，可以通过分数范围或成员获取有序集合的元素。

除了以上的基础数据类型，Redis还提供了一些其他的数据结构和功能，如地理位置（Geo）、位图（Bitmap）、HyperLogLog、发布订阅（Pub/Sub）等，这些功能丰富了Redis的应用场景和灵活性。此外，Redis还支持持久化、事务、Lua脚本等高级功能，使其成为一个功能强大的内存数据库和缓存系统。

### 场景举例

1. 字符串（String）：

* 缓存数据：将经常访问的数据存储在Redis的字符串类型中，以加快读取速度。
* 计数器：使用字符串类型存储的整数值进行自增、自减操作，实现计数功能。
* 分布式锁：利用字符串类型的原子操作（如SETNX）实现分布式锁的控制机制。

2. 列表（List）：

* 消息队列：将消息以先进先出（FIFO）的方式存储在列表中，实现简单的消息队列。
* 最新动态：存储最新的动态信息，如社交媒体中的最新发布内容。
* 历史记录：记录用户的浏览历史、操作日志等。

3. 哈希（Hash）：

* 对象存储：将对象的属性以字段和值的形式存储在哈希中，方便存取和更新对象属性。
* 用户信息：存储用户的详细信息，如用户名、年龄、性别等。

4. 集合（Set）：

* 标签系统：将标签存储在集合中，方便进行标签的添加、删除和查询。
* 好友关系：存储用户的好友关系，进行集合运算来查找共同好友等。
* 排行榜：将用户的得分或指标存储在集合中，根据分数进行排名。

5. 有序集合（Sorted Set）：

* 排行榜：将用户的得分或指标存储在有序集合中，按照分数进行排序，并可以查询排名。
* 帖子列表：存储帖子的发布时间或热度分数，实现帖子的排序和展示。

## 缓存穿透、缓存击穿、缓存雪崩

缓存穿透、缓存击穿和缓存雪崩是常见的缓存相关问题，它们分别指以下情况：

1. 缓存穿透（Cache Penetration）：

* 描述：指查询一个不存在的数据，导致缓存无法命中，请求直接穿透到后端数据库，增加数据库负载。
* 原因：可能是恶意攻击或异常请求导致。
* 解决方案：
  * 布隆过滤器（Bloom Filter）：在查询前使用布隆过滤器判断请求的数据是否存在于缓存中，如果不存在，可以提前返回，避免对数据库的查询操作。
  * 缓存空对象（Cache Null Object）：将查询为空的结果也缓存起来，设置较短的过期时间，这样下次查询相同的数据时可以从缓存中获取空结果。

2. 缓存击穿（Cache Breakdown）：

* 描述：指一个热点数据在缓存中过期或被删除，此时大量请求同时访问该数据，导致请求直接打到数据库上，增加数据库负载。
* 原因：可能是缓存数据的过期时间窗口内，该数据被大量请求访问而导致缓存失效。
* 解决方案：
  * 加锁/互斥：在缓存失效时，只允许一个请求去访问数据库，其他请求等待结果并从缓存中获取数据。
  * 降级策略：如果数据库压力过大，可以提供一个默认值或者静态化页面，保证服务的可用性。

3. 缓存雪崩（Cache Avalanche）：

* 描述：指缓存中的大量数据在同一时间失效，导致大量请求直接打到数据库上，造成数据库瞬时压力过大，甚至崩溃。
* 原因：可能是缓存服务器宕机、大规模缓存失效或同时过期等情况引起。
* 解决方案：
  * 设置不同的过期时间：可以将缓存的过期时间分散开，避免大量数据同时失效。
  * 缓存数据的异步刷新：可以在缓存数据即将过期时，异步更新数据，避免大规模缓存失效。
  * 分布式缓存：使用多台缓存服务器部署，避免单点故障，提高可用性。
  * 数据预加载：在非高峰期预先加载热点数据到缓存中，避免在高峰期缓存失效时大量请求访问数据库。

这些解决方案可以结合具体场景进行选择和实施，以减少缓存相关问题的发生，并保障系统的性能和稳定性。

## 缓冲击穿中的加锁解决方案说明

在缓存击穿情况下，可以采用加锁（互斥锁）的方式来解决并发访问数据库的问题。以下是一种常见的操作流程：

1. 当缓存失效时，多个请求同时到达，发现缓存不存在。
2. 在查询数据库之前，先尝试获取一个互斥锁。
3. 如果成功获取锁，则只有一个请求可以进入数据库查询数据，其他请求需要等待。
4. 查询数据库，并将查询结果写入缓存。
5. 释放锁。
6. 其他请求在获取到锁之前会一直等待，然后从缓存中获取数据。

这种加锁的方式确保了只有一个请求能够访问数据库，其他请求等待结果并从缓存中获取数据。这样可以避免多个请求同时访问数据库，减轻数据库的负载压力。

需要注意的是，在使用加锁解决缓存击穿问题时，要注意以下几点：

1. 锁的粒度：锁的粒度应该尽可能小，只锁定需要查询数据库的那一部分数据，避免影响其他数据的查询性能。
2. 锁的超时时间：为了防止某个请求长时间占用锁，可以设置锁的超时时间，确保即使出现异常情况，锁也能够自动释放。
3. 高可用性和并发性：使用分布式锁来实现高可用性和并发性，避免单点故障和性能瓶颈。

具体的加锁实现方式可以使用分布式锁工具（例如Redisson、ZooKeeper等）或者数据库的锁机制（例如数据库行锁）来实现。在选择具体的实现方式时，需要考虑系统的架构、性能要求和可用性等因素。

### 代码示例

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReentrantLock;

public class Cache {
    private Map<String, String> cacheData = new HashMap<>();
    private ReentrantLock lock = new ReentrantLock();

    public String getData(String key) {
        // 先从缓存中查找数据
        String data = cacheData.get(key);

        if (data == null) {
            // 数据不存在，需要查询数据库
            lock.lock(); // 获取锁

            try {
                // 再次检查缓存，防止其他线程已经查询了数据库并写入缓存
                data = cacheData.get(key);
                if (data == null) {
                    // 模拟从数据库查询数据
                    data = queryDataFromDatabase(key);

                    // 将查询结果写入缓存
                    cacheData.put(key, data);
                }
            } finally {
                lock.unlock(); // 释放锁
            }
        }

        return data;
    }

    private String queryDataFromDatabase(String key) {
        // 模拟从数据库查询数据的耗时操作
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // 这里假设从数据库查询到的数据是根据key计算得到的
        return "Data for " + key;
    }
}
```

## 分布式锁的两种实现方式

当使用Redis实现分布式锁时，可以借助Redis的原子性操作和特定的命令来实现锁的获取和释放。

以下是使用Redis实现分布式锁的一种简单方案：

1. 获取锁：

* 使用SETNX命令尝试将一个特定的键值对设置到Redis中，如果设置成功，表示获取到了锁。
* 可以设置一个适当的过期时间（例如防止死锁时的自动释放）。
* 通过为每个线程设置唯一的标识（如UUID）作为锁的值，以便在释放锁时进行校验。

2. 释放锁：

* 使用DEL命令删除锁的键，将锁释放。

```java
import redis.clients.jedis.Jedis;

public class RedisDistributedLock {
    private final Jedis jedis;
    private final String lockKey;
    private final String lockValue;

    public RedisDistributedLock(Jedis jedis, String lockKey, String lockValue) {
        this.jedis = jedis;
        this.lockKey = lockKey;
        this.lockValue = lockValue;
    }

    public boolean acquireLock() {
        // Set the lock key-value pair with NX (if not exist) and optional expiration time
        String result = jedis.set(lockKey, lockValue, "NX", "EX", 10);
        return "OK".equals(result);
    }

    public void releaseLock() {
        jedis.del(lockKey);
    }
}

```

以下是使用ZooKeeper实现分布式锁的一种常见方案：

1. 获取锁：

* 使用ZooKeeper客户端创建一个有序临时节点，表示当前请求获取锁。
* 使用getChildren命令获取所有子节点，并判断自己是否是最小的节点。
* 如果是最小的节点，则表示获取到了锁。

2. 释放锁：

* 使用ZooKeeper客户端删除自己创建的节点，将锁释放。
* 当节点被删除后，其他等待节点将通过监听机制接收到锁释放的事件，然后再次尝试获取锁。

```java
import org.apache.zookeeper.*;

public class ZooKeeperDistributedLock implements Watcher {
    private final ZooKeeper zooKeeper;
    private final String lockPath;
    private final String lockNode;
    private String currentLockNode;

    public ZooKeeperDistributedLock(ZooKeeper zooKeeper, String lockPath) {
        this.zooKeeper = zooKeeper;
        this.lockPath = lockPath;
        this.lockNode = lockPath + "/lock-";
    }

    public boolean acquireLock() throws KeeperException, InterruptedException {
        // Create an ephemeral sequential znode
        currentLockNode = zooKeeper.create(lockNode, new byte[0], ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);

        // Get all child znodes under the lockPath
        var children = zooKeeper.getChildren(lockPath, false);

        // Check if the created node is the smallest
        for (String child : children) {
            if (currentLockNode.endsWith(child)) {
                return true;
            }

            // Set a watcher on the previous node
            zooKeeper.exists(lockPath + "/" + child, this);
        }

        // Wait until the lock is acquired
        synchronized (this) {
            wait();
        }

        return true;
    }

    public void releaseLock() throws KeeperException, InterruptedException {
        zooKeeper.delete(currentLockNode, -1);
    }

    @Override
    public void process(WatchedEvent event) {
        synchronized (this) {
            notify(); // Notify waiting thread
        }
    }
}

```

无论使用Redis还是ZooKeeper实现分布式锁，都需要考虑锁的超时机制和错误处理，以及在锁被持有时的阻塞或轮询等策略。同时，需要注意处理分布式环境下的异常情况，例如节点宕机、网络故障等情况，以保证分布式锁的可靠性和正确性。

请注意，以上提供的是简单的示例方案，实际应用中还需要根据具体场景和需求进行细致的设计和优化。

## Redis实现分布式锁存在的问题

在使用Redis实现分布式锁时，可能会遇到以下一些问题：

1. 竞争条件（Race Condition）：多个客户端同时请求获取锁时，存在竞争条件，可能导致多个客户端同时获取到锁，造成资源冲突和数据不一致的问题。
2. 死锁（Deadlock）：如果持有锁的客户端出现异常或崩溃，没有及时释放锁，就会导致死锁问题，其他客户端无法获取锁。
3. 锁过期（Lock Expiration）：如果获取锁后客户端崩溃或执行时间过长，没有及时释放锁，就会导致锁过期问题，其他客户端无法获取锁，造成资源长时间被阻塞。
4. 锁误释放（Lock Release）：客户端释放锁时可能出现误释放的情况，例如客户端A获取锁后，客户端B也尝试获取锁并成功，然后客户端A误释放了锁，导致锁的所有权转移到了客户端B。
5. 高并发性能问题：使用Redis实现分布式锁时，需要频繁地进行锁的获取和释放操作，如果并发量很高，可能会导致Redis性能瓶颈，影响系统的吞吐量和响应时间。

为了解决以上问题，可以采取一些策略和措施，例如：

1. 设置锁的唯一标识符：可以为每个锁设置一个唯一的标识符，避免多个客户端同时获取到相同的锁。
2. 设置锁的过期时间：为获取到的锁设置一个合理的过期时间，确保即使出现异常情况，锁也能自动释放，避免死锁问题。
3. 使用分布式锁的附加功能：Redis提供了一些附加功能，如红锁（Redlock）和可重入锁（Reentrant Lock），可以更好地处理分布式环境下的锁问题。
4. 考虑使用分布式协调服务：除了Redis，还可以使用分布式协调服务如ZooKeeper、Etcd等来实现分布式锁，这些服务通常提供更强大的一致性保证和故障恢复能力。
5. 合理设置锁的粒度：尽量将锁的粒度控制在最小范围内，避免对整个资源或数据库进行锁定，从而减少锁竞争和性能问题。

综上所述，使用Redis实现分布式锁需要注意上述问题，并采取相应的策略和措施来解决和缓解这些问题。

## Redis是否支持事务

是的，Redis支持事务。在Redis中，事务是通过MULTI、EXEC、DISCARD和WATCH等命令来实现的。事务的执行流程如下：

1. 使用MULTI命令开启事务，表示开始一个事务块。
2. 在MULTI和EXEC之间，可以依次执行多个命令，这些命令将被放入事务队列中，而不是立即执行。
3. 使用EXEC命令来执行事务中的所有命令，Redis会按照命令的顺序依次执行。
4. 执行事务期间，Redis会将其他客户端的命令请求排队等待，保证事务的原子性。
5. 执行完所有的事务命令后，Redis将返回每个命令的执行结果，或者返回一个空数组（表示事务执行失败）。
6. 如果在EXEC执行之前，使用了DISCARD命令，则事务被取消，之前的所有命令都不会被执行。
7. 在事务执行过程中，可以使用WATCH命令来监视一个或多个键，如果被监视的键在事务执行期间被修改，事务将被中断。

需要注意的是，Redis的事务是原子性的，即在EXEC命令执行过程中，不会被其他客户端的命令请求打断。然而，事务并不支持回滚（Rollback）操作，即使在事务执行过程中出现错误，事务中的其他命令仍然会被执行。

通过使用Redis的事务，可以将多个命令打包在一个事务中进行批量操作，确保这些命令的原子性，同时可以减少网络通信的开销。

### 代码实现

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;
import redis.clients.jedis.exceptions.JedisException;

public class RedisTransactionExample {
    public static void main(String[] args) {
        // 连接到 Redis 服务器
        Jedis jedis = new Jedis("localhost", 6379);

        try {
            // 开启事务
            Transaction transaction = jedis.multi();

            // 执行事务中的命令
            transaction.set("key1", "value1");
            transaction.set("key2", "value2");

            // 提交事务
            transaction.exec();
        } catch (JedisException e) {
            // 事务执行失败，可以进行回滚操作
            transaction.discard();
            e.printStackTrace();
        } finally {
            // 关闭连接
            jedis.close();
        }
    }
}

```

### 结合spring的代码实现

1. 注入bean

```java
@Configuration
public class RedisConfig {

    @Value("${spring.redis.host}")
    private String host;

    @Value("${spring.redis.port}")
    private int port;

    @Bean
    public JedisConnectionFactory jedisConnectionFactory() {
        RedisStandaloneConfiguration redisConfig = new RedisStandaloneConfiguration(host, port);
        return new JedisConnectionFactory(redisConfig);
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate() {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(jedisConnectionFactory());
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        return template;
    }

}

```

2. 在需要使用事务的方法上使用@Transactional注解。（**需要确保在Spring配置中启用了事务管理器，例如使用@EnableTransactionManagement注解。**）

## 持久化RDB和AOF的区别

Redis中的RDB（Redis Database）和AOF（Append-Only File）是两种不同的持久化方式，用于在Redis服务器重启后恢复数据。

RDB持久化方式：

* RDB持久化是将Redis在内存中的数据按照一定的规则和格式保存到磁盘上的二进制文件。
* RDB快照是一个点对点的备份，即在某个时间点上将内存中的数据快照保存到磁盘上，以文件的形式存在。
* RDB持久化方式适合用于备份、全量恢复、数据迁移等场景。
* RDB文件相对较小，恢复速度较快，但是在Redis重启后可能会丢失最后一次持久化之后的数据。

AOF持久化方式：

* AOF持久化是将Redis服务器接收到的写操作追加到一个日志文件（Append-Only File）中。
* AOF文件记录了Redis服务器接收到的所有写操作的顺序和内容，是一个完整的操作日志。
* AOF持久化方式适合用于数据持久化和灾难恢复等场景。
* AOF文件相对较大，但是可以通过配置自动重写来压缩文件大小。
* AOF文件在Redis重启时会重新执行日志中的写操作，因此数据恢复的完整性较好。

RDB和AOF的区别如下：

* RDB是全量备份，AOF是增量备份。
* RDB文件较小，AOF文件较大。
* RDB恢复速度快，AOF恢复速度相对较慢。
* RDB文件丢失最后一次持久化之后的数据，AOF文件可以保留完整的写操作日志。

选择RDB还是AOF取决于具体的使用场景和需求。RDB适合对数据完整性要求不高、数据量较大、快速恢复的场景，而AOF适合对数据完整性要求较高、可靠性要求较高的场景。通常可以根据业务需求配置Redis使用的持久化方式，或者同时启用两种方式以提供更好的数据保护和恢复能力。

## Redis的集群架构

Redis集群架构是一种分布式架构，旨在提供高可用性和可扩展性。Redis集群通过将数据分片存储在多个节点上，并在节点之间进行数据复制和故障转移来实现高可用性。

以下是Redis集群架构的主要组件和特点：

1. 分片：Redis集群将数据分散存储在多个节点上，每个节点负责存储部分数据。数据分片是基于哈希槽（hash slot）的方式进行的，集群中有16384个哈希槽，每个节点负责一部分槽。
2. 主从复制：每个节点都可以有多个从节点。主节点负责处理客户端的读写请求，并将数据复制到从节点上。从节点通过复制主节点的数据来提供高可用性和读扩展能力。
3. 节点间通信：Redis集群使用Gossip协议进行节点间的通信和集群管理。节点之间相互发现，交换信息并进行故障检测，以便在节点故障时进行故障转移。
4. 故障转移：当主节点发生故障时，Redis集群会自动选举一个从节点作为新的主节点，并将哈希槽重新分配给新的主节点。这个过程是自动的，不需要手动干预。
5. 客户端路由：客户端与Redis集群交互时，需要将请求路由到正确的节点上。Redis集群使用客户端分片（client-side sharding）的方式，根据数据的哈希值将请求发送到相应的节点。
6. 高可用性：由于数据复制和故障转移机制，Redis集群具有高可用性。即使一个节点发生故障，集群仍然可以继续提供读写服务。
7. 扩展性：Redis集群可以通过增加节点来实现水平扩展。当数据量增加时，可以添加更多的节点来分担负载。

总的来说，Redis集群架构通过数据分片、主从复制和故障转移等机制提供高可用性和可扩展性。它适用于处理大规模数据和高并发访问的场景，如缓存、会话存储和实时数据处理等。

## Redis Cluster集群模式

Redis最新支持的集群模式是Redis Cluster。Redis Cluster是从Redis 3.0版本开始引入的，它提供了一种分布式的、高可用的数据存储解决方案。以下是Redis Cluster的主要特点：

1. 自动分片：Redis Cluster将数据分片存储在多个节点上，使用哈希槽（hash slot）的方式将数据均匀地分布到不同的节点上。集群中总共有16384个哈希槽，每个节点负责一部分槽。
2. 高可用性：Redis Cluster通过在每个槽上进行数据复制来提供高可用性。每个槽有一个主节点和多个从节点，主节点负责处理读写请求，从节点复制主节点的数据以提供故障转移和数据冗余。
3. 节点间通信：Redis Cluster使用Gossip协议进行节点间的通信和集群管理。节点之间相互发现、交换信息，并通过故障检测和故障转移来保持集群的稳定性。
4. 客户端路由：客户端通过Redis Cluster的客户端库与集群交互时，根据数据的哈希值将请求发送到相应的节点。客户端库会自动处理节点的故障和迁移，确保请求能够正确路由到集群中的节点。
5. 集群扩展：Redis Cluster支持动态添加和移除节点来实现集群的扩展和缩减。当需要增加容量或处理更大的数据量时，可以简单地添加新的节点到集群中。

Redis Cluster提供了一种方便、可靠的方式来部署和管理分布式Redis环境。它适用于需要高可用性和可扩展性的场景，如缓存、会话存储、实时数据处理等。

## 主从复制原理

Redis的主从复制是通过以下步骤实现的：

1. 配置主节点：在主节点的配置文件中，启用主从复制功能并设置`replicaof`参数为从节点的地址和端口号。例如，配置文件中的设置可以如下所示：

```sh
replicaof <slave_ip> <slave_port>
```

这告诉主节点将数据复制到指定的从节点。

2. 启动主节点：在主节点上启动Redis实例，确保它使用正确的配置文件和端口号。你可以使用

`redis-server`命令来启动Redis实例。

3. 配置从节点：在从节点的配置文件中，设置`replicaof`参数为空，表示它将成为主节点的复制节点。例如：

```
replicaof no one
```

4. 启动从节点：在从节点上启动Redis实例，确保它使用正确的配置文件和端口号。
5. 主从复制建立连接：当主节点和从节点都启动后，它们会尝试建立连接。主节点会将数据发送给从节点，并在后续的操作中将数据同步给从节点。
6. 数据同步和持续复制：主节点将所有写操作的数据变更记录到内存中的replication buffer中，并异步地将数据发送给从节点。从节点接收到数据后，将其应用到自己的数据集中。这样，主节点和从节点之间的数据保持一致。
7. 故障恢复：如果主节点发生故障，从节点可以自动切换为主节点并接管读写操作。当主节点恢复后，它可以重新成为从节点，并复制新的主节点。

通过主从复制，Redis可以提供数据冗余和高可用性。主节点处理写操作，并将数据复制给从节点，从节点负责处理读操作。这样可以提高系统的性能和容错能力。

请注意，以上是主从复制的基本原理和步骤。在实际应用中，你可能还需要考虑监控和管理复制过程中的状态、配置适当的复制拓扑结构、处理复制延迟等方面的问题。

参考：[https://doocs.gitee.io/advanced-java/#/docs/high-concurrency/redis-master-slave](https://doocs.gitee.io/advanced-java/#/docs/high-concurrency/redis-master-slave)

## 如果我在主节点设置了持久化，主节点崩了，那新的主节点会执行数据持久化吗？

在Redis的主从复制中，从节点不会自动执行数据持久化。只有主节点才会执行数据持久化操作。

当主节点崩溃后，从节点可以接管成为新的主节点，但它并不会自动执行数据持久化操作。从节点只会继续复制主节点发送过来的数据，保持数据的一致性。如果你在主节点上启用了持久化机制（例如RDB快照或AOF日志），那么从节点将仅从主节点复制持久化后的数据。

因此，在主节点崩溃后，如果你需要持久化数据，需要确保新的主节点（原来的从节点）正确地配置了持久化机制，并按需执行持久化操作。这可以通过在新的主节点的配置文件中启用适当的持久化选项来实现。例如，启用RDB持久化可以通过设置`save`选项来定期保存快照文件，或者启用AOF持久化可以通过设置`appendonly`选项来启用AOF日志。

请注意，如果你在原来的主节点发生崩溃之前已经执行了持久化操作，并且从节点已经复制了最新的持久化数据，那么在新的主节点上启用持久化后，它将包含最新的持久化数据，并可以保持数据的持久性。

总结起来，从节点在主从复制中主要用于数据冗余和提高读性能，并不负责自动执行数据持久化。如果你希望在主节点崩溃后仍然能够持久化数据，需要在新的主节点上配置和启用适当的持久化机制。

## 你的意思是如果主节点开启了数据持久化，那么从节点复制的数据就是主节点持久化后的数据，如果主节点没有开启数据持久化，那么从节点复制的数据就是主节点实时写入的数据，对吗？

几乎正确，但还有一个要注意的地方。在Redis的主从复制中，从节点复制的数据不仅取决于主节点是否开启了数据持久化，还取决于从节点当前的复制策略。

如果主节点开启了数据持久化（例如RDB快照或AOF日志），那么从节点在复制数据时，会复制主节点持久化后的数据。这意味着从节点复制的数据是主节点持久化数据的快照。

然而，如果主节点没有开启数据持久化，从节点会根据当前的复制策略来复制数据。默认情况下，从节点采用的是异步复制（async replication），即从节点会尽快地复制主节点的写操作，但不保证实时一致性。这意味着从节点复制的数据可能是主节点实时写入的数据，但可能会有一定的延迟。

此外，Redis还提供了一种复制策略称为同步复制（sync replication），在该策略下，从节点会等待主节点确认写操作后才复制数据，以确保复制的数据与主节点的数据完全一致。但这会带来一定的性能损耗和延迟。

因此，从节点复制的数据取决于主节点是否开启了数据持久化，并受到复制策略的影响。如果主节点开启了数据持久化，从节点复制的数据是持久化后的数据。如果主节点没有开启数据持久化，从节点复制的数据可能是主节点实时写入的数据，但可能会有一定的延迟，除非使用同步复制策略。

## 主从切换过程后的数据持久化如何实现

1. 手动设置，在新的主节点上设置持久化功能
2. 考虑使用Redis的"append-only file"（AOF）持久化方式，并开启AOF的自动重写功能。

在Redis的配置文件（redis.conf）中开启AOF持久化和自动重写功能，具体的配置项`appendonly`和 `auto-aof-rewrite-percentage`、 `auto-aof-rewrite-min-size`。

通过这样的配置，即使你无法立即感知到主节点崩溃，AOF文件会保留所有的写操作记录，而新的主节点在升级后会继续执行AOF日志的追加，保证数据的持久化。

## Redis内存固定1g, 现在容量快到1g了怎么办

如果Redis的内存容量已经接近1GB，并且无法进行扩容或增加节点的情况下，可以考虑以下解决方案：

1. 设置过期时间：对于一些不再使用或者不常访问的数据，可以为其设置适当的过期时间，让Redis自动清理这些过期数据，释放内存空间。
2. 使用LRU淘汰策略：将Redis的淘汰策略设置为LRU（Least Recently Used），这样在内存不足时，会优先淘汰最近最少使用的数据，以释放内存。
3. 使用数据压缩：对于可压缩的数据类型，可以考虑使用压缩算法对数据进行压缩，减小占用的内存空间。
4. 数据持久化到磁盘：将部分数据持久化到磁盘上，可以选择使用RDB持久化或者AOF持久化方式。这样可以释放内存空间，将一部分数据存储在磁盘上。
5. 数据分片：将数据分片存储在多个Redis节点上，将数据按照一定的规则分布到不同的节点上，以减少单个节点的内存使用。
6. 数据清理和优化：定期检查并清理无用或冗余的数据，优化数据结构和存储方式，减少内存占用。

需要根据具体的业务需求和系统架构来选择合适的解决方案。同时，建议进行容量规划和监控，及时预估内存使用情况，避免内存容量达到极限。

## Redis 淘汰机制

Redis的淘汰机制是为了在内存不足时选择合适的策略来淘汰一些键值对，以释放内存空间，以便存储新的数据。Redis提供了以下几种常见的淘汰策略：

1. LRU（Least Recently Used）

: 最近最少使用策略，会淘汰最近最少使用的键值对。

2. LFU（Least Frequently Used）

: 最不经常使用策略，会淘汰使用频率最低的键值对。

3. TTL（Time To Live）

: 设置键值对的过期时间，Redis会自动删除过期的键值对。

4. Random（随机）

: 随机选择键值对进行淘汰。

Redis还提供了一些可配置的淘汰策略，如`volatile-lru`、`volatile-lfu`和`volatile-random`，这些策略只会在设置了过期时间的键中选择淘汰。

你可以通过Redis的配置文件或者命令来设置淘汰策略。例如，使用`maxmemory-policy`配置项可以设置淘汰策略的名称，如`maxmemory-policy LRU`。

需要注意的是，淘汰机制是为了释放内存空间，但也可能导致某些数据被淘汰，从而无法访问。因此，在选择淘汰策略时需要权衡存储空间和数据访问需求，并根据具体的业务场景进行配置。

## 渐进式Rehash

//TODO







