+++
author = "eric"
date = "2017-05-19T19:18:11-04:00"
title = "Build a Thing Friday: CSV Parser"
tags = ["bat", "programming", "c++"]
+++

One of the keys to learning a new programming language is to _build_ things in the language you are trying to learn. Even if the thing is really small project or just a few lines of code, just keep writing code in the new language. With that in mind, I'm going to start what I'd like to call "Build a Thing Friday" where every Friday I try to build something small or large (probably small) in the language I am focusing on (C++ at this moment).

One little project that I have been tinkering with in C++ is a CSV file parser. My day job sometimes requires me to work directly with CSV files, but almost always in Python. Wouldn't it be fun to write a CSV parser in C++ though? Turns out it's not the most fun I've had to do, but it was interesting. Admittedly, parsing a file with C++ felt a little like hammering a nail with a sledgehammer but maybe this would be useful if you were dealing with _massive_ CSV files. The complete code is at the bottom if you don't want to read through everything.

Anyways, let's get started. I'll create three files - `main.cpp`, `parseIt.h`, and `parseIt.cpp`. Starting with the header file I'll add a `class` called `ParseCsv` with a private member called `filePath` and a public member function to set the file path for the file we want to work with.

**parseIt.h**
```C++
#include <iostream>
#include <string>

class ParseCsv {
    private:
        std::string filePath;
    public:
        void setFilePath(std::string path);
};
```

Let's jump over to `parseIt.cpp` and create the member function we added in the header file. `setFilePath` will be a function that just takes a string argument and sets the private variable `filePath`.

**parseIt.cpp**
```C++
#include <iostream>
#include <string>


void ParseCsv::setFilePath(std::string path) {
    filePath = path;
}
```

Now we can move over to `main.cpp` to add the header file and instantiate the class. We will also want to set the `filePath` for the file we want to work with here. This can be any file, but I am using one called `data2.csv` in my project root directory. It's really important to set the file path here so our parser knows which file to grab. You'll see pages of errors later if you forget to set `filePath`.

**main.cpp**
```C++
#include "parseIt.h"


int main() {
    // instantiate and set file path
    ParseCsv parseCsv;
    parseCsv.setFilePath("data2.csv");
}
```

Ok, so now we get to the good stuff. Let's work on the parser function itself. There are probably lots of different ways to do this, and mine is likely less than optimal but for now it works pretty good. The goal is to create a `2d vector` where the inner vector represents a row of data in the CSV file. From there you can access each row, cell, or column by iterating the 2d vector. My personal goal was to get to the columns so I could perform analysis on the data within the columns e.g. counts, sum, averages, ect. In our header file we can add a member function for the parser, which takes no arguments but returns a 2d vector containing our CSV data.

**parseIt.h**
```C++
#include <iostream>
#include <string>
#include <vector>

class ParseCsv {
    private:
        std::string filePath;
    public:
        void setFilePath(std::string path);
        std::vector<std::vector<std::string> > parser();
};
```

Our parser function is going to utilize the standard library string stream and file stream modules. We'll use `ifstream` to read the file. On my machine I run an older version of C++ so my `filePath` string needs to be converted to `char` using `c_str()`, but this shouldn't be an issue on newer versions of C++.

Here's what we have so far for our parser function that returns a 2d vector:
```C++
std::vector<std::vector<std::string> > ParseCsv::parser() {
    std::ifstream file(filePath.c_str());
}
```

So far we just read the file, but we'll need a variable to represent each line of the file which will strings. We'll also want to instantiate the 2d vector to hold our data that we'll call `fullData`.

```C++
std::vector<std::vector<std::string> > ParseCsv::parser() {
    std::ifstream file(filePath.c_str());
    std::string line;
    std::vector<std::vector<std::string> > fullData;
}
```

Next we need to get each line (or row) in the file. A `while` loop is a good candidate for this, and C++'s `getline` will allow us to iterate through the file until we reach the last line and gracefully exit.

```C++
std::vector<std::vector<std::string> > ParseCsv::parser() {
    std::ifstream file(filePath.c_str());
    std::string line;
    std::vector<std::vector<std::string> > fullData;
    while (std::getline(file, line) {
        // todo ...
    }
    return fullData;
}
```
What this gets us is a string representation of each line in the CSV file. But what we're really after is the "cells" or comma seperate values, which we can later access as columns. Inside the while loop we can operate on each line (row) of data by entering another while loop.

 Since we are going to be operating on strings, we can use `isstringstream` to represent each line. We'll also instantiate a string variable called `field` that we'll set in a moment. Most importantly, we can now create a new vector that will contain all of the data from the row in the second while loop. We'll be using `getline` again, but this time we are telling getline that we want the values between the commas by passing the optional delimiter argument `','` (comma). As we loop we'll push our results to the inner vector.

 ```C++
std::vector<std::vector<std::string> > ParseCsv::parser() {
    std::ifstream file(filePath.c_str());
    std::string line;
    std::vector<std::vector<std::string> > fullData;
    while (std::getline(file, line) {
        std::istringstream s(line);
        std::string field;
        std::vector<std::string> fullLine;
        while (std::getLine(s, field, ',')) {
            fullLine.push_back(field);
        }
    }
    return fullData;
}
```

Awesome! So far we iterate through the file to get each line, then enter another while loop to get each cell on the line and push the cells on the line to their own vector. Now we just need to build the 2d array by pushing each line vector to the main `fullData` 2d vector we instantiated earlier. We'll need to push the new `fullLine` vector after each iteration of the outer while loop.

 ```C++
std::vector<std::vector<std::string> > ParseCsv::parser() {
    std::ifstream file(filePath.c_str());
    std::string line;
    std::vector<std::vector<std::string> > fullData;
    while (std::getline(file, line) {
        std::istringstream s(line);
        std::string field;
        std::vector<std::string> fullLine;
        while (std::getLine(s, field, ',')) {
            fullLine.push_back(field);
        }
        fullData.push_back(fullLine);
    }
    return fullData;
}
```

Now we have the contents of the entire file parsed line by line and cell by cell into a 2d vector. I mentioned it above, but my goal was to get to a single column of data at a time, so let's add a function for that. Basically, we just need to know which column index to get and then we can iterate the 2d vector, access the data, and store it in it's own vector. We'll add this new member function to our header file.

This function will be called `singleCol` and it will return a vector containing string values, as well at take a 2d vector and column number integer as arguments. Here is how our header file looks now:

**parseIt.h**
```C++
#include <iostream>
#include <string>
#include <vector>

class ParseCsv {
    private:
        std::string filePath;
    public:
        void setFilePath(std::string path);
        std::vector<std::vector<std::string> > parser();
        std::vector<std::string> singleCol(std::vector<std::vector<std::string> > &fullFile, int colNum);
};
```
We'll iterate the 2d vector and access each "line" of the file, but only access elements in the inner vectors at the position specified by the `colNum` argument. This will give us only the data from the given column at a time. We'll go ahead and push this data to a new vector and return it. The left most column in your file is going to have an index (colNum) of zero and increase by one from there.

```C++
std::vector<std::string> ParseCsv::singleCol(std::vector<std::vector<std::string> > &fullFile, int colNum) {
	std::vector<std::string> col;
	for (int i = 0; i < fullFile.size(); i++) {
		col.push_back(fullFile[i][colNum]);
	}
	return col;
}
```
Now we can do whatever we want with the column data! But, remember there could be a header cell in the data, so be sure to account for that too. So here is the final code for each of our three files. The program can be compiled using a command like:

> g++ main.cpp parseIt.cpp -o parser.exe

**parseIt.h**
```C++
#include <iostream>
#include <string>
#include <vector>

class ParseCsv {
    private:
        std::string filePath;
    public:
        void setFilePath(std::string path);
        std::vector<std::vector<std::string> > parser();
        std::vector<std::string> singleCol(std::vector<std::vector<std::string> > &fullFile, int colNum);
};
```

**parseIt.cpp**
```C++
#include "parseIt.h"
#include <iostream>
#include <sstream>
#include <fstream>
#include <string>
#include <vector>


void ParseCsv::setFilePath(std::string path) {
    filePath = path;
}

std::vector<std::vector<std::string> > ParseCsv::parser() {
    std::ifstream file(filePath.c_str());
    std::string line;
    std::vector<std::vector<std::string> > fullData;
    while (std::getline(file, line) {
        std::istringstream s(line);
        std::string field;
        std::vector<std::string> fullLine;
        while (std::getLine(s, field, ',')) {
            fullLine.push_back(field);
        }
        fullData.push_back(fullLine);
    }
    return fullData;
}

std::vector<std::string> ParseCsv::singleCol(std::vector<std::vector<std::string> > &fullFile, int colNum) {
	std::vector<std::string> col;
	for (int i = 0; i < fullFile.size(); i++) {
		col.push_back(fullFile[i][colNum]);
	}
	return col;
}
```

**main.cpp**
```C++
#include "parseIt.h"


int main() {
    // instantiate and set file path
    ParseCsv parseCsv;
    parseCsv.setFilePath("data2.csv");

    // parse file and select certain column
    std::vector<std::vector<std::string> > parsed = parseCsv.parser();
    std::vector<std::string> firstCol = parseCsv.singleCol(parsed, 0);

    // print a column
    for (int i = 0; i < firstCol.size(); i++) {
        std::cout << firstCol[i] << std::endl;
    }
}
```

Well, that wasn't so bad! I think I'll expand on this little program in the next few weeks by adding some functions for data analyis...oooh exciting! Maybe on the next edition of Build a Thing Friday?! Could come in handy anyways. 