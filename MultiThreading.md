

**Multiple sources of Thread:**
1. Start user defined thread
2. Thread pool - ExecutorService
3. Schedule Thread pool - ScheduledExecutorService
4. In SpringBoot each individual request is handled as thread and framework uses ThreadPools
  ```java
  @RequestMapping("/usr/32")
  public void userDetails(){
  }
  ```
**Lock Types:**
1. Class level lock - ReentrantLock
2. Method level lock - using *synchronized* keyword.
3. Concurrent datastructures - BlockingQueue, Semaphore

**DeadLocks in MultiThreading**
_________________________________________________________________________________________________________________________________________

When two threads are executed 


Tool to detect deadlock

1. Use tools like Threaddump to detect deadlock. JVM will detect and either outputs the threaddump for a processID to a out file ($jstack processid>./out.txt) or on console ($kill -3 <processid>)
2. Java API to detect deadlock. *findDeadLockedThread*
```java
   ThreadMXBean threadBean = ManagementFactory.getThreadMXBean();
   long[] threadIds = threadBean.findDeadLockedThread();
```

Ways to avoid deadlock:
 - Include timeouts for locks.
 ```java
    private void execute() throws InterruptedException{
      Lock lock = new ReentrantLock();
      boolean acquired = lock.tryLock(2,TimeUnit.SECONDS);
      
      BlockingQueue queue = new ArrayBlockingQueue(16);
      queue.poll(2, TimeUnit.SECONDS);
      
      Semaphore sem = new Semaphore(1);
      sem.tryAcquire(2, Time.SECONDS);
    }
 ```
  - Ordering of locks
    If Thread1 acquires lock A and tries to lock B
    Thread2 too tries to acquire lock A and then lockB.
    
    *Global ordeing of locks*
    eg.,
    ```java
    private void transfer(Account from, Account to, int amt){
        //Adding these lines will take care of ordering
        Account acc1 = getLarger(from,to);
        Account acc2 = getSmaller(from,to);
        synchrozied(acc1){ //acc1 and acc2 will be different for different method call. Ordering  of acquiring varies
          synchronized(acc2){
            acc1.deduct(amount);
            acc2.add(amount);
           }
        }
     }
    public void execute(){
      new Thread(this::transfer(A,B,100)).start());
      new Thread(this::transfer(B,A,200)).start();
    }
    ```
