# Coding Problem: Two Sum

> [DailyCodingProblem](https://www.dailycodingproblem.com/) is a great website that sends coding problems to your inbox daily.

# The Question

> Given a list of numbers and a number `k`, return whether any two numbers from the list add up to `k`.

# Examples

1. `[1, 2, 3], k = 4`

Yes, since 1+3 = 4

2. `[10, 15, 3, 7], k=17`

Yes, since 10+7 = 17

3. `[5, 4, 7, 12, 1], k = 2`

No

# Solutions

## Brute force

A simple solution would be to iterate over all possible pairs in the array and checking if a pair adds up to k.

```
for i from 0 to n-1:
  for j from i+1 to n-1:
    if a[i] + a[j] == k:
      return true
return false
```

This will involve `n(n+1)/2` steps, so the time complexity will be `O(n^2)`, and `O(1)` space complexity.

## Sorting and two-pointer approach

We can sort the array and use two pointers, `front` (starting from 0) and `end` (starting from n-1):

1. If the elements pointed to currently sum up to `k`, then return true
2. If the sum is less than `k`, increment the `front` pointer, since we need to increase the sum and the array is sorted.
3. Symmetrically, if the sum is more than `k`, decrement the `end` pointer.
   We repeat this until `front` and `end` cross each other.

```
sort(a)
front = 0, end = n-1
while front < end:
  if a[front] + a[end] == k:
    return true
  else if a[front] + a[end] < k:
    front = front + 1
  else
    end = end - 1
return false
```

Since we are sorting an array (`O(nlogn)`) and looping with the `front` and `end` pointers (`O(n)`), the overall time complexity of this solution is (`O(nlogn)`). The space complexity is (`O(1)`).

You can find the implementation of this solution [here](https://github.com/akshatshah21/Data-Structures-and-Algorithms/blob/master/C%2B%2B/Arrays/Check_2_sum.cpp)

If we are required to return the indices, the sorting approach cannot be used directly. We will have to make an array of pairs of `(val, index)` and then sort this array.

## Using a set or map

We can iterate through the array and keep adding the elements to a set (or hashset, map or hashmap), and for every element, check if the set contains `(k-a[i])`. If we find this condition to be true, then there exists a pair that adds up to `k`: the current `a[i]` and the entry in the set, `k-a[i]`.

```
set m
for i from 0 to n-1:
  if m.has(k-a[i]):
    return true
  m.add(a[i])
return false
```

We are making a single pass through the array, finding whether a number exists in the set, and adding an element to the set. If we are using a BST implementation of set (C++ `set` or `map`) then the time complexity will be `O(nlogn)`, since every insert/find operation takes `O(logn)` time. If we use a hashset or hashmap (C++ `unordered_set` or `unordered_map`) then the time complexity will be `O(n)`, since insert/find operation can be done in constant time.

You can find the implementation of this solution [here](https://github.com/akshatshah21/Data-Structures-and-Algorithms/blob/master/C%2B%2B/Hashing%20or%20Maps/Check_Numbers_Add_Upto_k_in_Array.cpp)

If we are required to return indices of these elements, then need to use a map or hashmap, with the key-value pairs as `(value, index)`.

