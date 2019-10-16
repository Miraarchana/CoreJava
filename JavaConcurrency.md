How to timeout a thread?
  - no stop method in Thread class
  - Using ThreadPool - if a task is submitted to pool and its tpools responsibility to allocate thread to execute it.
    -Shutdown() - will not let to submit a new task to threadpool and any task already submitted are executed
    -ShutdownNow() - will not let to submit, previously submitted task in waiting q are returned, currently executing thread (with task) is attempted to stop. No guarantee.
