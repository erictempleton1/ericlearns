+++
date = "2017-05-15T06:50:06-04:00"
title = "Understanding Selection Sort"
author = "eric"
tags = ["algorithms"]

+++
Alright selection sort algorithm, let's dance. 

This algorithm is one type of many sorting algorithms used to sort values in an array. While I _understand_ how it works it might be a little hard to put into words, but I'll give it a go either way. The main idea is that you start at the first index in an array (usually always 0), then compare it to the element in the next index position. If the element in the next index position is larger than the previous than you check that element against the rest of the elements in the array to see if a smaller element exists. If no smaller element exists then you _swap_ the positions and repeat the process for the next element until the list is sorted. Phew.

Here is a quick visual tour. We'll start with the following array (or list if you're into Python)

```python
[18, 19, 66, 44, 9, 22, 14]
```

So we'll start with index 0, which is 18. 

Is 18 larger than the next element after 18, which is 19? Nope.

Is 18 larger than the next element after 19, which is 66? Nope.

Is 18 larger than the next element after 66, which is 44? Nope.

Is 18 larger than the next element after 44, which is 9? Yep!

Ok, this is where things get interesting. Since 18 is larger than 9, we now we need check if any other elements in the list are larger than 9. But we already know that the elements so far _must_ greater than 18 or we wouldn't have gotten so far. With that in mind, we only need to check the elements to the right of 9 to ensure that 9 is the smallest. Let's do that.

Is 9 larger than the next element after 9, which is 22? Nope.

Is 9 larger than the next element after 22, which is 14? Nope.

We've reached the end of our array. At this point we now know that 9 is the smallest number, so we need to _swap_ the first element, 18, with 9. Once we've made the swap, we can move to the next element and repeat the steps again. Our array now looks as follows

```python
[9, 19, 66, 44, 18, 22, 14]
```

We should go through the steps again one more time with the next element just to be sure this is all making sense. 

We are now at index 1, which is 19 now since we performed a _swap_.

Is 19 larger than the next element after 19, which is  66?  Nope.

Is 19 larger than the next element after 66, which is  44? Nope.

Is 19 larger than the next element after 44, which is  18? Yep!

Ding ding ding! Now we check everything after 18 to see if 18 is the smallest.

Is 18 larger than the next element after 18, which is  22? Nope.

Is 18 larger than the next element after 22, which is  14? Yep!

Now check to see if 14 is the smallest....but wait! 14 is the last element, so it has to be the next smallest after 9. We'll make another swap and restart the process again with index 2, 66. Our array now looks as follows

```python
[9, 14, 66, 44, 18, 22, 19]
```

That's pretty cool if you ask me. With that terrible explanation out of the way, let's take a look at how this is actually implemented and hopefully that will help clarify. Let me add a big _NOTE_ here that you should run this code in a debugger with a breakpoint set so you can step through each line and see what's actually happening. I found this to be the most helpful way of actually understanding this algorithm. Text explanations really don't do a great job or clarifying what is actually happening, even though this explanation is clearly top notch.

I am going to show this first in Python. In selection sort we are mostly going to be working with list indexes, so we'll use a `for loop` with `range` based on the length of the unsorted list

```python
def sel_sort(unsorted_list):
    for i in range(len(unsorted_list)):
        ...
```

We are going to need to increment the index `i` when we find larger elements in the list. Probably not a good idea to change the value of `i` directly in this case, so we'll set a variable to represent the minimum index value.

```python
def sel_sort(unsorted_list):
    for i in range(len(unsorted_list)):
        min_index = i
        ...
```

All of the elements to the right of `min_index` we can consider to be in a `sub list` or `sub array` that we'll need to iterate over and check. We'll create another `for loop` inside of our first to check these elements and update `min_index` as needed. _But_, we only want to check elements one index position to the right of `min_index` so we need to increment `i` in our next loop to start at the next position. This is what creates our `sub list`. Note the `range(i+1, len(unsorted_list))`.

```python
def sel_sort(unsorted_list):
    for i in range(len(unsorted_list)):
        min_index = i
        for j in range(i+1, len(unsorted_list)):
            current_elem = nums[min_index]
            next_elem = nums[j]
```

Now we can perform the greater than check and decide if we need to move to the next element or not.

```python
def sel_sort(unsorted_list):
    for i in range(len(unsorted_list)):
        min_index = i
        for j in range(i+1, len(unsorted_list)):
            current_elem = unsorted_list[min_index]
            next_elem = unsorted_list[j]
            if current_elem > next_elem:
                min_index = 1
```
The reason I created the variables `current_elem` and  `next_elem` is for debugging purposes. Everything makes more sense when stepping through the code if you set a variable to represent the values that you can see in the debugger.

There is one important portion of this algorithm we should take a closer look at now, and that is the _swap_. In Python, swapping elements in a list is as simple as 

```python
# swap first and second elements in a list
my_list[0], my_list[1] = my_list[1], my_list[0]
```

But in other languages, specifically C++, we need an extra variable to hold the value of the first item. I'll represent this in Python here because later you'll see it in the C++ selection sort example. So here is the full selection sort algorithm implemented in Python.

```python
def sel_sort(unsorted_list):
    for i in range(len(unsorted_list)):
        min_index = i
        for j in range(i+i, len(unsorted_list)):
            current_elem = unsorted_list[min_index]
            next_elem = unsorted_list[j]
            if current_elem > next_elem:
                min_index = 1
        tmp = unsorted_list[i]
        unsorted_list[i] = unsorted_list[min_index]
        unsorted_list[min_index] = tmp
    return unsorted_list

nums = [18, 19, 66, 44, 9, 22, 14]
print sel_sort(nums)

# prints [9, 14, 18, 19, 22, 44, 66]
```

And there you have it. Below is an example of selection sort in C++. This example creates a copy of the vector, mostly because I don't know how to do it a better way yet. Soon.

```C++
#include <iostream>
#include <vector>

std::vector<int> selectSort(std::vector<int> myVec) {
    for (int i = 0; i < myVec.size(); i++) {
        int minIndex = i;
        for (int j = i + 1; j < myVec.size(); j++) {
            if (myVec[minIndex] > myVec[j]) {
                minIndex = j;
            }
        }
        int tmp = myVec[i];
        myVec[i] = myVec[minIndex];
        myVec[minIndex] = tmp;
    }
    return myVec;
}

int main() {
    std::vector<int> vec {6, 55, 22, 4, 1, 88};
    std::vector<int> myVecSorted = selectSort(vec);
    for (int i = 0; i < myVecSorted.size(); i++) {
        std::cout << myVecSorted[i] << std::endl;
    };
}
```

So what's the efficieny of the selection sort algorithm? As the length of your array grows, so does the time complexity. For this algorithm, the time complexity in big O notation is `0(n^2)`. Did I just google that and paste it in here without fully understanding it? I sure did. Another post soon on time and space complexity.
