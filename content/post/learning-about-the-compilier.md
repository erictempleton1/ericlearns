+++
date = "2017-04-10T20:32:38-04:00"
title = "Learning About the g++ Compiler"
author = "eric"
tags = ["c++", "programming", "g++", "compiler"]


+++

Being that C++ is a _compiled_ language, we need a _compiler_ to _compile_ our code. Phew. For reasons that are not yet clear to me, my computers (Windows & Linux) have `g++` already installed on them, so that's what I've been using. `g++` is used primarily via the command line, and takes a few arguments including file names and an output flag. Let's write a simple C++ program and tinker with compiling it. I'll go ahead and create a new file called `awesomeCalc.cpp` and add the code below. The program just does a simple tax calculation and prints the results to the command line.

```C++
#include <iostream>

int taxCalc(int price, float taxRate) {
    return price + (price * taxRate);
}

void printTotalPrice() {
    int totalPaid = taxCalc(100, .15);  // $100 at a 15% tax rate
    std::cout << "Total price: " << "$" << totalPaid << std::endl;
}

int main() {
    printTotalPrice();
    return 0;
}
```

So, if I wanted to _compile_ this program I would move the location of the file using the command line and type the following:

```bash
g++ awesomeCalc.cpp
```

So far so good, but what just happened? The compiler went through the code, checked for errors, translated our C++ code to _machine code_, and created an output file (usually) called `a.out` or `a.exe` depending on the operating system. You could run the executable from the command line at this point if you wanted:

```bash
a.exe
```

Which should output the following text in the command prompt:

```bash
Total price: $115
```

Nice. But I'd like to call the output file something other than `a.exe`. How about `taxCalculator.exe`? Back in the command prompt I need to _re-compile_ the code with the `g++` optional flag `-o` and the filename of my choice.

```bash
g++ awesomeCalc.cpp -o taxCalculator.exe
```

Now there is a new executable file with the much more _meaningful_ name `taxCaculator.exe`.

Things get a little more complicated when there are multiple files to compile. For example, a `header` file, a `main` file, and `logic` file. I'll probably look back at this scenario more in depth pretty soon, but I started writing my C++ programs in multiple files like this recently to keep everything organized (it also seems like best practice) and I really like it. At first I had a hard time figuring out how to get my code to compile though. My code fed into the `header` and `main` files, but when I ran

```bash
g++ main.cpp -o awesomeProgram.exe
```
I was greeted with a couple pages of error messages. It turns out that what I needed to do instead is compile `main.cpp` with the logic file for my program `myAwesomeProgram.cpp`.

```bash
g++ main.cpp myAwesomeProgram.cpp -o awesomeProgram.exe
```

Yes! No pages of errors, and my executable is created. Does the order of the files matter when you compile? As far as I can tell it _usually_ does not, but this could be a nice surprise for me in the near future. 

```bash
g++ myAwesomeProgram.cpp main.cpp -o awesomeProgram.exe
```

It turns out that in C++ when you are `linking` files using `g++` (e.g. including `coolExistingFile.o`), the order is very important because one of the `.cpp` files could depend on what is in `coolExistingFile.o` and needs to reference it first. I haven't had a need to use `linking` yet, so for now I am throwing my file names at `g++` in whatever order I want.

Up until I recently started learning C++ I have only worked with _interpreted_ langauges, so all of this is very new to me. Among other things in C++, I actually really like compiling and checking for errors. The error output in C++ is a huge change from what I'm used to seeing in Python but it is teaching me to read the tracebacks very carefully. It seems to be helping me be more careful when writing my code because I have this lingering fear of a 10 page error output from `g++`. I tell myself:

> Be diligent and check everything over in the code, or face the consequences of trying to understand an enourmous error output at compile time.

Obviously, I could get pages of error messages no matter how thoroughly I check over my code before compiling, but I think it does helps my code quality and code writing efficiency a little bit. Sometimes fear is a good motivator I guess.
