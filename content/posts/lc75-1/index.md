---
title: "LeetCode 75 - Part 1 - Array/String"
date: 2024-04-27T19:30:30.123Z
draft: false
tags: ["data-structures", "algorithms", "coding-problems"]
authorLink: https://akshatshah21.github.io/
description: "LeetCode 75. Two problems a day. Part 1."
summary: "LeetCode 75. Two problems a day. Part 1."
categories: ["coding-problems"]
---

# Intent
**Confession**: I've never been comfortable with DSA, Coding Problems or Competitive Programming.  
And so this is one more attempt to change that.  
LeetCode 75. Two problems a day, starting April 25, 2024.  
To further motivate consistency and completion of this, I've decided I will do write-ups for all of these problems and perhaps publish them on my blog. Let's see.

This is Part 1 - Array/String.

# 1.1 Merge Strings Alternately
[Problem on LeetCode](https://leetcode.com/problems/merge-strings-alternately)
## Question
Given two strings, merge them such that the characters in the result string are alternately from each string. If one string is exhausted, append the remainder of the other string to the result.

## Solutions
This is like the simple merge operation from Merge Sort!
```cpp
class Solution {
public:
    string mergeAlternately(string word1, string word2) {
        string ret = "";
        int len1 = word1.length();
        int len2 = word2.length();
        int p1 = 0, p2 = 0;
        while (p1 < len1 && p2 < len2) {
            ret += word1[p1++];
            ret += word2[p2++];
        }
        while (p1 < len1) {
            ret += word1[p1++];
        }
        while (p2 < len2) {
            ret += word2[p2++];
        }
        return ret;
    }
};
```

# 1.2 Greatest Common Divisor of Strings
[Problem on LeetCode](https://leetcode.com/problems/greatest-common-divisor-of-strings)

## Question
* String `t` is said to *'divide'* string `s` if the `s = t + t + t + ... + t` (`t` concatenated one or more times)
* With this definition of `division`, find the `GCD` of two given strings `str1` and `str2` (the largest string that divides both)

> **Example 1:**
**Input:** `str1 = "ABCABC", str2 = "ABC"`
**Output:** `"ABC"`

> **Example 2:**
**Input:** `str1 = "ABABAB", str2 = "ABAB"`
**Output:** `"AB"`
	
> **Example 3:**
**Input:** `str1 = "LEET", str2 = "CODE"`
**Output:** `""`

## Solutions
### Observations
* Consider cyclically traversing both strings together one character at a time. If at any point we see a mismatch, then the GCD string must be empty
* This further reveals that we probably need to use that one repeating substring (evident in above examples as "AB" or "ABC"
### Initial Solution

After trying out some test cases on paper and submitting some naive solution, an interesting test case was revealed. Consider the following test case:

> Input: `str1 = "ABABABABABABABAB, str2 = "ABABAB"`

Basically str1 is "AB" 8 times, while str2 is "AB" 3 times. 

My naive solution involved checking whether the smaller string (potentially the GCD) matches cyclically with the larger string, but that's not correct as the above example shows. The correct output is just "AB", not "ABABAB". This is interesting since the GCD of 3 and 8 is 1.

So here's an idea: the GCD of the two strings is the repeating substring, concatenated `g` times, where g is the GCD of number of instances of that repeating substring in each string.

To find that *repeating substring* component, we try all possible prefixes of a string and check whether it *divides* the entire string. We take the smallest such prefix, and call it the `divisor`, and also count the number of concatenations it takes to get the complete string (~the *quotient*).

We then try finding the quotient for the other string, and then calculate the GCD of the two quotients, which is `g` as defined above.

If at any point, there is a character mismatch (while traversing one string - start to end -  and a potential divisor - cyclically), this means that the GCD string must be empty.
```cpp
class Solution {
private:
    pair<string, int> getSmallestDivisor(const string& s) {
        string prefix = "";
        int n = s.length();
        for (int i = 0; i < n; i++) {
            prefix += s[i];
            int p = 0;
            int quotient = 0;
            bool match = true;
            for (const char& c : s) {
                if (c != prefix[p]) {
                    match = false;
                    break;
                }
				p = (p + 1) % (i + 1);  // cyclic traveral, i = prefixLength
                if (p == 0)
                    quotient++;
            }
            if (match && p == 0)
                return { prefix, quotient };
        }
        return { s, 1 };  // no divisor except self
    }

public:
    string gcdOfStrings(string str1, string str2) {
        auto p = getSmallestDivisor(str1);
        string divisor = p.first;
        int q1 = p.second;
        int p2 = 0;
        int q2 = 0;
        int divisorLen = divisor.length();
        for (const char& c : str2) {
            if (c != divisor[p2]) {
                return "";
            }
            p2 = (p2 + 1) % divisorLen;  // cyclic traversal
            if (p2 == 0)
                q2++;
        }
        if (p2 != 0)
            return "";
        string ret = "";
        int g = gcd(q1, q2);
        for (int i = 0; i < g; i++) {
            ret += divisor;
        }
        return ret;
    }
};
```
The time complexity of this solution is `O(N^2 + M)`, where N = length of str1, M = length of str2. `N^2` because we try all prefixes of str1 and traverse str1 for each of them, and `+M` because we traverse str2 with the smallest divisor of str1. 

One simple optimization would be to to call the `getSmallestDivisor` function for the smaller of the two given strings. Another one is to check length divisibility before traversing to check for divisibility of the strings.

The auxiliary space is `O(N)`.

The brute force solution on LeetCode is similar to this solution.

### Optimal Solution
The optimal solution given on LeetCode is amazing, and uses the fact that the length of the GCD string will be the same as the GCD of the length of the two strings, assuming that the two are made of a common repeating substring. (In the above brute force solution, we dealed in terms of the GCD of number of repeating instances, which can be transformed to length by multiplying the number of instances with the length of the repeating substring).

There are two parts to it:
1. Do these two strings have a non-empty GCD string?
2. If they do, then the length of the GCD string must be the same as the GCD of the length of the two strings. And we can simply take the prefix of any string with that length - this prefix is common repeating substring (called `base` in the  LeetCode solution) repeated GCD(len1, len2) times.

The clever way of checking whether there exists a non-empty GCD string is to check if `str1 + str2 == str2 + str`. This extends from the fact that if there is a common repeating substring that forms both strings, these two concatenations would be the same.
For example: ABAB and ABABAB. Here since they have AB as the common repeating substring - (ABAB)(ABABAB) == (ABABAB)(ABAB)
```cpp
class Solution {
public:
    string gcdOfStrings(string str1, string str2) {
        if (str1 + str2 != str2 + str1) return "";
        int l1 = str1.length(), l2 = str2.length();
        int g = gcd(l1, l2);
        return str1.substr(0, g);
    }
};
```

# 1.3 Kids With the Greatest Number of Candies
[Problem on LeetCode](https://leetcode.com/problems/kids-with-the-greatest-number-of-candies)
## Question
Given an array of `n` numbers and a number `x`, return a boolean array with true values at those indices where, upon adding `x` to the element and leaving all other elements unchanged, the current element will be a maximum.
There can be multiple maximums.

## Solutions
Find the maximum number in the input array, and map each element to true if its value plus `x` is greater than or equal to the maximum
### C++
```cpp
class Solution {
public:
    vector<bool> kidsWithCandies(vector<int>& candies, int extraCandies) {
        int max = *max_element(candies.begin(), candies.end());
        vector<bool> res;
        for (const int &i: candies) {
            res.emplace_back(i + extraCandies >= max);
        }
        return res;
    }
};
```
### Java
```java
class Solution {
    public List<Boolean> kidsWithCandies(int[] candies, int extraCandies) {
        int max = Arrays.stream(candies).max().getAsInt();
        return Arrays.stream(candies).map(i -> i + extraCandies >= max).getAsBoolean();i
    }
}
```
### Python
```python
class Solution:
    def kidsWithCandies(self, candies: List[int], extraCandies: int) -> List[bool]:
        max_candies = max(candies)
        return [i + extraCandies >= max_candies for i in candies]
        
```
### JavaScript
```javascript
/**
 * @param {number[]} candies
 * @param {number} extraCandies
 * @return {boolean[]}
 */
var kidsWithCandies = function(candies, extraCandies) {
    const maxCandies = Math.max(...candies);
    return candies.map(i => i + extraCandies >= maxCandies);
};
```
### C\#
```csharp
public class Solution {
    public IList<bool> KidsWithCandies(int[] candies, int extraCandies) {
        var maxCandies = candies.Max();
        return candies.Select(i => i + extraCandies >= maxCandies).ToList();
    }
}
```

# 1.4 Can Place Flowers
[Problem on LeetCode](https://leetcode.com/problems/can-place-flowers)
## Question
Given a binary array, return whether it is possible to add `x` 1s in the array without there being two adjacent 1s.

## Solutions
Traverse the array, and try placing a 1 wherever possible (~greedily). It will be possible to place a 1 if `i-1` and `i+1` have 0s or are out of bounds.
### C++
```cpp
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        int l = flowerbed.size();
        for (int i=0;n > 0 && i < l; i++) {
            if (flowerbed[i] == 1) continue;
            if ((i == 0 || flowerbed[i-1] == 0) && (i == l-1 || flowerbed[i+1] == 0)) { 
                flowerbed[i] = 1;
                n--;
            }
        }
        return n == 0;
    }
};
```

### Java
```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        final int l = flowerbed.length;
        for (int i = 0; n > 0 && i < l; i++) {
            if (flowerbed[i] == 1)
                continue;
            if ((i == 0 || flowerbed[i-1] == 0) && 
                (i == l-1 || flowerbed[i+1] == 0)) {
                    flowerbed[i] = 1;
                    n--;
                }
        }
        return n == 0;
    }
}
```

# 1.5 Reverse Vowels of a String
[Problem on LeetCode](https://leetcode.com/problems/reverse-vowels-of-a-string
## Question
Given a string, reverse the order of vowels that appear in it.
For example, `hello` becomes `holle`.
The string can contain ASCII-printable characters.

## Solution
An immediate solution is to store a list of vowels as they appear in the string, then replace them in reverse order with the list.
The next logical step is to realize you can use two pointers and swap vowels only, skipping other characters (much like how you reverse a string with two pointers).

A subtle thing to note here is that the vowels can be lower as well as upper case

```cpp
class Solution {
private:
    const set<char> VOWELS = { 'a', 'A', 'e', 'E', 'i', 'I', 'o', 'O', 'u', 'U' };

    bool isVowel(const char &c) {
        return VOWELS.count(c);
    }
public:
    string reverseVowels(string s) {
        int l = 0, r = s.length() - 1;
        
        while (l < r) {
            if (!isVowel(s[l])) {
                l++;
                continue;
            }
            if (!isVowel(s[r])) {
                r--;
                continue;
            }
            swap(s[l], s[r]);
            l++;
            r--;
        }
        return s;
    }
};
```


# 1.6 Reverse Words in a String
[Problem on LeetCode](https://leetcode.com/problems/reverse-words-in-a-string)
## Question
Given a string, reverse the words in it. Also remove any extra whitespaces - leading, trailing and between the words.

**Follow-up**: If the string data type is mutable in your language, can you solve it **in-place** with `O(1)` extra space?

## Solutions
Ah, so easy to do if you have the right functions available... Check out the following solutions in decreasing order of simplicity - Python and Java. Both these languages have a convenient standard `split` function, as well as a function to `trim` or `strip` whitespaces. For an online assessment (or real life, lol), this would be the way to go. But if an interviewer is asking you this question, they're not looking for these solutions. Plus, look at the follow-up. 
### Python
```python
class Solution:
    def reverseWords(self, s: str) -> str:
        s = s.strip()
        words = s.split()
        words.reverse()
        return ' '.join(words)
```
Questions like these remind you that it's helpful to be fluent in multiple languages for coding problems

### Java
```java
class Solution {
    public String reverseWords(String s) {
        String[] words = s.trim().split("\\s+");
        StringBuilder stringBuilder = new StringBuilder();
        int n = words.length;
        for (int i=n-1; i>0; i--) {
            System.out.println(words[i]);
            stringBuilder.append(words[i]);
            stringBuilder.append(' ');
        }
        stringBuilder.append(words[0]);
        return stringBuilder.toString();
    }
}
```
Notice how using Java's `split` method here is different from using Python's: you need to trim the string in Java first, in Python you don't. Ah, language subtleties.
### Solving this in-place
1. Reverse the entire string
2. Then reverse each word in the reversed string (remember, words are delimited by one or more spaces).
This would reverse the order of words in the string. Try it out, and this isn't too difficult to come up with intuitively :-).

As a final step, we would need to get rid of extra whitespaces (leading, trailing and between words). For this we need to keep two pointers - a `read` pointer and a `write` pointer, and write the entire string again in the same buffer, with decisioning to ignore the extra spaces. The challenge here would be to write this parsing and writing logic elegantly. Refer to the C++ code below to see how I did it (likely not the best way).

And now for the big one.
```cpp
class Solution {
public:
    string reverseWords(string s) {
        reverse(s.begin(), s.end());
        int n = s.length();
        for (int i=0; i<n; i++) {
            if (s[i] == ' ') continue;
            int start = i;
            while (i < n && s[i] != ' ') i++;
            int end = i;
            reverse(s.begin() + start, s.begin() + end);
        }

        // collapse spaces
        int write = 0;
        bool endWord = false;
        for (int read = 0; read < n; read++) {
            if (endWord) {
                s[write++] = ' ';
                endWord = false;
            }
            if (s[read] != ' ') {
                s[write++] = s[read];
                if (read < n && s[read+1] == ' ') endWord = true;
            }
        }
        if (s[write-1] == ' ') write--;
        return s.substr(0, write);
    }
};
```


# 1.7 Product of Array Except Self
[Problem on LeetCode](https://leetcode.com/problems/product-of-array-except-self)
## Question
* Given an input array, return an array where each element of the result array holds the product of the entire input array except the element at that position in the input array.
* You cannot use the division operation
* Do it in O(N) time
* **Follow-up**: Do it in O(1) *auxiliary* space
## Solutions
The most direct way of doing this is calculating the product of the entire array and then dividing it by each element to get the result array. This needs to handle 0s in the array:
* If there are two or more 0s, then the entire result array would be 0s (at least one 0 would be part of every product)
* If there is exactly one 0, then only that position will have the entire product (without 0) and the rest of the array would hold 0s (since this 0 would be in every other product)
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        int product = 1;
        int zeroes = 0;
        int zeroIdx = -1;
        for (int i=0; i<n; i++) {
            if (nums[i] == 0) {
                zeroes++;
                zeroIdx = i;
            }
            else product *= nums[i];
        }
        vector<int> res(n, 0);
        if (zeroes > 1) {
            return res;
        }
        if (zeroes == 1) {
            res[zeroIdx] = product;
            return res;
        }

        for (int i=0; i<n; i++) {
            res[i] = product / nums[i];
        }
        
        return res;
    }
};
```
This is an O(N) time, O(1) solution, but it uses the division operator, which is not allowed according to the question.

The brute force way of solving this would be to calculate for each element, the product of the other elements - an O(N^2) solution. From this solution, we can observe that we're repeating a lot of calculations. What would help here are prefix and suffix products. So here's what we can do:
* Calculate a prefix product array: `pref[i] = nums[0] * nums[1] * ... * nums[i-1];        pref[0] = 1
* Calculate a suffix product array: `suff[i] = nums[i+1] * nums[i+2] * ... * nums[n-1];    suff[n-1] = 1`
* Then, the result array is simply: `res[i] = pref[i] * suff[i]`
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& a) {
        int n = a.size();
        int pre[n], suf[n];
        pre[0] = 1;
        suf[n-1] = 1;
        for(int i=1; i<n; i++) {
            pre[i] = pre[i-1] * a[i-1];
        }
        for(int i=n-2; i>=0; i--) {
            suf[i] = suf[i+1] * a[i+1];
        }
        vector<int> ans(n);
        for(int i=0; i<n; i++) {
            ans[i] = pre[i] * suf[i];
        }
        return ans;
    }
};
```
This is an O(N) time solution, but it takes O(N) space for the prefix and suffix array.

Observation: We're going from 0 to n-1 again for calculating the result array. 
Instead of keeping an array of prefix products, we can keep a single prefix product variable, use it for the result calculation and update the same variable (we don't need the previous prefix products). This takes saves creating and maintaining one array. But we still need the suffix array since we're going from 0 to n-1.
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> suffixProduct(n, 1);
        for (int i=n-2; i>=0; i--) {
            suffixProduct[i] = suffixProduct[i+1] * nums[i+1];
        }
        int prefixProduct = 1;
        vector<int> res(n, 1);
        for (int i=0; i<n; i++) {
            res[i] = prefixProduct * suffixProduct[i];
            prefixProduct *= nums[i];
        }
        return res;
    }
};
```

Now, we need to have O(1) auxiliary space. This is usually a hint for using the input array and modifying it in-place. But in this case, we can instead use the result array itself! We can initially store suffix products in the result array, and then using the prefix product, update each element as we go from 0 to n-1. This reduces our auxiliary space to O(1) - the result array is not auxiliary, and apart from that we just have single variables.
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        // first, fill the result with suffix product
        vector<int> res(n, 1);
        for (int i=n-2; i>=0; i--) {
            res[i] = nums[i+1] * res[i+1];
        }

        // then multiply prefix product
        int prefixProduct = nums[0];
        for (int i=1; i<n; i++) {
            res[i] = res[i] * prefixProduct;
            prefixProduct *= nums[i];
        }
        
        return res;
    }
};

```

# 1.8 Increasing Triplet Subsequence
[Problem on LeetCode](https://leetcode.com/problems/increasing-triplet-subsequence)
## Question
Given an array of integers, return true if there exist distinct indices `i`, `j`, `k` such that `i < j < k` and `nums[i] < nums[j] < nums[k]`

## Solutions
The brute force way of doing this is to find a pair `(i, k, j)` among all such unordered pairs where the conditions hold true. This solution has O(N^3) time complexity.
Intuition leads us to think in terms of minimum values. One way of thinking about this is that we need to find some *local* minimum `i`, after which there exists another *local* minimum `j` that is greater than the number at `i`, after which there should exist at least one number that's greater than the one at `j`. The way to do this in linear time would be to store the two lowest values encountered so far - `min` and `min2`. 
```
min = INT_MAX, min2 = INT_MAX
for(int i: nums) {
	if (i <= min) min = i
	if (i < min2) min2 = i
	if (i > min2) return true
}
return false
```

* `min` is a potential `nums[i]
* `min2` is a potential `nums[j]`, but it doesn't necessarily store one corresponding to `min` being `nums[i]`. Rather, it can also store a potential `nums[j]` corresponding to a previous value of `min` (a potential `nums[i]` that was encountered previously but is now replaced with another). 

The reason for `min2`'s definition can be understood with the following test case:
  `[9, 10, 5, 11, 10, 9, 8]`
  This is how we update `min` and `min2`. Initially, both hold `INT_MAX`:
  * At `i = 0`, `nums[i] = 9`: `min = 9, min2 = INT_MAX`*
  * At `i = 1`, `nums[i] = 10`: `min = 9, min2 = 10`
  * At `i = 2`, `nums[i] = 5`: `min = 5, min2 = 10`. Note that here we do not update `min2` to 9 (`j > i`), but neither do we reset it (I reset it in an earlier version, which won't work - we'll see why)
  * At `i = 3`, `nums[i] = 11`: `min = 5, min2 = 10`. Now we finally encounter a value that is greater than `min2` (`k > j` and `nums[k] > nums[j]`) And we also observe that we do indeed have an answer: (9, 10, 11). Note that `min` is holding `5`, but it's not part of the current `(nums[i], nums[j], nums[k])` tuple that we have as the match - `nums[i]` is actually 9, even though we replaced `min`. But it's `min2` storing `10` is what tells us that we have encountered some number before it such that it's lower than `10`. `10` is actually corresponding to `9` as its pair, not 5. This really is that one pivot of the solution.
  Let's say we reset `min2` each time we encounter a number lower than the current `min`, with this same test case. This is how it would turn out:
  * At `i = 0`, `nums[i] = 9`: `min = 9, min2 = INT_MAX`*
  * At `i = 1`, `nums[i] = 10`: `min = 9, min2 = 10`
  * At `i = 2`, `nums[i] = 5`: `min = 5, min2 = INT_MAX`. Note that here we **do reset it**
  * At `i = 3`, `nums[i] = 11`: `min = 5, min2 = 11`
  * At `i = 4`, `nums[i] = 10`: `min = 5, min2 = 10`
  * At `i = 5`, `nums[i] = 9`: `min = 5, min2 = 9`
  * At `i = 6`, `nums[i] = 8`: `min = 5, min2 = 8`
  We reach the end, and return false, even though we do have a possible answer: (9, 10, 11). This is why resetting `min2` will not work.
The fact that `min2` has already been replaced, means that there exists a `min` (before `min2` and less than `min2`) before it, even though the current value of `min` might be different. 

I found this in the comments in one of the solutions:
> updating `min1` simply prepares to form a new pair of `min<min2`. The existing `min2` represents the older `min1 < min2` pair

```cpp
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        int min = INT_MAX, min2 = INT_MAX;
        for (const int &i: nums) {
            if (i <= min)   min = i;        // prepares for new min, min2 pair. min2 still represents older min, min2 pair!!
            else if (i < min2)  min2 = i;
            else if (i > min2) return true;
        }
        return false;
    }
};
```
Effectively just three lines, wow.

# 1.9 String Compression
[Problem on LeetCode](https://leetcode.com/problems/string-compression)
## Question
Given an array of characters, compress it such that each subarray of the same character is replaced with the character followed by the length of that subarray. If the length of the subarray is 1, skip the length, just place the character. Modify the input in-place.
Examples:
* `["a","a","b","b","c","c","c"] -> ["a","2","b","2","c","3"]`
* `['a'] -> ['a']`

## Solutions
Classic read-write pointers approach. Traverse the array and maintain a `currentChar` and `currentCharCount`, and for each new character encountered, write the `currentChar` and `currentCharCount` to the array.
```cpp
class Solution {
private:
    void appendNumberToCharArray(const int &num, vector<char> &chars, int &write) {
        string s = to_string(num);
        for (const char &c: s) {
            chars[write++] = c;
        }
    }
    void appendCharCount(const int &num, const char &c, vector<char> &chars, int &write) {
        chars[write++] = c;
        if (num > 1) {
            appendNumberToCharArray(num, chars, write);
        }
    }
public:
    int compress(vector<char>& chars) {
        char currentChar = chars[0];
        int write = 0;
        int currentCharCount = 1;
        int n = chars.size();
        for (int i=1; i<n; i++) {
            if (chars[i] != currentChar) {
                appendCharCount(currentCharCount, currentChar, chars, write);
                currentCharCount = 0;
                currentChar = chars[i];
            }
            currentCharCount++;
        }
        appendCharCount(currentCharCount, currentChar, chars, write);
        return write;
    }
};
```
Looking at my previous submissions from June 2020 to Feb 2024 to now, I've gone from repeating code (not using functions) and using terrible one-letter variables for everything to good(?) variable names and helper functions. Progress?

