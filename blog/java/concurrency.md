# Java 多线程编程

多线程编程是 Java 开发中的重要技能，本文介绍并发编程的核心概念和实践。

## 线程基础

### 创建线程

```java
// 方式1: 继承 Thread 类
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running");
    }
}

// 方式2: 实现 Runnable 接口（推荐）
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable running");
    }
}

// 使用
Thread thread = new Thread(new MyRunnable());
thread.start();

// 方式3: 使用 Lambda 表达式
Thread thread = new Thread(() -> {
    System.out.println("Lambda running");
});
thread.start();
```

### 线程状态

```
新建 → 就绪 → 运行 → 阻塞/等待 → 终止
```

## 线程池

### Executor 框架

```java
// 固定大小线程池
ExecutorService fixedPool = Executors.newFixedThreadPool(10);

// 缓存线程池
ExecutorService cachedPool = Executors.newCachedThreadPool();

// 单线程池
ExecutorService singlePool = Executors.newSingleThreadExecutor();

// 定时任务线程池
ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(5);
scheduledPool.scheduleAtFixedRate(task, 1, 1, TimeUnit.SECONDS);

// 提交任务
Future<String> future = executorService.submit(() -> {
    return "Task completed";
});

// 关闭线程池
executorService.shutdown();
executorService.awaitTermination(60, TimeUnit.SECONDS);
```

### ThreadPoolExecutor

```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    5,                          // 核心线程数
    10,                         // 最大线程数
    60L,                        // 空闲线程存活时间
    TimeUnit.SECONDS,
    new LinkedBlockingQueue<>(100),  // 任务队列
    new ThreadFactory() {
        private final AtomicInteger count = new AtomicInteger(0);
        
        @Override
        public Thread newThread(Runnable r) {
            return new Thread(r, "my-pool-" + count.incrementAndGet());
        }
    },
    new ThreadPoolExecutor.CallerRunsPolicy()  // 拒绝策略
);
```

## 锁机制

### synchronized

```java
// 同步方法
public synchronized void syncMethod() {
    // 方法体
}

// 同步代码块
public void syncBlock() {
    synchronized (this) {
        // 代码块
    }
}

// 静态同步方法（锁住 Class 对象）
public synchronized static void staticSyncMethod() {
    // 方法体
}
```

### ReentrantLock

```java
ReentrantLock lock = new ReentrantLock();

public void lockMethod() {
    lock.lock();
    try {
        // 临界区
    } finally {
        lock.unlock();
    }
}

// 尝试获取锁
if (lock.tryLock()) {
    try {
        // 临界区
    } finally {
        lock.unlock();
    }
}

// 公平锁
ReentrantLock fairLock = new ReentrantLock(true);
```

### ReadWriteLock

```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();
Lock readLock = rwLock.readLock();
Lock writeLock = rwLock.writeLock();

public void read() {
    readLock.lock();
    try {
        // 读操作
    } finally {
        readLock.unlock();
    }
}

public void write() {
    writeLock.lock();
    try {
        // 写操作
    } finally {
        writeLock.unlock();
    }
}
```

## 并发工具类

### CountDownLatch

```java
CountDownLatch latch = new CountDownLatch(3);

// 启动多个线程
for (int i = 0; i < 3; i++) {
    new Thread(() -> {
        try {
            // 执行任务
            latch.countDown();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }).start();
}

// 等待所有线程完成
latch.await();
```

### CyclicBarrier

```java
CyclicBarrier barrier = new CyclicBarrier(3, () -> {
    System.out.println("All threads reached barrier");
});

for (int i = 0; i < 3; i++) {
    new Thread(() -> {
        try {
            // 执行任务
            barrier.await();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }).start();
}
```

### Semaphore

```java
Semaphore semaphore = new Semaphore(5); // 最多 5 个线程同时访问

public void accessResource() {
    try {
        semaphore.acquire(); // 获取许可
        try {
            // 访问共享资源
        } finally {
            semaphore.release(); // 释放许可
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}
```

### Exchanger

```java
Exchanger<String> exchanger = new Exchanger<>();

new Thread(() -> {
    try {
        String data = "Data from Thread 1";
        String received = exchanger.exchange(data);
        System.out.println("Thread 1 received: " + received);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();

new Thread(() -> {
    try {
        String data = "Data from Thread 2";
        String received = exchanger.exchange(data);
        System.out.println("Thread 2 received: " + received);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();
```

## 并发集合

### ConcurrentHashMap

```java
Map<String, String> concurrentMap = new ConcurrentHashMap<>();

concurrentMap.put("key", "value");
String value = concurrentMap.get("key");

// 原子操作
concurrentMap.putIfAbsent("key", "default");
concurrentMap.computeIfAbsent("key", k -> "computed");
```

### ConcurrentLinkedQueue

```java
Queue<String> queue = new ConcurrentLinkedQueue<>();

queue.offer("item1");
queue.offer("item2");

String item = queue.poll(); // 移除并返回队首
String peek = queue.peek();  // 返回队首但不移除
```

### BlockingQueue

```java
BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(10);

// 生产者
new Thread(() -> {
    try {
        blockingQueue.put("item"); // 队列满时阻塞
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();

// 消费者
new Thread(() -> {
    try {
        String item = blockingQueue.take(); // 队列空时阻塞
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();
```

## 原子类

```java
// 基本类型
AtomicInteger atomicInt = new AtomicInteger(0);
atomicInt.incrementAndGet();
atomicInt.compareAndSet(0, 1);

// 数组
AtomicIntegerArray atomicArray = new AtomicIntegerArray(10);
atomicArray.set(0, 100);

// 对象引用
AtomicReference<String> atomicRef = new AtomicReference<>("initial");
atomicRef.compareAndSet("initial", "updated");

// 对象属性
AtomicIntegerFieldUpdater<User> updater = 
    AtomicIntegerFieldUpdater.newUpdater(User.class, "age");
updater.getAndIncrement(user);
```

## CompletableFuture

```java
// 异步执行
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Hello";
});

// 链式调用
CompletableFuture<String> result = CompletableFuture.supplyAsync(() -> {
    return "Hello";
}).thenApply(s -> {
    return s + " World";
});

// 组合多个 Future
CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> "World");

CompletableFuture<String> combined = f1.thenCombine(f2, (s1, s2) -> s1 + " " + s2);

// 异常处理
CompletableFuture.supplyAsync(() -> {
    if (Math.random() > 0.5) {
        throw new RuntimeException("Error");
    }
    return "Success";
}).exceptionally(ex -> {
    System.err.println("Error: " + ex.getMessage());
    return "Fallback";
});

// 等待结果
String value = combined.get(5, TimeUnit.SECONDS);
```

## 线程通信

### wait/notify

```java
class SharedResource {
    private boolean available = false;
    
    public synchronized void produce() throws InterruptedException {
        while (available) {
            wait();
        }
        // 生产数据
        available = true;
        notifyAll();
    }
    
    public synchronized void consume() throws InterruptedException {
        while (!available) {
            wait();
        }
        // 消费数据
        available = false;
        notifyAll();
    }
}
```

### Condition

```java
ReentrantLock lock = new ReentrantLock();
Condition condition = lock.newCondition();

public void awaitCondition() throws InterruptedException {
    lock.lock();
    try {
        while (!conditionMet) {
            condition.await();
        }
    } finally {
        lock.unlock();
    }
}

public void signalCondition() {
    lock.lock();
    try {
        conditionMet = true;
        condition.signalAll();
    } finally {
        lock.unlock();
    }
}
```

## 最佳实践

1. **优先使用线程池**而非手动创建线程
2. **使用并发集合**而非同步集合
3. **使用原子类**进行简单原子操作
4. **使用 Lock**替代 synchronized（需要更灵活控制时）
5. **避免死锁**：保持一致的加锁顺序
6. **使用 volatile**保证可见性
7. **正确处理中断**

```java
// 正确处理中断
public void interruptMethod() {
    while (!Thread.currentThread().isInterrupted()) {
        try {
            // 执行任务
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            break;
        }
    }
}
```

## 参考资料

- [Java 并发编程实战](https://jcip.net/)
- [Java 并发包 Javadoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/package-summary.html)

---

*最后更新时间: {docsify-updated}*
