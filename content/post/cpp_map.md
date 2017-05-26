+++
tags = ["programming","c++"]
date = "2017-05-25T07:34:35-04:00"
title = "Taking a Look at Map in C++"
author = "eric"
+++

I recently stumbled across `map` in C++, and I thought I'd do a little write-up about this powerful little container. Maps basically store elements as _key value_ and _mapped value_ in a certain order. There is also an `unordered_map` which is faster than `map` if the key order is not important to you. So how do you declare a map? Let's take a look at the format with an example:

```C++
std::map <std::string, int> myMap;
```

Here we have declared a map called `myMap` that takes a string as the key and an int as the value. I think this format is fairly common, espcecially if you're implementing any type of counter. Given the nature of maps, you can't have duplicate keys which is why they are handy for counting (among other things). Let's tinker around with `maps` a little bit. First we'll build a `main` function containing our example map shown above.

```C++
#include <iostream>
#include <map>
#include <vector>
#include <string>

int main() {
    std::map <std::string, int> myMap;
}
```

That's nice, but we should add some keys and values to our map. We can do this by defining the key name (a string in our case) and the associated value.

```C++
#include <iostream>
#include <map>
#include <vector>
#include <string>

int main() {
    std::map <std::string, int> myMap;
    myMap["first value"] = 100;
}
```

In most cases you will usually want to use this syntax for adding keys and values, but you can also use `std::insert()`. One thing to be aware of with insert is that if a key you are trying to insert already exists in the map, the value of that key _will not_ be replaced. Seems like a huge headache if you weren't expecting this behaviour or hunting down a bug, so choose wisely.

So what about accessing a key value in our map? Well, you would just reference the key in the map you are looking for with `[]` notation.

```C++
#include <iostream>
#include <map>
#include <vector>
#include <string>

int main() {
    std::map <std::string, int> myMap;
    myMap["first value"] = 100;

    std::cout << myMap["first value"] << std::endl;
}
```

Compiling this program and running the executable should display `100` in the command line. What if we went looking for a key that was _not_ in our map?

```C++
#include <iostream>
#include <map>
#include <vector>
#include <string>

int main() {
    std::map <std::string, int> myMap;
    myMap["first value"] = 100;

    std::cout << myMap["first value"] << std::endl;
    std::cout << myMap["missing value"] << std::endl;
}
```

You might expect an error to be thrown or the program to not compile, but you would be wrong. In Python you get a `KeyError` thrown for trying to access a dictionary with a key that doesn't exist, but in a C++ map you would just get a 0 returned. If you're to access the keys directly, then checking for the key first is probably a good habit to get into. We can do this using `std::find()` and `std::end()` in an `if` statement.


```C++
#include <iostream>
#include <map>
#include <vector>
#include <string>

int main() {
    std::map <std::string, int> myMap;
    myMap["first value"] = 100;

    if (myMap.find("first value") == myMap.end()) {
        std::cout << "first value key not found!" << std::endl;
    } else {
        std::cout << myMap["first value"] << std::endl;
    }
}
```

Nice, now we have a better idea of what is actually in our `map`. It's also possible to iterate over a `map` in C++ to display the keys or values. There are a few different ways to handle this depending on your version of C++, but for my purposes (using an older version) I'll use an `iterator`. First I'll declare the iterator, then use `begin()` and `end()` to loop through and access the values.

```C++
#include <iostream>
#include <map>
#include <vector>
#include <string>

int main() {
	std::map <std::string, int> myMap;
	myMap["first value"] = 100;

	if (myMap.find("first value") == myMap.end()) {
		std::cout << "first value key not found!" << std::endl;
	} else {
		std::cout << myMap["first value"] << std::endl;
	}

	// add a few more key values
	myMap["second value"] = 200;
	myMap["third value"] = 300;


	// iterator example
	std::map <std::string, int>::iterator it;
	for (it = myMap.begin(); it != myMap.end(); it++) {
		std::cout << it->first << " : " << it->second << std::endl;
	}
}
```

Above I added a couple more key values than printed the contents of the map to standard out. The `->` notation is used to access a member through a pointer. I haven't _really_ come across this notation yet, so I am not entirely clear on the reasoning for this. More to come on that topic.

Lastly, if I wanted to _change_ the value of key I'd simply need to re-assign it.

```C++
myMap["first value"] = 500;
```

Maps are really useful I think. Very similar to Python's `dictionary`, but I think C++ maps are a little less obvious. Here is similar code in Python:

```Python
# create dict
my_dict = {"first value": 100, "second value": 200, "third value": 300}

# print contents of my_dict
for k, v in my_dict.iteritems():
    print k, v

# update dict
my_dict["first value"] = 500
```

I plan to use C++ `map` to expand on my [last](http://ericlearns.com/post/build-a-thing/) "Build a Thing Friday" post to implement a counter, and transferring the contents of the map to a vector for sorting. Should be interesting.

