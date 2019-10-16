

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

| synchronized                                                            	| Reentrant Locks                                                              	|
|-------------------------------------------------------------------------	|------------------------------------------------------------------------------	|
| implicit(no additional variable needed)                                 	| explicit(additional variable needed)                                         	|
| scope of lock is limited to ``` synchronized(this){ .. } ``` this block 	| scope of lock is flexible, acquire lock in one method and release in another 	|
| no additional method                                                    	| tryLock() and tryLock(timeout) additional method                             	|

**Reentrant Lock:**
  - allows threads to try gaining lock repeatedly. call lock method by same method on same object mutiple times.
    getHeldCount() will help to get the times a mtd has tried to access the lock.
  - advisable to surround the aquire lock and release with try..catch block in case of failure handling
  ```java
  private ReentrantLock lock = new ReentrantLock();
  private static acquireResource(){
    lock.lock();
    try{
    //access the resource
    }catch(){
    //handle exception
    }finally(){
      lock.unlock(); //release lock in either situation for other threads
    }
  ```
  - only one thread acquires at a time.
  - In this case irrespective of read or write opertaion of the thread, enters wait state when a thread is holding a lock.
  - fair lock (slow)
      ```th1 -> lock
      th2,th3,th4 ->wait (queue -FIFO)
      th1->release
      th2->lock
      th3,th4
    ```
    ```java
    ReentrantLock lock= new ReentrantLock(true); //will acquire fair lock
    ```
    Note: tryLock() method will make the attempt of other thread wait longer and acquire the lock even if it is a fair lock
    
   - unfair lock(faster)
    ```sequence
      th1 -> lock
      th2,th3,th4 -> wait (queue -FIFO)
      th1-> release lock <-th5 (same time)
      th5->lock
      th2,th3,th4 -> wait
    ``` 
    ```java
    ReentrantLock lock= new ReentrantLock(false); //will acquire unfair lock
    ```
 **ReentrantReadWriteLock:**
  - Multiple can own the read lock and view (only one operation is allowed either read or write at a time. Mutliple read is allowed simultaneously)
  
    ``` sequence
    readlocks ->resource
    writeThread1,writeThread2 ->wait
    readLocks ->release
    writeThread1 -> acquire lock
    writeThread2->wait
    ```
  - writelock will act like ReentrantLock.
  - threads in waiting queue are processed like fairLock.
      1. all thread(read) will gain lock and write is waiting. (this makes ReentrantReadWriteLock efficient)
      2. if another read thread comes in queue, it has to wait for write thread to finish. 
  
  **BlockingQueue:**
    - thread-safe
        1. producer threads th3-> th1,th2,....<--consumer threads 
        2. .......<----- consumer thread (consumer thread goes to **block** state as the queue is empty)
        3. producer thread th5----> th1,th2,th3,th4 (producer thread goes into **block** state as queue is full)
        
  **SynchronousQueue:**
     - similar to BlockingQueue, not storing element hence no peek().
        1. producer threads th3->[] <--consumer threads (put of producer thread is **block** till consumer thread is ready to get -*direct-handoff*)
        
   **Exchanger:**  
     - Datastructure that allows two threads to exchange items.
     - works similar to BlockingQueue and SynchronousQueue
     - Usecase: Buffer 
     producer thread has full buffer exchanges with consumer thread which has empty buffer
     
**DeadLocks in MultiThreading**
_____________________________________________________________________________________________________________________________________

Deadlock occurs when a thread waiting for a lock held by another thread and vice versa.
 ```
    Thread1-> LockA->LockB(waiting for Thread2 to release)
    Thread2-> LockB -> LockA(waiting for Thread1 to release)
 ```
 Detecting deadlock becomes difficult with multiple lock types and source of thread
 
Detect deadlock
1. At runtime, use tools like Threaddump to detect deadlock. JVM will detect and either outputs the threaddump for a processID to a out file ($jstack processid>./out.txt) or on console ($kill -3 <processid>)
2. Java API to detect deadlock. *findDeadLockedThread*
```java
   ThreadMXBean threadBean = ManagementFactory.getThreadMXBean();
   long[] threadIds = threadBean.findDeadLockedThread();
```

**Ways to avoid deadlock:**
 - *Include timeouts for locks.*
 ```java
    private void execute() throws InterruptedException{
      Lock lock = new ReentrantLock();
      boolean acquired = lock.tryLock(2,TimeUnit.SECONDS);//this attempt of acquiring will wait for 2 sec and then proceed something else if false.
      
      BlockingQueue queue = new ArrayBlockingQueue(16);
      queue.poll(2, TimeUnit.SECONDS);
      
      Semaphore sem = new Semaphore(1);
      sem.tryAcquire(2, Time.SECONDS);
    }
 ```
  - *Ordering of locks*
    If Thread1 acquires lock A and tries to lock B
    Thread2 too tries to acquire lock A and then lockB.
    
    *Global ordeing of locks*
    Locks in this example are consistent. 
    ```
    Thread1-> LockA->LockB 
    Thread2-> LockA(waiting)
    Thread1-> Releases both locks (Locks are available for Thread2)
    Thread2->LockA -> LockB
    ```
    Sometimes these Global ordering becomes tricky with inconsistent locks.
    eg.,
    Like acc1 in this example is A and acc2 is B for first thread
    acc2 is A and acc1 is B for second thread. This is called inconsistent ordering. 
    Adding a condition to make the lock consistent would solve this problem.
    ```java
    private void transfer(Account from, Account to, int amt){
        //Adding these lines will take care of ordering
        Account acc1 = getLarger(from,to); //say acc1 has accno. greater than acc2, always A gains lock first for both threads
        Account acc2 = getSmaller(from,to);
        synchrozied(acc1){ //acc1 and acc2 will be different for different method call. Ordering  of acquiring is inconsistent
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
