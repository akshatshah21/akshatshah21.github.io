# LeetCode 75 - Part 2 - Two Pointers

# Intent
**Confession**: I've never been comfortable with DSA, Coding Problems or Competitive Programming.  
And so this is one more attempt to change that.  
LeetCode 75. Two problems a day, starting April 25, 2024.  
To further motivate consistency and completion of this, I've decided I will do write-ups for all of these problems and perhaps publish them on my blog. Let's see.

This is Part 2 - Two Pointers.

# 2.1 Move Zeroes
[Problem on LeetCode](https://leetcode.com/problems/move-zeroes)
## Question
Given an array of integers, modify it in-place so that all the 0s in the array are at the end of the array and the relative order of other non-zero elements is retained.

**Follow-up:**: Minimize the total number of array writes.
## Solutions
First method that came to my mind was to count the number of zeroes, then move all non-zero elements to the front, and then write all 0s at the back. An important observation here is that for a non-zero element, its position in the result array can be its original position or one before its original position. So we can use two pointers to move non-zero elements ahead, and if we go from left to right, we won't accidentally overwrite an element that's at its final position.
```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int count = count_if(nums.begin(), nums.end(), [](const int &i) -> bool {
            return i == 0;
        });

        int write = 0;
        int n = nums.size();
        for (int i=0; i<n; i++) {
            if (nums[i] != 0) {
                nums[write++] = nums[i];
            }
        }
        for (int i=n-count; i<n; i++) {
            nums[i] = 0;
        }
    }
};
```
Time Complexity: O(N)  
Space Complexity: O(1)  
But, this has multiple passes of the array. Sure, we could combine counting zeros with moving elements ahead but writing all 0s at the end would still go through part of the array itself, and the number of writes in the array would be the same as number of elements.

We don't really need to keep a count of zeroes, simply moving all non-zero elements ahead and then writing 0s to the remaining positions in the array would also work:
```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        // 0 to write-1: non-zeroes
        // write to i-1: zeroes
        // i to n-1: unexplored
        int write = 0;
        int n = nums.size();
        for (int i=0; i<n; i++) {
            if (nums[i] != 0) {
                nums[write++] = nums[i];
            }
        }
        for (; write<n; write++) {
            nums[write] = 0;
        }

    }
};
```
Time Complexity: O(N)  
Space Complexity: O(1)  
This is still one complete pass, plus some more iterations to write 0s, and the number of array writes is still equal to the number of array elements.

What we're doing currently is writing 0s explicitly in all cases. Consider the following test case: `[0, 0, 0, 1]` -> `[1, 0, 0, 0]`. Here, even though effectively only `1` is shifted ahead, using above solution we are writing 1 once and 0 thrice. But, all that's required is putting the `1` at the first position, and putting a `0` at the final position (2 writes). So we can make use of swapping instead of separate explicit writes for non-zero and zero elements.   
However, consider the following test case: `[0, 1, 1, 1]` -> `[1, 1, 1, 0]`. Here, we still do 4 writes with swapping; however it's still as good as the above solution in terms of number of array writes.
```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        // 0 to write-1: non-zeroes
        // write to i-1: zeroes
        // i to n-1: unexplored
        int write = 0;
        int n = nums.size();
        for (int i=0; i<n; i++) {
            if (nums[i] != 0)
                swap(nums[write++], nums[i]);
        }
    }
};
```
Time Complexity: O(N)  
Space Complexity: O(1)  
The number of writes in this case depends on the testcase (2 times the number of non-zero elements), but is lower than or equal to than the previous solutions as demonstrated in the above example.

# 2.2 Is Subsequence
[Problem on LeetCode](https://leetcode.com/problems/is-subsequence)
## Question
Given strings `s` and `t`, check if `s` is a subsequence of `t`.   
> A subsequence of a string is a new string that is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (i.e., "ace" is a subsequence of "abcde" while "aec" is not).

> **Follow up:** Suppose there are lots of incoming `s`, say `s1, s2, ..., sk` where `k >= 10^9`, and you want to check one by one to see if `t` has its subsequence. In this scenario, how would you change your code?
## Solution
We can use two pointers, one for `t` and one for `s`. We move both pointers ahead if characters match, or we move the pointer to `t` only in case the characters don't match (a *deleted* character from `t` in subsequence terms).
```cpp
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int tIdx = 0, sIdx = 0;
        int tLen = t.length();
        int sLen = s.length();
        while (sIdx < sLen && tIdx < tLen) {
            if (s[sIdx] == t[tIdx])
                sIdx++;
            tIdx++;
        }
        return sIdx == sLen;
    }
};
```
Time Complexity: O(N+M) where N, M are the string lengths  
Space Complexity: O(1)  

### Follow-up
In case we have an incoming stream of `s` to check whether they are subsequences of `t`, we can pre-compute the following information: a map from character to a sorted list of indices of that character in `t`. Then, for each s:
```
pos = charIndices[s[0]][0]
for char in s[1..n-1]:
	pos = charIndices[char].upper_bound(pos)
	if pos == -1:    // not found => no further index in t which holds char
		return false

return true
```
For each character, we find the next closest possible position (upper bound: lowest element strictly greater than) in t that holds the same character. We keep doing this for every character and keep updating `pos`. If at any point the binary search (upper bound) fails, it indicates that there is no further position in `t` that has the character we're looking for, so the current `s` cannot be a subsequence of `t`. If we are able to exhaust all characters of an `s`, that means `s` is a subsequence of `t`.  
The time complexity of this solution is O(k * S * log(T)), where S = max length of an `s`, and `T` is the length of `T`. If we had used the same simple two pointer solution as above, it would be O(k * S * T) instead.

# 2.3 Container With Most Water
[Problem on LeetCode](https://leetcode.com/problems/container-with-most-water)
## Question
Given an array of positive integers indicating heights of vertical lines drawn such that the endpoints of the lines are `(i, 0)` and `(i, heights[i])`, find two lines that together with the x-axis form a container, such that the container contains the most water. Return _the maximum amount of water a container can store_.

## Solution
The brute force solution is to try all pairs of lines `(i, j), j > i` and find the maximum area `(j-i) * min(heights[i] * heights[j])`. This has O(N^2) time complexity and will not satisfy the given input size limits (10^5 lines).

There are two things that are contributing to the area - the width `(j-i)` and the lesser of the heights of the two lines. We can start with the widest possible container (first and last lines), and move inwards using two pointers greedily. How? By discarding the shorter of the two current lines. How can we justify this greedy step?
* Consider that currently we have a container with two lines of heights `a` and `b`, (`a > b`). And the width is `w`.
* So current area is `w * a`
* Now, since we're moving pointers inwards, `w` is going to decrease by `1` in the next iteration
* If we retain the shorter line `a` and move inwards by discarding `b`, then the maximum possible area of the next iteration will be `a * (w-1)`, since the height of the container is minimum of the heights of its two lines
* If we retain the longer line `b` and move inwards by discarding `a`, then the maximum possible area of the next iteration will be `b * (w-1)`.
So we can conclude that it's always optimal to stick with the longer line, since the shorter line will not be able to form a better container in future iterations, and we can ignore it safely for future considerations.
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0, right = height.size() - 1;
        int maxArea = 0;
        while (left < right) {
            maxArea = max(maxArea, (right - left) * min(height[left], height[right]));
            if (height[left] < height[right]) left++;
            else right--;
        }
        return maxArea;
    }
};
```
Time Complexity: O(N)  
Space Complexity: O(1)
# 2.4 Max Number of K-Sum Pairs
[Problem on LeetCode](https://leetcode.com/problems/max-number-of-k-sum-pairs)
## Question
Given an array of integers and an integer `k`, return the number of times it is possible to do the following operation:  
Pick two elements that add up to `k`, and remove them from the array.

## Solution
In other words, we need to find the number of pairs in the array that add up to `k`, and we can include an element in one pair only. This is a modification of the popular *Two-Sum* problem.

The brute force method is to consider all unordered pairs in the array - a O(N^2) solution.

We can sort the array and use two pointers at starting at each end (lowest and greatest numbers in the array). Then, for each iteration, check if the current numbers add up to `k`, and count that pair. If not, we update the left or right pointer to get to a higher or lower sum respectively. This works since the array is sorted.
```cpp
class Solution {
public:
    int maxOperations(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        int left = 0, right = nums.size() - 1;
        int res = 0;
        while (left < right) {
            int sum = nums[left] + nums[right];
            if (sum == k) {
                res++;
                left++;
                right--;
            } else if (sum < k) {
                left++;
            } else {
                right--;
            }
        }
        return res;
    }
};
```
Time Complexity: O(N * logN)  
Space Complexity: O(1)

Another method is to use a hashmap to keep a memory of elements encountered previously to find required pairs.
```cpp
class Solution {
public:
    int maxOperations(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        int res = 0;
        for (const int &i: nums) {
            if (freq.count(k-i) && freq[k-i] > 0) {
                freq[k-i]--;
                res++;
            } else {
                freq[i]++;
            }
        }
        return res;
    }
};
```
Time Complexity: O(N)  
Space Complexity: O(N)
