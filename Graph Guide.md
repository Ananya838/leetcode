# Graphs — DSA Revision Guide

> A practical guide to understand Graphs, represent them, traverse them, and recognize common interview patterns.

---

## 1. What is a Graph?

A **Graph** is a data structure used to represent relationships or connections between objects.

A graph consists of:

* **Vertices (Nodes)** → the objects
* **Edges** → the connections between objects

Example:

```text
      0
     / \
    1---2
     \
      3
```

Here:

```text
Vertices = {0, 1, 2, 3}

Edges = {
    (0,1),
    (0,2),
    (1,2),
    (1,3)
}
```

### Real-world examples

Graphs are useful for representing:

* Cities connected by roads
* People connected through friendships
* Computers connected through a network
* Web pages connected through links
* Courses connected through prerequisites
* Airports connected through flights
* Social-media followers
* Dependencies between tasks

---

# 2. Important Graph Terminology

### Vertex / Node

A single element in a graph.

```text
0
```

### Edge

A connection between two vertices.

```text
0 ---- 1
```

### Adjacent Nodes

Two nodes are **adjacent** if there is an edge between them.

```text
0 ---- 1
```

`0` and `1` are adjacent.

### Degree

The number of edges connected to a vertex.

```text
      1
      |
2 --- 0 --- 3
```

Degree of `0` = `3`.

For a directed graph:

* **Indegree** → number of incoming edges
* **Outdegree** → number of outgoing edges

---

# 3. Types of Graphs

## 3.1 Undirected Graph

Edges have no direction.

```text
0 ----- 1
```

Means:

```text
0 → 1
1 → 0
```

Example:

```python
edges = [
    [0, 1],
    [1, 2]
]
```

If an edge `[0,1]` exists, we can travel both ways.

Common examples:

* Friendships
* Roads that allow travel in both directions
* Network connections

---

## 3.2 Directed Graph

Edges have a direction.

```text
0 ----> 1
```

We can travel:

```text
0 → 1
```

but not necessarily:

```text
1 → 0
```

Example:

```python
edges = [
    [0, 1],
    [1, 2]
]
```

---

## 3.3 Weighted Graph

Edges have a value/cost.

```text
      5
0 -------- 1
 \        /
  2      3
   \    /
     2
```

Example:

```python
edges = [
    [0, 1, 5],
    [0, 2, 2]
]
```

The third value represents the weight.

Common examples:

* Distance between cities
* Cost of flights
* Network latency
* Time required to travel

---

## 3.4 Unweighted Graph

Every edge has the same cost.

```text
0 ---- 1 ---- 2
```

Usually represented simply as:

```python
[0, 1]
```

---

## 3.5 Connected Graph

A graph is connected if every node can be reached from every other node.

```text
0 --- 1 --- 2
     |
     3
```

All nodes belong to one connected component.

---

## 3.6 Disconnected Graph

Some nodes cannot reach other nodes.

```text
0 --- 1       2 --- 3
```

There are two connected components:

```text
Component 1 → {0,1}

Component 2 → {2,3}
```

---

## 3.7 Cyclic Graph

A graph containing a cycle.

```text
    0
   / \
  1---2
```

We can travel:

```text
0 → 1 → 2 → 0
```

---

## 3.8 Acyclic Graph

A graph with no cycles.

```text
0 --- 1 --- 2
     |
     3
```

---

## 3.9 DAG — Directed Acyclic Graph

A directed graph with no cycles.

```text
0 → 1 → 3
↓
2 → 3
```

DAGs are extremely important for:

* Course prerequisites
* Task scheduling
* Dependency resolution
* Build systems

Topological Sort is commonly used with DAGs.

---

# 4. Graph Representation

Before applying DFS or BFS, we need a way to **store and access the graph**.

The two most important representations are:

1. Adjacency Matrix
2. Adjacency List

---

# 5. Adjacency Matrix

An adjacency matrix uses a 2D array.

For:

```text
0 ----- 1
|       |
|       |
2 ----- 3
```

We can represent it as:

```python
graph = [
    [0, 1, 1, 0],
    [1, 0, 0, 1],
    [1, 0, 0, 1],
    [0, 1, 1, 0]
]
```

If:

```python
graph[i][j] == 1
```

there is an edge between `i` and `j`.

If:

```python
graph[i][j] == 0
```

there is no edge.

### Accessing neighbors

For node `0`:

```python
graph[0]
```

gives:

```text
[0, 1, 1, 0]
```

We scan the row to find its neighbors:

```text
0 → 1
0 → 2
```

### Complexity

Finding all neighbors of one node:

```text
O(V)
```

DFS/BFS using the matrix:

```text
O(V²)
```

Space:

```text
O(V²)
```

---

# 6. Adjacency List

An adjacency list stores the neighbors of each node directly.

For:

```text
0 ----- 1
|       |
|       |
2 ----- 3
```

we can store:

```python
graph = [
    [1, 2],
    [0, 3],
    [0, 3],
    [1, 2]
]
```

Meaning:

```text
0 → [1,2]
1 → [0,3]
2 → [0,3]
3 → [1,2]
```

Now if we want the neighbors of `0`:

```python
graph[0]
```

we immediately get:

```text
[1,2]
```

### Complexity

Space:

```text
O(V + E)
```

DFS/BFS:

```text
O(V + E)
```

For most interview problems, **adjacency lists are the most common representation**.

---

# 7. Converting Edges into an Adjacency List

This is extremely important for LeetCode problems.

Often the input looks like:

```python
edges = [
    [0,1],
    [1,2],
    [2,0]
]
```

The input itself is **not an adjacency list**.

We build one.

### Step 1

Create empty lists:

```python
graph = [[] for _ in range(n)]
```

For `n = 3`:

```text
0 → []
1 → []
2 → []
```

### Step 2

Process every edge:

```python
for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)
```

Because this is an **undirected graph**, we add both directions.

Final result:

```python
graph = [
    [1, 2],
    [0, 2],
    [1, 0]
]
```

Visual representation:

```text
     0
    / \
   /   \
  1-----2
```

---

# 8. The Most Important Graph Concept: Traversal

Once we can access the neighbors of a node, we need to **visit the graph**.

The two fundamental traversal techniques are:

* DFS — Depth First Search
* BFS — Breadth First Search

---

# 9. DFS — Depth First Search

DFS goes **as deep as possible before coming back**.

Example:

```text
      0
     / \
    1   2
    |
    3
```

Starting at `0`, one possible DFS order is:

```text
0 → 1 → 3 → 2
```

The idea is:

```text
Visit node
   ↓
Visit a neighbor
   ↓
Go deeper
   ↓
No more unvisited neighbors
   ↓
Backtrack
```

---

# 10. DFS Using Recursion

```python
visited = set()

def dfs(node):

    if node in visited:
        return

    visited.add(node)

    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor)
```

Start:

```python
dfs(source)
```

### Why do we need `visited`?

Graphs can contain cycles.

Example:

```text
0 → 1 → 2
    ↑   |
    └───┘
```

Without `visited`, DFS could keep doing:

```text
1 → 2 → 1 → 2 → 1 → 2 → ...
```

So:

```python
visited = set()
```

prevents repeated traversal.

---

# 11. DFS Using a Stack

Recursive DFS uses the **call stack** internally.

We can implement DFS explicitly using a stack:

```python
stack = [source]
visited = set()

while stack:

    node = stack.pop()

    if node in visited:
        continue

    visited.add(node)

    for neighbor in graph[node]:

        if neighbor not in visited:
            stack.append(neighbor)
```

Remember:

```text
DFS → Stack → LIFO
```

---

# 12. BFS — Breadth First Search

BFS explores the graph **level by level**.

Example:

```text
        0
      /   \
     1     2
    / \
   3   4
```

BFS:

```text
0
↓
1, 2
↓
3, 4
```

Traversal:

```text
0 → 1 → 2 → 3 → 4
```

BFS uses a queue.

```python
from collections import deque

queue = deque([source])
visited = set()

while queue:

    node = queue.popleft()

    if node in visited:
        continue

    visited.add(node)

    for neighbor in graph[node]:

        if neighbor not in visited:
            queue.append(neighbor)
```

Remember:

```text
BFS → Queue → FIFO
```

---

# 13. DFS vs BFS

| Feature                           | DFS            | BFS               |
| --------------------------------- | -------------- | ----------------- |
| Data structure                    | Stack          | Queue             |
| Order                             | Go deep        | Go level-by-level |
| Recursive implementation          | Yes            | Usually no        |
| Iterative implementation          | Stack          | Queue             |
| Shortest path in unweighted graph | Not guaranteed | Yes               |
| Cycle detection                   | Yes            | Yes               |
| Connected components              | Yes            | Yes               |

### Quick memory trick

```text
DFS → Deep → Stack

BFS → Broad → Queue
```

---

# 14. Graph Traversal Complexity

Using an adjacency list:

```text
DFS → O(V + E)
BFS → O(V + E)
```

Why?

Each vertex is visited at most once:

```text
O(V)
```

Each edge is examined:

```text
O(E)
```

Therefore:

```text
O(V + E)
```

Using an adjacency matrix:

```text
DFS → O(V²)
BFS → O(V²)
```

---

# 15. Connected Components

Suppose:

```text
0 --- 1       2 --- 3       4
```

There are:

```text
3 connected components
```

We can find them using DFS.

```python
visited = set()
components = 0

for node in range(n):

    if node not in visited:

        components += 1
        dfs(node)
```

The important idea:

> If a node has not been visited after finishing the previous DFS, it belongs to a new component.

---

# 16. Cycle Detection

Cycles are common in graph problems.

Example:

```text
0
| \
|  \
1---2
```

There is a cycle:

```text
0 → 1 → 2 → 0
```

For an undirected graph, DFS can detect cycles by keeping track of the **parent**.

Basic idea:

```python
def dfs(node, parent):

    visited.add(node)

    for neighbor in graph[node]:

        if neighbor not in visited:
            if dfs(neighbor, node):
                return True

        elif neighbor != parent:
            return True

    return False
```

The `parent` check is important because in an undirected graph:

```text
0 ---- 1
```

when we are at `1`, seeing `0` again does not automatically mean there is a cycle. `0` is simply the node we came from.

---

# 17. Directed Graph Cycle Detection

For directed graphs, a common technique is to maintain three states:

```text
0 → unvisited
1 → currently visiting
2 → completely processed
```

A cycle exists if we encounter a node that is currently being visited.

This pattern is especially important for:

* Course Schedule
* Prerequisites
* Dependency graphs

---

# 18. Weighted Graphs

For weighted graphs:

```text
0 ----5---- 1
 \          /
  2        3
   \      /
      2
```

The graph needs to store both:

```text
neighbor
weight
```

Example:

```python
graph = [
    [(1, 5), (2, 2)],
    [(0, 5), (2, 3)],
    [(0, 2), (1, 3)]
]
```

Then:

```python
for neighbor, weight in graph[node]:
    ...
```

Common algorithms:

```text
Dijkstra
Bellman-Ford
Floyd-Warshall
Prim's
Kruskal's
```

---

# 19. Important Graph Algorithms

You don't need to learn every graph algorithm at once.

Focus on patterns.

### Basic Traversal

```text
DFS
BFS
```

### Connectivity

```text
Connected Components
Number of Islands
Path Existence
```

### Cycle Detection

```text
Undirected cycle
Directed cycle
```

### Topological Sorting

Used for:

```text
Prerequisites
Dependencies
Task ordering
```

Common approaches:

```text
DFS
Kahn's Algorithm (BFS)
```

### Shortest Path

Unweighted:

```text
BFS
```

Weighted, non-negative:

```text
Dijkstra
```

Negative weights:

```text
Bellman-Ford
```

All-pairs shortest path:

```text
Floyd-Warshall
```

### Minimum Spanning Tree

```text
Kruskal
Prim
```

---

# 20. Graph Problem Recognition

When you see a problem, ask these questions.

### Question 1

**What are the nodes?**

Example:

```text
cities
people
courses
cells
words
```

### Question 2

**What represents an edge?**

Example:

```text
road
friendship
prerequisite
connection
transition
```

### Question 3

**Is the graph directed or undirected?**

```text
A → B
```

or

```text
A ↔ B
```

### Question 4

**Is it weighted?**

```text
A -- 5 -- B
```

### Question 5

**Can cycles exist?**

If yes:

```python
visited
```

is usually important.

### Question 6

**Do I need to visit everything or find a shortest path?**

Usually:

```text
Explore/reachability → DFS/BFS
Shortest unweighted path → BFS
```

---

# 21. The Graph Problem Workflow

This is the workflow to remember for interviews.

```text
             GRAPH PROBLEM
                   |
                   ↓
            Identify nodes
                   |
                   ↓
            Identify edges
                   |
                   ↓
       Directed / Undirected?
                   |
                   ↓
            Weighted / Not?
                   |
                   ↓
       Build graph representation
                   |
          ┌────────┴────────┐
          ↓                 ↓
   Adjacency List     Adjacency Matrix
          |
          ↓
      Need traversal?
          |
      ┌───┴───┐
      ↓       ↓
     DFS     BFS
      |       |
   Stack/    Queue
   Recursion
      |
      ↓
   visited set
      |
      ↓
Solve the specific graph pattern
```

---

# 22. Most Important Python Templates

## Adjacency List — Undirected

```python
graph = [[] for _ in range(n)]

for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)
```

## Adjacency List — Directed

```python
graph = [[] for _ in range(n)]

for u, v in edges:
    graph[u].append(v)
```

## DFS — Recursive

```python
visited = set()

def dfs(node):

    if node in visited:
        return

    visited.add(node)

    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor)
```

## DFS — Iterative

```python
stack = [source]
visited = set()

while stack:

    node = stack.pop()

    if node in visited:
        continue

    visited.add(node)

    for neighbor in graph[node]:

        if neighbor not in visited:
            stack.append(neighbor)
```

## BFS — Iterative

```python
from collections import deque

queue = deque([source])
visited = set()

while queue:

    node = queue.popleft()

    if node in visited:
        continue

    visited.add(node)

    for neighbor in graph[node]:

        if neighbor not in visited:
            queue.append(neighbor)
```

---

# 23. Tree vs Graph

This is an important transition if you already know trees.

### Tree

```python
node.left
node.right
```

A tree node has a limited number of child references.

### Graph

```python
graph[node]
```

A graph node can have many neighbors.

### Tree DFS

```python
def dfs(node):

    if not node:
        return

    dfs(node.left)
    dfs(node.right)
```

### Graph DFS

```python
def dfs(node):

    if node in visited:
        return

    visited.add(node)

    for neighbor in graph[node]:
        dfs(neighbor)
```

The biggest difference:

```text
TREE
↓
children

GRAPH
↓
neighbors + visited
```

---

# 24. Why `visited` Is Essential

Trees normally don't need a visited set because there is no cycle.

Graphs can have:

```text
0 → 1
↑   ↓
└── 2
```

Without `visited`:

```text
0 → 1 → 2 → 0 → 1 → 2 → ...
```

With:

```python
visited = set()
```

we say:

```text
"I have already explored this node, so don't explore it again."
```

---

# 25. Interview 80/20 Graph Topics

Focus on these first:

### Tier 1 — Must Know

```text
1. Graph terminology
2. Adjacency List
3. Adjacency Matrix
4. DFS
5. BFS
6. Visited set
7. Connected Components
8. Cycle Detection
9. Grid as a Graph
```

### Tier 2 — Very Important

```text
10. Topological Sort
11. Directed Graphs
12. Shortest Path using BFS
13. Dijkstra
14. Union-Find / DSU
```

### Tier 3 — Learn Later

```text
15. Bellman-Ford
16. Floyd-Warshall
17. Minimum Spanning Tree
18. Prim's Algorithm
19. Kruskal's Algorithm
20. Advanced Graph Algorithms
```

---

# 26. Recommended Learning Order

Don't randomly solve graph problems.

Follow this order:

```text
1. Graph Basics
       ↓
2. Adjacency List
       ↓
3. Adjacency Matrix
       ↓
4. DFS — Recursive
       ↓
5. DFS — Iterative
       ↓
6. BFS
       ↓
7. Path Existence
       ↓
8. Connected Components
       ↓
9. Number of Islands / Grid DFS-BFS
       ↓
10. Cycle Detection
       ↓
11. Bipartite Graph
       ↓
12. Topological Sort
       ↓
13. Shortest Path
       ↓
14. Dijkstra
       ↓
15. Union-Find
       ↓
16. MST
```

---

# 27. Core Mental Model

When solving a graph problem, don't immediately think:

> "Which algorithm should I use?"

First think:

```text
What are my nodes?
       ↓
What are my edges?
       ↓
How can I access neighbors?
       ↓
Do I need visited?
       ↓
Do I need DFS or BFS?
       ↓
What specific property am I looking for?
```

For example:

### "Is there a path from A to B?"

Think:

```text
Graph
 ↓
Adjacency List
 ↓
DFS / BFS
 ↓
visited
 ↓
Can I reach B?
```

### "Shortest path in an unweighted graph?"

Think:

```text
Graph
 ↓
Adjacency List
 ↓
BFS
 ↓
distance
```

### "How many groups/components?"

Think:

```text
Graph
 ↓
visited
 ↓
Run DFS/BFS from every unvisited node
 ↓
Count traversals
```

### "Does a cycle exist?"

Think:

```text
Graph
 ↓
DFS/BFS
 ↓
visited
 ↓
Cycle detection logic
```

---

# 28. Quick Revision Cheat Sheet

```text
GRAPH
│
├── Nodes + Edges
│
├── Types
│   ├── Directed
│   ├── Undirected
│   ├── Weighted
│   ├── Unweighted
│   ├── Connected
│   ├── Disconnected
│   ├── Cyclic
│   ├── Acyclic
│   └── DAG
│
├── Representation
│   ├── Adjacency Matrix → O(V²) space
│   └── Adjacency List   → O(V + E) space
│
├── Traversal
│   ├── DFS
│   │   ├── Recursive
│   │   └── Stack
│   │
│   └── BFS
│       └── Queue
│
├── Important Concepts
│   ├── visited
│   ├── connected components
│   ├── cycles
│   └── shortest path
│
└── Algorithms
    ├── DFS / BFS
    ├── Topological Sort
    ├── Dijkstra
    ├── Union-Find
    ├── Prim
    └── Kruskal
```

---

# 29. Final Mental Model

The most important thing to remember:

```text
                    EDGES
                      ↓
              Build Graph
                      ↓
             Adjacency List
                      ↓
              graph[node]
                      ↓
                Neighbors
                      ↓
              ┌───────┴───────┐
              ↓               ↓
             DFS             BFS
              ↓               ↓
            Stack            Queue
              ↓               ↓
           visited          visited
              ↓               ↓
        Explore Graph    Explore Graph
```

### One-line revision

> **A graph is a collection of nodes connected by edges. Build an adjacency list to access neighbors, then use DFS/BFS with a visited set to explore the graph.**

---
