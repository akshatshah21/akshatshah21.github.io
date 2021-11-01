---
title: "Coding Problem: First Missing Positive Integer"
date: 2021-03-01T18:07:55.126Z
tags: ["data-structures", "algorithms"]
authorLink: https://akshatshah21.github.io/
description: "Given an array of integers, find the first missing positive integer in linear time and constant space."
summary: "Given an array of integers, find the first missing positive integer in linear time and constant space."
categories: ["coding-problems"]
draft: false
---

> [DailyCodingProblem](https://www.dailycodingproblem.com/) is a great website that sends coding problems to your inbox daily.

## The Question

> Given an array of integers, find the first missing positive integer in linear time and constant space. In other words, find the lowest positive integer that does not exist in the array. The array can contain duplicates and negative numbers as well.

You can solve this question on [LeetCode](https://leetcode.com/problems/first-missing-positive).

## Solutions

### Use a "map" to mark presence

A simple way (albeit requiring linear auxilliary space) is to traverse through the given array, and mark all the positive number present in the array as _present_ using a simple boolean array of size equal to the maximum number present in the array. Then we iterate through the present array and report the first missing number. If there is no missing number, then the first missing positive integer is the maximum element + 1. The following pseudocode assumes 1-based indexing.

```
present = boolean array of size max_element(a)
for i in a:
  if i > 0 then
    present[i] = true

for i from 1 to max_element(a)
  if !present[i] then
    return i
return max_element(a) + 1
```

This is a linear time solution, and has space complexity linear in terms of the maximum element present. And that presents a problem: what if the numbers are too large?

### Put elements where they belong

The question mentions that we can modify the array in place! And since we have to find the first missing positive integer, we can simply follow the same principle of "marking presence" but use the given array itself, instead of the auxilliary array. To see why this would work, say the first missing positive integer is `x`. To find `x`, we need all elements from 1 to `x-1` to be placed in their correct positions. For any "large" numbers (that is, numbers greater than the size of the array), we can simply ignore them, because `x`\_ has to be in the range `[1, n+1]`. Think about it.

So when we encounter some element of the array that is positive and less than or equal to `n`, we put it at its correct place, that's the position indexed by the element. So we place 1 at 1, 2 at 2, and so on.

If an element `y` is already at position `y`, then we simply move forward, else we try to move it to position `y`. But this would mean replacing other elements. Note that if the elements getting replaced are negative, zero or more than `n`, then we don't need to worry about them. However, if the element that will be replaced is a _valid_ number, then we can swap the two elements instead of replacing them, and **_not moving forward_** for this case, since we need to process the swapped element too.

For handling duplicates, we change `y` to 0 (or any other _invalid_ number) if there's already a `y` at position `y`, so that it can be potentially replaced by another number that should be at the current position.

The conditions are a little tricky, so make note of them. For finding the first missing positive integer, we simply iterate through the array again and check for the first position that doesn't match the value at that position.

```
for i from 1 to n
  if a[i] <= 0 or a[i] > n or a[i] == i then
    continue
  else
    if a[a[i]] <= 0 or a[a[i]] > n then
      a[a[i]] = a[i]
    else if a[a[i]] == a[i] then
      a[i] = 0
    else
      swap(a[i], a[a[i]])
      i = i - 1
for i from 1 to n
  if a[i] != i then
    return i
return n+1
```

You can find the C++ implementation [here](https://github.com/akshatshah21/Data-Structures-and-Algorithms/blob/master/C%2B%2B/Arrays/First_Missing_Positive_Int.cpp)
