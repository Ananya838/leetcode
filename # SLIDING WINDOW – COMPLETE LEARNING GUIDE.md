# SLIDING WINDOW – COMPLETE LEARNING GUIDE

## 1. What is Sliding Window?

Sliding Window is an algorithmic technique used to solve problems involving a continuous portion of an array or string.

Instead of repeatedly calculating information about different portions of an array, we maintain a window and move it through the array.

A window is simply a continuous range of elements.

### Example

Consider this array:

```python
arr = [2, 1, 5, 1, 3, 2]
```

Suppose the question asks:

Find the maximum sum of any 3 consecutive elements.

The possible windows are:

```text
[2, 1, 5]  1, 3, 2
 2, [1, 5, 1] 3, 2
 2, 1, [5, 1, 3] 2
 2, 1, 5, [1, 3, 2]
```

Their sums:

```text
2 + 1 + 5 = 8
1 + 5 + 1 = 7
5 + 1 + 3 = 9
1 + 3 + 2 = 6
```

Answer = 9.

The maximum sum comes from:

```python
[5, 1, 3]
```

### Why do we need Sliding Window?

A beginner might calculate every window independently.

That means repeatedly adding elements.

Sliding Window avoids unnecessary calculations by:

1. Removing the element leaving the window.
2. Adding the new element entering the window.
3. Updating the answer.

This makes the algorithm faster.

---

## 2. Understanding the Two Pointers

Sliding Window usually uses two pointers:

- `left`: Starting position of the window.
- `right`: Ending position of the window.

Example:

```python
arr = [2, 1, 5, 1, 3, 2]
```

Suppose our window contains three elements.

```text
Index:  0  1  2  3  4  5
Array:  2  1  5  1  3  2
        L     R
```

Initially:

```python
left = 0
right = 2
```

Window:

```python
[2, 1, 5]
```

Move the window one position:

```text
Index:  0  1  2  3  4  5
Array:  2  1  5  1  3  2
           L     R
```

Now:

```python
left = 1
right = 3
```

Window:

```python
[1, 5, 1]
```

The window moves forward until the array ends.

**Important:** Both pointers generally move from left to right. They do not move backward.

---

# PART 1: BEGINNER LEVEL

## 3. Technique 1 – Fixed Size Sliding Window

This is the easiest type of Sliding Window.

### When should you use it?

Look for questions containing:

- Maximum sum of K consecutive elements.
- Minimum sum of K consecutive elements.
- Average of every K consecutive elements.
- Maximum average of a subarray of size K.
- First negative number in every window of size K.

The window size is already given.

For example:

```text
Find the maximum sum of 3 consecutive elements.
```

Here:

```python
k = 3
```

The window must always contain exactly three elements.

### Step 1: Understand the brute-force approach

```python
arr = [2, 1, 5, 1, 3, 2]
k = 3

max_sum = 0

for i in range(len(arr) - k + 1):
    current_sum = sum(arr[i:i+k])
    max_sum = max(max_sum, current_sum)

print(max_sum)
```

Time complexity: O(n × k) in the general case.

### Step 2: Optimize using Sliding Window

Instead of calculating the sum again, maintain the current sum.

Logic:

1. Add the next element.
2. If the window size becomes greater than K, remove the leftmost element.
3. Move `left` forward.
4. When the window size equals K, update the answer.

### Python Template

```python
def sliding_window(arr, k):

    left = 0
    current_sum = 0
    max_sum = float('-inf')

    for right in range(len(arr)):

        current_sum += arr[right]

        if right - left + 1 > k:
            current_sum -= arr[left]
            left += 1

        if right - left + 1 == k:
            max_sum = max(max_sum, current_sum)

    return max_sum
```

### Dry Run

Input:

```python
arr = [2, 1, 5, 1, 3, 2]
k = 3
```

| Right | Added | Current window | Sum | Maximum |
|---|---:|---|---:|---:|
| 0 | 2 | [2] | 2 | - |
| 1 | 1 | [2,1] | 3 | - |
| 2 | 5 | [2,1,5] | 8 | 8 |
| 3 | 1 | [1,5,1] | 7 | 8 |
| 4 | 3 | [5,1,3] | 9 | 9 |
| 5 | 2 | [1,3,2] | 6 | 9 |

Output:

```text
9
```

Time Complexity: O(n)

Space Complexity: O(1)

### Important Trick

For fixed-size windows, remember:

```python
if right - left + 1 > k:
    current_sum -= arr[left]
    left += 1

if right - left + 1 == k:
    # Process the window
```

This is your first important template.

---

# PART 2: INTERMEDIATE LEVEL

## 4. Technique 2 – Variable Size Sliding Window

This is where Sliding Window becomes more interesting.

Unlike fixed-size windows, the window size is not given.

You expand or shrink the window depending on a condition.

### When should you use it?

Look for problems asking:

- Longest subarray satisfying a condition.
- Shortest subarray satisfying a condition.
- Longest substring without repeating characters.
- Longest substring with at most K distinct characters.
- Minimum window satisfying a sum or constraint.

### Basic Logic

Two operations are important:

**Expand the window:**

```python
right += 1
```

**Shrink the window:**

```python
left += 1
```

Usually:

- Expand using the `for` loop.
- Shrink using the `while` loop.

### General Template

```python
def sliding_window(arr):

    left = 0
    answer = 0

    for right in range(len(arr)):

        # Add new element to window

        while condition_is_invalid:

            # Remove leftmost element
            left += 1

        # Update answer

    return answer
```

Remember this structure.

---

## 5. Technique 3 – Find the Longest Valid Window

This is one of the most frequently used Sliding Window patterns.

### Example

Find the longest subarray whose sum is less than or equal to 7.

```python
arr = [2, 1, 1, 1, 3, 2]
k = 7
```

We need the longest continuous subarray satisfying:

```python
current_sum <= k
```

### Logic

1. Expand the window.
2. Add the new element.
3. If the sum becomes greater than K, shrink the window.
4. Once valid, calculate the window length.
5. Keep the maximum length.

### Python Code

```python
def longest_subarray(arr, k):

    left = 0
    current_sum = 0
    max_length = 0

    for right in range(len(arr)):

        current_sum += arr[right]

        while current_sum > k:
            current_sum -= arr[left]
            left += 1

        max_length = max(max_length, right - left + 1)

    return max_length
```

Output:

```text
4
```

One longest valid window is:

```python
[2, 1, 1, 1]
```

Time Complexity: O(n)

Space Complexity: O(1)

### Important Condition

This particular sum-based technique assumes non-negative array elements.

Why?

Because adding positive elements increases the sum, and removing them decreases it.

If negative numbers are present, the window sum might decrease when expanding. The usual shrinking logic is no longer reliable.

For arrays containing negative numbers, consider Prefix Sum + HashMap or other techniques.

---

## 6. Technique 4 – Find the Minimum Valid Window

Now reverse the previous problem.

Instead of finding the longest window, find the shortest window satisfying a condition.

### Example

Find the minimum length subarray whose sum is at least 7.

```python
arr = [2, 3, 1, 2, 4, 3]
target = 7
```

A valid window is:

```python
[2, 3, 1, 2]
```

Its length is 4.

But:

```python
[4, 3]
```

Its length is 2.

Therefore, the answer is 2.

### Logic

1. Expand the window.
2. Add elements to the current sum.
3. When the sum becomes greater than or equal to the target, the window is valid.
4. Update the minimum length.
5. Shrink the window to find a smaller valid window.
6. Continue until the array ends.

### Python Code

```python
def min_subarray_length(target, nums):

    left = 0
    current_sum = 0
    min_length = float('inf')

    for right in range(len(nums)):

        current_sum += nums[right]

        while current_sum >= target:

            min_length = min(
                min_length,
                right - left + 1
            )

            current_sum -= nums[left]
            left += 1

    return 0 if min_length == float('inf') else min_length
```

Time Complexity: O(n)

Space Complexity: O(1)

### Remember the Difference

| Problem | When to update answer |
|---|---|
| Longest valid window | After making window valid |
| Shortest valid window | Before shrinking a valid window |

This difference is extremely important.

---

# PART 3: ADVANCED INTERMEDIATE TECHNIQUES

## 7. Technique 5 – Sliding Window with HashMap

Sometimes we need to track frequencies or distinct elements inside the window.

For this, we combine:

- Sliding Window
- Dictionary / HashMap

### Example

Find the longest substring without repeating characters.

Input:

```python
s = "abcabcbb"
```

Expected output:

```text
3
```

Because:

```python
"abc"
```

is the longest substring without repeating characters.

### Logic

Maintain a dictionary containing the frequency of each character.

Expand the window by adding characters.

If a character repeats, shrink the window until the substring becomes valid again.

### Python Code

```python
def longest_substring(s):

    left = 0
    char_count = {}
    max_length = 0

    for right in range(len(s)):

        char = s[right]

        char_count[char] = char_count.get(char, 0) + 1

        while char_count[char] > 1:

            left_char = s[left]

            char_count[left_char] -= 1

            if char_count[left_char] == 0:
                del char_count[left_char]

            left += 1

        max_length = max(
            max_length,
            right - left + 1
        )

    return max_length
```

### Important Python Dictionary Methods

```python
freq = {}

# Add or update frequency
freq[ch] = freq.get(ch, 0) + 1

# Decrease frequency
freq[ch] -= 1

# Remove key
del freq[ch]

# Check whether key exists
if ch in freq:
    print("Exists")
```

### Important LeetCode Problems

- 3. Longest Substring Without Repeating Characters
- 567. Permutation in String
- 438. Find All Anagrams in a String
- 904. Fruit Into Baskets

---

## 8. Technique 6 – At Most K Distinct Elements

This is an important advanced pattern.

### Example

Find the longest substring containing at most 2 distinct characters.

```python
s = "eceba"
k = 2
```

Possible substrings:

```text
"ece" → 2 distinct characters
"ce"  → 2 distinct characters
```

The longest valid substring is:

```python
"ece"
```

Answer:

```text
3
```

### Python Code

```python
def longest_k_distinct(s, k):

    left = 0
    freq = {}
    max_length = 0

    for right in range(len(s)):

        ch = s[right]
        freq[ch] = freq.get(ch, 0) + 1

        while len(freq) > k:

            left_char = s[left]

            freq[left_char] -= 1

            if freq[left_char] == 0:
                del freq[left_char]

            left += 1

        max_length = max(
            max_length,
            right - left + 1
        )

    return max_length
```

### The Important Trick: At Most K vs Exactly K

Suppose a problem asks:

Find the number of subarrays with exactly K distinct elements.

A useful mathematical transformation is:

```text
Exactly(K) = AtMost(K) - AtMost(K - 1)
```

Why?

At most K includes:

- 0 distinct elements
- 1 distinct element
- 2 distinct elements
- ...
- K distinct elements

At most K - 1 includes all the smaller cases.

Subtracting removes those smaller cases.

Only exactly K remains.

### Python Template

```python
def at_most_k(nums, k):

    left = 0
    freq = {}
    count = 0

    for right in range(len(nums)):

        freq[nums[right]] = freq.get(nums[right], 0) + 1

        while len(freq) > k:

            freq[nums[left]] -= 1

            if freq[nums[left]] == 0:
                del freq[nums[left]]

            left += 1

        count += right - left + 1

    return count


def exactly_k(nums, k):

    return at_most_k(nums, k) - at_most_k(nums, k - 1)
```

### Why do we add `right - left + 1`?

Suppose the current valid window is:

```text
[2, 1, 2, 3]
       L     R
```

For a fixed right endpoint, all subarrays starting from `left` through `right` are valid when the window condition is satisfied.

They are:

```text
[3]
[2, 3]
[1, 2, 3]
[2, 1, 2, 3]
```

There are four possibilities.

Therefore:

```python
count += right - left + 1
```

This is one of the most important Sliding Window tricks.

---

## 9. Technique 7 – Frequency Matching / Anagram Window

This technique is useful when comparing character frequencies.

### Example

Find all starting indices of anagrams of:

```python
p = "ab"
```

inside:

```python
s = "cbaebabacd"
```

An anagram contains the same characters with the same frequencies.

For example:

```text
"ab" and "ba"
```

are anagrams.

Expected output:

```python
[1, 2, 7]
```

### Logic

1. Calculate the frequency of the pattern.
2. Maintain a fixed-size window in the string.
3. Calculate or maintain its character frequencies.
4. Compare the window frequency with the pattern frequency.
5. Record the starting index when they match.

### Python Code

```python
from collections import Counter

def find_anagrams(s, p):

    k = len(p)

    if k > len(s):
        return []

    target = Counter(p)
    window = Counter()

    result = []

    for right in range(len(s)):

        window[s[right]] += 1

        if right >= k:
            left_char = s[right - k]
            window[left_char] -= 1

            if window[left_char] == 0:
                del window[left_char]

        if window == target:
            result.append(right - k + 1)

    return result
```

This is a fixed-size Sliding Window combined with frequency counting.

---

# PART 4: ADVANCED SLIDING WINDOW

## 10. Technique 8 – Sliding Window + Monotonic Deque

This is an important technique for harder LeetCode problems.

### When should you use it?

When the question asks:

- Maximum element in every window.
- Minimum element in every window.
- Maximum or minimum while maintaining a moving range.

A normal Sliding Window can maintain sums easily.

But finding the maximum element efficiently is more difficult.

A monotonic deque solves this problem.

### What is a Deque?

A deque is a double-ended queue.

You can:

- Add elements from the front.
- Add elements from the back.
- Remove elements from the front.
- Remove elements from the back.

Python:

```python
from collections import deque
```

### What is a Monotonic Deque?

A deque that maintains elements in increasing or decreasing order.

For maximum values, maintain a decreasing deque.

For minimum values, maintain an increasing deque.

### Example

Find the maximum element in every window of size 3.

```python
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
```

Expected output:

```python
[3, 3, 5, 5, 6, 7]
```

### Python Code

```python
from collections import deque

def max_sliding_window(nums, k):

    dq = deque()
    result = []

    for right in range(len(nums)):

        # Remove indices outside the window
        while dq and dq[0] <= right - k:
            dq.popleft()

        # Remove smaller elements
        while dq and nums[dq[-1]] <= nums[right]:
            dq.pop()

        dq.append(right)

        # Start recording when window size reaches k
        if right >= k - 1:
            result.append(nums[dq[0]])

    return result
```

### Important Tricks

For maximum:

```python
while dq and nums[dq[-1]] <= nums[right]:
    dq.pop()
```

For minimum:

```python
while dq and nums[dq[-1]] >= nums[right]:
    dq.pop()
```

Always store indices instead of only values.

Why?

Because indices help us identify whether an element has moved outside the current window.

Time Complexity: O(n)

Space Complexity: O(k)

---

## 11. Technique 9 – Sliding Window with Multiple Conditions

Some problems have more than one condition.

Example:

Find the longest substring containing:

- At most 2 zeros.
- At least 3 ones.

You must maintain the required information inside the window.

General approach:

1. Expand the window.
2. Update the required counters.
3. Check all conditions.
4. Shrink when the window becomes invalid.
5. Update the answer when the window is valid.

Template:

```python
left = 0
answer = 0

for right in range(len(arr)):

    # Update window information

    while invalid_condition:

        # Remove leftmost element
        left += 1

    if valid_condition:
        answer = max(
            answer,
            right - left + 1
        )
```

The important part is identifying exactly what makes the window valid or invalid.

---

## 12. Technique 10 – Sliding Window with Binary Search

Some advanced problems require finding the smallest or largest possible window size.

In these cases, binary search may be combined with a window-based feasibility check.

General idea:

1. Guess a possible window size.
2. Check whether a valid window of that size exists.
3. If possible, search for a better answer.
4. Otherwise, adjust the search range.

This technique is problem-dependent.

Do not confuse it with ordinary Sliding Window. Binary Search is useful when the answer has a monotonic feasibility property.

---

# PART 5: IMPORTANT CONCEPTS AND TRICKS

## 13. How to Identify Sliding Window Problems

Look for these keywords:

| Problem statement | Possible technique |
|---|---|
| K consecutive elements | Fixed window |
| Subarray of size K | Fixed window |
| Maximum sum of K elements | Fixed window |
| Longest substring | Variable window |
| Shortest subarray | Variable window |
| Without repeating characters | HashMap + window |
| At most K distinct | Frequency map |
| Exactly K distinct | AtMost(K) - AtMost(K-1) |
| Every anagram | Fixed window + frequency |
| Maximum in every window | Monotonic deque |
| Minimum in every window | Monotonic deque |

Remember: Keywords are clues, not guarantees. Always check the actual constraints and whether the window condition is maintainable.

## 14. Five Important Sliding Window Tricks

### Trick 1: Calculate window size

Always remember:

```python
window_size = right - left + 1
```

Why `+1`?

Because both endpoints are included.

### Trick 2: Use `while`, not always `if`

When the window becomes invalid, sometimes you must remove multiple elements.

Use:

```python
while invalid_condition:
    # Shrink window
```

### Trick 3: Count all valid subarrays

When every subarray ending at `right` and starting between `left` and `right` is valid:

```python
count += right - left + 1
```

### Trick 4: Exactly K transformation

```python
exactly_k = at_most_k - at_most_k_minus_1
```

### Trick 5: Maintain information instead of recalculating

Instead of repeatedly using:

```python
sum(arr[left:right+1])
```

Maintain the sum:

```python
current_sum += arr[right]
current_sum -= arr[left]
```

This is the main optimization behind Sliding Window.

---

# PART 6: WHEN SLIDING WINDOW DOES NOT WORK

This is equally important.

Not every subarray or substring problem can be solved using Sliding Window.

### Example

Find a subarray with sum equal to 5.

```python
arr = [4, -2, 3, 1]
```

A normal sum-based Sliding Window is not reliable here because negative numbers can reduce the sum when the window expands.

Instead, consider:

- Prefix Sum
- HashMap
- Prefix Sum + HashMap

### General Rule

| Situation | Recommended approach |
|---|---|
| Positive numbers + sum constraint | Sliding Window |
| Non-negative numbers + sum constraint | Sliding Window |
| Negative numbers + exact sum | Prefix Sum + HashMap |
| Fixed-size maximum sum | Fixed Sliding Window |
| Longest substring with constraints | Variable Sliding Window |
| Maximum in every window | Monotonic Deque |
| Exactly K distinct | AtMost transformation |
| Arbitrary subarray sum with negative values | Prefix Sum techniques |

**Important:** Sliding Window works when you can efficiently update the window and the validity condition behaves correctly as the window expands and shrinks.

---

# PART 7: PYTHON TEMPLATES TO REMEMBER

## Template 1: Fixed Size

```python
left = 0

for right in range(len(arr)):

    # Add current element

    if right - left + 1 > k:

        # Remove left element
        left += 1

    if right - left + 1 == k:

        # Update answer
```

## Template 2: Variable Size – Longest

```python
left = 0
answer = 0

for right in range(len(arr)):

    # Expand window

    while invalid_condition:

        # Shrink window
        left += 1

    answer = max(answer, right - left + 1)
```

## Template 3: Variable Size – Minimum

```python
left = 0
answer = float('inf')

for right in range(len(arr)):

    # Expand window

    while valid_condition:

        answer = min(answer, right - left + 1)

        # Shrink window
        left += 1
```

## Template 4: Frequency Map

```python
left = 0
freq = {}

for right in range(len(s)):

    ch = s[right]

    freq[ch] = freq.get(ch, 0) + 1

    while invalid_condition:

        left_char = s[left]

        freq[left_char] -= 1

        if freq[left_char] == 0:
            del freq[left_char]

        left += 1

    # Update answer
```

## Template 5: Count Valid Subarrays

```python
left = 0
count = 0

for right in range(len(arr)):

    # Expand window

    while invalid_condition:

        # Shrink window
        left += 1

    count += right - left + 1
```

---

# PART 8: LEETCODE PRACTICE ROADMAP

Do not randomly solve Sliding Window problems.

Follow this order so that every new problem introduces a new concept.

## LEVEL 1 – Beginner: Fixed Size Windows

Understand how to expand and move a fixed-size window.

| No. | LeetCode | Problem | Difficulty |
|---|---|---|---|
| 1 | 643 | Maximum Average Subarray I | Easy |
| 2 | 1343 | Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold | Medium |
| 3 | 1456 | Maximum Number of Vowels in a Substring of Given Length | Medium |
| 4 | 1984 | Minimum Difference Between Highest and Lowest of K Scores | Easy |

Goal: Solve without looking at the solution.

## LEVEL 2 – Beginner to Intermediate: Variable Windows

| No. | LeetCode | Problem | Difficulty |
|---|---|---|---|
| 5 | 209 | Minimum Size Subarray Sum | Medium |
| 6 | 3 | Longest Substring Without Repeating Characters | Medium |
| 7 | 1004 | Max Consecutive Ones III | Medium |
| 8 | 1493 | Longest Subarray of 1's After Deleting One Element | Medium |
| 9 | 904 | Fruit Into Baskets | Medium |

Goal: Understand when to expand and when to shrink.

## LEVEL 3 – Intermediate: HashMap + Sliding Window

| No. | LeetCode | Problem | Difficulty |
|---|---|---|---|
| 10 | 567 | Permutation in String | Medium |
| 11 | 438 | Find All Anagrams in a String | Medium |
| 12 | 424 | Longest Repeating Character Replacement | Medium |
| 13 | 1208 | Get Equal Substrings Within Budget | Medium |
| 14 | 1658 | Minimum Operations to Reduce X to Zero | Medium |

Goal: Become comfortable maintaining frequencies and other window information.

## LEVEL 4 – Advanced Intermediate: At Most / Exactly K

| No. | LeetCode | Problem | Difficulty |
|---|---|---|---|
| 15 | 992 | Subarrays with K Different Integers | Hard |
| 16 | 1248 | Count Number of Nice Subarrays | Medium |
| 17 | 930 | Binary Subarrays With Sum | Medium |
| 18 | 1358 | Number of Substrings Containing All Three Characters | Medium |

Goal: Understand counting valid subarrays and mathematical transformations.

## LEVEL 5 – Advanced: Monotonic Deque

| No. | LeetCode | Problem | Difficulty |
|---|---|---|---|
| 19 | 239 | Sliding Window Maximum | Hard |
| 20 | 1438 | Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit | Medium |
| 21 | 862 | Shortest Subarray with Sum at Least K | Hard |

Goal: Learn deque-based window optimization and recognize when a normal window is insufficient.

## LEVEL 6 – Advanced Challenges

| No. | LeetCode | Problem | Difficulty |
|---|---|---|---|
| 22 | 76 | Minimum Window Substring | Hard |
| 23 | 30 | Substring with Concatenation of All Words | Hard |
| 24 | 480 | Sliding Window Median | Hard |
| 25 | 2398 | Maximum Number of Robots Within Budget | Hard |

Goal: Combine multiple techniques and handle complex constraints.

---

# PART 9: HOW TO APPROACH EVERY SLIDING WINDOW PROBLEM

Before writing code, answer these questions.

### Step 1: What is the window?

Is it:

- A subarray?
- A substring?
- A fixed-size range?
- A variable-size range?

### Step 2: Is the window size fixed or variable?

If K is given, investigate fixed-size Sliding Window.

If the window must grow and shrink based on a condition, investigate variable-size Sliding Window.

### Step 3: What information should I maintain?

Examples:

- Sum
- Frequency dictionary
- Number of distinct elements
- Maximum and minimum
- Number of zeros
- Number of invalid elements

### Step 4: When should I shrink?

Identify the exact invalid condition.

Example:

```python
while current_sum > k:
    current_sum -= arr[left]
    left += 1
```

### Step 5: When should I update the answer?

For longest:

```python
answer = max(answer, right - left + 1)
```

For shortest:

```python
answer = min(answer, right - left + 1)
```

For counting:

```python
count += right - left + 1
```

### Step 6: Check edge cases

Always consider:

- Empty array or string.
- Single element.
- K = 1.
- K greater than array length.
- Repeated characters.
- All elements equal.
- Negative numbers.
- No valid window.

---

# PART 10: YOUR LEARNING SCHEDULE

A suggested 14-day plan:

| Day | Topics |
|---|---|
| 1 | Understand Sliding Window and two pointers |
| 2 | Fixed-size Sliding Window |
| 3 | Practice fixed-size problems |
| 4 | Variable-size Sliding Window |
| 5 | Longest valid window |
| 6 | Minimum valid window |
| 7 | Revision + mixed problems |
| 8 | Sliding Window + HashMap |
| 9 | Frequency counting |
| 10 | At Most K / Exactly K |
| 11 | Anagram and permutation problems |
| 12 | Monotonic Deque |
| 13 | Advanced problems |
| 14 | Revision and timed practice |

### How to study each problem

Use this method:

1. Read the problem carefully.
2. Identify the brute-force approach.
3. Understand why brute force is slow.
4. Identify the window.
5. Decide which information to maintain.
6. Write the algorithm in simple English.
7. Write Python code.
8. Dry-run using a small example.
9. Analyze time and space complexity.
10. Revisit the problem after 2–3 days.

Do not immediately look at the solution.

Try to understand the pattern first.

---

# FINAL CHEAT SHEET

| Technique | Main idea | Key code |
|---|---|---|
| Fixed Window | Maintain K elements | `right-left+1 == k` |
| Variable Window | Expand and shrink | `while invalid:` |
| Longest Window | Maximize length | `max(answer, window_size)` |
| Minimum Window | Minimize length | `min(answer, window_size)` |
| Frequency Window | Track element counts | `freq[x] += 1` |
| At Most K | Restrict distinct elements | `len(freq) <= k` |
| Exactly K | Subtract two counts | `atMost(k)-atMost(k-1)` |
| Count Windows | Count valid endings | `right-left+1` |
| Monotonic Deque | Maintain max/min | `deque()` |
| Prefix Sum | Handle arbitrary sums | `prefix + hashmap` |

## The Most Important Thing to Remember

Sliding Window is not about memorizing 25 different solutions.

It is about understanding a few reusable patterns:

1. Fixed-size window.
2. Variable-size window.
3. Longest valid window.
4. Minimum valid window.
5. Frequency-based window.
6. At Most K / Exactly K.
7. Monotonic Deque.

Once you understand these seven patterns, you will be able to recognize and solve a large number of Sliding Window problems on LeetCode.
