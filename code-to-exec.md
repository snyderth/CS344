# The Underlying Hardware #
- OSs provide software access to hardware through abstractions
- A chipset block diagram proovides a general setup for the hardware that executes the instructions

## A Processor ##
- A circuit that does logical operations and stores values in registers (Local Memory)
- Most recently accessed data stored in cache
- Two special registers
    1. Program Counter --> points to next instruction
    2. Stack Pointer --> Contains memory addres of current thread's stack
## Memory ##
- An array of bytes
- Temporary storage only:
    - Much slower than the processor and cache
    - Much faster than a disk

## Other Storage Parts ##
- Persistent storage
    - Magnetic hard Disk Drives (HDD)
    - Non-volatile memory
        - Solid State Drives (SSD)
        - Flash memory
    - Magnetic tapes
    - optical (CD-ROM, DVDs, BD, etc)
- All Slower than RAM, but keep their memory without power

## Virtual Memory ##
- Hardware memory used to be linear
- Now it is virual, abstracting into linear virtual memory, which is associated with sections of memory so that multiple things can use the same memory

## Running a Program ##
- How is the program laid out in memory?
```
|code segment --> Program instructions
|-------------------------------------
|Data segment --> INitialized Global & Static variables in read/write and read-only sections
|Uninitialized global & static variables
|-------------------------------------
|Stack - automatic variables, function return pointers
|------------------------------------
|                ||
|                \/
|
|                /\
|                ||
|------------------------------------
|Heap - dynamically allocated memory
```

## Stack Versus Heap ##
The Stack
- Stores local automatic variables and function return pointers
- Mmeory managed by CPU
- Variable size is limited by OS settings
- Variables cannot be resized

The Heap
- Variables are allocated manualy
- You can reallocate variables with realloc()
- You can allocate more memory in one call with this theoretically

## Creating The Program Code - High Level ##
```
Source Code => Compiler => Assembly Code => Assembler => Assembly Language (binary) 
```
This is not cross-platform. Each processor will have its own unique set of instructions.

## The compile/link process ##
1. The C pre-processor expands macros and strips out comments
    - #include, #define, #ifdef, //, /* */, etc.
2. The compiler parses your source, checks for erors and generates assembly code
3. The compiler calls the assembler, which converts assembly code to machine binary code
4. If you are compiling an exe, the linker step tries to match function calls to function code (maybe in different files)

## GCC - the Standard UNIX Compiler ##
- Basic compilation options

    -g Compile with debugging info for GDB
    -c Compiles only (without linking)
    -S Generates assembly code
    -O3 Optimizes as much as possible
    -o Specifies the name of the output file
    -Wall Turns on all warnings
    -l <library> Adds support for library <library> when linking (i.e. -lpthread)

## Compiling an executable ##
- If you have one source (.c) file:
```
$ gcc -o dbtest dbtest.c
```
- If you have multiple source (.c) files:
```
$ gcc -o dbtest dbtest.c dbcreate.c dbopen.c
```

## Separate Compile and Link
- If you have multiple source (.c) files::
    * Compile one at a time and link them at the end

1. First compile all source files separately with the -c flag
2. Link all the .o files

## Library Archives ##
- Library archives are collections of object files (.o) gathered into a large file

- to create a library
    * First create all the object files
    * Use the `ar` command:
```
$ ar -r libdb.a dbcreate.o dbopen.o dbread.o
```

- To use Library Archives
- Include the library anywhere you can use an object file:
```
$ gcc -o dbtest dbtest.o libdb.a
```
To not have to append `./` when you run a program, just add `.` to `$PATH`



Godbolt.org => live compilation
