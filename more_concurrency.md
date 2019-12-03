# Servers #

* So far we talked about servers
* Main part of managing servers is managing how we handle connections
* Four methods to handle server connections
    1) One process/thread per connections
        * One process per clinet
        * Fork a new process to handle every connection
        * Advantage of simplicity
        * Disadvantage of having to make an entire new process

    2) Pool of processes 
        * Create a pool of available processes for clients to use
        * Advantage of no longer having to fork, rapid connection, can set pool size
        * Disadvantage that still have to context swetch

    3) Thread Solutions 1 & 2
        * Threads allow multiple conturrent executions in a single process
        * Can implement a server as a single process with multiple threads
            * Either one thread per connection or a pool of threads
        * Advantage of fast thread switching and shared address space, shared code, shared data
        * Disadvantage of needing thread-safe code and always worry about data sharing


## `select()` ##
* `select()` takes a list of file descriptors that are available
    * returns which ones that are readable or writable
    * blocks

```
int select(
    int nfds,       //highest numbered FD+1
    fd_set* readfds,    //input FDs of interest
    fd_set* writefds,   //output FDs of interest
    fd_set* errorfds,   // FDs where exception has occurred
    struct timeval* timeout // when to time out
)
```

* The readfds, writefds, and errorfds are bit masks
* See lecture slides
