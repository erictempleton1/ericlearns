+++
date = "2017-06-02T06:44:04-04:00"
title = "Build a Thing Friday: Improving the CSV Parser"
author = "eric"
tags = ["batf","c++", "programming"]
+++

Welcome to this week's edition of Build a Thing Friday! [Last Friday](http://ericlearns.com/post/build-a-thing/) I started building a CSV parser with the goal of accessing individual columns in a CSV file. I've actually posted this code (although a little changed) to [GitHub](https://github.com/erictempleton1/parseIt), and the code there will also include the work I do today. This week I'd like to expand on what I did last week by adding functionality that will `aggregate` data in a column returning the count for each item. Once I have the count of each item I'd also like to `sort` the results in ascending order. At this point I am truly tiptoeing into data analytics with C++ territory. Sort of a scary thought, but here we are anyways. So let's get started. 

My usual disclaimer should be mentioned here that I mostly have no idea what I'm doing, so take any code displayed here with a grain of salt. There is almost certainly a better way to do everything I am attempting, I'm learning.

Last Friday I finished with a function called `singleCol` that returns all of the values from the given column in a vector. This function is a member function to the `ParseCsv` class. Basically, it iterates a 2d vector and pulls out the values at the column index specified. 

```C++
std::vector<std::string> ParseCsv::singleCol(std::vector<std::vector<std::string> > &fullFile, int colNum) {
	std::vector<std::string> col;
	for (int i = 0; i < fullFile.size(); i++) {
		col.push_back(fullFile[i][colNum]);
	}
	return col;
}
```

What I'd like to do now with that single column is the count the `occurances` of each value. I would think that implementing a `counter` would be the best approach for this. But which C++ structure would be best? In a [previous post](http://ericlearns.com/post/cpp_map/) I looked at `map`, and I think this would be a really good candidate to use for counting occurances. What I'll need the map to do is take the `string` value of each item in our single column then use an `int` to keep count of the count. Our map declaration will look like this:

```C++ 
std::map<std::string, int> count;
```

We are going to add a member function to `ParseCsv` called `counter` that takes a vector (the column we are working with). Let's start building the function and declare our map.

```C++
std::map<std::string, int> ParseCsv::counter(std::vector<std::string> &col) {
    std::map<std::string, int> count;
}
```

Next we'll want to iterate the vector, check if the value exists already in the map, and increment the count accordingly. If a value does not exist already, we need to set the count to 0 first otherwise we are incrementing the memory location and that is not something we want to do. A simple way to check if a value exists in a map already is to use `std::find()` and compare it to `std::end()`. One we are finished iterating the vector we'll return our new map. Here's what the finished code for this function looks like:

```C++
std::map<std::string, int> ParseCsv::counter(std::vector<std::string> &col) {
	std::map<std::string, int> count;
	for (int i = 0; i < col.size(); i++) {
		if (count.find(col[i]) == count.end()) {
			count[col[i]] = 0;
			count[col[i]] += 1;
		} else {
			count[col[i]] += 1;
		}
	}
	return count;
}
```

Sweet! We now have an aggregated count total of the values in our column. As this map is now it is sorted by the key, alphabetically in our case since our key is a string. It would be awesome if we could sort by the `int` count though, so let's give that a try. Maps by nature can't be sorted other than the default sorting by value, so we need another way. C++ allows for `pairs` in vectors and we could use the second value in the pair to sort the vector. 

We'll use `std::sort` provided by the algorithm library. In order to sort ascending we need to specificy a `comparision` function to tell the compiler how we'd like to sort. The comparison function needs to take two parameters that can't be modified (const) and return a `bool`.

Here is what our comparison member function will look like:

```C++
bool ParseCsv::comparison(const std::pair<std::string, int> &a, const std::pair<std::string, int> &b) {
    return (a.second > b.second);
}
```

Something new to me is that we'll be using this function in another function in our program. When you intended to use a member function somewhere else it needs to be declared as `static`. It took me a few tries to get this right, but we'd add it as a member function like this:

```C++
static bool ParseCsv::comparison(const std::pair<std::string, int> &a, const std::pair<std::string, int> &b);
```

`static` tells the compiler that we intend to use this function within other functions.

Now that we have our `comparison` function for the sort we can start creating our sorting function that we'll call `sortCounts`. We'll start by creating a new vector to hold our pairs, and we'll create an iterator for looping through the map.

```C++
std::vector<std::pair<std::string, int> > ParseCsv::sortCounts(std::map<std::string, int &countMap) {
    std::vector<std::pair<std::string, int> > pairs;
    std::map<std::string, int>::iterator it;
}
```

We can use the iterator `it` to loop through the map, access the keys and values and add them as pairs in our vector using `std::make_pair` and the `->` operator to get the first (key) and second (value) items. After we've finished looping through the map we'll sort our resulting vector of pairs using `std::begin()`, `std::end()`, and our handy new `comparison` function.

Our final code for this function will be as follows:

```C++
std::vector<std::pair<std::string, int> > ParseCsv::sortCounts(std::map<std::string, int> &countMap) {
	std::vector<std::pair<std::string, int> > pairs;
	std::map<std::string, int>::iterator it;
	for (it = countMap.begin(); it != countMap.end(); it++) {
		pairs.push_back(std::make_pair(it->first, it->second));
	}

	std::sort(pairs.begin(), pairs.end(), comparison);
	return pairs;
}
```

Nice! So if we wanted to print the sorted results to the console we would just need to iterate our sorted vector of pairs like this:

```C++
for (int i = 0; i < sortedVecPair.size(); i++) {
		std::cout << sortedVecPair[i].first << " " << sortedVecPair[i].second << std::endl;
}
```

You could wrap this for loop up in another member function if you'd like, because it's probably pretty handy to have around.

_But wait_, couldn't we have just skipped the map and gone directly to a vector of pairs?! Probably. But I didn't realize it until afterwards. You would only need the vector of pairs if you were sorting by the key or wanted didn't want the default map sorting. I'll likely re-visit this to see if any efficiency can be gained, but for now it was a good learning experience to see how you can move from a map to a vector of pairs and sort in C++.

Anyways, as I mentioned above the full code is on [GitHub](https://github.com/erictempleton1/parseIt).

