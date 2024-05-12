# LeetCode 75 - Part 5 - Hash Map / Set

# Intent

**Confession**: I've never been comfortable with DSA, Coding Problems or Competitive Programming.  
And so this is one more attempt to change that.  
LeetCode 75. Two problems a day, starting April 25, 2024.  
To further motivate consistency and completion of this, I've decided I will do write-ups for all of these problems and perhaps publish them on my blog. Let's see.

This is Part 5 - Hash Map / Set.

# 5.1 Find the Difference of Two Arrays
[Problem on LeetCode](https://leetcode.com/problems/find-the-difference-of-two-arrays)

## Question
Given two arrays, find both set differences of the two arrays (A-B and B-A).

## Solution
We can find a solution for A-B, and use the same one for B-A.

A naive way of finding set difference is to search through the *subtrahend* B for each element in the *minuend* A (are these the correct terms to use here?). If we don't find a match, that element from A is part of the result set A-B. Note that we need to return a set as the result, so it cannot have duplicates, so we need to deduplicate the result we find from the naive search too (sorting or hashset). This would have a time complexity of `O(N*M)`.

We can use a hash set to store the unique occurences of B, and then iterate through A, checking for a match in B's hash set. If we don't find a match, we add it to the result hash set A-B. This gives us an `O(N+M)` time solution with `O(M)` auxiliary space complexity (A-B is part of the result).

We can also sort B, and then iterate through A to find matches in a sorted B using binary search. Each search would be `O(logM)`, so overall time complexity would be `O(N*logM + M*logM) = O((N+M)logM)` (`NlogM` for searching A's elements, and `MlogM` for sorting B). Again deduplication would be required (or use hash set of A to begin with).

```cpp
class Solution {
public:
    vector<vector<int>> findDifference(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> s1 = unordered_set<int>(nums1.begin(), nums1.end());
        unordered_set<int> s2 = unordered_set<int>(nums2.begin(), nums2.end());
        vector<int> diff1, diff2;
        
        for (const int &i: s1) {
            if (!s2.count(i)) diff1.emplace_back(i);
        }

        for (const int &i: s2) {
            if (!s1.count(i)) diff2.emplace_back(i);
        }

        return { diff1, diff2 };
    }
};
```

Fancy C++:
```cpp
class Solution {
public:
    vector<vector<int>> findDifference(vector<int>& nums1, vector<int>& nums2) {
        set<int> s1 = set<int>(nums1.begin(), nums1.end());
        set<int> s2 = set<int>(nums2.begin(), nums2.end());
        vector<int> diff1, diff2;
        set_difference(s1.begin(), s1.end(), s2.begin(), s2.end(), std::inserter(diff1, diff1.begin()));
        set_difference(s2.begin(), s2.end(), s1.begin(), s1.end(), std::inserter(diff2, diff2.begin()));

        return { diff1, diff2 };
    }
};
```

We can also use a procedure like the merge in Merge-sort. Good for practice with pointers and procedurals :dizzy_face: but i don't think this version is very readable:
```cpp
class Solution {
private:
    vector<int> set_difference(const vector<int> &nums1, const vector<int> &nums2) {
        vector<int> diff;
        int p1 = 0, p2 = 0;
        int n1 = nums1.size(), n2 = nums2.size();
        while (p1 < n1 && p2 < n2) {
            if (!diff.empty() && diff.back() == nums1[p1]) {
                p1++;
                continue;
            }
            if (nums1[p1] < nums2[p2]) {
                diff.emplace_back(nums1[p1++]);
            } else if (nums1[p1] == nums2[p2]){
                while (p1 < n1 && nums1[p1] == nums2[p2]) p1++;
            } else {
                p2++;
            }
        }
        while (p1 < n1) {
            if (diff.empty()) {
                diff.emplace_back(nums1[p1]);
            } else if (nums1[p1] != diff.back()) {
                diff.emplace_back(nums1[p1]);
            }
            p1++;
        }
        return diff;
    }
public:
    vector<vector<int>> findDifference(vector<int>& nums1, vector<int>& nums2) {
        sort(nums1.begin(), nums1.end());
        sort(nums2.begin(), nums2.end());

        vector<int> diff1 = set_difference(nums1, nums2);
        vector<int> diff2 = set_difference(nums2, nums1);

        return { diff1, diff2 };
    }
};
```

In other languages:
### Python
```python
class Solution(object):
    def findDifference(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[List[int]]
        """
        s1 = set(nums1)
        s2 = set(nums2)
        d1 = s1 - s2
        d2 = s2 - s1
        return [list(d1), list(d2)]
```
Lol

### JavaScript
```javascript
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[][]}
 */
var findDifference = function(nums1, nums2) {
    const s1 = new Set(nums1);
    const s2 = new Set(nums2);
    const diff1 = [];
    const diff2 = [];
    s1.forEach(x => {
        if (!s2.has(x)) diff1.push(x);
    });
    s2.forEach(x => {
        if (!s1.has(x)) diff2.push(x);
    });

    return [diff1, diff2];
};
```
Apparently the `difference` method of JS's `Set` has [limited availability](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set/difference).

### C\#
```csharp
public class Solution {
    public IList<IList<int>> FindDifference(int[] nums1, int[] nums2) {
        IList<IList<int>> res = new List<IList<int>>();
        res.Add(nums1.Distinct().Where(x => !nums2.Contains(x)).ToList());
        res.Add(nums2.Distinct().Where(x => !nums1.Contains(x)).ToList());
        return res;
    }
}
```

### Java
```java
class Solution {
    private List<Integer> getSetDifference(int[] nums1, int[] nums2) {
        Set<Integer> s1 = new HashSet<Integer>();
        Set<Integer> s2 = new HashSet<Integer>();

        for (final int i: nums1) s1.add(i);
        for (final int i: nums2) s2.add(i);

        List<Integer> diff = new ArrayList<Integer>();
        for (final int i: s1) {
            if (!s2.contains(i)) diff.add(i);
        }
        return diff;
    }
    public List<List<Integer>> findDifference(int[] nums1, int[] nums2) {
        return Arrays.asList(
            getSetDifference(nums1, nums2),
            getSetDifference(nums2, nums1)
        );
    }
}
```

# 5.2 Unique Occurrences
[Problem on LeetCode](https://leetcode.com/problems/unique-number-of-occurrences)

## Question
Given an array of integers, check whether number of occurences of each value in the array is unique or not.

## Solution
We need to check if the frequencies of each value are unique or not. To find the frequencies, we can use a hash map, and then to find if all these frequencies are unique or not, we can use a hash set and check if the size of this hash set of frequencies is the same as the frequency hash map. We can also sort the frequencies and then check if each one is unique or not. This would save some space, but will not change the space complexity as we're already creating a hash map of frequencies (`O(N)`). Also, just cleaner?

```cpp
class Solution {
public:
    bool uniqueOccurrences(vector<int>& arr) {
        unordered_map<int, int> freq;
        for (const int &i: arr) freq[i]++;
        unordered_set<int> freqSet;
        for (const auto &i: freq) freqSet.insert(i.second);
        return freqSet.size() == freq.size();
    }
};
```

# 5.3 Determine if Two Strings Are Close
[Problem on LeetCode](https://leetcode.com/problems/determine-if-two-strings-are-close)

## Question
From LeetCode:
> Two strings are considered **close** if you can attain one from the other using the following operations:
> - Operation 1: Swap any two **existing** characters.
>     - For example, `abcde -> aecdb`
> - Operation 2: Transform **every** occurrence of one **existing** character into another **existing** character, and do the same with the other character.
> 	- For example, `aacabb -> bbcbaa` (all `a`'s turn into `b`'s, and all `b`'s turn into `a`'s)
> You can use the operations on either string as many times as necessary.  
> Given two strings, `word1` and `word2`, return `true` _if_ `word1` _and_ `word2` _are **close**, and_ `false` _otherwise._

## Solution
After trying out some examples, we can see that this problem can be reduced to finding whether the list of frequencies of characters in the two strings are the same and if every character present in one string is present in the other. Note that the frequency of a particular character in both strings need not match - rather it is the list of frequencies of the characters (decoupled from the characters themselves (since they can be transformed into others)) that must match. We can use a hash map to find frequencies in each string (here a 26-length array is fine since only lowercase alphabets are present in the strings).

```cpp
class Solution {
public:
    bool closeStrings(string word1, string word2) {
        vector<int> charFreq1(26, 0);
        vector<int> charFreq2(26, 0);
        for (const char &c: word1) charFreq1[c-'a']++;
        for (const char &c: word2) charFreq2[c-'a']++;

        for (int i=0; i<26; i++) {
            if (charFreq1[i] == 0 && charFreq2[i] != 0) return false;
            if (charFreq1[i] != 0 && charFreq2[i] == 0) return false;
        }

        sort(charFreq1.begin(), charFreq1.end());
        sort(charFreq2.begin(), charFreq2.end());

        for (int i=0; i<26; i++) {
            if (charFreq1[i] != charFreq2[i]) return false;
        }
        return true;
    }
};
```

# 5.4 Equal Row and Column Pairs
[Problem on LeetCode](https://leetcode.com/problems/equal-row-and-column-pairs)

## Question
Given an $N \times N$ matrix, find the number of pairs (row, column) where the row == column (element-wise).

## Solution
The brute-force solution will involve checking each column for each row and comparing the two. A comparison would take `O(N)` time and to iterate through each candidate pair would take `O(N^2)` time, bringing the overall time complexity of this approach to `O(N^3)`.
```cpp
class Solution {
public:
    int equalPairs(vector<vector<int>>& grid) {
        int n = grid.size();
        int count = 0;
        for (int r=0; r<n; r++) {
            for (int c=0; c<n; c++) {
                int i;
                for (i=0; i<n; i++) {
                    if (grid[r][i] != grid[i][c]) break;
                }
                if (i == n) count++;
            }
        }
        return count;
        
    }
};
```

We need to somehow store the knowledge of a row in a data structure so that we can check if a column matches with it. This is a use case for hashing. We need to hash each row and store its hash in a hash map (the value being the frequency of such a row), and then iterate through each column, check whether a column's hash exists in the map, and add to the count of result pairs accordingly. Now the question is - how to hash a row (or in general, an array or vector)?

Some solutions suggest using serialization to string or JSON for the row - then this serialized string becomes the hash key. The calculation and comparison of these hashes is `O(N)`, so for  `N` rows, creating the hash map would be `O(N^2)` and checking for N columns would again be `O(N^2)`. This is better than the naive `O(N^3)` solution, and takes `O(N^2)` space (since each row could be unique, so N serialized arrays of size N would be stored as keys).

Hashing strings should hint towards tries! Instead of storing each array by serializing it, we could instead use a trie, where each node corresponds to an element in the arrays. The root node denotes an empty array and each node has a `children` hash map which points to its children using the elements as keys. In the leaf nodes, we store the count of such arrays (number of rows). Then we try finding each column in the trie, and if we do find a match till the leaf node, we update the result count of matching row-column pairs with the count at the leaf node. This has the same time complexity `O(N^2)` and space complexity `O(N^2)` but is still more space-efficient than the hash map using serialization (lot of subarrays can be repeated in the keys).

```cpp
class TrieNode {
public:
    unordered_map<int, TrieNode*> children;
    bool isTerminal;
    int count;
    TrieNode(): isTerminal(false), count(0) {}
};

class Solution {
public:
    int equalPairs(vector<vector<int>>& grid) {
        int n = grid.size();
        TrieNode *root = new TrieNode();
        for (int i=0; i<n; i++) {
            TrieNode* curr = root;
            for (int j=0; j<n; j++) {
                if (curr -> children.count(grid[i][j]) == 0) {
                    curr -> children[grid[i][j]] = new TrieNode();
                }
                curr = curr -> children[grid[i][j]];
            }
            curr -> isTerminal = true;
            curr -> count++;
        }
        int count = 0;
        for (int j=0; j<n; j++) {
            TrieNode* curr = root;
            for (int i=0; i<n; i++) {
                if (curr -> children.count(grid[i][j]) == 0) {
                    break;
                }
                curr = curr -> children[grid[i][j]];
            }
            if (curr -> isTerminal) {
                count += curr -> count;
            }
        }
        return count;
    }
};
```

