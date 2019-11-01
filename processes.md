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

