+++
author = "eric"
date = "2017-04-16T07:20:57-04:00"
title = "Function Overloading - A Quick Intro"
tags = ["C++","programming", "overloading"]
+++

C++ function overloading is an interesting concept. It allows you to create many functions with the same name but that take different arguments. The compiler knows which function you are referencing based on the arguments passed to the function. Hey, that's pretty cool! Using my old friend `struct BikeRoute` (remember the trailing `;`) I'll try out some basic function overloading:

```C++
#include <iostream>

struct BikeRoute {
	int distance, watts;
	double time;
	std::string routeName;

        // member functions
	int accountForHills(int gradient, int multiplier);
	int accountForHills(int gradient, int timeMultiplier);
};
```

But there is a problem here. I defined the function `accountForHills` that takes two `int` parameters twice. Even though the parameter is _named_ differently, the compiler only knows which function I am referencing based on the parameter _type_. The name of the parameter itself is meaningless to the compiler for function overloading reference purposes. 

Really though, the second instance of `accountForHills` is not needed because it's exactly the same as the first, but let's say instead that there are some cases where I don't need the `multiplier` parameter and _only_ need the gradient. Now my code looks as follows:

```C++
#include <iostream>

struct BikeRoute {
	int distance, watts;
	double time;
	std::string routeName;

        // member functions
	int accountForHills(int gradient);

        // sometimes I pedal harder than usual, so use multiplier to account for effort
	int accountForHills(int gradient, int multiplier);
};
```

So, let's create the member functions and put everything together.

`ov1.h`:
```C++
#include <iostream>

struct BikeRoute {
	int distance, watts;
	double time;
	std::string routeName;

        // member functions
	int accountForHills(int gradient);
        // sometimes I pedal harder than usual, so use multiplier to account for effort
	int accountForHills(int gradient, int multiplier);
};
```
`ov1.cpp`:
```C++
#include "ov1.h"

int BikeRoute::accountForHills(int gradient) {
	return distance * gradient;
}

int BikeRoute::accountForHills(int gradient, int multiplier) {
	return (distance * gradient) * multiplier;
}
```
`main.cpp`:
```C++
#include "ov1.h"

int main() {
	BikeRoute dailyCommute = { 25, 450, 25.5, "Friday Commute" };
	int hillGradient = dailyCommute.accountForHills(2);
	int hillGradientWithMultiplier = dailyCommute.accountForHills(2, 2);

	std::cout << "Distance factoring in hills: " << hillGradient << std::endl;
	std::cout << "Distance factoring in hills and effort: " << hillGradientWithMultiplier << std::endl;
}
```

We can compile this program from the command line with `g++ main.cpp ov1.cpp -o bikeCalcs.exe`, and running the resulting executable called `bikeCalcs.exe` will show the following in standard out:

```
Distance factoring in hills: 50
Distance factoring in hills and effort: 100
```

Now of course, this is a really simple example with calculations that don't really make sense but the point was to show how C++ function overloading works, as well as building on my previous blog post on `C++ structs`.

Is function overloading possible in Python? Well, no not really. Python is _not_ statically typed, so there is no way to know which method is being referenced based on the arguments. Python does however lend itself to the concept of function overloading by allowing optional arguments:

```python
def account_for_hills(distance, gradient, multiplier=None):
    if multiplier:
        return (distance * gradient) * multiplier
    return distance * gradient
```

Not exactly the same as C++ function overloading, but I think it's still similar and obviously useful. I think just as having many functions with the same name in C++ could get confusing and hard to maintain, having many optional arguments in a Python function will also become confusing and hard to maintain. It's a give and a take in the end, extensibility vs readability as they say.
