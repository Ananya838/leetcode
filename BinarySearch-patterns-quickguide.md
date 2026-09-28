# 🔍 Binary Search — Interview Patterns

A compact revision guide for Binary Search patterns, templates, and interview techniques.

---

## 1. Core Idea

Binary Search works when we can **eliminate half of the search space** at every step.

```text
Search Space
     ↓
  [1 2 3 4 5 6 7]
        ↑
       mid
     ↓     ↓
   left   right
```

### Complexity

```text
Time  → O(log N)
Space → O(1)
```

---

# 2. Basic Binary Search

### Pattern

```text
Find an exact target in a sorted array.
```

### Template

```python
def binary_search(nums, target):

    low = 0
    high = len(nums) - 1

    while low <= high:

        mid = low + (high - low) // 2

        if nums[mid] == target:
            return mid

        elif nums[mid] < target:
            low = mid + 1

        else:
            high = mid - 1

    return -1
```

### Remember

```text
target > nums[mid] → go RIGHT
target < nums[mid] → go LEFT
```

Use:

```python
while low <= high
```

because `low == high` still contains one candidate.

---

# 3. Lower Bound ⭐

### Meaning

> First index where `nums[i] >= target`

```text
F F F T T T
      ↑
   answer
```

### Template

```python
def lower_bound(nums, target):

    low = 0
    high = len(nums)

    while low < high:

        mid = low + (high - low) // 2

        if nums[mid] >= target:
            high = mid
        else:
            low = mid + 1

    return low
```

### Key

```text
>= target → high = mid
< target  → low = mid + 1
```

---

# 4. Upper Bound

### Meaning

> First index where `nums[i] > target`

```python
def upper_bound(nums, target):

    low = 0
    high = len(nums)

    while low < high:

        mid = low + (high - low) // 2

        if nums[mid] > target:
            high = mid
        else:
            low = mid + 1

    return low
```

### Difference

```text
Lower Bound → first >= target
Upper Bound → first >  target
```

---

# 5. First / Last Occurrence

For:

```text
[1, 2, 4, 4, 4, 7]
```

First `4`:

```python
first = lower_bound(nums, 4)
```

Last `4`:

```python
last = upper_bound(nums, 4) - 1
```

---

# 6. `low < high` vs `low <= high`

### Exact search

```python
while low <= high:
```

Use when checking whether a target exists.

### Boundary search

```python
while low < high:
```

Use when finding:

```text
first valid
minimum
maximum
boundary
```

At the end:

```text
low == high
```

So:

```python
return low
```

and `return high` give the same value.

---

# 7. Binary Search on Answer ⭐⭐⭐

The array does **not** need to be sorted.

Instead, the **answer space must be monotonic**.

Ask:

> If I guess `X`, can I check whether `X` is possible?

Example:

```text
F F F T T T
      ↑
 first valid answer
```

### Template — Minimum Valid Answer

```python
low = minimum_possible
high = maximum_possible

while low < high:

    mid = low + (high - low) // 2

    if feasible(mid):
        high = mid
    else:
        low = mid + 1

return low
```

---

# 8. Maximum Valid Answer

Pattern:

```text
T T T T F F F
      ↑
  last valid
```

### Template

```python
low = minimum_possible
high = maximum_possible

while low < high:

    mid = low + (high - low + 1) // 2

    if feasible(mid):
        low = mid
    else:
        high = mid - 1

return low
```

### Why `+1`?

It prevents the search from getting stuck when:

```text
high = low + 1
```

---

# 9. Feasibility Function

For Binary Search on Answer:

```python
def feasible(x):
    # Can we achieve the goal using x?
    return True or False
```

The result must be monotonic:

```text
F F F T T T
```

or:

```text
T T T F F F
```

If it looks like:

```text
F T F T F
```

Binary Search cannot directly use that condition.

---

# 10. Rotated Sorted Array ⭐⭐

Example:

```text
[4, 5, 6, 7, 0, 1, 2]
```

### Key Question

> Which half is sorted?

```python
if nums[low] <= nums[mid]:
    # LEFT half is sorted
else:
    # RIGHT half is sorted
```

Then ask:

> Is the target inside the sorted half?

### Pattern

```text
Which half is sorted?
        ↓
Does target belong there?
    ↓           ↓
   YES          NO
    ↓            ↓
search it    search other half
```

Important problems:

```text
LC 33 → Search in Rotated Sorted Array
LC 81 → Rotated Sorted Array II
LC 153 → Find Minimum in Rotated Sorted Array
```

---

# 11. Peak Element ⭐⭐

Example:

```text
       /\
      /  \
     /    \
```

Compare:

```python
nums[mid] < nums[mid + 1]
```

If rising:

```python
low = mid + 1
```

Otherwise:

```python
high = mid
```

### Template

```python
while low < high:

    mid = low + (high - low) // 2

    if nums[mid] < nums[mid + 1]:
        low = mid + 1
    else:
        high = mid

return low
```

---

# 12. Common Binary Search on Answer Problems

| Problem                      | Search For               |
| ---------------------------- | ------------------------ |
| LC 875 — Koko Eating Bananas | Minimum speed            |
| LC 1011 — Ship Packages      | Minimum capacity         |
| LC 1283 — Smallest Divisor   | Minimum divisor          |
| LC 1482 — Minimum Days       | Minimum days             |
| LC 1552 — Magnetic Force     | Maximum minimum distance |
| LC 410 — Split Array         | Minimum largest sum      |

---

# 13. Choosing Search Bounds

Always ask:

> What is the smallest possible answer?

> What is the largest possible answer?

### Ship Packages

```python
low = max(weights)
high = sum(weights)
```

### Koko

```python
low = 1
high = max(piles)
```

### Magnetic Force

```python
low = 1
high = max_position - min_position
```

---

# 14. Binary Search Recognition Checklist

When you see a problem, ask:

```text
1. Is the array sorted?
          ↓
       Binary Search

2. Is there a first/last occurrence?
          ↓
       Lower/Upper Bound

3. Is the array rotated?
          ↓
       Find sorted half

4. Is there a peak/mountain?
          ↓
       Compare mid and mid+1

5. Does the question ask:
   minimum / maximum / smallest / largest?
          ↓
       Binary Search on Answer

6. Can I check "Is X possible?"
          ↓
       Feasibility function

7. Is feasibility monotonic?
          ↓
       Binary Search
```

---

# 15. The 3 Templates to Memorize

### Template 1 — Exact Search

```python
while low <= high:

    mid = low + (high - low) // 2

    if nums[mid] == target:
        return mid
    elif nums[mid] < target:
        low = mid + 1
    else:
        high = mid - 1
```

### Template 2 — First Valid / Minimum

```python
while low < high:

    mid = low + (high - low) // 2

    if feasible(mid):
        high = mid
    else:
        low = mid + 1

return low
```

### Template 3 — Last Valid / Maximum

```python
while low < high:

    mid = low + (high - low + 1) // 2

    if feasible(mid):
        low = mid
    else:
        high = mid - 1

return low
```

---

# 🧠 Golden Rule

Don't memorize dozens of Binary Search solutions.

Always identify:

```text
SEARCH SPACE
     ↓
MID
     ↓
CONDITION
     ↓
LEFT / RIGHT
     ↓
BOUNDARY
```

The most important question is:

> **"What does `low` represent when the loop finishes?"**

If you can answer that, `return low`, `return high`, and the update conditions become much easier to understand.

---

# 🎯 Practice Order

### Basic

```text
LC 704 → Binary Search
LC 35  → Search Insert Position
```

### Bounds

```text
LC 34 → First and Last Position
```

### Rotated

```text
LC 33  → Search Rotated Array
LC 153 → Minimum Rotated Array
```

### Peak

```text
LC 162 → Find Peak Element
```

### Binary Search on Answer

```text
LC 875  → Koko
LC 1011 → Ship Packages
LC 1283 → Smallest Divisor
LC 1482 → Minimum Days
LC 1552 → Magnetic Force
LC 410  → Split Array
```

---
