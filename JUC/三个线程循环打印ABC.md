#### Synchronized

###### 示例代码一

```java
public class PrintSynchronized1 {

    @Test
    public void printTest() throws InterruptedException {
        Object objectA = new Object();
        Object objectB = new Object();
        Object objectC = new Object();

        PrintThread printThreadA = new PrintThread(objectC, objectA, "线程A");
        PrintThread printThreadB = new PrintThread(objectA, objectB, "线程B");
        PrintThread printThreadC = new PrintThread(objectB, objectC, "线程C");

        new Thread(printThreadA).start();
        TimeUnit.MILLISECONDS.sleep(100);
        new Thread(printThreadB).start();
        TimeUnit.MILLISECONDS.sleep(100);
        new Thread(printThreadC).start();

        TimeUnit.SECONDS.sleep(3);
    }


    public static class PrintThread implements Runnable {
        private Object pre;
        private Object self;
        private String name;

        public PrintThread(Object pre, Object self, String name) {
            this.pre = pre;
            this.self = self;
            this.name = name;
        }

        @Override
        public void run() {
            int count = 1;
            while (count < 15) {
                synchronized (pre) {
                    synchronized (self) {
                        System.out.println("----" + name + "第" + count + "次");
                        count++;

                        self.notifyAll();
                    }
                    try {
                        if (count == 15) {
                            pre.notifyAll();//最后一次唤醒其它线程，自己正常退出
                        } else {
                            pre.wait();//释放pre锁，当前线程休眠
                        }
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```

###### 示例代码二

```java
public class PrintSynchronized2 {
    private static Object object = new Object();
    private static int state = 0;

    @Test
    public void printTest() throws InterruptedException {

        new Thread(new Runnable() {
            @Override
            public void run() {
                try{
                    int count = 1;
                    while (count < 11) {
                        synchronized (object) {
                            if (state%3 == 0){
                                System.out.println(Thread.currentThread().getName()+" 第"+count+"次"+" : A");
                                state++;
                                count++;
                                object.notifyAll();
                            }
                            if (count == 11) {
                                object.notifyAll();
                            }else {
                                object.wait();
                            }
                        }
                    }
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try{
                    int count = 1;
                    while (count < 11) {
                        synchronized (object) {
                            if (state%3 == 1){
                                System.out.println(Thread.currentThread().getName()+" 第"+count+"次"+" : B");
                                state++;
                                count++;
                                object.notifyAll();
                            }
                            if (count == 11) {
                                object.notifyAll();
                            }else {
                                object.wait();
                            }
                        }
                    }
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try{
                    int count = 1;
                    while (count < 11) {
                        synchronized (object) {
                            if (state%3 == 2){
                                System.out.println(Thread.currentThread().getName()+" 第"+count+"次"+" : C");
                                state++;
                                count++;
                                object.notifyAll();
                            }
                            if (count == 11) {
                                object.notifyAll();
                            }else {
                                object.wait();
                            }
                        }
                    }
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }).start();

        TimeUnit.SECONDS.sleep(1);
    }
}
```

#### Lock And State

###### 示例代码

```java
public class PrintLockState {

    private static int state = 1;
    private static Lock lock = new ReentrantLock();
    
    @Test
    public void printTest() throws InterruptedException {
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i < 11; ) {
                    lock.lock();
                    try {
                        if (state % 3 == 1) {
                            System.out.println("A------"+state);
                            state++;
                            i++;
                        }
                    } catch (Exception e) {
                        e.printStackTrace();
                    }finally {
                        lock.unlock();
                    }
                }
            }
        },"线程A").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i < 11; ) {
                    lock.lock();
                    try {
                        if (state % 3 == 0) {
                            System.out.println("C------"+state);
                            state++;
                            i++;
                        }
                    } catch (Exception e) {
                        e.printStackTrace();
                    }finally {
                        lock.unlock();
                    }
                }
            }
        },"线程C").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i < 11; ) {
                    lock.lock();
                    try {
                        if (state % 3 == 2) {
                            System.out.println("B------"+state);
                            state++;
                            i++;
                        }
                    } catch (Exception e) {
                        e.printStackTrace();
                    }finally {
                        lock.unlock();
                    }
                }
            }
        },"线程B").start();
        
        TimeUnit.SECONDS.sleep(1);
    }
}
```

#### Lock And Condition

###### 示例代码

```java
public class PrintLockCondition {
    private static int count = 0;
    private static Lock lock = new ReentrantLock();
    private static Condition conditionA = lock.newCondition();
    private static Condition conditionB = lock.newCondition();
    private static Condition conditionC = lock.newCondition();

    @Test
    public void printTest(){
        Thread printThreadA = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    printA();
                }
            }
        },"线程A");
        Thread printThreadB = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    printB();
                }
            }
        },"线程B");
        Thread printThreadC = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    printC();
                }
            }
        },"线程C");

        printThreadC.start();
        printThreadB.start();
        printThreadA.start();
    }

    public static void printA(){
        lock.lock();
        try{
            if(count%3 == 0){
                System.out.println(Thread.currentThread().getName());
                count++;
                conditionB.signal();
            }else {
                conditionA.await();
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    
    public static void printB(){
        lock.lock();
        try{
            if (count%3 == 1){
                System.out.println(Thread.currentThread().getName());
                count++;
                conditionC.signal();
            }else {
                conditionB.await();
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    
    public static void printC(){
        lock.lock();
        try{
            if (count%3 == 2){
                System.out.println(Thread.currentThread().getName());
                count++;
                conditionA.signal();
            }else {
                conditionC.await();
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
```

#### Semaphore

###### 示例代码

```java
public class PrintSemaphore {

    private static Semaphore semaphoreA = new Semaphore(1);
    private static Semaphore semaphoreB = new Semaphore(0);
    private static Semaphore semaphoreC = new Semaphore(0);

    @Test
    public void printTest() throws InterruptedException {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 10; i++) {
                        semaphoreA.acquire();
                        System.out.println("i"+i+"A");
                        semaphoreB.release();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 10; i++) {
                        semaphoreB.acquire();
                        System.out.println("i"+i+"B");
                        semaphoreC.release();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    for (int i = 0; i < 10; i++) {
                        semaphoreC.acquire();
                        System.out.println("i"+i+"C");
                        semaphoreA.release();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();

        TimeUnit.SECONDS.sleep(1);
    }
}
```

#### AtomicInteger

###### 示例代码

```java
public class PrintAtomicInteger {

    private static AtomicInteger state = new AtomicInteger(0);

    @Test
    public void printTest() throws Exception{
        new Thread(new Runnable() {
            @Override
            public void run() {
                while (state.get() < 29) {
                    if (state.get() % 3 == 0) {
                        System.out.println(Thread.currentThread().getName()+" A"+" STATE "+state);
                        state.incrementAndGet();
                    }
                }
            }
        },"线程A").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (state.get() < 30) {
                    if (state.get() % 3 == 1) {
                        System.out.println(Thread.currentThread().getName()+" B"+" STATE "+state);
                        state.incrementAndGet();
                    }
                }
            }
        },"线程B").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (state.get() < 30) {
                    if (state.get() % 3 == 2) {
                        System.out.println(Thread.currentThread().getName()+" C"+" STATE "+state);
                        state.incrementAndGet();
                    }
                }
            }
        },"线程C").start();

        TimeUnit.SECONDS.sleep(1);
    }
}
```

