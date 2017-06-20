+++
date = "2017-06-20T06:34:21-04:00"
title = "Build a Thing Friday: SQLite3 Command Line Interface"
author = "eric"
tags = ["programming","c++","sqlite3"]
+++

For this edition of Build a Thing Friday I'll be building a command line interface for SQLite3 in C++. Truth is, this is really just a walkthrough of the [SQLite C/C++ tutorial](https://sqlite.org/quickstart.html) but I'll add a few features of my own to try and make it more interesting and _learn_ a thing or two extra. To get started, you'll want to be sure you have [SQLite3 installed](https://sqlite.org/download.html) on your machine. I went with the _Source Code_ download to ensure I had all the header files and dll's needed.

Let's start out by including the libs at the top of our file, which I'll call `main.cpp`:

```C++
#include <iostream>
#include <string>
#include "sqlite3.h"
```

Since this will be a _command line_ tool, we need to pass command line values to our program. We can work with command line arguments in C++ using `argc` and `*argv[]`. I'll create a `main()` function and instantiate a few vars to get us started. We'll need a `db` instance, error message instance, and db client instance. We will also want to pass the command line arguments to the main function so they can be used within the function.

```C++
#include <iostream>
#include <string>
#include "sqlite3.h"

int main(int argc, char *argv[]) {
    sqlite *db;
    char *errMsg = 0;
    int rc;
}
```

Mostly straightforward stuff, but we are using some pointers (oh my) and a type called `sqlite` (from the sqlite3 lib) to instantiate the db. 

The `argc` parameter in our `main` function is the _argument count_ from the command line. When we compile this program we'll use something like:

```
g++ sql_ex.cpp "SELECT * FROM myawesometable;"
```

The first argument (`argv[0]`) is `sql_ex.cpp`, and the second argument (`argv[1]`) is the SQL statement. Sometimes I do stupid things when I compile my programs, like forgetting to actually include the SQL statement. I should make sure that when I do something stupid the program doesn't implode.

```C++
#include <iostream>
#include <string>
#include "sqlite3.h"

int main(int argc, char *argv[]) {
    sqlite3 *db;
    char *errMsg = 0;
    int rc;

    if (argc < 2) {
        std::cout << "Please enter a SQL statement to execute" << std::endl;
        return 1;
    }
}
```

The reason for I check if  `argc < 2` is because the first argument when we compile will be the filename. We assume the second argument is the SQL statement string. Also, why `return 1;` here? `1` is `false`. We want the program to halt and exit here if there is no SQL statement supplied. 

So now we're finally ready to actually connect to a database. I should mention at this point that I wanted to use this as a small command line utility to track some exercises I've been doing, so you'll notice that theme moving forward. 

We'll use SQLite's `sqlite3_open` [method](https://sqlite.org/c3ref/open.html) to connect to the database file. This method takes a `const char` filename and reference to a db as arguments. We'll also add an connection error check.

```C++
#include <iostream>
#include <string>
#include "sqlite3.h"

int main(int argc, char *argv[]) {
    sqlite3 *db;
    char *errMsg = 0;
    int rc;

    if (argc < 2) {
        std::cout << "Please enter a SQL statement to execute" << std::endl;
        return 1;
    }

    rc = sqlite3_open("workouts.db", &db);
    if (rc) {
        std::cout << "Can't open database: " << sqlite3_errmsg(db) << std::endl;
    } else {
        std::cout << "Opened database successfully\n" << std::endl;
    }
}
```

The next thing to do is excecute the SQL statement supplied from the command line using the `sqlite3_exec` method. This method is a wrapper for multiple other methods in the SQLite3 library. But before we can do that, we need to build a `callback` function to pass as an argument to `sqlite3_exec`. The callback function tells the `sqlite3_exec` method (and the compiler) what we intend to with any results from our SQL statement. Right now, we'll just print the results from our statement (if any) to the console. Let's add a function called `callback` that takes some interesting arguments, including a pointer _to_ a pointer(!).

```C++
#include <iostream>
#include <string>
#include "sqlite3.h"

int callback(void *notUsed, int argc, char **argv, char **colName) {
    for (int i = 0; i < argc; i++) {
        std::cout << colName[i] << ": " << argv[i] << std::endl;
    }
    std::cout << "\n" << std::endl;
    return 0;
}

int main(int argc, char *argv[]) {
    sqlite3 *db;
    char *errMsg = 0;
    int rc;

    if (argc < 2) {
        std::cout << "Please enter a SQL statement to execute" << std::endl;
        return 1;
    }

    rc = sqlite3_open("workouts.db", &db);
    if (rc) {
        std::cout << "Can't open database: " << sqlite3_errmsg(db) << std::endl;
    } else {
        std::cout << "Opened database successfully\n" << std::endl;
    }
}
```

We'll also now add a call to `sqlite3_exec` which includes the `db`, SQL statement from the command line, our `callback` function, a zero value argument for the callback, and a reference to any error messages. Phew. Sounds like a lot more than it really is. Here's how it looks:

```C++
#include <iostream>
#include <string>
#include "sqlite3.h"

int callback(void *notUsed, int argc, char **argv, char **colName) {
    for (int i = 0; i < argc; i++) {
        std::cout << colName[i] << ": " << argv[i] << std::endl;
    }
    std::cout << "\n" << std::endl;
    return 0;
}

int main(int argc, char *argv[]) {
    sqlite3 *db;
    char *errMsg = 0;
    int rc;

    if (argc < 2) {
        std::cout << "Please enter a SQL statement to execute" << std::endl;
        return 1;
    }

    rc = sqlite3_open("workouts.db", &db);
    if (rc) {
        std::cout << "Can't open database: " << sqlite3_errmsg(db) << std::endl;
    } else {
        std::cout << "Opened database successfully\n" std::endl;
    }

    rc = sqlite3_exec(db, argv[1], callback, 0, &errMsg);
    if (rc != SQLITE_OK) {
        std::cout << "Error executing supplied statement: " << errMsg << std::endl;
        return 1;
    }

    std::cout << "Completed statement execution: " << argv[1] << std::endl;
    std::cout << "Closing database connection" << std::endl;
    sqlite3_close(db);
}
```
I added some extra lines to show the status on the command line, but this code is similar to opening the database. Check for an error and proceed. Lastly, close the database when finished.

So let's see what this program can do! We'll go ahead and compile with the following (note- paths may vary): 

```
g++ .\main.cpp  -o workouts.exe -I "C:\Sqlite3\" -L "C:\Sqlite3\" -lsqlite3
```

We can run the program from the command line with:
```
workouts.exe
```
Which returns:
```
Please enter a SQL statement to execute
```
Nice! So we know the argument check is working. Let's create a table!
```
workouts.exe "CREATE TABLE IF NOT EXISTS exercises (id INTEGER PRIMARY KEY, name TEXT, reps INTEGER, sets INTE
GER, weight INTEGER);"
```
Which returns:
```
Opened database successfully

Completed statement execution: CREATE TABLE IF NOT EXISTS exercises (id INTEGER PRIMARY KEY, name TEXT, reps INTEGER, sets INTEGER, weight INTEGER);
Closing database connection
```
Let's add some data (multiple INSERT statements at once) and query it:
```
workout.exe "INSERT INTO exercises (name, reps, sets, weight) VALUES ('Shoulder press', 6, 4, 45); INSERT INTO exercises (name, reps, sets, weight) VALUES ('Bench press', 6, 4, 135);"
```
We'll get the confirmation in printed to the console and we can run another statement to query the newly added data:
```
workout.exe "SELECT * FROM exercises;"
```
Which returns:
```
Opened database successfully

id: 1
name: Shoulder press
reps: 6
sets: 4
weight: 45

id: 2
name: Bench press
reps: 6
sets: 4
weight: 135
```

As you've guessed, you can execute _any_ SQL statement via the command line. This is a pretty handy tool. I think I am going to expand on this over the next couple weeks to _really_ make an exercise tracking command line tool. More to come!

Code (slightly modified) can be found [here](https://github.com/erictempleton1/workouts).

