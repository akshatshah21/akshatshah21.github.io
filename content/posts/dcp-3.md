---
title: "Coding Problem: Serialize a Binary Tree"
date: 2021-08-28T11:14:53+05:30
tags: ["data-structures", "algorithms"]
draft: false
---

> [DailyCodingProblem](https://www.dailycodingproblem.com/) is a great website that sends coding problems to your inbox daily.

## The Question

> Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

You can solve this question on [LeetCode](https://leetcode.com/problems/serialize-and-deserialize-binary-tree)

## Solutions

### Recursion!

We can use preorder traversal of a tree to encode it as a string. We start with the root, encode it, and then move on to the left subtree and serialize it, then move on to the right tree and serialize it. The base case will be when we call the recursive function for a child of a leaf node, we can encode it as "null".

```
fun serialize(root)
  if root is null then return "null"
  return str(root->val) + "," + serialize(root->left) + "," + serialize(root->right)
```

For deserializing a string of the type formed by `serialize`, we can again make use of the preorder traversal, since we know that is the order in which the serialized string was built.

First, we can split the string by its delimiter (a comma, in this case) to get a list of nodes (including null nodes) and then traverse the list, while we build the tree in preorder traversal. We do this by keeping the pointer of the list common across all recursive calls, and each recursive call builds a subtree, in preorder. Whenever we hit a null element in the list, we simply return null.

```
fun deserialize(tree)
  nodes = tree.split(",")
  i = 0
  root = buildTree(nodes, i)
  return root

fun buildTree(nodes, i)
  if nodes[i] == "null" then
    return null
  else
    root = Node(int(root))
    i = i + 1
    root.left = buildTree(nodes, i)
    i = i + 1
    root.right = buildTree(nodes, i)
    return root
```

You can find the Python implementation [here](https://github.com/akshatshah21/Data-Structures-and-Algorithms/blob/python/Python/BinaryTree/Serialize_Deserialize_Binary_Tree.py).
This can be implemented in C++ easily too, but right now I'm a little lazy to write out the split function :-).
