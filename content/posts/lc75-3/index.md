---
title: "LeetCode 75 - Part 3 - Sliding Window"
date: 2024-05-11T18:13:02.093Z
draft: false
tags: ["data-structures", "algorithms", "coding-problems"]
authorLink: https://akshatshah21.github.io/
description: "LeetCode 75. Two problems a day. Part 3."
summary: "LeetCode 75. Two problems a day. Part 3."
categories: ["coding-problems"]
---
# Intent

**Confession**: I've never been comfortable with DSA, Coding Problems or Competitive Programming.  
And so this is one more attempt to change that.  
LeetCode 75. Two problems a day, starting April 25, 2024.  
To further motivate consistency and completion of this, I've decided I will do write-ups for all of these problems and perhaps publish them on my blog. Let's see.

This is Part 3 - Sliding Window.
# 3.1 Maximum Average Subarray I
[Problem on LeetCode](https://leetcode.com/problems/maximum-average-subarray-i)

## Question
Given an array of integers, find the a contiguous subarray of length `k` such that its average is maximized. Return the max average.

## Solution
Brute-force solution will be to iterate through the array and find the average of each subarray of length `k` starting at each position, keeping track of maximum average. This would have a time complexity of `O(N*k)`, while auxiliary space is constant.

In the above approach, as we move the start index of the subarray under consideration from `i` to `i+1`, we repeat the steps of calculating the sum from `i+1` to `i+k`. This observation leads us to thinking of a sliding window approach (subarray of fixed length is a hint). So we maintain the sum of elements in a k-length sliding window, and use these sums to find averages of the subarray. While moving the window by one position, we remove the leftmost element and add the next element on the right - we update sum accordingly. So this approach gives us an `O(N)` time complexity solution (space is constant).

```cpp
class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {
        double sum = accumulate(nums.begin(), nums.begin() + k, 0.0);
        double maxSum = sum;
        int n = nums.size();
        for (int i=k; i<n; i++) {
            sum += nums[i] - nums[i-k];
            maxSum = max(maxSum, sum);
        }
        return maxSum / k;
    }
};
```

# 3.2 Maximum Number of Vowels in a Substring of Given Length
[Problem on LeetCode](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length)

## Question
Given a string, find a substring of length `k` such that the number of vowels in the substring is maximized. Return this maximum count.

## Solution
**Brute-force**: For each index, consider the substring starting from that index of length `k`, and count the number of vowels in that substring. Keep track of max count of vowels in a substring. This has a time complexity of `O(N*k)`, where `N` is the length of the complete string.

We can observe that when moving from the substring starting at `i` to `i+1`, we're counting vowels from `i+1` to `i+k` again. We can instead maintain the count of vowels in a sliding window of length `k`. We check whether the leftmost element (the one being removed from sliding window) or the element to the right of the window (the one being added) are vowels, and update the count of vowels in the window accordingly, while maintaining a global max count. This gives us an `O(N)` time solution with constant auxiliary space.

```cpp
class Solution {
private:
    static bool isVowel(const char &c) {
        return (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u');
    }
public:
    int maxVowels(string s, int k) {
        int count = count_if(s.begin(), s.begin() + k, isVowel);
        int maxCount = count;
        int n = s.length();
        for (int i=k; i<n; i++) {
            if (isVowel(s[i])) count++;
            if (isVowel(s[i-k])) count--;
            maxCount = max(maxCount, count);
        }
        return maxCount;
    }
};
```

# 3.3 Max Consecutive Ones III
[Problem on LeetCode](https://leetcode.com/problems/max-consecutive-ones-iii/)

## Question
Given a binary array, find the maximum number of consecutive 1s in the array if you can flip at most `k` 0s. 

## Solution
We need to find the longest subarray consisting of all 1s and at most `k` 0s (that will be flipped to 1s). We can maintain a sliding window (of variable length) that tries to expand as much as possible (1s are fine to expand with, and for 0s, we have an upper limit of `k`). 

When it's no longer possible to expand (there's a 0 on the immediate right of the window, but we've already included `k` 0s), we need to shrink from the left, so that we get more room for including the 0 on the right. To be able to include the 0, we need to let go of one 0 from the left, so we shrink in from left until after a 0 is removed. Then we can include the 0 on the right and we can expand further. This is an `O(N)` time solution, since the start and end pointers go through each element at most once each. Auxiliary space complexity is constant.

One thing that's tricky in questions like these is to be able to express the correct intent exhaustively using conditionals and loops. Looking at my historic solutions, I guess my latest version is decent:

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int zeros = 0;
        int start = 0;
        int n = nums.size();
        int maxl = 0;
        for (int end = 0; end < n; end++) {
            if (nums[end] == 0) {
                if (zeros < k) 
                    zeros++;
                else {
					while (nums[start] == 1) start++;  // removing 1s doesn't give room for another 0
                    start++;  // remove the 0
                }
            }
            maxl = max(maxl, end - start + 1);
        }
        return maxl;
    }
};
```

# 3.4 Longest Subarray of 1's After Deleting One Element
[Problem on LeetCode](https://leetcode.com/problems/longest-subarray-of-1s-after-deleting-one-element)

## Question
Given an binary array, find the longest non-empty subarray containing only 1s after deleting one element from the array.

## Solution
This problem can be stated as finding the maximum number of consecutive 1s after deleting exactly one element. Hence it's very similar to 3.3 Max Consecutive Ones III. We can find the max number of consecutive 1s, allowing there to be at most 1 zero in the subarray (since we can delete that 0). Since we must delete one element, the length to consider would be one less than such a candidate subarray. The time complexity of this solution will be `O(N)`, with constant auxiliary space.

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums) {
        int n = nums.size();
        int start = 0;
        int maxl = 0;
        int zeros = 0;
        for (int end = 0; end < n; end++) {
            if (nums[end] == 0) {
                if (zeros < 1) 
                    zeros++;
                else {
                    while (nums[start] == 1) start++;
                    start++;
                }
            }
            maxl = max(maxl, end - start + 1);
        }
        return maxl - 1;
    }
};
```