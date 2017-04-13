+++
author = "eric"
date = "2017-04-13T06:42:44-04:00"
title = "Starting Out With Structs"
tags = ["C++","programming", "structs", "data structures"]

+++

From this point on I am going to write all my C++ programs with a minimum of 3 files: `main.cpp` `headerFile.h` `logicFile.cpp`

`main.cpp` and `logicFile.cpp` will contain an `#include headerFile.h` statement at the top. This seems like the best way to keep everything organized, as well as a C++ best practice.

So, with that out of way let's talk about `structs`. A `struct` is a C++ data structure that allows you to store different _types_ of variables together in one place (encapsulation anyone?). That seems pretty handy, but it also seems very similar to a class....and it turns out that a `struct` is _very_ similar to a `class` in C++. The primary difference between the two being that anything in a `struct` is `public` by default.

Ok, interesting. But what does `public` mean, and why should I care? `public` means that anything in a `struct` can be accessed and modified by another function. Sometimes we don't want things in our structs (or classes) to be accessed and modified because when I go to use that thing somewhere else, it might not do or return what I am expecting it to as it is defined. This is where `private` comes in, but this is a post for another time. Back to `struct`.

In my header file `structDemo.h` I will create the following:

```C++
#include <iostream>

struct BikeRoute {
    int distance, watts;
    std::string routeName;
};
```

Here we defined a `struct` named `BikeRoute` that has 3 members: `int distance` `int watts` and `string routeName`. One really important point is the _trailing semi-colon_. In C++, `struct`'s must have a trailing semi-colon. I _always_ forget this.

In `main.cpp` I'll add the following code:

```C++
#include "structDemo.h"

int main() {
    BikeRoute tuesdayRide = { 24, 450, "Daily Commute" };
    std::cout << tuesdayRide.distance << std::endl;

    return 0;
}
```

Now I am `instantiating` the `struct` named `BikeRoute` and setting the values for distance, watts, and routeName that represent my `tuesdayCommute` in this case. Compiling this program and running the output file would print `24` to standard out. I could have also definied each member like this (shortened):

```C++
...
BikeRoute tuesdayRide;
tuesdayRide.distance = 24;
tuesdayRide.watts = 450;
tuesdayRide.routeName = "Daily Commute;
...
```

At this point I have a file called `structDemo.cpp` that is empty. In my `main` function I am sending the distance I rode to standard out. I think it would be nice to have a function that printed the details of my ride in a nice format. Let's add a _member function_ to the BikeRoute `struct`. In `structDemo.h` I'll add a reference to the yet to be created function `prettyPrint();`, which will display a nicely formatted output to standard out. Adding the new function to the struct tells the compiler that `prettyPrint()` is a _member_ function, and it is going to reference items in the BikeRoute `struct`. My header file now looks as follows:

```C++
#include <iostream>

struct BikeRoute {
	int distance, watts;
	std::string routeName;

	// member function
	void Time::prettyPrint();
};
```

And in `structDemo.cpp` I'll create the `prettyPrint()` function. Note the `#include "structDemo.h` at the top of the file.

```C++
#include <iostream>
#include "structDemo.h"

void BikeRoute::prettyPrint() {
	std::cout << "Route Name: " << routeName << std::endl;
	std::cout << "Distance: " << distance << std::endl;
	std::cout << "Watts: " << watts << std::endl;
}
```

Now what's pretty cool here is that I just used `routeName`, `distance` and `watts` directly. I didn't need to instantiate BikeRoute to access them because `prettyPrint()` is a member function and the compiler knows where to look to get the values.

In `main.cpp` I have the following:

```C++
#include "structDemo.h"

int main() {
	BikeRoute tuesdayRide = { 24, 450, "Daily Commute" };
	tuesdayRide.prettyPrint();

	return 0;
}
```
Here I instantiate an instance of BikeRoute, and `call` the member function `prettyPrint`. To compile this program I'll run `g++ main.cpp structDemo.cpp` from the command line, then run the executable which outputs:

```
Route Name: Daily Commute
Distance: 24
Watts: 450
```

This was a really simple example just to get my feet wet with C++ structs, which is a data structure whos equivalent I haven't really come across in Python. I think I would just use a `class` in Python, and I think eventually in C++ you just end up using a `class` too but I am sure there are good use cases for structs.

