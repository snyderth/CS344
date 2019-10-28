# Strings in C #

## Displaying C Strings with Formatted Printing ##
* Formatted means numbers correctly printed with text
* Formatted printing is done with:
    * `printf()` Prints to stdout
    * `sprintf()` Prints to a string (a char array)
    * `fprintf()` Prints to a file
* Each looks for a null terminator

## Basic C String Functions ##
* Use the string library functions:
    * `strcmp()`  Compares two strings for equality
    * `strlen()`  Returns the length of the string in characters, not including the null characters
    * `strcpy()` Copies one string into another
    * `strcat()` Returns one string that is a concatenation of itself with another string
* n-character versions:
    * `strncpy()` Copy ony n charavters - won't null terminate a full array, or actually rpevent you from over-writing an array
    
    
## Declaring C Strings ##
* Three ways of decalaring the same string
    1. `char* mystring = "my string";` <-- this is in read-only memory, immutable (segfault if tampered with)
    2. `char  mystring[] = "my string";`
    3. `char  mystring[20] = "my string";`

## Initializing C string Arrays - The Preferred ##

`strtok()::String tokenizer
* Splits strings into chunks
* Makes your hair fall out
* Maxes out your credit cards
* Unfriends all your friends 
* Sometimes the only tool for the job 


### `strtok()` Example ###
```
char input[18] = "this.is my/strings";

char* token = strtok(input, " ./"); <== "this"

token = strtok(NULL, " ./"); <== "is"

token = strtok(NULL, " "); <== "my/strings"
```

* As `strtok()` finds the delinminating character, it replaces the delimiter with a `NULL` terminator and returns a pointer to the string it was given 
