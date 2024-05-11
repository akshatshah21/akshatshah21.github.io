# LeetCode 75 - Part 4 - Prefix Sum

# Intent

**Confession**: I've never been comfortable with DSA, Coding Problems or Competitive Programming.  
And so this is one more attempt to change that.  
LeetCode 75. Two problems a day, starting April 25, 2024.  
To further motivate consistency and completion of this, I've decided I will do write-ups for all of these problems and perhaps publish them on my blog. Let's see.

This is Part 4 - Prefix Sum.
# 4.1 Find the Highest Altitude
[Problem on LeetCode](https://leetcode.com/problems/find-the-highest-altitude)

## Question
Given an array of integers, where each integer denotes the *gain* in altitude (starting altitude 0) from the previous position, find the highest altitude ever reached. If the integer is negative, the gain is negative (drop in altitude).

## Solution
We can simply maintain the current altitude and the global maximum altitude. `O(N)` time, `O(1)` auxiliary space

```cpp
class Solution {
public:
    int largestAltitude(vector<int>& gain) {
        int maxAlt = 0;
        int currentAlt = 0;
        for (const int &i: gain) {
            currentAlt += i;
            maxAlt = max(maxAlt, currentAlt);
        }
        return maxAlt;
    }
};
```

# 4.2 Find Pivot Index
[Problem on LeetCode](https://leetcode.com/problems/find-pivot-index/)

## Question
Given an array of integers, find the leftmost index where the sum of all elements to the left is equal to the sum of all elements to the right (element at current index is not included in either sum). This index is termed the *pivot* index.

## Solution
The brute force solution will be to test each index `i` for being the pivot by calculating the sum of elements to its left, $\sum_{j=0}^{i-1}{a[j]}$ and the sum of elements to its right, $\sum_{j=i+1}^{n-1}{a[j]}$ and checking if they are equal. This would be a quadratic time solution.

We can observe that we're repeating a lot of sum calculations. We can instead use prefix and suffix sums. We can also avoid creating and storing prefix/suffix sum arrays and just maintain two variables for the prefix sum and suffix sum, since at every index, we only need its prefix and suffix sum. So we can maintain these two sums as we move from left to right and for each index we can check if they are equal. This is an `O(N)` time solution and uses constant space. It requires two passes - for calculating suffix sum, and then to test each index for being the pivot. 

```cpp
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        int suffixSum = accumulate(nums.begin(), nums.end(), 0);
        int prefixSum = 0;
        int n = nums.size();
        for (int i=0; i<n; i++) {
            suffixSum -= nums[i];
            if (prefixSum == suffixSum) return i;
            prefixSum += nums[i];
        }
        return -1;
    }
};
```
