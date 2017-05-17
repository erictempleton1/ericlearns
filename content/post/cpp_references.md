+++
title = "References in C++"
tags = ["C++","programming"]
author = "eric"
date = "2017-05-17T06:47:07-04:00"

+++

Another new C++ concept, references! I recently google `C++ references` and [found](https://en.wikipedia.org/wiki/Reference_(C%2B%2B)) the following definition 

> The definition of a reference in C++ is such that it does not need to exist.

What in the actual fuck does that even mean? I have no idea. Maybe it's just a bad definition (likely), or maybe it will make more sense later. Anyways, on to references. A reference in C++ is defined with an ampersand `&` as well as the type and name, but the `&` is not used again after the variable has been defined.

 ```C++
 int &myRef
 ```
 But if you popped the above variable declaration into a program and compiled it, you would be greeted with the following message

 > error: 'myRef' declared as reference but not initialized

The `reference` needs to _reference_ something. Let's give it something to refer to, and create a little program below to keep track of things and actually do something. The code below _will_ compile, but it's not really doing anything meaningful just yet. If you go ahead and compile the program and run the executable you should get two lines of identical output. What it's showing you is `myVar`'s memory location, and that the reference variable `myVarRef` is basically now a second label for that memory location.

```C++
#include <iostream>

int main() {
    int myVar;
    int &myVarRef = myVar;

    std::cout << myVar << std::endl;
    std::cout << myVarRef << std::endl;
}
```

We can go ahead and give the same code above some values to work with. Compiling this program and running the executable will result in two identical lines again, but this time with the actual value, 500 in this case, for both the referencee and referencer.

```C++
#include <iostream>

int main() {
    int myVar = 500;
    int &myVarRef = myVar;

    std::cout << myVar << std::endl;
    std::cout << myVarRef << std::endl;
}
```

```
500
500
```


Here is where things get interesting. If we change the value of `myVarRef`, something I was not expecting to happen to `myVar` happens.

```C++
#include <iostream>

int main() {
	int myVar = 500;
	int &myVarRef = myVar;

	std::cout << "Original value of myVar: " << myVar << std::endl;

	myVarRef = 20;
	std::cout << "Value of myVar after changing the reference value: " << myVar << std::endl;
	std::cout << "Reference var value: " << myVarRef << std::endl;
}
```
```
Original value of myVar: 500
Value of myVar after changing the reference value: 20
Reference var value: 20
```

The original value of `myVar` was changed when I changed the value of the reference variable! Holy crap. So the original variable and the reference variable both refer to the same value and as such, you can perform the same operations through the reference variable as you could with the original. That is amazing.

Let's try to expand on this and change the value of `myVar`. I'm curious to see what impact that will have on `myVarRef`.

```C++
#include <iostream>

int main() {
	int myVar = 500;
	int &myVarRef = myVar;

	std::cout << "Original value of myVar: " << myVar << std::endl;

	myVarRef = 20;
	std::cout << "Value of myVar after changing the reference value: " << myVar << std::endl;
	std::cout << "Reference var value: " << myVarRef << std::endl;

	myVar = 200;
	std::cout << "Value of myVar after changing it's value: " << myVar << std::endl;
	std::cout << "Update reference var value: " << myVarRef << std::endl;
}
```
```
Value of myVar after changing the reference value: 20
Reference var value: 20
Value of myVar after changing it's value: 200
Update reference var value: 200
```

So that changes the values going both ways! Ok, great. But when am I actually going to use this? For one thing, it's really handy when passing arguments to functions. When you do not include the argument as a reference, you are `passing by value` and depending on the size this can be very expensive and slow down your program. Using a reference in your argument, you are `passing by reference` which is much more memory efficient.

Couldn't we just use pointers instead of references? Well, yeah but one major difference is that the compiler handles allocating and deallocating memory for references, but it does not do this pointers. Memory management is up to you when using pointers. Probably pros and cons to that difference, but I think using references also helps with the always important code readability.

Let me revisit that C++ references definition from the very beginning of this post really quick. Does it make sense now? Hell no. This is a bad definition in my opinion. I would define C++ references as

> An alias (or another name) for an existing variable.

More to come on references. I'll be trying to include them more and more in my code.
