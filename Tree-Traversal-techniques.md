# 🌳 Tree Traversal Patterns — Interview Revision

A quick-reference guide for solving Binary Tree problems in coding interviews.

---

## 📌 Tree Traversal Overview

There are two main ways to traverse a tree:

```text
                    TREE
                      │
             ┌────────┴────────┐
             │                 │
            DFS               BFS
             │                 │
      ┌──────┼──────┐          │
      │      │      │          │
   Preorder Inorder Postorder  Level Order
```

### Core Traversals

| Traversal   | Order               | Data Structure    |
| ----------- | ------------------- | ----------------- |
| Preorder    | Root → Left → Right | Recursion / Stack |
| Inorder     | Left → Root → Right | Recursion / Stack |
| Postorder   | Left → Right → Root | Recursion / Stack |
| Level Order | Level by Level      | Queue             |

---

# 1️⃣ Preorder DFS

### Pattern

```text
ROOT → LEFT → RIGHT
```

### Recursive Template

```python
def preorder(root):
    if root is None:
        return

    # Process ROOT
    print(root.val)

    # LEFT
    preorder(root.left)

    # RIGHT
    preorder(root.right)
```

### Mental Pattern

```text
PROCESS
   ↓
LEFT
   ↓
RIGHT
```

### Common Uses

* Root-to-leaf problems
* Serialize a tree
* Copy/clone a tree
* Process parent before children
* Tree construction

---

# 2️⃣ Inorder DFS ⭐

### Pattern

```text
LEFT → ROOT → RIGHT
```

### Recursive Template

```python
def inorder(root):
    if root is None:
        return

    # LEFT
    inorder(root.left)

    # Process ROOT
    print(root.val)

    # RIGHT
    inorder(root.right)
```

### Mental Pattern

```text
LEFT
 ↓
PROCESS
 ↓
RIGHT
```

### ⭐ Important BST Property

For a Binary Search Tree:

```text
Inorder Traversal
       ↓
Sorted Order
```

Example:

```text
        5
       / \
      3   7
     / \   \
    2   4   8
```

Inorder:

```text
2 → 3 → 4 → 5 → 7 → 8
```

### Common Uses

* Validate BST
* Kth smallest element
* Kth largest element
* BST Iterator
* Getting sorted values from BST

---

# 3️⃣ Postorder DFS ⭐⭐⭐

### Pattern

```text
LEFT → RIGHT → ROOT
```

### Recursive Template

```python
def postorder(root):
    if root is None:
        return

    # LEFT
    postorder(root.left)

    # RIGHT
    postorder(root.right)

    # Process ROOT
    print(root.val)
```

### Mental Pattern

```text
LEFT
 ↓
RIGHT
 ↓
PROCESS
```

### Key Idea

> Children first → Parent later

The parent often needs information from its children before it can calculate its own answer.

### Common Uses

* Maximum depth
* Diameter of binary tree
* Balanced binary tree
* Maximum path sum
* Tree Dynamic Programming
* Subtree calculations

---

# 4️⃣ Level Order BFS ⭐⭐⭐

### Pattern

```text
LEVEL 1
LEVEL 2
LEVEL 3
...
```

### Template

```python
from collections import deque

def levelOrder(root):
    if root is None:
        return []

    queue = deque([root])
    result = []

    while queue:

        # Number of nodes in current level
        level_size = len(queue)

        level = []

        for _ in range(level_size):

            node = queue.popleft()

            # Process node
            level.append(node.val)

            if node.left:
                queue.append(node.left)

            if node.right:
                queue.append(node.right)

        result.append(level)

    return result
```

### Mental Pattern

```text
QUEUE
  ↓
while queue
  ↓
level_size = len(queue)
  ↓
process current level
  ↓
add children
```

### Common Uses

* Level Order Traversal
* Zigzag Traversal
* Right Side View
* Left Side View
* Average of Levels
* Minimum Depth
* Top/Bottom View
* Vertical Traversal

---

# 5️⃣ Iterative DFS Using Stack

Recursion internally uses a call stack.

We can replace recursion with our own stack.

---

## Preorder — Iterative

### Pattern

```text
ROOT → LEFT → RIGHT
```

### Template

```python
def preorder(root):
    if root is None:
        return []

    stack = [root]
    result = []

    while stack:

        node = stack.pop()

        # Process ROOT
        result.append(node.val)

        # RIGHT first
        if node.right:
            stack.append(node.right)

        # LEFT second
        if node.left:
            stack.append(node.left)

    return result
```

### Why RIGHT first?

Stack follows:

```text
LIFO
Last In → First Out
```

We want:

```text
LEFT before RIGHT
```

So push:

```text
RIGHT
LEFT
```

Then `LEFT` comes out first.

### Pattern to remember

```text
POP
 ↓
PROCESS
 ↓
PUSH RIGHT
 ↓
PUSH LEFT
```

---

# 6️⃣ Inorder — Iterative ⭐⭐⭐

### Pattern

```text
LEFT → ROOT → RIGHT
```

### Template

```python
def inorder(root):
    stack = []
    result = []

    current = root

    while current or stack:

        # Go as far LEFT as possible
        while current:
            stack.append(current)
            current = current.left

        # Process node
        current = stack.pop()
        result.append(current.val)

        # Go RIGHT
        current = current.right

    return result
```

### Mental Pattern

```text
1. Go LEFT
2. Push nodes
3. Pop
4. Process
5. Go RIGHT
6. Repeat
```

### Important

This pattern is especially useful for BST problems because:

```text
BST
 ↓
Inorder
 ↓
Sorted order
```

---

# 7️⃣ Postorder — Iterative

### Pattern

```text
LEFT → RIGHT → ROOT
```

A simple approach uses two stacks.

### Template

```python
def postorder(root):
    if root is None:
        return []

    stack1 = [root]
    stack2 = []
    result = []

    while stack1:

        node = stack1.pop()

        stack2.append(node)

        if node.left:
            stack1.append(node.left)

        if node.right:
            stack1.append(node.right)

    while stack2:

        node = stack2.pop()

        result.append(node.val)

    return result
```

### Mental Pattern

First generate:

```text
ROOT → RIGHT → LEFT
```

Then reverse:

```text
LEFT → RIGHT → ROOT
```

```text
Stack 1
   ↓
ROOT → RIGHT → LEFT
   ↓
Stack 2
   ↓
LEFT → RIGHT → ROOT
```

---

# 8️⃣ DFS — Returning Information ⭐⭐⭐⭐⭐

This is one of the most important patterns for solving tree problems.

### General Template

```python
def dfs(root):

    if root is None:
        return BASE_CASE

    left = dfs(root.left)
    right = dfs(root.right)

    answer = COMBINE(root, left, right)

    return answer
```

### Example — Maximum Depth

```python
def maxDepth(root):

    if root is None:
        return 0

    left = maxDepth(root.left)
    right = maxDepth(root.right)

    return 1 + max(left, right)
```

### Mental Model

```text
        ROOT
       /    \
      ↓      ↓
    LEFT    RIGHT
      ↓      ↓
    answer  answer
       \     /
        \   /
         ROOT
          ↓
       calculate
```

### Key Idea

```text
CHILDREN
    ↓
return information
    ↓
PARENT
    ↓
calculate answer
```

### Common Problems

* Maximum Depth
* Diameter of Binary Tree
* Balanced Binary Tree
* Maximum Path Sum
* Tree DP
* Subtree problems

---

# 9️⃣ DFS — Passing Information Down ⭐⭐⭐⭐

Sometimes the parent gives information to its children.

### General Template

```python
def dfs(root, information):

    if root is None:
        return

    # Update information
    information = UPDATE(information, root)

    # Pass information to children
    dfs(root.left, information)
    dfs(root.right, information)
```

### Example — Path Sum

```python
def dfs(root, current_sum):

    if root is None:
        return False

    current_sum += root.val

    # Leaf node
    if root.left is None and root.right is None:
        return current_sum == target

    return (
        dfs(root.left, current_sum)
        or
        dfs(root.right, current_sum)
    )
```

### Mental Model

```text
PARENT
   │
   │ information
   ↓
 CHILD
   │
   │ updated information
   ↓
GRANDCHILD
```

### Common Uses

* Root-to-leaf path
* Path Sum
* Current maximum/minimum
* Depth tracking
* Passing constraints
* Backtracking on trees

---

# 🧠 Master Tree Cheat Sheet

## Recursive DFS

```python
def dfs(root):

    if not root:
        return

    # PREORDER
    process(root)

    dfs(root.left)

    # INORDER
    process(root)

    dfs(root.right)

    # POSTORDER
    process(root)
```

The position of `process(root)` determines the traversal:

```text
Before children  → PREORDER

Between children → INORDER

After children   → POSTORDER
```

---

# 🔥 Which Pattern Should I Use?

| Problem Type                   | Think            |
| ------------------------------ | ---------------- |
| Process parent before children | Preorder         |
| BST + sorted order             | Inorder          |
| Need child answers first       | Postorder        |
| Level by level                 | BFS              |
| Side views                     | BFS              |
| Zigzag                         | BFS              |
| Height                         | Postorder        |
| Diameter                       | Postorder        |
| Balanced Tree                  | Postorder        |
| Maximum Path Sum               | Postorder        |
| Root → Leaf information        | Top-down DFS     |
| Information from children      | Bottom-up DFS    |
| BST validation                 | Inorder / bounds |
| Kth smallest BST               | Inorder          |
| Vertical/Top/Bottom view       | BFS/DFS + column |

---

# 🎯 Tree Problem-Solving Framework

Whenever you see a new tree problem, ask:

```text
1. Do I need to go level-by-level?
       ↓
      YES → BFS + Queue


2. Do I need information from children?
       ↓
      YES → DFS + Return information


3. Do I need to pass information from parent?
       ↓
      YES → Top-down DFS


4. Is it a BST and do I need sorted order?
       ↓
      YES → Inorder


5. Do I need to process parent before children?
       ↓
      YES → Preorder


6. Do I need children before parent?
       ↓
      YES → Postorder
```

---

# 📌 Traversal Quick Reference

```text
PREORDER
Root → Left → Right
      ↓
Parent first


INORDER
Left → Root → Right
      ↓
BST → Sorted


POSTORDER
Left → Right → Root
      ↓
Children first


BFS
Level → Level → Level
      ↓
Queue
```

---

# ⏱️ Complexity

For a tree with `N` nodes:

```text
Time:  O(N)
```

because every node is visited once.

### Recursive DFS

```text
Space: O(H)
```

where `H` = height of tree.

Worst case:

```text
O(N)
```

Balanced tree:

```text
O(log N)
```

### BFS

```text
Space: O(W)
```

where `W` = maximum width of the tree.

Worst case:

```text
O(N)
```

---

# ⭐ Must-Memorize Templates

Before solving difficult tree problems, be able to write these without looking:

```text
✓ Recursive Preorder
✓ Recursive Inorder
✓ Recursive Postorder
✓ Level Order BFS
✓ Iterative Preorder
✓ Iterative Inorder
✓ Iterative Postorder
✓ DFS returning information
✓ DFS passing information
```

Once these become automatic, focus on recognizing the **pattern behind the problem**, rather than memorizing individual solutions.
