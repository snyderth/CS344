# More UNIX I/O #
## Open File Inheritance ##
* When you `exec...()` a process, you replace the current process with a new one, but the files are still open and accessible to the new process
* This may not be good for stdin and stdout... especially if you don't want another process reading the stdin you want or outputting to your stdout
* In this lecture: Other IPC methods and UNIX I/O:
    * Close on exec
    * File redirection in C
    * Pipes
## I/O redirection ##
* We saw I/O redirection in the shell:
```
ls > file
stats < file1
cat longfile | more
find . -name "paper" -print 2> /dev/null
echo "an error occurred" 1>&2
```
* I/O redirection is possible because open files are shared across `fork()` and `exec...()`
* each child process has the same files open as its parent, as the file descriptors are the same file descriptors in both
* Note this important point: child and parent operate on the same file pointer table. To avoid this, once a fork is spawned, close and reopen the file in one process
* Recall: The kernel opens stdin, stdout, and stderr automatically for every process created:
    * File descriptor 0 is stdin
    * File descriptor 1 is stdout
    * File descriptor 2 is stderr

## Redirecting stdout ##
File Descriptor | points to
--------------------------
0               | terminal
1               | terminal
2               | terminal
3               | myfile.data

```
dup2(3, 1);
```
File Descriptor | points to
--------------------------
0               | terminal
1               | myfile.data
2               | terminal
3               | myfile.data

## Close on Exec Details ##
* A flag specified in an `open()` call that tells the kernel to close any open files when/if the process gets `exec...()`'d
* Why do we care? Because open files are inherited by child processes


```
#include <fcntl.h>
...
int fd;
fd = open("file", O)RDONLY);
...
fcntl(fd, F_SETFD, FD_CLOEXEC);
...
//exec...
```
## Real Inter-Process Communication (IPC) ##
* IPC methods in UNIX
    * Intermediate/temporary files::often used with I/O redirection
    * Pipes :: IPC between two processes forked by a common ancestor process
    * FIFOs (named pipes) :: communication between any two processes on the same machine
    * Message queues
    * Sockets

## Between-Process IPC - Intro to Pipes ##
* I/O redirection with `dup2()` allows you to redirect input and output betewen processes and files...
* But what if you have a lot of data or data that you want to process in that temp file while the other process wants to write to that file

## Pipes ##
* Pipes rovide a way to connect a write-only file descriptor in one process to a read-only file descriptor in another process
* `write()` puts bytes in the pipe. `read()` takes bytes out

## Creating a Pipe ##
* Pipes are possible because descriptors are shared across `fork()` and `exec...()`
* A parent process creates a pipe
    * Results in two new open file descriptors: one for in and one for out
* The parent calls `fork()` and possibly `exec()`
    * Parent and child have the file descriptors created with the pipe
* the child process now reads from the input file descriptor, and the parent process writes to the output file descriptor
* the `pipe()` function is passed an array of two integers and fills it with file descriptors. The first fd is the input and the second is the output.
* The parent should use one and the child should use the other

## Flow Control with `read()` ##
* `read()` succeeds if data is available
    * receives the data and returns immediately
    * The return value of `read()` tells you how many bytes were read, which may be less than you requested
* If data is not available, `read()` will block waiting for data

## Flow Control with `write()` ##
* Write will not return until all data has been written
* Pipes have a certain size and typically 64K bytes will fit
* If the pipe fills up, there is no more room to write, so `write()` will block until it can send all the data that it has

## Error Checking Reads and Writes ##
* Chekcing the return value of these functions is recommended to see everything has gone well

## Closing Pipes ##
* See Lecture

## Named Pipe = FIFO ##
* Persists in the file sstem
* Create in C with `mkfifo()` or `mkfifo` in bash
* Once created, any process can open a FIFO with `open()`
* Once opened, it works like a pipe

* Useful for
    * persistent channel of communication
    * Communicate between processes without worrying about networking

## Opening a FIFO ##
* YOu must open `O_RDONLY` or `O_WRONLY`
