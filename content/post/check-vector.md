+++
date = "2017-06-09T06:35:20-04:00"
title = "What does this Vector Contain?"
author = "eric"
tags = ["programming","c++"]
+++

I was doing some work with C++ vectors today and I ended up needing to check if an element was in a vector. This seemed simple enough at first, but it turned out to be slightly more complicated than I expected.

Here is how I'd check if an item is in a list in Python:
```python
my_list = ['test', 'items', 'go', 'here']
if 'test' in my_list:
    # it's here. do some stuff...
else:
    # dang, not here. do some other stuff...
```

Or if we wanted to check if an element is in an array in JavaScript:
```JavaScript
myArr = ["test", "items", "go", "here"]
if (myArr.indexOf("test") > -1) {
    // it's here. do some stuff...
} else {
    // dang, not here. do some other stuff...
}
```

Easy enough, right? No way it's going to be that easy in C++. Here's the equivalent to the above in element checks in C++:

```C++
#include <vector>
#include <algorithm>

int main() {
    std::vector<std::string> myVec {"test", "items", "go", "here"};
    if (std::find(myVec.begin(), myVec.end(), "test") != myVec.end()) {
        std::cout << "it's here!" << std::endl;
    } else {
        std::cout << "dang, it's not here!" << std::endl;
    }
}
```
Actually, that's not _too_ bad. Everything is very verbose in C++, so I was expecting worse. Basically what the above C++ code does is use the `algorithm` library's `find()` method which creates an iterator to search through the vector. It them compares the results with `end()`, the last element in the vector.

Hey, you could wrap this up in a handy function!

```C++
#include <vector>
#include <algorithm>

bool vectorContains(std::vector<std::string> &myVec, std::string myElem) {
    if (std::find(myVec.begin(), myVec.end(), myElem) != myVec.end()) {
        return true;
    } else {
        return false;
    }
}

int main() {
    std::vector<std::string> myVec {"hello", "vector"};
    bool contains = vectorContains(myVec, "hello");
    std::string result;
    if (contains) {
        result = "it's here!";
    } else {
        result = "dang, it's not here!";
    }
    std::cout << result << std::endl;
}
```

Pretty nice!
