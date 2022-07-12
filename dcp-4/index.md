# Coding Problem: First Missing Positive Integer


> [DailyCodingProblem](https://www.dailycodingproblem.com/) is a great website that sends coding problems to your inbox daily.

## The Question

> Given an array of integers, find the first missing positive integer in linear time and constant space. In other words, find the lowest positive integer that does not exist in the array. The array can contain duplicates and negative numbers as well.

You can solve this question on [LeetCode](https://leetcode.com/problems/first-missing-positive).

## Solutions

### Use a set to mark presence
A simple way (albeit requiring linear auxiliary space) is to traverse through the given array, and mark all the positive number present in the array as _present_ using an ordered set. Then we iterate through the ordered set or from 1 to `n`, the size of the array and report the first missing number. If there is no missing number found by the time we finish, then the first missing positive integer is the `n + 1`. The following pseudocode assumes 1-based indexing.
```
set = an ordered set
for i in a {
	if i > 0
		set.insert(i)
}

j = 1
for i in set {
	if i != j
		return j
	j = j + 1
}

return j
```
This is a `O(nlogn)` time solution, and has `O(n)` space complexity.
### Sort the array
The need for finding the _first_ missing positive integer hints that sorting can be useful here. We can simply sort the array and then find the first positive element. We start traversing from 1 until we find a break in the natural numbers sequence, or we reach the end of the array, ultimately finding the required integer.
```
sort(a)

i = 0
while (i < n && nums[i] <= 0) {
	i = i + 1
}

if i == n {
	return 1
}

j = 1
while (i < n && j <= n) {
	if (nums[i] > j) {
		return j
	}
	while (i < n && nums[i] == j) {
		i = i + 1
	}
	j = j + 1
}

return j
```
The time complexity of this solution is `O(nlogn)`, while the space complexity is `O(1)`.

### Put elements where they belong
The question mentions that we can modify the array in place! And since we have to find the first missing positive integer, we can simply follow the same principle of "marking presence" but use the given array itself, instead of the set. To see why this would work, say the first missing positive integer is `x`. To find `x`, we need all elements from 1 to `x-1` to be placed in their correct positions. For any "large" numbers (that is, numbers greater than the size of the array), we can simply ignore them, because `x` _has_ to be in the range `[1, n+1]`. Think about it.

So when we encounter some element of the array that is positive and less than or equal to `n`, we put it at its correct place, that's the position indexed by the element. So we place 1 at 1, 2 at 2, and so on.

If an element `y` is already at position `y`, then we simply move forward, else we try to move it to position `y`. But this would mean replacing other elements. Note that if the elements getting replaced are negative, zero or more than `n`, then we don't need to worry about them. However, if the element that will be replaced is a _valid_ number, then we can swap the two elements instead of replacing them, and ___not moving forward___ for this case, since we need to process the swapped element too.

For handling duplicates, we change `y` to -1 (or any other _invalid_ number) if there's already a `y` at position `y`, so that it can be potentially replaced by another number that should be at the current position.

The conditions are a little tricky, so make note of them. For finding the first missing positive integer, we simply iterate through the array again and check for the first position that doesn't match the value at that position.

```
for i from 1 to n {
	if a[i] <= 0 or a[i] > n
		a[i] = -1
	else if a[i] == i
		continue
	else if a[a[i]] == a[i]
			a[i] = -1
	else {
		swap(a[i], a[a[i]])
		i = i - 1
	}
}

for i from 1 to n {
	if a[i] != i
		return i
}
return n+1
```
This solution has `O(n)` time complexity and `O(1)` extra space (it modifies the input array).  
You can find the C++ implementation [here](https://github.com/akshatshah21/Data-Structures-and-Algorithms/blob/master/C%2B%2B/Arrays/First_Missing_Positive_Int.cpp).

