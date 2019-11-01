# Debugging in C #

## UNIX C Debugging ##

1. Live Examination
2. Post-mortem Debugging
3. Trace Statement

## Using a debugger with gcc ##
* Compile with the "-g" option
```
$ gcc -g testit.c -o testit
```
* Then start the debugger
```
$ gdb ./testit
```

* In gdb, some key commands:
    * run :: (re)starts the program running
    * break :: sts a breakpoint will the debugger will stop
    * step :: executes a single line of C code; will enter a function call
    * next :: executes a signle line of C code; will not enter a function call
    * continue :: continues until another breakpoint is hit or rogram completes
    * print :: prints out a variable
    * quit :: stop debugging (exit gdb)



* `jump #` jumps the line number provided 

* `info breakpoints` prints out the break points that are set
* `watch <variable>` will show everytime that the variable changes

## Valgrind ##
* valgrind helps find memory leaks in C programs
* Compile with -g to add better diagnostics
* Recommended options: `--leak-check=yes` and `--show-reachable=yes`
* There are options that allow valgrind to show you the uninitialized variable

