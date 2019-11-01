# Mixing Languages #
## Scripting Languages ##
* The bash shell scripting language is not the only standard UNIXscripting lannguage

## awk ##
* Commonly used for writing one-line programs on UNIX systems
* adds computational ability to the command line

```
#!/usr/bin/awk -f
BEGIN {prinf "Hello world!"; exit;}
```

## Associative arrays ##
* awk features a kind of array called an associative array
* You can store multiple different items in an array: some given input to some given output
* example:
```
myarray[0] = "dog"
myarray["cat"] = "feline"
myarray[3] = 6
```

* This is a sparse array because there are breaks in the integer numbering from 0 to 3
* An associative array is also called:
    * Map, hash, lookup table

## Perl ##
* General purpose programming language
    * Practical Extraction and report language
* Borrows feature sfrom C, shell scripting, awk, sed, Lisp, and others
* Designed to be easy to use, not necessarily elegant

```
#!/usr/bin/perl
# The traditional first program

# strict and warnings are recommeded
use strict
use warnings

print "Hello World\n"
```

## Python ##
* Similar philosophies as Perl, but now far more widespread than Perl
* Python is faster, with better support for object Oriented Programming

## Perl & Python ##
* Interpretted languages
* When you want to run code you've written, it is first read by an interpreter, slightly optimized ("compiled"), and then executed
* Perl can only be interpreted by `perl`, Python is interpreted by `python`

## Mixing Languages ##
* Scripting languages and compiled languages can call each other
* This allows us to combine the best parts of one with the other

#### Mixing C into Python ####
```
$ gcc -o c-billion c-billion.c
$ cat python-billion-fast
#!/usr/bin/python
from subprocess import call
call("./c-billion")
```

* Ways to get data back into Python:
    * have the C program write a datafile that is read by Python
    * Create a UNIX pipe that python and C can read and write
    * Create a C funtion inside the Python program with the "instant" module


* You can write a C program that calls Python and returns the value back to C


