

## 进程和线程

进程：是系统进行资源分配和调度的基本单位

线程：是CPU分配（时间片轮转方式让线程轮询占用）的基本单位

一个进程中至少有一个线程，进程中的多个线程共享进程的资源（多个线程共享进程的堆和方法区资源，每个线程都有自己的程序计数器和栈区域）。



## 并发和并行

并发：同一个时间段内多个任务同时都在执行（多个进程 同一时间点 争取同一个进程）

并行：单位时间内多个任务同时在执行



## 虚假唤醒

多线程交互中，必须要防止虚假唤醒，

虚假唤醒：一个线程可以从挂起状态变为可以运行状态，即使该线程没有被其他线程调用notify(),notifyAll()方法进行通知，或者被中断，或者等待超时。这就是虚假唤醒



虽然虚假唤醒在应用实践中很少发生，但要防范于未然，做法就是不停的去测试该线程被唤醒的条件是否满足，不满足则继续等待，也就是说在一个**循环**（不可以用if）中调用wait方法进行防范。

````java
synchronized (obj){
    while (条件不满足) {   //此处不能用if
        obj.wait();
    }
}
````



## Lock和Synchronized

#### 等待和通知方法对比：

![1610957894352](.\images\1610957894352.png)

````java
//synchronized
this.wait();
this.notify();
this.notifyAll();


//lock
Condition condition = lock.newCondition();
condition.await();
condition.signal();
condition.signalAll();
````

#### 

#### lock的精准唤醒

```java
Condition condition1 = lock.newCondition();
Condition condition2 = lock.newCondition();
condition1.await();
condition2.signal();
```



#### Synchronized8锁

笔记：一个对象里面如果有多个synchronized方法，某一个时刻内，只要一个线程去调用其中的一个synchronized方法了，其他的线程如果也想调用其中的synchronized方法就都只能等待，换句话说，某一个时刻内，只能有唯一一个线程去访问这些synchronized方法。**synchronized方法锁的是当前对象this**，被锁定后，其他的线程都不能进入到当前对象的其他synchronized方法。

所有的非静态同步方法都是同一把锁——实例对象本身



synchronized实现同步的基础：Java中的每一个对象都可以作为锁。

具体表现为以下3种形式：

**对于普通同步方法，锁是当前实例对象（this）**

**对于静态同步方法，锁是当前类的Class对象。**

**对于同步方法块，锁是synchronized括号里配置的对象**



当一个线程试图访问同步代码块时，它首先必须得到锁，退出或抛出异常时必须释放锁



也就是说如果一个实例对象的非静态同步方法获取锁后，该实例对象的其他非静态同步方法必须等待获取锁的方法释放锁后才能获取锁。



举例：

作用在方法上时，锁住的是方法所在的整个类，同一时间段只能有一个线程访问这个类中的同步方法(对非同步方法不影响)

```java
class A {
    public synchronized void function(){
        System.out.println("---function");
    }
    
    public synchronized void function2(){
        System.out.println("---function2");
    }
    
    public void function3(){
        System.out.println("---function3");
    }
}
```





## JUC辅助工具类

### CountDownLatch

主要有两个方法，当一个或多个线程调用await方法时，这些线程会阻塞。

其他线程调用countDown方法会将计数器减1（调用countDown方法的线程不会阻塞）

当计数器的值变为0时，因await方法阻塞的线程会被唤醒，继续执行。

```java
public class CountDownLatchDemo {

    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch =new CountDownLatch(6);
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName()+"\t离开教室");
                countDownLatch.countDown();
            }, String.valueOf(i)).start();
        }

        countDownLatch.await();
        System.out.println("班长离开教室");

    }
}
```





### CyclicBarrier

和countDownLatch区别，相当于做加法

```java
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("召唤神龙");
        });

        for (int i = 0; i <= 7; i++) {
            final int tempInt = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t收集到第" + tempInt + "颗龙珠");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
        
    }
}
```



### Semaphore

在信号量上我们定义两种操作

acquire（获取）当一个线程调用acquire操作时，它要么通过成功获取信号量（信号量-1），要么一直等下去，直到有线程释放信号量，或超时。

release（释放）实际上会将信号量的值加1，然后唤醒等待的线程。



信号量主要用于两个目的，一个是用于多个共享资源的互斥使用，另一个用户并发线程数的控制。

```java
public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore semaphore =new Semaphore(3);  //模拟资源类，有3个空车位

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"\t抢占到了车位");
                    TimeUnit.SECONDS.sleep(3);
                    System.out.println(Thread.currentThread().getName()+"\t离开了车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

结果

````
0	抢占到了车位
1	抢占到了车位
2	抢占到了车位
1	离开了车位
0	离开了车位
4	抢占到了车位
3	抢占到了车位
2	离开了车位
5	抢占到了车位
3	离开了车位
5	离开了车位
4	离开了车位
````



### ReadWriteLock

多个线程同时读一个资源类没有任何问题，所以为了满足并发量，读取共享资源应该可以同时进行

但是 如果有一个线程想去写共享资源，就不应该再有其他线程可以对该资源进行读或写

小总结：

​	读-读能共存

​	读-写不能共存

​	写-写不能共存



```java
class MyCache {
    private volatile Map<String, Object> map = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void put(String key, Object value) {
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t写入数据");
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "\t写入成功");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }


    }

    public void get(String key) {
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t读取数据");
            Object result = map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t读取完成" + result);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }

    }
}

/**
 * 多个线程同时读一个资源类没有任何问题，所以为了满足并发量，读取共享资源应该可以同时进行
 * <p>
 * 但是 如果有一个线程想去写共享资源，就不应该再有其他线程可以对该资源进行读或写
 * <p>
 * 小总结：
 * <p>
 * ​	读-读能共存
 * <p>
 * ​	读-写不能共存
 * <p>
 * ​	写-写不能共存
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {

        MyCache myCache = new MyCache();

        for (int i = 0; i < 6; i++) {
            final int tempInt = i;
            new Thread(() -> {
                myCache.put(tempInt + "", tempInt);
                try {
                    TimeUnit.SECONDS.sleep(300);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }

        for (int i = 0; i < 6; i++) {
            final int tempInt = i;
            new Thread(() -> {
                myCache.get(tempInt + "");
                try {
                    TimeUnit.SECONDS.sleep(300);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```



## ThreadPool线程池

