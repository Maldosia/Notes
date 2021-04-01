# JUC

- java.util.concurrent
- java.util.concurrent.atomic
- java.util.concurrent.lock

# 1. 常用接口

## 1.1 Lock接口

### 1.1.1 实现关系图

![image-20210401110317135](images\Lock实现类.png)

- NonfairSync和FairSync继承Sync，同时这三个都是ReentrantLock的内部类。
- WriteLock和ReadLock实现了Lock接口，Sync继承了AQS，这两个都是ReentrantReadWriteLock的内部类。

### 1.1.2 常用API

- void lock() 如果没有其它线程占用，则立即获取锁，并**设置锁的持有数为1**；如果当前线程已经获取了锁，则重新再获取一次（可重入），并设置**锁的持有数+1**；如果被其它线程持有锁，则该线程进入休眠等待，知道获取锁为止，获取后**设置锁的持有数为1**；锁的公平策略，取决于构造器的传入，默认不公平；不会被中断，不会抛出InterruptedException。
- boolean tryLock() 如果获取不到锁，则立即返回，不会等待（与lock()的最大区别）;如果当前线程已持有该锁，则**设置锁的持有数+1**；如果该锁正在被占用，返回false。
- boolean tryLock(long time, TimeUnit unit) 设置超时时间，即允许等待一段时间。
- void unlock() 如果当前线程持有锁，则**设置锁的持有数-1**（减后不一定是0）；如果当前持有数已经是0，再unlock则抛IllegalMonitorStateException。
- Condition newCondition() 返回这个锁关联的Condition

## 1.2 ReadWriteLock接口

### 1.2.1 实现关系图

![image-20210401111133163](images\ReadWriteLock实现类.png)

- ReadLock和WriteLock都实现了Lock接口，都是ReentrantReadWriteLock的内部类。
- NonfairSync和FairSync继承Sync，Sync继承了AQS，同时这三个都是ReentrantReadWriteLock的内部类。

### 1.2.2 常用API

- Lock readLock() 返回读锁
- Lock writeLock() 返回写锁

## 1.3 Condition接口



















