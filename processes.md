# Processes #

## The Process ##
* Process Management is a necessary component of a multiprogrammable operating system
* Process:
    * An instance of an executing program. Kind of like a bundle of resources for a program

## UNIX Process Components ##
* A unique identity (process id) :: `pid_t pid = getpid();`
* A virtual address space
* Program code and data
* User/ group identity (umask value)
* An execution environment all to itself
    * Environment variables
    * Current working directory
    * List of open files
    * A description of actions to take on receiving signals
* Resource limits, scheduling priority
* and more... see the `exec()` main page

## Programs vs Processes ##
* A program is the executable code (recipe)
* A process is a running instance of a program (dish)
* More than one process can be concurrently executing the same program code with separate process resources

## Process States in UNIX ##
* Running -- on the CPU
* Waiting -- waiting for I/O, etc... Blocked on something
* Runnable -- waiting for CPU
* Zombie -- Waiting for parent to clean it up

## How do you create a process? ##
* Let the shell do it for you
    * when you execute a program
* You can do it yourself
* UNIX provides an API for this



## Managing Processes ##
* Functions we'll be covering:
    * `fork()`
    * the `exec()` family
    * `execl(), ex....` Lecture slides

## How to start a new process ##
* Cloning
* When you call `fork()`, your process clones itself, both has the same code and execution line
* When fork returns 0, your the child, nonzero is the parent

### Process A == Process B?? ###

* Two processes have different pids
* Each process returns a different value from fork()
* Process B has all the same variables that have the same values, but are not separately mangesd

```
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

void main(){
    pid_t spawnpid = -5;
    int ten = 10;

    spawnpid = fork();
    switch(spawnpid)
    {

        case -1:
            perror("Hull Breach!");
            return 1;
            break;
        case 0:
            ten = ten + 1;
            printf("I am the child! ten = %d\n", ten);
            break;

        default:
            ten = ten - 1;
            printf("I am the parent! ten = %d\n", ten);
            break;



    }

    printf("This will be executed by both of us!");

}
```

### Key Items Inherited ###
* Inherited by the child from the parent:
    * Program code
    * Process credentials
    * Virtual memory contents including stack and heap
    * Open file descriptors
    * Close-on-exec flags
    * signal handling interupts

### Key Items unique to children ##
* Unique to the child:
    * Proces ID
    * Parent process ID is different
    * Own copy of file descriptors
        * This means that the child has access to everything parent has
    * Process, text, data and other memory locks are NOT inherited
    * Pending signals initialized to the empty set

* `fork()` forms a family tree

## Child Process Termination ##
* A chold process can exit for two reasons
    * It completes execution and exits normally
        * Case 1: The child process completed what it was supposed to do and exited with a successful exit status
        * Case 2: The chold process encountered an error condition, recognized and exited with non-successful exit status
    * It was killed by the signal

## Checking the Exit Status ##
* Both of these commands check for child process id
    * `wait()`, `waitpid()`

## `wait` vs `waitpid` ##
* `wait()` will block until any one process terminates and returns the pid of the terminated child
* `waitpid()` wll block until the child process with the specified process id terminates
    * You can use this to check instead of block


## `wait()` and `waitpid()` syntax ##
* Block this parent until any process terminates:
```
childPID = wait(&childExitMethod);
```
* Block this parent until the secified child process terminates:
```
childPID_actual = waitpid(childPID_intent, &childExitMethod, 0);
```
* check if any process has completed, return immediately with 0 if none have:
```
childPID = waitpid(-1, &childExitMethod, WNOHANG);
```
* check if a certain process has completed
```
childPID_actual = waitpid(childPID_intent, &childExitMethod, WNOHANG);
```
## Checking the Exit Status - Normal Termination ##
* `wait(&childExitMethod)` and `waitpid(..., &childExitMethod, ...)`
can identify two ways a process can terminate:

    * If the process terminates normally then the WIFEXITED macro returns non-zero:
       SLIDES
```
WIFSIGNALED(childExitMethod); <-- checks if signal terminated the process
int sigID = WTERMSIG(childExitMethod);
```
NOTE: Remember to check for -1 and 0 depending on if there are processes 

* Barring the use of the non-standard WCONTINUED and WUNTRACED flags in `waitpid()`, only one of the `WIFEXITED()` and `WIFSIGNALED()` macros will be non-zero
* This if you want to know how a child process died, you need to use both `WIFEXITED` and `WIFSIGNALED`
* If the child process has terminated normally, do not run `WTERMSIG()` on it, as there is _no signal number_ that killed it!
* If the child process was terminated by a signal, do not run `WEXITSTATUS()` on it, as it has _no exit staus_


## Checking the Exit Status ##
```
int childExitMthod;
pid_t childPID = wait(&childExitMethod);

if(childPID == -1)
{
    perror("wait failed");
    exit(1);
}
if(WIFEXITED(childExitMethod)){
    printf("The process exited normally\n");
    int exit Status = WEXITStATUS(childExitMethod);
    printf("exit status was %d\n", exitStatus);


}
else
    printf("Child terminated by a signal\n");
```




# How to run a completely different program #
## `exec...()` - Execute ##
* `exec()` replaces the currently running program with a _new_ program tht you speccify
    * SO to keep the same program, `fork()` and then `exec()`
* `exec()` functions do not return 
* You can specify arguments to `exec()`


## Two Types of Execution ##
`int execl(char* path, char*arg1, ..., char* argn);`
* Executes the program specified by _path_ and gives it the comand line arguments specifed by strings _arg1_ through _argn_

`int execv(char* path, char* argv[]);`
* Executes the program specified by _path_ and gives it the command line arguments indicated by the pointers in _argv_

## Current Working Directory ##
* `execl()` and `execv()` do not examine PATH variable, only the current working directory
* If you don't specify a fully qualified path name, the your programs will not be executed ecen if they are in a directory lested in PATH and `execl()` and `execv()` will return an error
* To move around the directory structure in C, use the following:
    * `getcwd()` :: Gets the current working directory
    * `chdir()` :: Sets the current working directory

* `int execlp()` and `int execvp` will search your PATH variable for the executable given in _path_

## Execute a New Process ##
* `exec...()` _replaces_ the program it is called from - does not create a new process!
* Using `fork()` and `exec...()`, we can keep our original program going and spawn a brand-new process!

## Passing parameters to `execlp()` ##
* `int execlp(char* path, char* arg1, ..., cahr*argn);`
* First parameter to `execlp()` is the pathname of the new program
* Remaining parameters are "command line arguments"
* First argument should be the same as the first parameter (the command itself)
* the last argument must always be NULL, indicating that there are no more parameters
* Do not pass any shell-specific operators into an member of the `exec...()` family, like <,>, |, &, or ~

```
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
$include <stdlib.h>
void main(){
    pid_t spawnPid = -5;
    int childExitStatus = -5;

    spawnPid = fork();
    switch(spawnPid){
        case -1: { perror("Hull Breach!\n"); exit(1); break;}
        case 0: {
            printf("CHILD(%d): SLeeping for 1 second\n", getpid());
            sleep(1);
            printf("CHILD(%d) : Converting into \`ls -a\`\n", getpid());
            execlp("ls", "ls", "-a", NULL);
            perror("CHILD: exec failure!\n");
            exit(2); break;
        }
        default: {
            printf("PARENT(%d): Sleeping for 2 seconds\n", getpid());
            sleep(2);
            printf("PARENT(%d): Wait()ing for child(%d) to terminate\n", getpid(), spawnPid);
            pid_t actualPid = waitpid(spawnPid, &childExitStatus, 0);
            printf("PARENT(%d): Child(%d) terminated, Exiting!\n", getpid(), actualPid);
            exit(0); break;
        }
    }
}
```
* To pass N number of arguments, you can use `execv()` because it takes an array of strings
## `exit()` ##
* `atexit()` function takes a funciton pointer and arranges for a function to be called before `exit()`
* `exit()` does the following:
    * Calls all functions registered by `atexit()`
    * flushes all stdio output streams
    * removes files created by `tmpfile()`
    * then calls `_exit()`
* `_exit()` does the following:
    * Closes all files
    * Cleans up everything - see the man page for `wait()` to see what happens on exit

## Environment Variables ##
* A set of text variables, often used to pass information between the shell and a C program
* May be useful if:
    * You need to specify a configuration for a program that you call frequently (LESS, MORE)
    * You need to specify a configuration that will affect many differnt commands 
* You can view/edit environment from bash by using `printenv` and `export` commands

* The environment can be edited in C  with `setenv()` and `getenv()`
