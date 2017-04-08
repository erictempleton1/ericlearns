+++
author = "eric"
date = "2017-04-08T07:06:45-04:00"
title = "A Closer Look At Namespaces"
tags = ["C++","programming", "namespaces"]
slug = "closer-look-at-namespaces"
+++

According to [Microsoft's C++ Language Reference](https://msdn.microsoft.com/en-us/library/5cb46ksf.aspx), C++ namespaces are (more or less):

> A declarative region that provides a scope to types, functions, variables, etc inside it. Namespaces are used to organize code into logical groups and to prevent name collisions when working with a code base that includes multiple libraries.

So, C++ namespaces are used to organize your code and prevent duplicate names (collisions). Got it.

When I first started working in C++ I was writing `using namespace std;` at the top of my files. It turns out that this can be a bad idea for more than a few reasons. The main reason being `name collisions`. 

```C++
using namespace awesome1;
using namespace awesome2;
```

In the example above (modified from [this SO post](http://stackoverflow.com/questions/1452721/why-is-using-namespace-std-considered-bad-practice)) if `namespace awesome2` got an update that included a function named exactly the same as a function that exists in `namespace awesome1` and you were using said function somewhere in your code, you now have a collision. I would expect to see a lengthy error output from the compiler telling me that there is a problem.

The first C++ [tutorial](http://www.greenteapress.com/thinkcpp/) I worked through had me putting `using namespace std;` at the top of my files, as I mentioned above. While this isn't _terrible_ in practice, it's also not ideal. To make declarations more clear, and to avoid potential name collisions, I switched over to prepending `std::` to my declarations instead. 

Using the prepend (`std::cout` for example) you're basically saying:

> Hey, I'm using a reference to something that lives somewhere else! But, let me tell you exactly where it comes from. I'll also do you the nice favor of not bringing in a bunch of other things that might not be needed.

The "not bringing in a bunch of other things" is an important point too. It's better to bring in exactly what you need instead of cluttering up the namespace with _everything_ in the given library. This can get confusing pretty quickly. I'm picturing the equivalent in Python:

```python
from pymongo import *  # Now I have tons of things I don't need and don't know about. Dang.
```
vs
```python
from pymongo import MongoClient  # I only need this thing right now and nothing else. Awesome!
```

In C++, this looks something like the example code below. Adding the prepend `std::` (or others) is very tedious as I found out, but I think it does make the code more readable in the long run. 

```C++
#include <iostream>

int main() {
    int awesomeIntVar = 100;
    std::cout << "My awesome int variable: " << awesomeIntVar << std::endl;
    return 0;
}
```

Another reasonable alternative is the `using` directive:

```C++
#include <iostream>
using std::cout;
using std::endl;

int main() {
    int awesomeIntVar = 100;
    cout << "My awesome int variable: " << awesomeIntVar << endl;
    return 0;
}
```

But how much is the `using` directive pattern above really saving me? Am I giving up some _code readability_ in exchange for a slight convenience? I think I'll stick with the _tedious_ explicit declaration for now. 

Apparently `using namespace ...` or using `using std::cout` (for example) is a big no-no in header files. Similar to the above scenarios, this can cause major name collisions. The [example](http://stackoverflow.com/questions/4872373/why-is-including-using-namespace-into-a-header-file-a-bad-idea-in-c) below shows what happened if I decided for some silly reason to create a `struct` named `string` with the `std` namespace declared and `#include <string>`. The compiler has no clue which `string` to use, and you get an error.

```C++
#include <string>
using namespace std;

struct string {const char* p;};  // hmm, probably a bad name choice

int main() {
    // declare a string....but from where? Error.
    string x;
}
```

_But wait, there's more!_ You can (and should) declare your own namespaces in C++!

```C++
#include <iostream>

namespace awesomeNamespace {
    // whatever variable you need!
    int height = 300;
}

namespace coolFunc {
    // functions too!
    void func() {
        std::cout << "heck yeah" << std:: endl;
    }
}

int main() {
    std::cout << awesomeNamespace::height << std::endl;
    coolFunc::func();
    return 0;
}
```

Why would you want to declare your own namespace? Well, I think this would help differentiate functions with the same name in the same space. If you use the namespace prepend, you can let the compiler know exactly which function you are referring to. Brilliant.

All this C++ namespace research got me wondering about namespaces in Python. Is there such a thing as a Python namespace? Well, yes but it works much differently than C++ namespaces. Python has `local` and `global` namespaces. A `local` namespace exists in a package, module, class, or function. A `global` namespace exists outside of the `local` namespace when a name cannot be found in the `local` namespace. This wonderful [SO answer](http://stackoverflow.com/questions/3913217/what-are-python-namespaces-all-about?answertab=votes#tab-top) sums it up nicely.

