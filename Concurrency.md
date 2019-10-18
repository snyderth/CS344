# Concurrency #

## What is Concurrency? ##
* Concurrency: Doing multiple things at a time

    * Program Decomposition into order-independent chunks

* On UNIX concurrecy is easy
    
    * Multiply Processes can be running simultaneously
    * Multiple copies of the same program can be running
    * CPU time can be split and shared


* Concurrency ius powerful
    
    * Greatly increases efficiency of an OS: one process may wait for I/O while another uses CPU


## Important Definitions ##

System Calls

    * A request for service that causes the normal operation of a process to be interrupted and control passed to the OS
    * Typically process is now blocked and is now unable to proceed until the call returns
    * read(), write(), etc.

C Library Functions
    
    * Faster, no permissions or blocking issues
    * sqrt(), printf()


## Illusions of Simultaneous Execution ##

Multiprogramming

    * More than one process can be ready to execute
    * System calls trigger "context switches", which let the next process run
    * The process will not execute again until its system call returns

Timesharing
    * CPU time split between multiple processes
    * This gives the illusion tha tmany processes are running at once

** Multiprogramming: there are times when a program may hand off control
** Timesharing: CPU splits up time regardless of who needs it

## Multiprocessing ##

* Executing multiple processes at the actual same time
* Today's CPUs have multiple cores
* Each core acts like a mini*CPI, each which can do multiprogramming and timesharing


## Possible Complications ##

* Concurrently running processes can share data and/or resources
* What if multiple resources access the same resource at the same time?

    * This is a race condition

Classic Example:
    * Two ATM machines each withdraw $20 from the same account

        * To update bank account balance:
            * Read current balance into memory
            * subtract $20
            * write new balance back to account

    * The issue is that each atm will read the previous balance and both will only return the same balance, so one of the $20 won't be recorded as withdrawn

## Race conditions ##

* Why are the hard to detect?
    
    * May only show up once under strange conditions
    * Race hazard is a flaw in a system or process where the output exhibits unexpected critical dependence on the relative timing of events

## Provide Access Control ##

* Concurrent update situation
    
    * 2+ processes accessing resource concurrently
    * At least one process might write

* __Must provide Access Control__
    * If one process is writing, no other should access (read OR write) the resource 


* "Locks" solve these problems
    * Only the process owning the lock may access the resource
    * Locking usually requires support from the OS


## Access Control with a Lock File

```
do{
    lock_fd = open(lock_file_path, o_WRONLY | O_CREAT | O_EXCL, 0644);

    if (lock_fd == -1)
    {


        if (errno == EExist)
        {
            // File already exists - wait then retry
            sleep(1);

        }
        else
        {

            // An unexpected error -bail
            perror("Couldn't open lock file\n");
            return(-1);
        }

    }

} while (lock_fd == -1);
```



## Deeper Definitions ##

Kernal
    * The central part of an Operating System
    * Manages hardware (and drivers)
    * Provides the Scheduler
    * Not interacted with by users: system calls are requests to the kernel

Scheduler
    * Distributes prioritized and/or fair CPU time


## Process-Level Concurrency ##

* Imagine a chat server that needs to:
    * watch for users connecting
    * Send chat output
    * receive chat input

* A __process__ can only do one of these things at a time

* __Threads__ allow a process to do all of these things at the same time

## Threads vs. Processes ##
* Comms between threads is vastly simpler than interprocess communication (IPC)
    * share:
        * Code, Heap, Data
    * They each have their own stack, but they can access the Stacks of other threads

* The CPU switches between executing threads much faster than between processes because all this memory is shared
    * On a multi-core CPU, the CPU can run each kernel-level thread on a separate core, true concurrency

## Thread Disadvantages ##

* Shared resources means increased possibilities of:
    * Race conditions
    * Resource contention, including file access
    * Leaking of sensitive data across threads

## Types of Threads ##

Kernel-Level Threads
    * Controlled by the kernal, given time by the scheduler
    * Much like a mini-process

User-Level Threads
    * The kernel doesn't know about these -- they are entirely within a process and don't involve the schduler
    * Really just _emulation_ of threading
    * Somtimes called green threads

### Thread Type Comparison ###

Kernel-Level Threads
    * Controlled by the kernel, given time by the scheduler
    * simple to create with built-in libraries in UNIX
    * Generally considered the better choice 

User-Level Threads
    * Switching between threads is even faster because you're not actually switching data, just places in your code
    * Entire process may be interrupted by the scheduler
    * A blocking system call can halt the process, stopping all the threads



## Thread Implementation in UNIX ##

* Implemented with the POSIX Threads API in UNIX

`#include <pthread.h>`

Compile with `-lpthread` in gcc



## Creating a Thread ##

```
int pthread_create(     pthread_t* thread,   <-- Stores the thread ID (in a struct)
                        const pthread_attr_t* attr,  <-- Points to a struct that contains option flag (NULL if none)
                        void* (*start_routine) (void*), <-- Function pointer to the start point of execution fo rthe _new_ thread that copies this one
                        void* arg <-- points to the sole argument that is passed into start_routine. Only a single argument, so multiple args must be passed through a struct.
                  );

```

#### Example: ####
```
int resultInt;

pthread_t myThreadID;

resultInt = pthread_create( &myThreadID,
                            NULL,
                            start_routine,
                            NULL
                          );
```

## Destroying a Thread ##
Threads can be killed by:
    * `pthread_exit()`
    * The thread returns from `start_routine()`
    * Externally by another thread calling `pthread_cancel()`
    * If the process `exit()`s

## Identifying the Executing Thread ##

* Getting the thread id of an executing thread (not necessarily an integer):
```
pthread_t myThreadID = pthread_self();
```
* Testing for equality:
```
pthread_equal(myThreadID, unknownThreadID);
```

`pthread_join()` is a critical function: it blocks until a specific thread exits. It takes a thread ID (struct)

## Mutexes ##

* An abbreviation for "mutual exclusion"
* Implemented as part of hte POSIX `pthread` API to provide thread synchronization via "locks"
* Provides the programmer the ability to protect data from multiple reads and writes on the same files
* There are several types which check variously for erros, perform faster, etc.

### Mutex Lifespan ###
```
pthread_mutex_t myMutex = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_destroy(myMutex);
```

## Mutex Locking ##
* A thread acquires a lock on a mutex variable by attempting to call a special lock function:

```
pthread_mutex_t myMutex = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(myMutex);
```

* Once the mutex is locked, any other thread attempting to lock it will block

## Mutex Unlocking ##

* To unlock, the thread that has previously locked the mute calls:
```
pthread_mutex_unlock(myMutex);
```

* Once niblocked, one of the other blocked threads (that have tried to access the resource) (chosen at random) currently blocked on the mutex will unblock and gain the lock

* A non-blocking attempt to lock the mutex:
```
int resultCode = pthread_mutex_trylock(myMutex);
```

Mutexes Generalized

* A mutex is a form of a _semaphore_, which comes in two types:
    * Counting semaphore
        * Allow some sort of arbitrary resource count, e.g. number of available buffers
    * Binary semaphore
        * Equal to 1 or 0, indicating locked/unavailable or unlocked/abailable




