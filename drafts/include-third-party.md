So, you want to `include` a 3rd party library in your C++ program? Cool. I wanted to do this too, but it turned out to be not as straight forward as I thought it would be. Why I thought it would be straight forward I am not sure, but I need to do it either way.

I wanted to connect to a postgres database on my local machine, so I decided to use `libpq-fe.h` which comes with the postgres download. 

Here was my first attempt:

```C++
#include <iostream>
#include "libpq-fe.h"

int main() {
    // my code here...
}
```
And compiled with:
```
g++ main.cpp
```
Result:
```
fatal error: libpq-fe.h: No such file or directory
```

Hmm. Ok, I mostly expected this result actually. The compiler doesn't know anything about `libpq-fe.h` and we need to tell it where the file is.

Next attempt (hard coding the path):
```C++
#include <iostream>
#include "C:\path\to\postgres\include\libpq-fe.h"

int main() {
    // my code here...
}
```
Compiled with:
```
g++ main.cpp
```
Result (abbreviated):
```
undefined reference to `PQconnectdb'
```
Well, that's different. So the compiler knows where the header file is now but it knows nothing about the other methods in the library. Interesting. I think what we need are some flag to tell the compiler where the `include` file is _and_ where the actual library is.

I'll add the `-I` flag with the path to the `libpq-fe.h` include and the `-L` flag with the path to the actual library.

Another attempt:
```C++
#include <iostream>
#include "libpq-fe.h"

int main() {
    // my code here...
}
```
Compiled with:
```
g++ random.cpp -I "path\to\postgres\include" -L "path\to\postgres\lib"
```
Drum roll please...
```
undefined reference to `PQconnectdb'
```
Fuck. I was truly stuck here, but I stumbled across some [postgres docs](https://www.postgresql.org/docs/9.4/static/libpq-build.html) that mentioned include `-lpq` in the compile command to tell the compiler which library you intend to use.

So, using the code directly above I tried again with the following:
```
g++ random.cpp -I "path\to\postgres\include" -L "path\to\postgres\lib" -lpq
```

Annnnnd...no compiler errors. Awesome! I did, of course, have some other unrelated issues with my code but I'll save that for another post. With all of this being said, what I should really be using is a [Makefile](https://stackoverflow.com/questions/2481269/how-to-make-a-simple-c-makefile) to build my programs. The Makefile lets you dump all of the include paths and link paths into a file so they don't have to be typed out over and over. More to come on that too.