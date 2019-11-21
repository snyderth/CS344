# Signals #

## Inter-Process Communication (IPC) ##

* How can we connect our processes together? How can they communicate?
* Signals indicate to a program that some critical event has occured

## Signals ##
* There are 32 signals (32 bit field)
* You cannot create your own signals, but how the program reponds to the signal is up to the programmer
* There are two signals with no inherent meaning at all - you assign meaning to them by catching them and running code
* A segfault is a signal. You can catch and ignore a segfault
* Signals Interrupt and commondere a thread and puts its own code on the stack and then cleans up after
* You cannot be sent the same signal once if you have not handled the previous one. They get aggregated to one single signal handler

### Uses for signals: Kernal to Process ###
* Notifications from the Kernel
    * A process has done something wrong
    * A timer has expired
    * A child process has completed executing
    * An event associated with the terminal has occured
    * The process on the other end of a communication link has gone away

### Users for Signals: Process to Process ###
* User process to user process notifications, perhaps to:
    * Suspend or resume execution of the process
    ...


## Signal Dictionary - Termination ##

signal  | #     | Easy Name     | Catchable     | Meaning of Signal     | Default action if not caught  | core dump
_________________________________________________________________________________________________
SIGABRT | 6     | Abort         | yes           | Terminate; sent by process itself during abort() call |   Terminate   | yes
_________________________________________________________________________________________________
SIGQUIT | 3     | Quit          | Yes           | Terminate; sent by user.  | Terminate | Yes
_________________________________________________________________________________________________
SIGINT  |   2   |   Interrupt   |   Yes The process si requested to terminate; performs cleanup; CTRL-C sends this and all its children.    |   Terminate   |   No
_________________________________________________________________________________________________
SIGTERM | 15    |   Terminate   |   yes |   The process is requested to terminate and performs cleanup | Terminates| Yes


## `kill` ##
```
kill -TERM 1234
```
* The given PID affects who the signal is sent to:
    * If PID > 0, then the signal will be sent to the process PID given
    * If pid == 0, then the signal is sent to all processes in the same process group as the sender. (From an interactive command line, this means the foreground process group, i.e. your shell)
    * More trickiness for pid < 0


## Signaling a Script ##
```
$ cat sigtermtest
#!/bin/bash
trap "echo 'SIGTERM Received! Exiting with 0!; exit 0" SIGTERM
while [ 1 -eq 1]
do
    echo "nothing" > /dev/null
done
```
```
$ sigtermtest &
[1] 1708
$ psme
...
$ kill -SIGTERM 1708
SIGTERM Received! Exiting with 0!
[1]+    Done                sigtermtest
```

## Signal Dictionary - Notification of Wrongdoing ##
* Signal: SIGSEGV, SIGBUS, SIGFPE, SIGILL, SIGSYS, SIGPIPE
* Easy Name: Segmentation fault, Bus error, floating point error, Illegal instruction, System Call, Pipe
* All catchable
* All Terminate
* All Core dump

## Why Notify on Events? Branching Logic! ##
* Gives the process a chane to clean up and finish any important tasks:
    * Perform final file writes
    * `free()` data
    * Write to log files
    * Send signals itself
* A process catching a signal and handling it will do all, some, or none of the above, and then either terminate itself or continue executing

## Signal Dictionary - Control ##
* SIGALRM, SIGSTOP, SGTSTP, SIGCONT, SIGHUP, SIGTRAP
* Alarm, Stop, Terminal Stop, Continue, Hang up, Trap

## Timers! ##
* If you want to wait a specified period of time...
    * You can do a busy wait which will consume the CPU continuously while accomplishing nothing
    * Or you can tell the kernel that you want to be notified after a certain amount of time passes

* To set a timer in UNIX
    * call the `alarm()` or `ualarm()` functions
    * After the time you specify has passed the kernel will send your process a SIGALRM signal

* There is also a SIGCHLD signal
* Normally, `wait()` and `waitpid()` will suspend a process until one of its child processes has terminated
* Using the signal SIGCHLD allows a parent process to do othe rwork instead of going to sleep and be notified via signal when a child terminates
* When a SIGCHLD is received, the process can call `wait()` or `waitpid()` when ready. SIGCHLD does not clean up

## User defined signals ##
* SIGUSR1, SIGUSR2
* No Special kernel meaning
* the author of bot sending and receiving processes must agree on the interpretation of the meaning of the signals
 
## Abmormal termination: Core Dumps ##
 * Some signals received case an "abnormal termination"
 * This also occurs during runtime if the process crashes
 * When this happens, a memory core dump is created which contains:
    * Contents of all varialbes
    * Hardware registers
    * Kernel process info at the tim eof termination


## "Core" Etymology ##
* Memory used to have a magnetic core


## Sleeping while doing something ##
* Sometimes has nothing to do, so you consider calling `sleep()`, but you are able to respond to signals
* If a signal is set to be ignored then the `pause()` continues to be in effect
* If a signal causes a termination, `pause()` does nothing because the process dies

## Sending signals to Yourself: `raise()` and `alarm()`
* You can send yourself a specifed signal immediately with `raise()`:
```
int raise(int signal);
```
More on lecture slides

## Utility Type: Signal Sets ##
* A signal set is simply a list of signal types used elsewhere
* A signal set is defined using the special type `sigset_t` defined <signal.h>
* To declare signal type:
```
sigset_t my_signal_set;
```
* init or reset the signal set to have no signal tpes:
```
sigemptyset(&my_signal_set);
```
* init or reset the signal set to have all signal types:
```
sigfillset(&my_signal_set);
```
* To add a signal signal type
```
sigaddset(&my_signal_set, signal);
```
* There is a function to remove signals

## `sigaction()` ##
* `sigaction()` registers a signal handling function that you've created for a specified set of signals
```
int sigaction(int signo, struct sigaction *newact, struct sigaction *origact);
```
* This is good for temporarily replacing a signal response

## The sigaction Structure ##
```
struct sigaction
{
    void (*sa_handler)(int);
    sigset_t sa_mask;
    int sa_flags;
    void (*sa_sigaction)(int, siginfo_t*, void*);
};
```
* The first attrubute is a function pointer

## Pointers to Functions in C ##

* Declaring a function:
```
void* sa_handler(int);
```
* Declaring a function pointer:
```
void (*sa_handler)(int);
```

* You can use the `sa_mask` attribute to indicate what signals should be blocked while the signal handler is executing
    * _Blocked_ means that the signals arriving diring the execution of the handler function are held until your signal is finished
* Pass `sa_mask` a `sigset_t` type as described above

* Do not do non-reentrant things during signal handlers.
* i.e. When you malloc something, a mutex is locked to control the heap. Don't do printf. You must use write or read to prevent this from happening if you have to output to the user.

* The fourth attricute specifies an alternative signal handler function to be called. This will only be used of SA_SIGINFO flag is set in `sa_flags`
* The `siginfo_t` struct pointer you pass in will be written to once `sa_sigaction` is invoked; it will then contain information such as which process sent the signal
* Most of the time, you'll use `sa_handler` and not `sa_sigaction`

## Child Processes and Inheritance ##
* When calling `fork()` child processes inherit and get their own instance of the signal handler functions declared in the parent
* However, calling `exec...()` in your process will remove and special signal handlers

## Blocking Signals ##
* `sigprocmask()`

## Signals and System Calls ##
* Signals can arrive and stop a system call 
* You can tell ssystem calls to automatically restart by setting `SA_RESTART` in the `sa_flags` variable of the `sigaction` struct





