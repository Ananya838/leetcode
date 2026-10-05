# LeetCode 200 — Number of Islands

## 1. Main Idea

This problem is a **Connected Components** problem on a grid.

* `"1"` = land
* `"0"` = water
* Connected `"1"` cells = one island

So the logic is:

```text
Find an unvisited land
        ↓
Start BFS/DFS
        ↓
Visit the complete connected island
        ↓
count += 1
        ↓
Continue scanning the grid
```

The most important rule:

> **One BFS/DFS call = One connected component (one island).**

---

# 2. Why Do We Check Every Direction?

From the current cell, the island can continue in any of the 4 allowed directions:

```text
          UP
           ↑
           |
LEFT ← CURRENT → RIGHT
           |
           ↓
         DOWN
```

So we use:

```python
directions = [
    (0, -1),   # left
    (0, 1),    # right
    (-1, 0),   # up
    (1, 0)     # down
]
```

For every cell we visit, we must check **all four directions** because we don't know where the remaining part of the island is.

For example:

```text
1 1 0
0 1 0
0 1 1
```

Starting from the first `1`, the island continues:

```text
1 → 1
    ↓
    1
    ↓
    1 → 1
```

If we checked only right, we would miss the cells connected through down.

Therefore:

> **For every current cell → check every allowed direction.**

---

# 3. How Do We Find the Neighbor?

Suppose the current cell is:

```python
(row, col)
```

For every direction:

```python
for dr, dc in directions:
```

we calculate:

```python
nr = row + dr
nc = col + dc
```

For example:

```text
(0, -1) → left
(0, 1)  → right
(-1, 0) → up
(1, 0)  → down
```

`dr` changes the row and `dc` changes the column.

---

# 4. Boundary Check

A direction may take us outside the grid.

For example, from row `0`, moving UP gives:

```text
nr = -1
```

which is invalid.

So:

```python
if nr < 0 or nr >= rows or nc < 0 or nc >= cols:
    continue
```

means:

> This neighbor is outside the grid, so ignore it.

---

# 5. Which Neighbors Do We Visit?

After checking that the position is valid:

```python
if grid[nr][nc] == "1" and not visited[nr][nc]:
```

we check:

1. Is it land?
2. Is it unvisited?

If both are true:

```python
visited[nr][nc] = True
queue.append((nr, nc))
```

We mark it immediately and put it into the queue.

---

# 6. Why Mark Visited Immediately?

Suppose:

```text
1 1
1 1
```

Several cells can reach the same cell.

If we don't mark a cell when we discover it, the same cell might be added to the queue multiple times.

Therefore:

```text
Discover cell
     ↓
Mark visited immediately
     ↓
Add to queue
```

This prevents duplicate processing.

---

# 7. Why Do We Scan the Whole Grid?

BFS only finds **one island**.

Example:

```text
1 1 0 0
0 0 0 1
1 0 0 1
```

Starting BFS from the first `1` only explores:

```text
1 1
```

It cannot magically explore the separate island on the right.

So after BFS finishes, we continue scanning.

When we find another:

```text
1 + unvisited
```

we start another BFS.

Therefore:

```python
count += 1
```

is done **once for every new BFS/DFS**.

---

# 8. Complete Logic

```text
Scan every cell
      ↓
Is it land + unvisited?
      ↓
     YES
      ↓
Start BFS/DFS
      ↓
Explore the entire component
      ↓
Check all allowed directions
      ↓
Visit valid connected neighbors
      ↓
BFS finishes
      ↓
count += 1
      ↓
Continue scanning
```

---

# 9. Connected Components

A **connected component** is a group of nodes where every node belongs to the same connected group.

Example:

```text
1 -- 2 -- 3       5 -- 6

4                  7
```

There are:

```text
Component 1 → {1,2,3}
Component 2 → {4}
Component 3 → {5,6}
Component 4 → {7}
```

Therefore:

```text
Number of connected components = 4
```

The same idea applies to Number of Islands:

```text
Connected group of 1s = Connected Component
```

So:

```text
Number of Islands
        =
Number of Connected Components in a Grid
```

---

# 10. The Most Common Connected Components Template

This is the template you should remember.

## General Graph

```python
visited = set()
count = 0

for node in graph:

    if node not in visited:

        bfs(node)

        count += 1
```

BFS:

```python
def bfs(start):

    visited.add(start)

    queue = deque([start])

    while queue:

        node = queue.popleft()

        for neighbor in graph[node]:

            if neighbor not in visited:

                visited.add(neighbor)
                queue.append(neighbor)
```

---

# 11. Grid Version of the Same Template

For a grid:

```python
visited = [[False] * cols for _ in range(rows)]

directions = [
    (0, -1),
    (0, 1),
    (-1, 0),
    (1, 0)
]

count = 0

for row in range(rows):
    for col in range(cols):

        if grid[row][col] == "1" and not visited[row][col]:

            bfs(row, col)

            count += 1
```

The only major difference is:

```text
Graph → neighbors come from graph[node]

Grid → neighbors come from directions
```

The **connected-component logic remains exactly the same**.

---

# 12. What to Remember for Interviews

When you see words like:

```text
islands
groups
clusters
provinces
regions
connected components
separate networks
```

think:

```text
CONNECTED COMPONENTS
        ↓
Need to visit each component
        ↓
Use BFS or DFS
        ↓
Need visited
        ↓
Scan every node/cell
        ↓
Unvisited → BFS/DFS
        ↓
count++
```

## The Golden Template

```python
count = 0

for every node/cell:

    if valid and not visited:

        BFS/DFS(node)

        count += 1
```

And inside BFS/DFS:

```text
Take current node
      ↓
Check all its neighbors
      ↓
If neighbor is valid + unvisited
      ↓
Mark visited
      ↓
Add/visit neighbor
      ↓
Continue until component is completely explored
```

### Final rule to remember:

> **Scan → Find an unvisited node → Explore its entire component → Count it → Continue scanning.**

That is the core logic behind **Connected Components**, **Number of Islands**, and many other BFS/DFS problems.


########################################################################################################################################################################
```
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0
        rows = len(grid)
        cols = len(grid[0])

        visited = [[False]*cols for _ in range(rows)]
        
        directions = [
                (0,-1),
                (0,1),
                (-1,0),
                (1,0)
            ]
        def bfs(row,col):
           
            visited[row][col] = True

            queue = deque([(row,col)])

            while queue:
                prow ,pcol = queue.popleft()

                for dr , dc in directions:

                    nr = prow + dr
                    nc = pcol + dc

                    if (nr < 0 or nr >= rows ) or (nc < 0 or nc >= cols):
                        continue
                    
                    if grid[nr][nc] == "1" and not visited[nr][nc]:
                        queue.append((nr,nc))
                        visited[nr][nc] = True
        count = 0

        #### main logic for connected components it is almost same for every cc propblem  
        for row in range(rows):
            for col in range(cols):
                if grid[row][col] == "1" and not visited[row][col]:
                    bfs(row,col)
                    count+=1
        return count    
```
        
        
