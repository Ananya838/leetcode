# Distance of Nearest Cell Having 1

## 1. Problem

Given a binary matrix containing only `0` and `1`, for every cell we need to find the **distance to the nearest cell containing `1`**.

### Example

Input:

    0 0 1
    0 0 0
    1 0 0

Output:

    2 1 0
    1 2 1
    0 1 2

### What does the output mean?

For every cell:

- If the cell itself is `1`, distance = `0`.
- If the cell is `0`, find the closest `1`.
- Distance means the number of moves using:

    Up
    Down
    Left
    Right

For example:

    0 0 1
          ↑
        distance = 0

The `0` immediately to the left of `1` has distance `1`.

The next `0` has distance `2`.

---

# 2. First Understand the Real Problem

At first, the problem may look like:

> "For every 0, search for the nearest 1."

A direct approach would be:

    For every 0:
        search the entire grid
        find the nearest 1

But this is inefficient because we may repeat the same searching many times.

Instead, we think in the opposite direction:

> "What if all the 1s start spreading their distance at the same time?"

This immediately gives us:

# Multi-Source BFS

---

# 3. What Pattern Is This?

This problem uses:

    Multi-Source BFS

It is a very important graph/grid pattern.

The main idea is:

    Multiple starting points
            ↓
    Put all starting points into queue
            ↓
    BFS from all of them simultaneously
            ↓
    First time we reach a cell
            ↓
    That is the shortest distance

Here, the starting points are:

    All cells containing 1

So:

    1 = source
    0 = cell whose distance we need

---

# 4. Why BFS?

BFS explores things level by level.

For example:

    Distance 0
        ↓
    Distance 1
        ↓
    Distance 2
        ↓
    Distance 3

Suppose we have:

    1 0 0 0

Starting from `1`:

    1 0 0 0
    ↑
    distance 0

After one step:

    1 0 0 0
      ↑
      distance 1

After two steps:

    1 0 0 0
        ↑
        distance 2

After three steps:

    1 0 0 0
          ↑
          distance 3

Therefore BFS naturally gives the shortest distance.

---

# 5. Why Multi-Source BFS?

Suppose:

    0 0 0 0 0
    0 0 1 0 0
    0 0 0 0 0
    1 0 0 0 0

There are two `1`s.

If we start BFS only from the first `1`, we might calculate distances incorrectly for cells that are actually closer to the second `1`.

Instead, both `1`s must start at the same time.

So initially:

    Queue:

    (1,2)
    (3,0)

Both have:

    distance = 0

Then BFS expands from both simultaneously.

This is exactly what Multi-Source BFS means.

---

# 6. The Most Important Mental Model

Think of every `1` as a source that sends out a wave.

Example:

    0 0 0 1 0
    0 0 0 0 0
    1 0 0 0 0

The `1`s send waves:

    distance 0:

    0 0 0 1 0
    0 0 0 0 0
    1 0 0 0 0

    distance 1:

    0 0 1 0 1
    0 1 0 1 0
    0 0 1 0 0

The waves continue expanding.

When two waves meet, every cell keeps the distance from the closest source.

That is why Multi-Source BFS solves the problem.

---

# 7. Why Put ALL 1s in the Queue First?

This is the most important part.

We first scan the entire grid:

    for every cell:
        if cell == 1:
            put it in queue
            distance = 0

So if the grid is:

    0 1 0
    0 0 0
    1 0 0

The initial queue is:

    queue = [
        (0,1),
        (2,0)
    ]

And:

    ansgrid:

    -1  0 -1
    -1 -1 -1
     0 -1 -1

All `1`s are considered starting points at the same time.

---

# 8. Why Is the Distance of 1 Equal to 0?

Because the nearest `1` to a cell containing `1` is itself.

There is no movement required.

Therefore:

    grid[r][c] == 1

means:

    ansgrid[r][c] = 0

---

# 9. What Does ansgrid Do?

We use:

    ansgrid

to store the shortest distance.

Initially:

    ansgrid = [[-1] * cols for _ in range(rows)]

Why `-1`?

Because `-1` means:

    "This cell has not been visited yet."

Example:

    -1 -1  0
    -1 -1 -1
     0 -1 -1

The `0`s are the source cells.

The `-1`s are cells whose distance has not been calculated yet.

---

# 10. Why Can ansgrid Also Act Like visited?

We don't actually need a separate:

    visited[][]

array.

Because:

    ansgrid[r][c] == -1

means:

    not visited

and:

    ansgrid[r][c] != -1

means:

    already visited

So `ansgrid` performs two jobs:

    1. Stores distance
    2. Tells us whether the cell was visited

This is a useful optimization.

---

# 11. The BFS Logic

After putting all `1`s into the queue:

    while queue:

        take one cell

        check its 4 neighbors

        if neighbor has not been visited:

            distance[neighbor]
                =
            distance[current] + 1

            put neighbor into queue

This is the core logic.

---

# 12. Why Is Distance = Current Distance + 1?

Suppose:

    current cell distance = 2

We move one step to a neighbor.

Therefore:

    neighbor distance = 2 + 1

So:

    ansgrid[nr][nc] = ansgrid[pr][pc] + 1

This is exactly how BFS builds shortest distances.

---

# 13. Why Is the First Visit Always the Shortest Distance?

This is the important BFS property.

BFS explores in this order:

    distance 0
    distance 1
    distance 2
    distance 3
    ...

Suppose a cell is first reached with distance `3`.

Could there be another path with distance `2`?

No.

Because BFS would have explored every distance-2 cell before processing distance-3 cells.

Therefore:

> The first time BFS reaches a cell, it has found the shortest possible distance.

This is one of the most important things to remember about BFS.

---

# 14. Four Directions

For a grid, we usually move:

    Up
    Down
    Left
    Right

We represent this as:

    directions = [
        (0, -1),   # left
        (0, 1),    # right
        (-1, 0),   # up
        (1, 0)     # down
    ]

For current cell:

    (pr, pc)

the new cell is:

    nr = pr + dr
    nc = pc + dc

---

# 15. Boundary Checking

A neighbor may be outside the grid.

For example, from:

    (0,0)

moving up gives:

    (-1,0)

which is invalid.

So we check:

    if nr < 0 or nr >= rows or nc < 0 or nc >= cols:
        continue

This means:

    "Ignore this direction if it goes outside the grid."

---

# 16. Complete Code

    from collections import deque

    class Solution:
        def nearest(self, grid):

            rows = len(grid)
            cols = len(grid[0])

            queue = deque()

            ansgrid = [[-1] * cols for _ in range(rows)]

            directions = [
                (0, -1),
                (0, 1),
                (-1, 0),
                (1, 0)
            ]

            # Put all 1s into the queue
            for row in range(rows):
                for col in range(cols):

                    if grid[row][col] == 1:
                        queue.append((row, col))
                        ansgrid[row][col] = 0

            # Multi-source BFS
            while queue:

                pr, pc = queue.popleft()

                # Check 4 directions
                for dr, dc in directions:

                    nr = pr + dr
                    nc = pc + dc

                    # Outside the grid
                    if nr < 0 or nr >= rows or nc < 0 or nc >= cols:
                        continue

                    # Not visited yet
                    if ansgrid[nr][nc] == -1:

                        ansgrid[nr][nc] = ansgrid[pr][pc] + 1

                        queue.append((nr, nc))

            return ansgrid

---

# 17. Dry Run

Consider:

    grid =

    0 0 1
    0 0 0
    1 0 0

## Step 1: Find all 1s

The `1`s are:

    (0,2)
    (2,0)

So:

    queue = [(0,2), (2,0)]

And:

    ansgrid =

    -1 -1  0
    -1 -1 -1
     0 -1 -1

---

## Step 2: Process (0,2)

Current:

    (0,2)

Distance:

    0

Neighbors:

    left  -> (0,1)
    right -> outside
    up    -> outside
    down  -> (1,2)

So:

    distance(0,1) = 1
    distance(1,2) = 1

Now:

    ansgrid =

    -1  1  0
    -1 -1  1
     0 -1 -1

Queue contains:

    (2,0)
    (0,1)
    (1,2)

---

## Step 3: Process (2,0)

Current distance:

    0

Its unvisited neighbors:

    (1,0)
    (2,1)

Set:

    distance(1,0) = 1
    distance(2,1) = 1

Now:

    ansgrid =

    -1  1  0
     1 -1  1
     0  1 -1

---

## Step 4: Process distance-1 cells

BFS continues.

From `(0,1)`:

    (0,0) gets distance 2
    (1,1) gets distance 2

From `(1,2)`:

    (1,1) gets distance 2
    (2,2) gets distance 2

Since `(1,1)` is already visited, we do not update it again.

Final answer:

    2 1 0
    1 2 1
    0 1 2

---

# 18. Why Don't We Need size = len(queue)?

In Rotting Oranges, we used:

    size = len(queue)

because we were counting:

    minutes

Each BFS level represented one minute.

Here we don't need to count minutes separately.

We directly store:

    distance

inside `ansgrid`.

So this is enough:

    while queue:

        current = queue.popleft()

        for neighbor:

            distance[neighbor] = distance[current] + 1

Therefore:

    size = len(queue)

is unnecessary here.

---

# 19. Difference Between This Problem and Rotting Oranges

Both use:

    Multi-Source BFS

But the output is different.

## Rotting Oranges

Question:

    How many minutes until all oranges rot?

Therefore we track:

    minutes / levels

## Distance of Nearest 1

Question:

    What is the shortest distance for every cell?

Therefore we store:

    distance for every cell

So:

    Rotting Oranges
        ↓
    BFS levels → time

    Nearest 1
        ↓
    BFS distances → distance matrix

The underlying BFS pattern is the same.

---

# 20. The General Multi-Source BFS Template

This is the reusable pattern you should learn.

    Create queue

    For every source:
        put source in queue
        distance[source] = 0

    while queue:

        current = queue.popleft()

        for every neighbor:

            if neighbor is not visited:

                distance[neighbor] =
                    distance[current] + 1

                mark neighbor visited

                queue.append(neighbor)

This template can solve many problems.

---

# 21. How to Recognize Multi-Source BFS

When reading a new problem, ask these questions:

## Question 1

Are there multiple starting points?

For example:

    multiple 1s
    multiple rotten oranges
    multiple fires
    multiple people
    multiple sources

If yes, think:

    Multi-Source BFS

---

## Question 2

Do all sources start at the same time?

If yes:

    put ALL sources into the queue initially.

Do NOT run BFS separately from each source.

---

## Question 3

Does the thing spread/move to neighboring cells?

Examples:

    up
    down
    left
    right

If yes, BFS/grid traversal may be useful.

---

## Question 4

Are we looking for minimum distance/time/steps?

If yes, BFS is a strong candidate.

---

# 22. Important Pattern

Remember this:

    Multiple sources
          +
    Equal starting time
          +
    Spread through neighbors
          +
    Minimum distance/time
          =
    Multi-Source BFS

This is the main pattern you should take from this problem.

---

# 23. Where Can This Pattern Be Used?

## 1. Rotting Oranges

Multiple rotten oranges spread simultaneously.

    Source = rotten oranges

    BFS = spreading

    Answer = minimum time

---

## 2. Distance to Nearest 1

Multiple `1`s are sources.

    Source = cells containing 1

    BFS = distance spreading

    Answer = nearest distance

---

## 3. Walls and Gates

Multiple gates can be sources.

    Source = gates

    BFS = distance from gates

    Answer = nearest gate distance

---

## 4. Nearest Exit / Escape Problems

Sometimes there can be multiple starting or target points.

Multi-source BFS can calculate the minimum distance to any source.

---

## 5. Fire Spreading Problems

Suppose there are multiple fire locations.

    Source = all fire cells

    BFS = fire spreading

    Answer = time/distance

---

## 6. Infection / Virus Spread

Multiple infected cells spread simultaneously.

    Source = infected cells

    BFS = infection spread

    Answer = time until cells become infected

---

# 24. Single-Source BFS vs Multi-Source BFS

## Single-Source BFS

One starting point:

    Source
       ↓
      BFS
       ↓
    shortest distance

Example:

    Find shortest path from A to B.

---

## Multi-Source BFS

Multiple starting points:

    Source  Source  Source
       ↓      ↓       ↓
          BFS
       ↓
    nearest source

Example:

    Find distance of every cell from nearest 1.

---

# 25. Important Difference From DFS

DFS can visit the entire grid.

But if the question asks:

    shortest distance
    minimum number of steps
    nearest source

BFS is usually the better choice for an unweighted grid.

Why?

DFS may reach a cell through a long path first.

BFS explores:

    0 steps
    1 step
    2 steps
    3 steps
    ...

Therefore BFS naturally finds the minimum distance.

---

# 26. Why Not Run BFS From Every 0?

A possible approach is:

    for every 0:
        run BFS
        search for nearest 1

But suppose there are `N` cells.

You may perform BFS many times.

That can become expensive.

Multi-source BFS performs one BFS for the entire grid.

So instead of:

    0 → search
    0 → search
    0 → search
    0 → search

we do:

    all 1s
      ↓
    one BFS
      ↓
    calculate every distance

Much better.

---

# 27. Complexity

Let:

    R = number of rows
    C = number of columns

Every cell is added to the queue at most once.

Every cell checks at most 4 neighbors.

Therefore:

    Time Complexity = O(R × C)

The answer matrix contains `R × C` cells.

The queue can also contain many cells.

Therefore:

    Space Complexity = O(R × C)

If the input grid itself can be modified, sometimes we can reduce extra space by storing information directly in the grid, but this implementation uses a separate answer matrix.

---

# 28. Important Code Pattern to Memorize

Don't memorize the whole problem.

Remember these 5 pieces:

    1. Put all sources into queue.

    2. Give all sources distance 0.

    3. While queue is not empty:

    4. Visit neighbors.

    5. distance[neighbor] =
           distance[current] + 1

That's the real pattern.

---

# 29. Common Mistakes

## Mistake 1: Starting BFS from only one 1

Wrong:

    Find first 1
    BFS from it

Why wrong?

Because another `1` may be closer.

Correct:

    Put ALL 1s into queue.

---

## Mistake 2: Running BFS separately for every 0

This repeats work.

Correct:

    One Multi-Source BFS.

---

## Mistake 3: Forgetting to initialize source distance

For every `1`:

    ansgrid[row][col] = 0

---

## Mistake 4: Forgetting to enqueue the source

We need:

    queue.append((row, col))

---

## Mistake 5: Using `visited` incorrectly

We need to make sure a cell is processed only once.

Here:

    ansgrid[nr][nc] == -1

means:

    not visited

---

## Mistake 6: Updating an already visited cell

Do:

    if ansgrid[nr][nc] == -1:

        ansgrid[nr][nc] =
            ansgrid[pr][pc] + 1

Don't repeatedly update cells.

---

## Mistake 7: Forgetting boundary checks

Always verify:

    0 <= nr < rows
    0 <= nc < cols

---

## Mistake 8: Using `size = len(queue)` unnecessarily

You only need level processing when the problem asks about:

    time
    minutes
    number of levels

If you're directly storing distance, you don't need it.

---

# 30. How This Connects With Rotting Oranges

You should now see the connection:

    Rotting Oranges
          ↓
    Multi-Source BFS
          ↓
    All rotten oranges = sources
          ↓
    BFS levels = minutes

And:

    Distance of Nearest 1
          ↓
    Multi-Source BFS
          ↓
    All 1s = sources
          ↓
    BFS distance = nearest distance

So these are not two completely different algorithms.

They are the SAME pattern with a different answer requirement.

---

# 31. The Bigger Grid BFS Pattern

You have now learned an important family of grid problems.

## Pattern 1: DFS/BFS for connected area

Example:

    Number of Islands

Question:

    How many connected regions?

---

## Pattern 2: DFS/BFS for maximum area

Example:

    Max Area of Island

Question:

    How large is a connected region?

---

## Pattern 3: BFS for shortest path

Example:

    Shortest Path in Binary Matrix

Question:

    Minimum number of steps from A to B?

---

## Pattern 4: Multi-Source BFS

Example:

    Distance of Nearest 1

Question:

    Minimum distance from every cell to any source?

---

## Pattern 5: Multi-Source BFS + time

Example:

    Rotting Oranges

Question:

    How long does spreading take?

---

# 32. What You Should Actually Learn From This Problem

Don't just learn:

    "This is the Distance of Nearest Cell Having 1 problem."

Learn these concepts:

    1. BFS gives shortest distance in an unweighted graph.

    2. Multiple starting points can be handled by
       putting all sources into the queue initially.

    3. Multi-source BFS means all sources start at distance 0.

    4. The first time a cell is visited,
       we have found its minimum distance.

    5. A grid can be treated as a graph.

    6. Each cell is a node.

    7. Adjacent cells are connected by edges.

    8. Four-direction movement represents graph edges.

    9. A distance matrix can also act as a visited array.

    10. We can solve the entire grid with one BFS.

---

# 33. How to Apply This Knowledge to a New Problem

When you see a new grid problem, don't immediately write code.

First ask:

    What is a cell?

Usually:

    cell = node

Then:

    Who are its neighbors?

Usually:

    up
    down
    left
    right

Then ask:

    Is the problem about reaching something?

    Is it about minimum distance?

    Is it about time?

    Are there multiple starting points?

Then choose the pattern.

---

# 34. Pattern Recognition Cheat Sheet

    Problem asks:
    "Is it connected?"
        ↓
    DFS / BFS

    Problem asks:
    "How many islands/components?"
        ↓
    DFS / BFS + visited

    Problem asks:
    "Largest connected area?"
        ↓
    DFS / BFS + count

    Problem asks:
    "Shortest path?"
        ↓
    BFS

    Problem asks:
    "Nearest source?"
        ↓
    Multi-Source BFS

    Problem asks:
    "Multiple sources spread simultaneously?"
        ↓
    Multi-Source BFS

    Problem asks:
    "How many minutes until everything spreads?"
        ↓
    Multi-Source BFS + levels/time

---

# 35. One Very Important Rule

Remember:

    BFS + ONE source
        =
    shortest distance from ONE source

    BFS + MULTIPLE sources
        =
    shortest distance from the NEAREST source

This single idea is extremely useful in graph interviews.

---

# 36. Final Mental Picture

Imagine the `1`s are water drops.

    0 0 1 0
    0 0 0 0
    1 0 0 0

Both drops start at the same time.

They spread:

    distance 0
       ↓
    distance 1
       ↓
    distance 2
       ↓
    distance 3

Every cell records:

    "How long did it take for the nearest 1 to reach me?"

That recorded value is the answer.

---

# 37. Final Takeaway

The most important thing to remember from this problem is NOT the code.

The important pattern is:

    Multiple sources
          ↓
    Put all sources in queue
          ↓
    Set source distance = 0
          ↓
    Multi-Source BFS
          ↓
    Visit neighbors
          ↓
    distance[neighbor] =
        distance[current] + 1
          ↓
    First visit = shortest distance

Whenever you see:

    "nearest"
    "minimum distance"
    "closest source"
    "multiple starting points"
    "spread simultaneously"
    "minimum time"

think:

    MULTI-SOURCE BFS

This pattern directly connects to:

    Distance of Nearest 1
    Rotting Oranges
    Walls and Gates
    Infection Spread
    Fire Spread
    Nearest Source problems
    Shortest distance from multiple sources

Once you understand this pattern, you are not solving one problem anymore — you are learning a reusable graph algorithm.
