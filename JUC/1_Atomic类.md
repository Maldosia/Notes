# JUC

- java.util.concurrent
- java.util.concurrent.atomic
- java.util.concurrent.lock

## 1. Unsafe类

### 1.1 简介

Unsafe类拥有像C语言的指针一样操作内存的能力，一旦能够直接操作内存，这就意味着（1）不受jvm管理，也就意味着无法被GC，需要手动GC；（2）Unsafe中的不少方法必须提供原始地址(内存地址)和被替换的内存地址，偏移量需要自己算，一旦出现问题就是jvm奔溃级别的异常，会导致整个jvm实例崩溃，表现为应用程序直接crash掉；（3）直接操作内存，速度更快，在高并发条件下能够很好的提高效率。

### 1.2 方法

```text
1.初始化操作：通过getUnsafe()方法获取，单例模式，会判断当前类加载器是否是bootstrap classloader，若不是，则抛异常。
2.操作对象属性
3.操作数组元素
4.线程挂起和恢复
5.CAS机制
```

## 2. CAS操作

compare and swap。使用一个期望值和一个变量的当前值进行比较，如果相等，就使用一个新值替换当前变量的值。

```text
优点：	
	硬件层面保证原子性，不会锁住当前线程，效率很高。
缺点：
	1.ABA问题。CAS时，检查值是否发生变化，没有变化的情况下才进行更新。但是如果值A变更成B，再变成A，那么CAS会认为当前值没有变化。使用AtomicStampedReference通过增加版本戳解决ABA问题。
	2.并发越高，失败的次数越多，如果CAS长时间不成功，会极大的增加CPU的开销，因此CAS不适用竞争非常频繁的场景。
	3.只能保证一个共享变量的原子操作。当对多个共享变量操作时，CAS无法保证操作的原子性，此时可以通过锁。也可以把多个共享变量放到一个对象，再通过AtomicReference类来保证引用对象的原子性。
```

## 3. Atomic类

### 3.1 分组

- 基本类型原子类：AtomicBoolean、AtomicInteger、AtomicLong
- 数组类型：AtomicIntegerArray、AtomicLongArray、AtomicReferenceArray
- 引用类型：AtomicMarkableReference、AtomicStampedReference、AtomicReference
- 对象属性修改类型：AtomicLongFieldUpdater、AtomicIntegerFieldUpdater、AtomicReferenceUpdater

### 3.2 使用方法

#### 3.2.1 基本类型原子类

这三种类的方法基本一样，以AtomicInteger为例：

```java
public final int get();//获取当前值
public final int set(int newValue);//设一个值
public final int getAndSet(int newValue);//获取当前值，并设置一个新值
public final int getAndIncrement();//获取当前值，并自增
public final int getAndDecrement();//获取当前值，并自减
public final int getAndAdd(int delta);//获取当前值，并加上预期值
boolean compareAndSet(int expect, int update);//如果当前值等于预期值(expect)，则设置为新值(update)
public final void lazySet(int newValue);//最终设置为newValue,使用lazySet设置之后可能导致其他线程在之后的小段时间内还是可以读到旧值
```

#### 3.2.2 数组类型

这三种类的方法基本一样，以AtomicIntegerArray为例：

```java
public final int get(int i);//获取第i个位置
public final int set(int i,int newValue);//给第i个位置设置一个值
public final int getAndSet(int i);//获取第i个位置的值，并设置为新值
public final int getAndIncrement(int i);//获取第i个位置的值，并自增
public final int getAndDecrement(int i);//获取第i个位置的值，并自减
public final int getAndAdd(int i);//获取第i个位置的值，并加上预期值
boolean compareAndSet(int i,int expect, int update);//如果第i个位置的值等于预期值，则设置为新值
public final void lazySet(int i);//最终将第i个位置设置为新值，也可能导致其他线程在之后的一段时间还是可以读到旧值
```

#### 3.2.3 引用类型

以AtomicReference为例：

```java
public final V get();//获取当前对象
public final V set(V newValue);//设一个对象
public final V getAndSet(int newValue);//获取当前对象，并设置一个新对象
public final V getAndUpdate(UnaryOperator<V> updateFunction);//TODO:
public final V updateAndGet(UnaryOperator<V> updateFunction);//TODO:
boolean compareAndSet(V expect, V update);//如果当前对象是预期对象(expect)，则设置为新对象(update)
public final void lazySet(int newValue);//最终设置为新对象，也可能导致其他线程在之后的一段时间还是可以读到旧对象
```

