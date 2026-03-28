# 07 — Data Structures & Algorithms (DSA)

---

> **TCS Digital expects MEDIUM-level DSA.** You cannot avoid this. Focus on Arrays, Strings, Linked Lists, Sorting, Searching, and Complexity Analysis. Write clean Python code and ALWAYS state Time/Space Complexity.

---

## 7.1 Complexity Analysis — Big-O Notation

### Common Complexities (Memorize This Table)

| Big-O | Name | Example | 1M elements |
|-------|------|---------|-------------|
| O(1) | Constant | Hash map lookup, array index access | Instant |
| O(log n) | Logarithmic | Binary search | ~20 operations |
| O(n) | Linear | Single loop, linear search | 1M operations |
| O(n log n) | Linearithmic | Merge sort, quick sort (avg) | ~20M operations |
| O(n²) | Quadratic | Nested loops, bubble sort | 1 trillion operations |
| O(2ⁿ) | Exponential | Recursive Fibonacci (naive) | ∞ (impossible) |

**Q: "Why is O(n log n) better than O(n²) for large inputs?"**
> "For n = 1,000,000: O(n²) = 10¹² operations (~hours). O(n log n) = ~20,000,000 operations (~seconds). The difference grows exponentially as n increases. This is why we optimize sorting from bubble sort O(n²) to merge sort O(n log n)."

### How to Determine Complexity

```python
# O(1) — constant
def get_first(arr):
    return arr[0]

# O(n) — single loop
def linear_search(arr, target):
    for item in arr:       # n iterations
        if item == target:
            return True
    return False

# O(n²) — nested loops
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):           # n iterations
        for j in range(n-i-1):   # n iterations (inner)
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]

# O(log n) — halving each step
def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
```

---

## 7.2 Arrays — Most Common in TCS Interviews

### Problem 1: Two Sum
**Given**: Array of integers and a target. Return indices of two numbers that add up to target.

```python
# Brute Force: O(n²) time, O(1) space
def two_sum_brute(nums, target):
    for i in range(len(nums)):
        for j in range(i+1, len(nums)):
            if nums[i] + nums[j] == target:
                return [i, j]

# Optimal: O(n) time, O(n) space — Hash Map
def two_sum(nums, target):
    seen = {}  # value → index
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

### Problem 2: Maximum Subarray (Kadane's Algorithm)
```python
# O(n) time, O(1) space
def max_subarray(nums):
    max_sum = current_sum = nums[0]
    for num in nums[1:]:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)
    return max_sum

# Example: [-2, 1, -3, 4, -1, 2, 1, -5, 4] → 6 (subarray [4, -1, 2, 1])
```

### Problem 3: Move Zeroes to End
```python
# O(n) time, O(1) space — Two Pointer
def move_zeroes(nums):
    insert_pos = 0
    for num in nums:
        if num != 0:
            nums[insert_pos] = num
            insert_pos += 1
    while insert_pos < len(nums):
        nums[insert_pos] = 0
        insert_pos += 1
```

### Problem 4: Find Kth Largest Element
```python
import heapq

# Using Min-Heap: O(n log k) time, O(k) space
def kth_largest(nums, k):
    # Maintain a min-heap of size k
    heap = nums[:k]
    heapq.heapify(heap)
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)
    return heap[0]

# Why NOT just sort? 
# sort is O(n log n). Heap approach is O(n log k) — better when k << n
```

### Problem 5: Rotate Array by K Positions
```python
# O(n) time, O(1) space — Reversal Algorithm
def rotate(nums, k):
    n = len(nums)
    k = k % n  # Handle k > n
    nums.reverse()
    nums[:k] = reversed(nums[:k])
    nums[k:] = reversed(nums[k:])
```

---

## 7.3 Strings

### Problem 1: Check Palindrome
```python
def is_palindrome(s):
    s = s.lower().replace(' ', '')
    return s == s[::-1]

# Alternatively — Two Pointer approach
def is_palindrome_v2(s):
    s = s.lower().replace(' ', '')
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

### Problem 2: Check Anagram
```python
# Optimal: O(n) time using frequency array
def is_anagram(s1, s2):
    if len(s1) != len(s2):
        return False
    freq = {}
    for char in s1:
        freq[char] = freq.get(char, 0) + 1
    for char in s2:
        if char not in freq or freq[char] == 0:
            return False
        freq[char] -= 1
    return True

# Why NOT sorting? Sorting is O(n log n). Frequency approach is O(n).
```

### Problem 3: Reverse String / Reverse Words
```python
# Reverse string
def reverse_string(s):
    return s[::-1]

# Reverse words: "Hello World" → "World Hello"
def reverse_words(s):
    return ' '.join(s.split()[::-1])
```

### Problem 4: First Non-Repeating Character
```python
def first_non_repeating(s):
    freq = {}
    for char in s:
        freq[char] = freq.get(char, 0) + 1
    for char in s:
        if freq[char] == 1:
            return char
    return None
```

---

## 7.4 Linked Lists

### Node Definition
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### Problem 1: Reverse a Linked List
```python
# Iterative: O(n) time, O(1) space
def reverse_list(head):
    prev = None
    current = head
    while current:
        next_node = current.next   # Save next
        current.next = prev        # Reverse pointer
        prev = current             # Move prev forward
        current = next_node        # Move current forward
    return prev  # New head
```

### Problem 2: Detect Cycle — Floyd's Tortoise and Hare
```python
# O(n) time, O(1) space
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next           # 1 step
        fast = fast.next.next      # 2 steps
        if slow == fast:
            return True            # They met — cycle exists
    return False                   # fast reached end — no cycle
```

### Problem 3: Find Middle of Linked List
```python
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # slow is at the middle when fast reaches end
```

### Problem 4: Merge Two Sorted Linked Lists
```python
def merge_sorted(l1, l2):
    dummy = ListNode(0)
    current = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next
    current.next = l1 or l2
    return dummy.next
```

---

## 7.5 Sorting Algorithms

### Comparison Table

| Algorithm | Time (Best) | Time (Avg) | Time (Worst) | Space | Stable? |
|-----------|-------------|------------|--------------|-------|---------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | ✅ |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | ❌ |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | ✅ |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | ❌ |

### Implementations You Must Write

**Bubble Sort:**
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(n - i - 1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
                swapped = True
        if not swapped:
            break  # Optimization: stop if no swaps
    return arr
```

**Insertion Sort:**
```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j+1] = arr[j]
            j -= 1
        arr[j+1] = key
    return arr
```

**Merge Sort (Explain the Logic):**
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

---

## 7.6 Searching

### Binary Search (Must Write From Memory)
```python
def binary_search(arr, target):
    """Array MUST be sorted. O(log n) time."""
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1  # Not found
```

---

## 7.7 Stacks & Queues (Bonus — Know the Concepts)

```python
# Stack — LIFO (Last In, First Out)
stack = []
stack.append(1)    # Push
stack.append(2)
stack.pop()        # Pop → 2

# Queue — FIFO (First In, First Out)
from collections import deque
queue = deque()
queue.append(1)    # Enqueue
queue.append(2)
queue.popleft()    # Dequeue → 1
```

**Q: "Where did you use stack/queue concepts in your project?"**
> "The chunked ETL pipeline conceptually uses a queue pattern — chunks are processed in FIFO order. The logging system uses a stack-like trace for exception handling (the most recent call is at the top of the traceback)."

---

## 7.8 Fibonacci Series (Classic TCS Question)

```python
# Recursive: O(2^n) — TERRIBLE
def fib_recursive(n):
    if n <= 1:
        return n
    return fib_recursive(n-1) + fib_recursive(n-2)

# Dynamic Programming (Bottom-Up): O(n) time, O(n) space
def fib_dp(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# Space-Optimized: O(n) time, O(1) space
def fib_optimized(n):
    if n <= 1:
        return n
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b
```

---

## 7.9 Additional Practice Problems (Questions Only)

Master the above first. If time permits, practice these:

1. Remove duplicates from a sorted array (in-place)
2. Find the intersection of two arrays
3. Check if a string has all unique characters
4. Implement a stack using two queues
5. Find the longest common prefix among strings
6. Check balanced parentheses using a stack
7. Find the missing number in an array [1, 2, ..., n]
8. Dutch National Flag problem (sort 0s, 1s, 2s)
9. Implement `pow(x, n)` efficiently — O(log n)
10. Find the majority element (appears > n/2 times)

---

*Next: [08_CLOUD_ARCHITECTURE.md](./08_CLOUD_ARCHITECTURE.md) — Cloud, Architecture & Deployment*
