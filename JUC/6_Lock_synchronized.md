# JUC

- java.util.concurrent
- java.util.concurrent.atomic
- java.util.concurrent.lock

# 1. 对象头之markword

![](images\对象头之Markword.png)

# 2. Object类

# 3. Synchronized

## 3.1 概念

```shell
#可以把任意一个非NULL的对象当成锁，属于独占式的悲观锁，同时属于可重入锁。
#jdk1.6是默认开启偏向锁和轻量级锁，可以通过-XX:-UseBiasedLocking来禁用偏向锁。
```

## 3.2 作用范围

- 作用在方法时，锁对象是实例(this)；
- 作用在静态方法时，锁对象是Class实例；
- 作用在某个实例对象时，锁对象是该实例对象。

## 3.3 锁的升级(synchronized)

### 3.3.1 升级过程

```shell
#无锁-->偏向锁-->轻量级锁-->重量级锁
无锁：程序不会有锁的竞争，不需要加锁
#无锁-->偏向锁
当对象被当作同步锁并有一个线程A抢到了锁时，锁标志位不变，是否偏向锁设置为1，前23bit设置线程A的ID，此时表示进入偏向锁状态。
#偏向锁-->轻量级锁
当线程B试图获取这个锁时，JVM发现同步锁处于偏向状态，但是markword中线程ID记录的不是B，则B会先用CAS操作试图获取获取锁(置换线程ID)。一定会失败？失败则撤销偏向锁并且设置为轻量级锁。
#轻量级锁-->重量级锁
线程B自旋一定次数任未获取到锁，设置markword为重量级锁，线程阻塞。线程A同步代码执行完后，CAS替换markword失败(因为线程B修改了)，释放锁并唤醒等待的线程，然后markword设置为0|10。
```

### 3.3.2 撤销偏向锁流程

```shell
#待到全局安全点，暂停线程A(原持有偏向锁的线程)
#暂停线程A，判断线程A是否存活。
#否，则markword设置无锁状态；是，则设置为轻量级锁，唤醒线程。
```

## 3.4 核心组件

```shell
#1.Contention List：竞争队列，所有请求锁的线程首先放在这里
#2.Entry List：Contention List中有资格成为候选资源的线程移动到这里
#3.Wait Set：调用了wait方法被阻塞的线程放到这里
#4.OnDeck：任意时刻，最多只有一个线程正在竞争锁资源，该线程被称为OnDeck
#5.Owner：当前已经获得锁的线程
#6.!Owner：当前释放锁的线程
```

## 3.5 流程

1. JVM每次从ContentList尾部取出一个用于锁竞争候选者(OnDeck)，但是并发情况下，ContentList会被大量的并发线程CAS访问，为了降低对尾部元素的竞争，JVM会将一部分线程移动到Entry List。
2. Owner线程会在unlock时，将ContentList中的部分线程迁移到EntryList中，并指定EntryList中的某个线程为OnDeck线程(一般是先进去的那个)。
3. Owner线程并不会直接把锁传递给OnDeck线程，而是把锁竞争的权利交给OnDeck，OnDeck也需要重新竞争锁。称为竞争切换。
4. OnDeck线程获取到锁资源后会变成Owner线程，而没有获取到锁的任然停留在EntryList中。如果Owner线程被wait方法阻塞，则转移到WaitSet中，知道某个时刻被notify或者notifyAll唤醒重新进入EntryList中。

## 3.6 非公平性

```shell
#1.在线程进入ContentList前，等待的线程会尝试自旋获取锁，如果获取不到进入ContentList，这对于已经进入队列的线程来说是不公平的。
#2.自旋获取锁的线程还可能直接抢占OnDeck线程的锁。
```

# 4. 分段CAS(卧槽)

LongAdder

