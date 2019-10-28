# File Access in C #

In Unix, everything is a file
* except processes

* Directory contents could include:
    * Hard links
    * Sym links
    * named pipes
    * Device character special file
    * Device block special file
    * Named Socket


What is a File in UNIX?
* A stream of bytes
    * could be accessed an an array
    * Newlines/carriage returns & tabs are all just bytes
* persistent

## Opening a File
* Files can be open for:
    * read only :: O_RDONLY
    * write only :: O_WRONLY
    * read and write :: O_RDWR

## The File Pointer
* Tracks where the next file operation occurs in an open file
* A separate file pointer is maintained for each open file


## Open for Read
```
char file[] = "cs344/grades.txt";
int file_descript;

file_descript = open(file, O_RDONLY);

if(file_descript < 0)
{
    fptrintf(stderr, "Could not open %s\n", file);
    exit(1);
}

close (file_descript);
return 0;

```
## Open for Write
```
#include <fcntl.h>
char file[] = "cs344/grades.txt";
int file_descript;

file_descript = open(file, O_WRONLY);

if(file_descript < 0)
{
    fptrintf(stderr, "Could not open %s\n", file);
    perror("Error in main()");
    exit(1);
}

close (file_descript);
return 0;

```

## Truncating an Existing File
* O_TRUNC
See lecture slides

## Appending
* O_APEND 
* adds to the end of the file

## Creating a New File
* O_CREAT flag to open and create
* If you create a file, you must set the permissions

file_descriptor = open(filepath, O_WRONLY | O_CREAT, 0600);


## lseek()

* Manipulates a file pointer in a file
* Used to control where you're messing with the file
```
//Move to byte #16
newpos = lseek(file_descriptor, 16, SEEK_SET);
//Move forward 4 bytes
newpos = lseek(file_descriptor, 4, SEEK_CUR);
//Move to 8 bytes from end
newpos = lseek(file_descriptor, -8, SEEK_END);
```

* lseek writes to the byte that it points to
 

## Files or Streams?
* __stdin, stdout,__ and __stderr__ are actually _file streams_
* Getting input from the user:
    * getline allocates a buffer size for how many chars you need if you pass in zero `numCharsEntered = getline(&lineEntered, &buffersize, stdin); // get a line from the user`
        * `char* lineEntered = NULL;`
        * `size_t bufferSize = 0;`


## Obaining File Information
* stat() and fstat()
* Retrieve information about a file or directory
    * Which device it is stored on
    * Number of hard links pointing to it
    * Size of the file
    * Important Timestamps
