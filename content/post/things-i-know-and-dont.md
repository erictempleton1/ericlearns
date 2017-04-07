+++
author = "eric"
tags = ["programming","c++"]
draft = false
date = "2017-04-06T20:47:16-04:00"
title = "Things I Know, Things I Don't Know, and Things I'm Avoiding"
slug = "things-i-know-things-dont-know"
+++

Let me get the thing I'm avoiding in C++ out of the way first: _Pointers_. I recently read an article that boldy proclaimed 

> C++ pointers are easy and fun to learn!

Hmm, this is a strange assertion and I'm not entirely convinced that the person who made this statement was entirely in their right mind. To me, pointers are very foreign concept. Even just googling "what is a pointer C++?" lead me to find many different definitions of _what_ a pointer actually is. Here is my best definition of a pointer for right now 

> A variable that stores the (memory) address of another variable

That seems simple enough but I know that's not all there is to it, so I am storing pointers under "Things I'm Avoiding" for now.

How about some things I know? Well, I know that C++ is a _statically typed_ language, and all variable types must be defined as a result of this. Compared to Python and JavaScript (_dynamically typed_) where basically anything goes, this is a bit of a change. But I will say that after writing some C++ for few days that having no option but to define the type is sort of refreshing. It's nice to know exactly what is going in and what being returned.

```c++
int myInt, myOtherInt;
bool myBool;

int awesomeBool() {
    ...
    return 100;
}
```

Another thing I know, but maybe don't fully understand yet is the `main` function. Another foreign concept to me, but it's essentially the _entry point_ for the program. It must also _usually_ return an `int` even if the `int` is just a placeholder zero.

```c++
int main() {
    return 0;
}
```

Of course, `main` also exists in Python but it's not really as common and there is no entry point enforcement really. A more common idiom in Python is the `if __name__ == '__main__:`, which can take a function named `main` or any object of your choice.

Lastly, something I don't fully understand yet are vectors. I know that vectors are a very important data structure in C++, but I can't stop picturing them as Python dictionaries....or maybe a list? They are accessed in a similar fashion to a Python list, but they're not exactly the same. You can iterate them, add (read `push_to`) them, and even _nest_ them. They also need to be `#include`'d when they're used. Very handy, but I need to explore more.