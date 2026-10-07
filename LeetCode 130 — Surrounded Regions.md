
# LeetCode 130 — Surrounded Regions

## Problem

We are given a 2D board containing only:

- `X`
- `O`

We need to change every `O` that is completely surrounded by `X` into `X`.

An `O` should **not** be changed if it is connected to the boundary of the board.

---

# Example

Input:

    X X X X
    X O O X
    X X O X
    X O X X

Output:

    X X X X
    X X X X
    X X X X
    X O X X

Why?

The `O` at the bottom is connected to the boundary, so it is safe.

The other `O`s are surrounded by `X`, so they are converted to `X`.

---

# Main Idea

Instead of directly trying to find which `O`s are surrounded, we do the opposite.

We find all the `O`s that are **NOT surrounded**.

How?

An `O` that is connected to the boundary can never be surrounded.

So:

1. Find all boundary `O`s.
2. Put all boundary `O`s into the queue.
3. Run Multi-Source BFS from all of them.
4. During BFS, visit every `O` connected to the boundary.
5. These visited `O`s are safe.
6. Finally, scan the entire board.
7. Any `O` that is still unvisited is surrounded.
8. Convert those unvisited `O`s into `X`.

---

# Why Do We Start From Boundary O's?

Consider:

    X X X X X
    X O O O X
    X X X O X
    X X X X X

The `O` cells in the middle are connected to the boundary.

If an `O` is connected to the boundary, there is a path from that `O` to the outside of the board.

Therefore, it cannot be completely surrounded.

So we start BFS from the boundary `O`s.

---

# Important Observation

The problem becomes:

    Find all O's connected to the boundary.

Then:

    Visited O  → Safe → Keep it as O

    Unvisited O → Surrounded → Change it to X

This makes the problem much easier.

---

# Step-by-Step Logic

## Step 1 — Create a visited matrix

We create:

    visited = [[False] * cols for _ in range(rows)]

This tells us whether an `O` has already been reached by our BFS.

Initially:

    False False False False
    False False False False
    False False False False
    False False False False

---

# Step 2 — Find Boundary O's

A cell is a boundary cell if:

    row == 0

OR

    row == rows - 1

OR

    col == 0

OR

    col == cols - 1

We check all boundary cells.

If the boundary cell contains `O`:

    Add it to the queue.

    Mark it as visited.

Example:

    O O X X
    X O O X
    X X O X
    X X X O

The boundary `O`s are:

    Top-left O
    Top-second O
    Bottom-right O

We put all of them into the queue.

This is why this is called **Multi-Source BFS**.

Instead of having one starting point, we have multiple starting points.

---

# Step 3 — Run BFS

Now we process the queue.

For every cell removed from the queue, we check its four neighbors:

    Left
    Right
    Up
    Down

If the neighbor:

1. Is inside the board
2. Contains `O`
3. Has not been visited

Then:

    Mark it visited.

    Add it to the queue.

We do NOT change it to `X`.

We are only identifying safe `O`s.

---

# Step 4 — Finish BFS

After BFS finishes, every `O` that is connected to a boundary `O` will be marked as visited.

For example:

    X X X X X
    O O O X X
    X X O X X
    X O O X X

The BFS starting from the boundary `O`s may mark:

    O O O
        O
    O O

as visited.

Those cells are safe.

---

# Step 5 — Find the Remaining O's

Now we scan the entire board again.

For every cell:

    if board[row][col] == 'O'
    and visited[row][col] == False

Then that `O` was never reached from a boundary `O`.

Therefore, it is surrounded.

So we change:

    O → X

---

# Final Logic

The complete logic is:

    Find boundary O's
            ↓
    Put them into queue
            ↓
    Mark them visited
            ↓
    Multi-Source BFS
            ↓
    Visit every O connected to boundary
            ↓
    Scan the board
            ↓
    Unvisited O → X
            ↓
    Visited O → Keep O

---

# Complete Code

```python
from collections import deque

class Solution:
    def solve(self, board: list[list[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """

        rows = len(board)
        cols = len(board[0])

        visited = [[False] * cols for _ in range(rows)]

        directions = [
            (0, -1),   # left
            (0, 1),    # right
            (-1, 0),   # up
            (1, 0)     # down
        ]

        queue = deque()

        # Step 1:
        # Find all boundary O's and put them into the queue.
        for row in range(rows):
            for col in range(cols):

                if (row == 0 or row == rows - 1 or
                    col == 0 or col == cols - 1):

                    if board[row][col] == 'O':
                        queue.append((row, col))
                        visited[row][col] = True

        # Step 2:
        # Multi-source BFS
        while queue:

            pr, pc = queue.popleft()

            for dr, dc in directions:

                nr = pr + dr
                nc = pc + dc

                # Check if the neighbor is outside the board
                if nr < 0 or nr >= rows or nc < 0 or nc >= cols:
                    continue

                # If the neighbor is an unvisited O,
                # it is connected to the boundary.
                if board[nr][nc] == 'O' and not visited[nr][nc]:

                    visited[nr][nc] = True
                    queue.append((nr, nc))

        # Step 3:
        # Any unvisited O is surrounded.
        for row in range(rows):
            for col in range(cols):

                if board[row][col] == 'O' and not visited[row][col]:
                    board[row][col] = 'X'
