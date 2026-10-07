# Detect Cycle in an Undirected Graph using BFS

## 1. Problem

Given an undirected graph with `V` vertices and a list of edges, determine whether the graph contains a cycle.

For example:

    0 ---- 1
    |      |
    |      |
    2 ---- 3

This graph contains a cycle:

    0 → 1 → 3 → 2 → 0

We need to return:

    True  → if a cycle exists
    False → if there is no cycle

---

# 2. Important Concept

For an undirected graph, when we move from one node to another, we can always move back.

For example:

    0 ---- 1

If we go:

    0 → 1

then from `1` we will see `0` again.

But this does NOT mean there is a cycle.

Why?

Because `0` is simply the node from which we came.

Therefore, when detecting a cycle, we need to remember the:

    Parent of every node

This gives us the important rule:

    If a neighbor is already visited
    AND
    that neighbor is NOT the current node's parent

    → Cycle exists.

---

# 3. Main BFS Logic

We use BFS with:

    (node, parent)

inside the queue.

For example:

    queue = [(0, -1)]

Here:

    node = 0
    parent = -1

`-1` means that node `0` has no parent because it is our starting node.

---

# 4. Why Do We Need Parent?

Consider:

    0 ---- 1

We start at `0`.

    0
    ↓
    1

When we are at node `1`, its neighbor is `0`.

Node `0` is already visited.

If we simply say:

    "Already visited → cycle"

we would incorrectly detect a cycle.

But `0` is just the parent of `1`.

So:

    neighbor == parent

means:

    "This is the edge we came from."

Therefore, we ignore it.

---

# 5. Actual Cycle Situation

Consider:

    0 ---- 1
    |      |
    |      |
    2 ---- 3

Suppose BFS visits:

    0
   / \
  1   2
   \ /
    3

Now imagine we are at node `1`.

Its neighbors are:

    0
    3

`0` is already visited.

But:

    parent of 1 = 0

So:

    neighbor == parent

This is normal.

Now suppose we see `3`.

If `3` has already been visited and:

    neighbor != parent

then we found a cycle.

---

# 6. Graph Representation

We first convert the edge list into an adjacency list.

Suppose:

    edges = [
        [0, 1],
        [1, 2],
        [2, 0]
    ]

The graph is:

    0 ---- 1
     \     /
      \   /
        2

The adjacency list becomes:

    graph[0] = [1, 2]
    graph[1] = [0, 2]
    graph[2] = [1, 0]

Because the graph is undirected, every edge must be stored in both directions.

For:

    0 ---- 1

we store:

    graph[0].append(1)
    graph[1].append(0)

---

# 7. Why We Use a List of Lists

Instead of:

    graph = {}

we use:

    graph = [[] for _ in range(V)]

If:

    V = 4

we initially get:

    graph = [
        [],
        [],
        [],
        []
    ]

Then we add the edges.

This makes the adjacency list easy to use.

---

# 8. Visited Array

We create:

    visited = [False] * V

For example:

    V = 4

Initially:

    visited = [False, False, False, False]

When we visit node `0`:

    visited[0] = True

Now:

    visited = [True, False, False, False]

The purpose of `visited` is simply:

    Have we already visited this node?

The parent information is NOT stored in `visited`.

Parent is stored separately in the queue.

---

# 9. Queue Structure

Our queue stores:

    (node, parent)

For example:

    queue = [(0, -1)]

After visiting node `1` from `0`:

    queue = [(1, 0)]

This means:

    current node = 1
    parent = 0

After visiting node `2` from `0`:

    queue = [(1, 0), (2, 0)]

---

# 10. Complete Algorithm

The algorithm is:

### Step 1

Create the adjacency list.

### Step 2

Create a visited array.

### Step 3

Go through every vertex.

Why every vertex?

Because the graph may be disconnected.

For example:

    0 ---- 1        2 ---- 3
                     \    /
                       4

The first component and second component are separate.

If we start BFS only from `0`, we will never check the second component.

Therefore:

    for start in range(V):

We start BFS whenever we find an unvisited node.

### Step 4

Put:

    (start, -1)

into the queue.

### Step 5

Perform BFS.

For every neighbor:

#### Case 1 — Neighbor is not visited

Visit it:

    visited[neighbor] = True

and store:

    (neighbor, current_node)

in the queue.

#### Case 2 — Neighbor is already visited

Check:

    neighbor != parent

If true:

    Cycle exists.

---

# 11. Complete Code

```python
from collections import deque

class Solution:
    def isCycle(self, V, edges):

        # --------------------------------
        # Step 1: Build adjacency list
        # --------------------------------

        graph = [[] for _ in range(V)]

        for a, b in edges:
            graph[a].append(b)
            graph[b].append(a)

        # --------------------------------
        # Step 2: Visited array
        # --------------------------------

        visited = [False] * V

        # --------------------------------
        # Step 3: Check every component
        # --------------------------------

        for start in range(V):

            # Already visited means this
            # component was already checked
            if visited[start]:
                continue

            # --------------------------------
            # Step 4: Start BFS
            # --------------------------------

            queue = deque()

            # Store:
            # (current_node, parent_node)

            queue.append((start, -1))

            visited[start] = True

            # --------------------------------
            # Step 5: BFS
            # --------------------------------

            while queue:

                node, parent = queue.popleft()

                # Check all neighbors
                for neighbor in graph[node]:

                    # Case 1:
                    # Neighbor has not been visited
                    if not visited[neighbor]:

                        visited[neighbor] = True

                        # Current node becomes
                        # the parent of neighbor
                        queue.append((neighbor, node))

                    # Case 2:
                    # Neighbor is already visited
                    # and it is NOT our parent
                    elif neighbor != parent:

                        return True

        # No cycle found
        return False
