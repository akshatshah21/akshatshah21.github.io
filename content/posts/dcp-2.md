---
date: 2022-07-03T10:45:56+05:30
draft: false
title: "Coding Problem: Array of Product Except Self"
tags: ["data-structures", "algorithms"]
authorLink: https://akshatshah21.github.io/
description: "Given an array, return an array where each element is the product of the all elements except the current one."
summary: "Given an array, return an array where each element is the product of the all elements except the current one."
categories: ["coding-problems"]
---

> [DailyCodingProblem](https://www.dailycodingproblem.com/) is a great website that sends coding problems to your inbox daily.

## The Question

> Given an array of integers, return a new array such that each element at index `i` of the new array is the product of all the numbers in the original array except the one at `i`.
> 
> Follow-up: What if the division operation is not allowed?

## Examples
1. `[1, 2, 3, 4, 5]`  
   Result: `[120, 60, 40, 30, 24]`
2. `[3, 2, 1]`  
   Result: `[2, 3, 6]`
3. What's interesting about this question is if there are 0s in the array: `[1, 2, 3, 0]`  
   Result: `[0, 0, 0, 6]`
4. `[1, 2, 0, 3, 4, 0, 5]`  
   Result: `[0, 0, 0, 0, 0, 0, 0]`

## Solutions
### Division of Overall Product
* The most intuitive solution is to first calculate the overall product of the array, `P` and then for each element in the given array, divide `P` by it to get the corresponding number in the result array.
* Dividing `P` by every element can be a problem if one of the elements is 0, since we'll get a runtime error for dividing by 0. However, if there is indeed a 0 in the array, then `P` will also be 0. So we realize that we need to handle zeroes carefully in this solution.
* Trying out some test cases (see *Examples* above), we realize that there are three cases when it comes to 0s in the array:
	1. No zeroes: We can follow our simple solution above
	2. One zero, at index `z`: All positions in the result array except `z` will be 0, while `z` will hold the product of all numbers in the given array without the 0
	3. More than one zeroes: All positions in the result array will hold 0.
	Hence, we need to keep track of the number of zeroes and construct the result accordingly. We can simply keep a running count of zeroes and `firstZeroIndex` for this, and calculate `P` accordingly.
```
P = 1
zcount = 0
firstZeroIndex = -1
for i=0 to n-1:
	if a[i] == 0:
		if zcount == 0:
			firstZeroIndex = i
		zcount++
	else:
		P = P * a[i]

	if zcount > 1:
		break

if zcount > 1:
	result = [0, 0, ..., 0] of size n
else if zcount == 1:
	result = [0, 0, ..., 0] of size n
	result[firstZeroIndex] = P
else:
	result = []
	for i=0 to n-1:
		result[i] = P / a[i]

return result

return result
```

The time complexity of this solution is `O(n)` with two passes required, and auxilliary space complexity is `O(1)`. 

{{<admonition type=warning title="Take care of overflow!" open=false >}}
  Care must be taken that the variable `P` does not overflow. Use a larger integer type, or use modular arithmetic if allowable.
{{</admonition>}}


### Product Prefix and Suffix
To address the follow-up of disallowing use of the division operation, we can calculate prefix and suffix product arrays and use the two to construct the final result array. These arrays hold the product of the prefix / suffix of the array without including the current element.
* Prefix array: `pref[i] = a[0] * ... a[i-1]`,  
  `pref[0] = 1`
* Suffix array: `suff[i] = a[i+1] * ... a[n-1]`,  
  `suff[n-1] = 1`
To then construct the final result array, we simply multiply these two arrays elementwise. That is, `result[i] = pref[i] * suff[i]`. This works because `pref[i]` and `suff[i]` together hold the product of every element in the prefix and the suffix respectively, accounting for the whole array except `a[i]`, which is the required result.

A nice thing about this solution is that we do not need to handle 0s separately. However it does take three passes. We can skip two passes in case of zeroes, but even without that, the solution is correct.
```
pref = []
pref[0] = 1
for i=1 to n-1:
	pref[i] = pref[i-1] * a[i-1]

suff = []
suff[n-1] = 1
for i=n-2 to 0:
	suff[i] = suff[i+1] * a[i+1]

result = []
for i=0 to n-1:
	result[i] = pref[i] * suff[i]

return result
```
The time complexity of this solution is `O(n)` and it takes three passes, while the auxilliary space complexity is `O(n)` for the prefix and suffix product arrays.