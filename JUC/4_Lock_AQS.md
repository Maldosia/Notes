```java
public abstract class AbstractQueuedSynchronizer{
    static final class Node {}
    public class ConditionObject implements Condition, java.io.Serializable {}
    
    //同步状态(setState()、getState()、compareAndSetState())
    private volatile int state;
    //FIFO同步队列的头节点
    private transient volatile Node head;
    //FIFO同步队列的尾节点
    private transient volatile Node tail;
    
    //独占式获取同步状态
    public final void acquire(int arg) {
    	if (!tryAcquire(arg) &&   //子类实现tryAcquire()
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))//放入等待队列
            selfInterrupt();//当前线程设置interrupt
    }
    //独占式释放同步状态
    public final boolean release(int arg) {
        if (tryRelease(arg)) { //子类实现tryRelease()
            Node h = head;
            if (h != null && h.waitStatus != 0)
                unparkSuccessor(h);  //唤醒头节点
            return true;
        }
        return false;
    }
    //共享式获取同步状态
    public final void acquireShared(int arg) {
        if (tryAcquireShared(arg) < 0)  //子类实现tryAcquireShared()
            doAcquireShared(arg);  //若未获取到同步状态，进入同步队列
    }
    //共享式释放同步状态
    public final boolean releaseShared(int arg) {
        if (tryReleaseShared(arg)) {  //子类实现tryReleaseShared()
            doReleaseShared();  //唤醒头节点
            return true;
        }
        return false;
    }
    
    
    
}
```

```java
static final class Node {
    //记录节点的等待状态
    //CANCELLED:1,由于同步队列中等待线程超时或者被中断，需要从同步队列中取消等待，节点进入该状态将不会变化
    //INITAL:0,初始状态
    //SIGNAL:-1,后继节点的线程处于等待状态，而当前线程如果释放了同步状态或者取消，将会通知后继节点，使得后继节点得以运行
    //CONDITION:-2,节点在等待队列中，当其它线程对Condition调用了signal方法后，该节点转移到同步队列
    //PROPAGATE:-3,下一次共享式同步状态将会无条件地被传播下去
    volatile int waitStatus;
    //共享式的NODE
    static final Node SHARED = new Node();
    //独占式的NODE
    static final Node EXCLUSIVE = null;
    //前节点
    volatile Node prev;
    //后节点
    volatile Node next;
    //等待队列
    Node nextWaiter;
}
```

```java
public class ConditionObject implements Condition, java.io.Serializable {
    //等待队列的头节点
    private transient Node firstWaiter;
    //等待队列的尾节点
    private transient Node lastWaiter;
    //当前线程进入等待状态直到被通知或者中断
    public final void await() throws InterruptedException {}
    public final long awaitNanos(long nanosTimeout){}
    public final boolean awaitUntil(Date deadline){}
    public final boolean await(long time, TimeUnit unit){}
    public final void signal() {}
    public final void signalAll() {}
}
```

