== History ==
- Charles Babbage was the OG computer archtect because the miltary needed to aim guns
- Ada Lovelace was the daughter of a partier, so her mom moved her to math, and she met Charles Babbage. She was the first person that proposed data abstraction, etc.

== C is A High-Level Language ==
- As opposed to a low level language, like assembly
- The original version of C has 32 reserved keywords, and 50+ operators and syntax characters
- C syntax influences programming language syntax development today

==== Hello_World.c ====
```
#include <stdio.h>

int main()
{
    printf(Hello world\n");
    return 0;

}

```

==== Printing out 2 decimal floats ====
```
int main()
{
    char* oblig = "Hello World\n";
    float itsOver = 9000.0f;
    printf("%s\n", oblig);
    printf("IT\'S OVER %.2f!\n", itsOver);

    return 0;

}
```

=== Cool Historical CS Folks ===
- Grace Hopper developed the first human-friendly coding languages
- Alan Turing Beat Nazis with math, laying the groundwork for CS


== Strings ==
```
#include <stdio.h>
#include <string.h>


void main(){

    char* boring = "boring";
    char* weirdDadSaying = "Eat more beef, kick less cats\n";
    int length;

    length = strlen(weirdDadSaying);
    printf("Length of the entered string is = %d\n", length);

    if(strcmp(boring, weirdDadSaying) == 0)
        printf("Entered strings are equal.\n");
    else
        printf("Entered strings are not equal.\n");

}
```

== How to Not Read in Input ==
```
#include <stdio.h>
#include <string.h>

void main()
{
    char a[1000], b[1000];

    printf("Enter the first string\n");
    gets(a);

    printf("Enter the second string\n");
    gets(b);

    strcat(a,b);

    printf("String obtained on concatenation is %s\n", a);
}
```




