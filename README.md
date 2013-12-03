gcc-poison
==========

gcc-poison is a simple header file for developers to ban unsafe C/C++ functions from applications. It uses the #pragma GCC poison directive define a number of words (function names) as unsafe. Compilation will fail if these names are present.

Please see the blog post at http://blog.leafsr.com for more information

http://leafsr.com

Example usage
=============

    #include <stdio.h>
    #include <string.h>
    #include "gcc-poison.h"
    
    int main(int argc, char *argv[]) {
       char buf[10];
       strcpy(buf, argv[1]);
       return 0;
    }
    
    $ gcc -o 2 2.c
    1.c: In function ‘main’:
    1.c:8:2: error: attempt to use poisoned "strcpy"
    
Excluding specific functions from poisoning
===========================================

As pointed out in the GCC documentation (http://gcc.gnu.org/onlinedocs/cpp/Pragmas.html), "If a poisoned identifier appears as part of the expansion of a macro which was defined before the identifier was poisoned, it will not cause an error. This lets you poison an identifier without worrying about system headers defining macros that use it."

Here is an example of how to use gcc-poison.h but continue to allow the usage of the 'strcat' function, via a macro:

    #define _unsafe_strcat strcat
    #include "gcc-poison.h"
    
    int main(void)
    {
        char x[512];
        /* this will raise an error */
        strcat((char *)&x, "lol");
        /* ... while this will NOT raise an error */
        _unsafe_strcat((char *)&x, "lol");
    }

Note that you must define any such macros BEFORE you include gcc-poison.h. This can be a handy way to allow developers to continue to use certain functions for which GCC has no safe alternative, while forcing them to acknowledge that they are doing so unsafely.
