+++
date = "2017-05-15T06:43:48-04:00"
title = "A Quick Intro to Classes in C++"
author = ""
tags = ["C++","programming"]
+++

Classes are a powerful data structure in C++ (and many other programming languages). But classes in C++ are a bit different from classes in Python, or even JavaScript (gasp) if that's your thing. C++ allows you to set `private` members in your class that can only be accessed by member functions in the class. This is really handy for a few reasons, one of them being if you need to change the value or behavior of a private member you can do so without breaking every member function that uses that private member. The concept of changing the value of private members is called `getting and setting`. Getting and setting exists in Python, but it is not as common of a pattern as in C++.

Let's go through a really simple class example to get the idea of how this data structure works in C++. I am going to create three files- `main.cpp`, `pracClass.cpp`, and `pracClass.h`. In this simple example I am going to create a class to represent a bank, and it will contain some methods to handle transactions. In `pracClass.h` I'll go ahead and create the class.

```C++
#include <iostream>

class Bank {
    private:
        int balance;
    public:
        // todo - add public members
};
```

Here we have created our class with a private member `balance` whos value can only be set by members of the Bank class. In `pracClass.cpp` we can go ahead and create a member function to set the value for `balance` as needed.

```C++
#include <iostream>
#include "pracClass.h"

void Bank::setBalance(int amt) {
    balance = amt;
}
```

And we'll add it to our Bank class as a member function.

```C++
#include <iostream>

class Bank {
    private:
        int balance;
    public:
        void setBalance(int amt);
};
```

It would be nice if we could see what the balance is at any given time, so I'll go ahead and add another member function that prints the balance to standard out. In `pracClass.cpp` I'll add the function `printBalance`.

```C++
#include <iostream>
#include "pracClass.h"

void Bank::setBalance(int amt) {
    balance = amt;
}

void Bank::printBalance() {
    std::cout << "Balance: $" << balance << std::endl;
}
```

And add the new member function to the Bank class

```C++
#include <iostream>

class Bank {
    private:
        int balance;
    public:
        void setBalance(int amt);
        void printBalance();
};
```

Next we can add a member function for making a withdrawl and making a deposit, which we'll call `makeWithdrawl` and `makeDeposit` respectively and add them to `printClass.cpp`.

```C++
#include <iostream>
#include "pracClass.h"

void Bank::setBalance(int amt) {
    balance = amt;
}

void Bank::printBalance() {
    std::cout << "Balance: $" << balance << std::endl;
}

void Bank::makeWithdrawl(int amt) {
    balance -= amt;
}

void Bank::makeDeposit(int amt) {
    balance += amt;
}
```

Adding both of these new member functions to the Bank class in our header file

```C++
#include <iostream>

class Bank {
    private:
        int balance;
    public:
        void setBalance(int amt);
        void printBalance();
        void makeWithdrawl(int amt);
        void makeDeposit(int amt);
};
```

Now, this is a really simply example so I am going to the leave the member functions as they are and assume they won't be passed an int value that will result in an error. But, we should really consider some scenarios that _could_ break e.g. withdrawing more money than available. Maybe an exception could be thrown for that case? For now, they are ok as-is to illustrate the basics of classes.

We'll switch over to our `main.cpp` file and start using our Bank class. First we'll create an instance of the Bank class called `myBank`, then we'll set the balance in our account. This is where the `private` member really shines. We can set the balance to be whatever we want at any given time, and someone else using the class can set the balance to a completely different value if they needed to without effecting us. That is pretty awesome! 

An important point here is that _do_ need to actually set the value for the balance though. If we forgot to do this we'd get an integer we weren't expecting, the memory space representation for the variable.

In `main.cpp`

```C++
#include <iostream>
#include "pracClass.h"

int main() {
    Bank myBank;
    myBank.setBalance(0);
    myBank.printBalance();

    myBank.makeDeposit(150);
    myBank.printBalance();

    myBank.makeWithdrawl(100);
    myBank.printBalance();
}
```

From the command line we can compile this program with 

```
g++ prac_class.cpp main.cpp -o bank.exe
```

Running `bank.exe` results in the output

```
Balance: $0
Balance: $150
Balance: $50
```

Classes in C++ can obviously get much more complex than this and I'll probably do a post soon on inheritance in C++, but this quick example shows just how flexible and powerful of a data structure they can be and how private members provide `encapsulation` for your program.

But what about Python?! Well, there are no public or private attributes in Python. You could add an underscore to your variable name, but it does nothing more than signal to the user that you _intend_ for this variable to private, they can still access or modify it however they like. Python does allow `@property` decorators for getters and setters, but this is not a common pattern. Anyways, here's an similar example written in Python.

```python
class Bank(object):

    def __init__(self, balance):
        self.balance = balance

    def make_deposit(self, amt):
        self.balance += amt

    def make_withdrawl(self, amt):
        self.balance -= amt

    def print_balance(self):
        print 'Balance: ${}'.format(self.balance)


my_bank = Bank(0)
my_bank.print_balance()

# oh crap, the attribute value changed
my_bank.balance = 200

my_bank.make_deposit(100)
my_bank.print_balance()

my_bank.make_withdrawl(50)
my_bank.print_balance()
```

Which outputs
```
Balance: $0
Balance: $300
Balance: $250
```