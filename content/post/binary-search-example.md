+++
date = "2017-05-10T06:18:05-04:00"
title = "Binary Search Example"
tags = ["algorithms","c++", "python"]
author = "eric"
+++

I want to talk about `logarithms` briefly to lay some of the ground work for Binary Search. What is a logarithm exactly? At a basic level it's how times we can multiply a number to get another number. For example, how many times can we multiply 2 to get 8? 3 times.
```
2 x 2 x 2 = 8
```
Ok, that makes sense but what does it have to do with Binary Search? Binary Search is essentially searching for an element in an list by cutting the list in half multiple times to narrow in on the target. So, if we look at the logarithm question above another way- if we had an array of length 8, how many times can it be cut in half?

```python
[1, 2, 3, 4, 5, 6, 7, 8]  # starting point
[5, 6, 7, 8]  # cut in half once
[7, 8]  # cut in half twice
[8]  # cut in half a third time
```
If we wanted to know if the number 7 was in the list above, using binary search it would take us _at worst_ 4 or `log(8) + 1` (including first guess) guesses to determine which index the 7 was at or if it was in the list at all. Written another way  in Python using the list above, it would take us at worst `log(len(my_nums)) + 1` guesses.

One of the most important requirements of binary search is that your list _must_ be sorted. The reason for this is because you will always start with a first guess that the middle element of your list is your target, and if it's not you need to know if you should search left (larger) or right (smaller) of the middle next. The next element to the left _must_ be greater than or equal to the target, and the element next to the right _must_ less than or equal to the target.

So, lets talk through the steps a little bit. We have a sorted list with 8 numbers in it, and the number are checking for (our target) is going to be 13.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
```

The first element in a list is always going to be at index 0, so we create a variable to represent the starting point that will be updated as we search through the list.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
```

We also need to know what the index of the last element in our list is. Since lists start with an index of 0, we can assume that the last index is going to be the length of list (8 in this case) minus 1.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
```

Now, we are going to need to keep making guesses until we find the target in the list or we determine that the target is not in the list. A `while` loop is probably a good candidate for making repeated guess until a condition is met.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
while True:
    # todo do some checking
    break
```

Let's set a variable to represent our first guess, the middle element in our list. How do we get the middle element in our list? We can assume that it is going to be the `min_index` plus the `max_index` divided in half, which is index 3 in our example. We'll represent the first guess as a variable called `index_guess`.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
while True:
    index_guess = (min_index + max_index)/2
```

Now let's do an equality check to see if our first guess was correct.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
while True:
    index_guess = (min_index + max_index)/2
    if my_nums[index_guess] == target:
        print 'found the target at index ', index_guess
        break
    break
```
If we were lucky enough to have our first guess be correct, then we'd get a nice output to congratulate us on our success. Unfortunately, we are not going to be so lucky today and we need to search again. Let's check if the target is larger or than our first guess.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
while True:
    index_guess = (min_index + max_index)/2
    if target == my_nums[index_guess]:
        print 'found the target at index ', index_guess
        break
    elif target > search_list[index_guess]:
        # todo decide where to look next
        print 'larger'
        break
    break
```

This is where binary search gets really interesting and efficient! Since our target is larger than the middle index in this case, we are guaranteed that our target will not be to the left of `index_guess` and we can ignore all of those values and focus on the values to the right. This eliminates searching in half of the list! Pretty cool. This also illustrates why a sorted list for binary search is so crucial. You cannot make any guarantees about the next element to check with an unsorted list. 

So at this point our target is larger than our first guess, so we need to check again but we only care about checking values to the right now. We also know that the middle index value is not a match, so let's increment our `min_index` to be one index position larger than our first guess. If you can picture it, we are basically sliding our search window over to the right by one index position.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
while True:
    index_guess = (min_index + max_index)/2
    if target == my_nums[index_guess]:
        print 'found the target at index ', index_guess
        break
    elif target > my_nums[index_guess]:
        print 'larger'
        min_index = index_guess + 1
    break
```

Our target was larger than our first guess, but our target could be smaller than our next guess so we need to handle that situation. If the target is smaller than our guess then we need to check the next index position to the left, or one minus `index_guess`. I'll also remove the additional `break` statement at this point so our `while` loop will keep checking.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
while True:
    index_guess = (min_index + max_index)/2
    if target == my_nums[index_guess]:
        print 'found the target at index ', index_guess
        break
    elif target > my_nums[index_guess]:
        print 'larger'
        min_index = index_guess + 1
    elif target < my_nums[index_guess]:
        print 'smaller'
        max_index = index_guess - 1
```

As this binary search algorithm is defined above, the target will be found at index 5. But what if we are searching for a target that is not in the list? The `while` loop is going to keep executing and we'll be stuck in an infinite loop. While this is happening, our `min_index` value is going to keep increasing and eventually become greater than the value for `max_index`. When our `min_index` is larger than our `max_index` we can be sure that our target is not in the list and we can break out of the loop. Lets add a check for this condition.

```python
target = 13
my_nums = [1, 3, 5, 6, 11, 13, 17, 21]
min_index = 0
max_index = len(my_nums) -1
while True:
    index_guess = (min_index + max_index)/2
    if max_index < min_index:
        print 'not in list'
        break
    elif target == my_nums[index_guess]:
        print 'found the target at index ', index_guess
        break
    elif target > my_nums[index_guess]:
        print 'larger'
        min_index = index_guess + 1
    elif target < my_nums[index_guess]:
        print 'smaller'
        max_index = index_guess - 1
```

What will happen to our performance as the size of the list increases? Our performance will decrease, and it will decrease a specific rate. Right now our 8 element list has a worst case scenario of 4 guesses to find the target. What if we doubled the length of our list?

```python
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16]  # first guess
[9, 10, 11, 12, 13, 14, 15, 16]  # cut in half once
[13, 14, 15, 16]  # cut in half twice
[15, 16]  # cut in half three times
[16]  # cut in half a final fourth time
```

When we double the length of the list we add a guess, and now we have a worst case of 5 guesses to get our target element. This pattern continues as you add more elements to the list. If our first guess is a match then we remain in constant time, but if not our runtime in big-o notation is going to be `O(log(n))`.

Just for fun, here is binary search implemented in C++ and using strings instead of integers in the search vector. I also included a `guesses` variable to keep track of how many guess it took to find the target.

```c++
#include <iostream>
#include <vector>

void binarySearch(std::string target, std::vector<std::string> searchVec) {
    int minIndex = 0;
    int maxIndex = searchVec.size();
    int guesses = 0;
    while (true) {
        guesses += 1;
        int indexGuess = (minIndex + maxIndex)/2;
        if (maxIndex < minIndex) {
            std::cout << "not in list" << std::endl;
            break;
        } else if (searchVec[indexGuess] == target) {
            std::cout << "found it at pos " << indexGuess << std::endl;
            break;
        } else if (target > searchVec[indexGuess]) {
            minIndex = indexGuess + 1;
            std::cout << "bigger" << std::endl;
        } else if (target < searchVec[indexGuess]) {
            maxIndex = indexGuess - 1;
            std::cout << "smaller" << std::endl;
        }
    };
    std::cout << "total guesses: " << guesses << std::endl;
}

int main() {
    std::vector<std::string> primeVec {"abs", "babs", "cabs", "dabs", "eabs", "fabs", "gabs", "habs"};
    binarySearch("fabs", primeVec);
}
```

