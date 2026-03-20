# 🧠 TCS NQT 2026 — Complete DSA Mastery Guide
# Every Pattern, Every Problem, Every Trick You Need

> **Your Exam:** TCS NQT 2026 (March 2026) | 2 Coding Questions | 90 Minutes
> **Target:** Digital (₹7-7.5 LPA) or Prime (₹9-11 LPA)
> **Language:** Python | **Source:** Your DSA.pdf (100+ Problems, 15 Patterns)
> **Philosophy:** Intuition First → Brute Force → Optimal → Clean Code

---

## 📋 Document Structure

This guide is split across multiple parts for readability:

| Part | Covers |
|------|--------|
| **Part 1** (this file) | Introduction + Patterns 1-4 (Two Pointers, Sliding Window, Fast & Slow, Math) |
| **Part 2** | Patterns 5-8 (Hash Maps, Binary Search, Stack, Dynamic Programming) |
| **Part 3** | Patterns 9-12 (BFS/DFS, Bit Manipulation, Backtracking, Merge Intervals) |
| **Part 4** | Patterns 13-15 (Kadane's, Prefix Sums, Heaps) + Exam Strategy + Revision Sheet |

---

## 🎯 Your 15 DSA Patterns — The Master Key

Before diving into individual topics, understand this: **every coding problem you'll ever face in TCS NQT maps to one (or a combination) of these 15 patterns.** Learn the pattern, and you can solve any variation.

| # | Pattern | Core Idea | When to Use |
|---|---------|-----------|-------------|
| 1 | Two Pointers | Two indices moving towards each other or in same direction | Sorted arrays, pairs, palindromes |
| 2 | Sliding Window | Fixed/variable-size window moving across data | Subarrays, substrings with constraints |
| 3 | Fast & Slow Pointers | Two pointers at different speeds | Cycles, middle of list, duplicates |
| 4 | Math & Number Theory | Mathematical properties and formulas | Primes, GCD, digit manipulation |
| 5 | Hash Maps | Key-value lookup for O(1) access | Frequency counting, pair finding |
| 6 | Binary Search | Halving search space each step | Sorted data, search problems |
| 7 | Stack (LIFO) | Last-In-First-Out processing | Matching brackets, expression eval |
| 8 | Dynamic Programming | Breaking into overlapping subproblems | Optimization, counting paths |
| 9 | BFS/DFS | Graph/tree traversal strategies | Trees, graphs, connected components |
| 10 | Bit Manipulation | Using binary operations (XOR, AND, OR) | Single number, power of 2 |
| 11 | Backtracking | Try all possibilities, undo bad choices | Permutations, subsets, combinations |
| 12 | Merge Intervals | Combining overlapping ranges | Scheduling, interval problems |
| 13 | Kadane's Algorithm | Maximum subarray sum tracking | Contiguous subarray problems |
| 14 | Prefix Sums | Precomputed cumulative sums | Range sum queries, subarray sums |
| 15 | Heaps/QuickSelect | Efficient min/max tracking | Kth largest/smallest, top-K |

---

# ═══════════════════════════════════════════════════
# PATTERN 1: TWO POINTERS (Collision & Parallel)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** The Two Pointers technique uses two index variables that move through a data structure (usually an array or string) simultaneously. Instead of using nested loops (O(n²)), we use two pointers to solve the problem in a single pass (O(n)).

**Why does it exist?** Many problems ask you to find pairs, reverse elements, or process data from both ends. Without two pointers, you'd need nested loops which are too slow. Two pointers lets you do this efficiently by maintaining "where I am looking" from two different positions.

**When do you use it in coding interviews?** Whenever you see a **sorted array** and need to find pairs, or when you need to rearrange elements in-place (like moving zeroes, removing duplicates), or when checking palindromes. The problem often says "do it in-place" or "O(1) extra space" — that's a huge hint for two pointers.

## 2. VISUAL EXPLANATION

**Real-life analogy:** Imagine you're in a library, and two friends start from opposite ends of a bookshelf, walking towards each other. Friend A starts from the left (smallest books), Friend B from the right (biggest books). They compare books as they walk — if the combined weight is too heavy, B steps left. If too light, A steps right. They meet in the middle. That's Two Pointers (Collision)!

**For Parallel Two Pointers:** Imagine two runners on a track. A slow one marks "where to write" and a fast one scans ahead. The fast one finds good elements and tells the slow one to write them.

**Step-by-step (Two Sum on sorted array):**
```
Array: [1, 3, 5, 7, 9]   Target = 8
        ↑              ↑
       left          right

Step 1: left=1, right=9 → sum=10 > 8 → move right ←
        [1, 3, 5, 7, 9]
         ↑        ↑

Step 2: left=1, right=7 → sum=8 = 8 → FOUND! Return [0, 3]
```

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| Initialize two pointers | O(1) | O(1) | Set left=0, right=len-1 |
| Move pointers | O(n) | O(1) | Each pointer moves at most n times |
| Total traversal | O(n) | O(1) | Single pass through data |

**Why use Two Pointers instead of nested loops?**
- Nested loops: O(n²) — checks every pair
- Two Pointers: O(n) — skips unnecessary pairs using sorted order

## 4. TEMPLATE CODE

### Template A: Collision (Opposite Directions)
```python
def two_pointer_collision(arr, target):
    """
    Template: Two pointers moving towards each other.
    Use when: array is SORTED, finding pairs, palindrome check.
    """
    left = 0                    # Start from beginning
    right = len(arr) - 1        # Start from end

    while left < right:         # Stop when they meet
        current = arr[left] + arr[right]

        if current == target:
            return [left, right]  # Found the answer
        elif current < target:
            left += 1             # Need bigger sum → move left forward
        else:
            right -= 1            # Need smaller sum → move right backward

    return []  # No pair found
```

### Template B: Parallel (Same Direction)
```python
def two_pointer_parallel(arr):
    """
    Template: Slow and fast pointer moving in same direction.
    Use when: removing elements, partitioning, overwriting in-place.
    """
    slow = 0  # Points to where we should write next

    for fast in range(len(arr)):       # Fast scans everything
        if arr[fast] != 0:             # Condition to keep element
            arr[slow] = arr[fast]      # Write at slow position
            slow += 1                  # Move slow forward

    # Everything after slow index needs to be filled (e.g., with 0)
    while slow < len(arr):
        arr[slow] = 0
        slow += 1

    return arr
```

## 5. PATTERN CONNECTION

- **Connects to:** Pattern 1 (Two Pointers), Pattern 3 (Fast & Slow), Pattern 6 (Binary Search — binary search is like a one-pointer collision)
- **Trigger phrases in problem statements:**
  1. "Find a pair in a **sorted** array that sums to..."
  2. "Do it **in-place** with O(1) extra space"
  3. "**Rearrange** the array so that..."

## 6. EASY WALKTHROUGH: Move Zeroes (LeetCode 283)

**Problem:** Given an array, move all 0s to the end while maintaining the order of non-zero elements. Do it in-place.

**Example:** `[0, 1, 0, 3, 12]` → `[1, 3, 12, 0, 0]`

**Thought process:**
1. I need to rearrange in-place → Two Pointers (Parallel)
2. `slow` points to where the next non-zero should go
3. `fast` scans every element
4. When `fast` finds a non-zero, swap it with `slow`'s position

```python
def moveZeroes(nums):
    """
    Move all zeroes to end, keep non-zero order.
    Pattern: Two Pointers (Parallel)
    TC: O(n) — single pass
    SC: O(1) — in-place
    """
    slow = 0  # Position where next non-zero should be placed

    for fast in range(len(nums)):
        if nums[fast] != 0:
            # Swap non-zero element to the slow position
            nums[slow], nums[fast] = nums[fast], nums[slow]
            slow += 1

    return nums

# Test
print(moveZeroes([0, 1, 0, 3, 12]))  # [1, 3, 12, 0, 0]
print(moveZeroes([0, 0, 0]))          # [0, 0, 0]
print(moveZeroes([1]))                # [1]
```

**Dry run:**
```
[0, 1, 0, 3, 12]    slow=0, fast=0: nums[0]=0, skip
                     slow=0, fast=1: nums[1]=1≠0, swap(0,1) → [1, 0, 0, 3, 12], slow=1
                     slow=1, fast=2: nums[2]=0, skip
                     slow=1, fast=3: nums[3]=3≠0, swap(1,3) → [1, 3, 0, 0, 12], slow=2
                     slow=2, fast=4: nums[4]=12≠0, swap(2,4) → [1, 3, 12, 0, 0], slow=3
Result: [1, 3, 12, 0, 0] ✓
```

## 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 7.1 Two Sum (LeetCode 1) — 🔴 HIGH Priority

**Problem Understanding:** Given an array of integers and a target, return indices of two numbers that add up to target.

**Key Observation:** If array is unsorted → use Hash Map (Pattern 5). If sorted → use Two Pointers. TCS may give sorted arrays!

**Approach 1: Brute Force**
```python
def twoSum_brute(nums, target):
    """
    Check every pair. TC: O(n²), SC: O(1)
    """
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] + nums[j] == target:
                return [i, j]
    return []
```

**Approach 2: Optimal (Hash Map)**
```python
def twoSum(nums, target):
    """
    Use hash map to find complement in O(1).
    TC: O(n), SC: O(n)
    """
    seen = {}  # value → index

    for i, num in enumerate(nums):
        complement = target - num    # What do I need?
        if complement in seen:       # Have I seen it before?
            return [seen[complement], i]
        seen[num] = i                # Remember this number

    return []
```

**Edge Cases:** Single element array, negative numbers, duplicate values, target = 0.

**TCS Tip:** TCS sometimes asks "find the pair" not "find indices" — read carefully!

**Quick Revision:** "Two Sum = for each number, check if (target - number) exists in a hash map."

---

### 7.2 Container With Most Water (LeetCode 11) — 🟡 MED Priority

**Problem Understanding:** Given heights of vertical lines, find two lines that form a container holding the most water.

**Key Observation:** Water = min(height[left], height[right]) × (right - left). Use collision two pointers — always move the shorter line inward because moving the taller one can only reduce the width.

**Approach 1: Brute Force**
```python
def maxArea_brute(height):
    """TC: O(n²), SC: O(1)"""
    max_water = 0
    for i in range(len(height)):
        for j in range(i + 1, len(height)):
            water = min(height[i], height[j]) * (j - i)
            max_water = max(max_water, water)
    return max_water
```

**Approach 2: Optimal (Two Pointers)**
```python
def maxArea(height):
    """
    Two Pointers: move the shorter line inward.
    TC: O(n), SC: O(1)
    """
    left, right = 0, len(height) - 1
    max_water = 0

    while left < right:
        # Calculate water for current pair
        width = right - left
        h = min(height[left], height[right])
        max_water = max(max_water, width * h)

        # Move the shorter line (it limits the water)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1

    return max_water
```

**Edge Cases:** All same heights, descending/ascending heights, only 2 elements.

**Quick Revision:** "Container Water = Two Pointers, always move the shorter wall inward."

---

### 7.3 3Sum (LeetCode 15) — 🟡 MED Priority

**Problem Understanding:** Find all unique triplets in the array that sum to zero.

**Key Observation:** Sort the array first. Fix one element, then use Two Pointer collision on the rest. Skip duplicates!

**Approach 1: Brute Force**
```python
def threeSum_brute(nums):
    """TC: O(n³), SC: O(1)"""
    result = set()
    nums.sort()
    for i in range(len(nums)):
        for j in range(i+1, len(nums)):
            for k in range(j+1, len(nums)):
                if nums[i] + nums[j] + nums[k] == 0:
                    result.add((nums[i], nums[j], nums[k]))
    return [list(t) for t in result]
```

**Approach 2: Optimal (Sort + Two Pointers)**
```python
def threeSum(nums):
    """
    Fix one number, two-pointer for the rest.
    TC: O(n²), SC: O(1) extra (excluding output)
    """
    nums.sort()  # Sort first!
    result = []

    for i in range(len(nums) - 2):
        # Skip duplicate for first number
        if i > 0 and nums[i] == nums[i - 1]:
            continue

        left = i + 1
        right = len(nums) - 1

        while left < right:
            total = nums[i] + nums[left] + nums[right]

            if total == 0:
                result.append([nums[i], nums[left], nums[right]])
                # Skip duplicates for second and third numbers
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
            elif total < 0:
                left += 1
            else:
                right -= 1

    return result
```

**Edge Cases:** All zeroes `[0,0,0]`, less than 3 elements, no valid triplet.

**Quick Revision:** "3Sum = Sort + Fix one + Two Pointers on rest. Skip duplicates!"

---

### 7.4 Trapping Rain Water (LeetCode 42) — 🟢 LOW Priority (Hard)

**Problem Understanding:** Given elevation map bars, find how much water can be trapped after rain.

**Key Observation:** Water at position i = min(max_left, max_right) - height[i]. Use two pointers tracking max from each side.

```python
def trap(height):
    """
    Two Pointers tracking max from both sides.
    TC: O(n), SC: O(1)
    """
    if not height:
        return 0

    left, right = 0, len(height) - 1
    left_max, right_max = height[left], height[right]
    water = 0

    while left < right:
        if left_max < right_max:
            left += 1
            left_max = max(left_max, height[left])
            water += left_max - height[left]
        else:
            right -= 1
            right_max = max(right_max, height[right])
            water += right_max - height[right]

    return water
```

**Quick Revision:** "Trapping Rain = Two Pointers + track max_left and max_right, water = min(maxes) - current_height."

---

### 7.5 Remove Duplicates from Sorted Array (LeetCode 26) — 🔴 HIGH Priority

**Problem Understanding:** Remove duplicates in-place from a sorted array. Return the count of unique elements.

```python
def removeDuplicates(nums):
    """
    Parallel Two Pointers: slow writes, fast scans.
    TC: O(n), SC: O(1)
    """
    if not nums:
        return 0

    slow = 0  # Points to last unique element

    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow]:  # Found a new unique element
            slow += 1
            nums[slow] = nums[fast]   # Write it at next position

    return slow + 1  # Count of unique elements

# Test: [1,1,2,2,3] → [1,2,3,_,_], returns 3
```

**Quick Revision:** "Remove Duplicates = slow marks last unique, fast scans for next different element."

---

### 7.6 Sort Colors / Dutch National Flag (LeetCode 75) — 🔴 HIGH Priority

**Problem Understanding:** Sort array of 0s, 1s, 2s in one pass without using sort().

**⚠️ TCS ALERT:** This is a TCS favorite! They love asking you to sort without built-in sort functions.

```python
def sortColors(nums):
    """
    Dutch National Flag: Three pointers (low, mid, high).
    TC: O(n), SC: O(1)
    """
    low = 0            # Everything before low is 0
    mid = 0            # Current element being examined
    high = len(nums) - 1  # Everything after high is 2

    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1           # 1 is already in correct zone
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1          # Don't increment mid! We haven't checked swapped element

    return nums

# Test: [2,0,2,1,1,0] → [0,0,1,1,2,2]
```

**Why not increment mid when swapping with high?** Because the element swapped from `high` hasn't been examined yet — it could be 0, 1, or 2.

**Quick Revision:** "Sort Colors = 3 pointers: low(0s), mid(scan), high(2s). Swap 0→low, skip 1, swap 2→high."

---

### 7.7 Valid Palindrome (LeetCode 125) — 🔴 HIGH Priority

```python
def isPalindrome(s):
    """
    Two Pointers from both ends, skip non-alphanumeric.
    TC: O(n), SC: O(1)
    """
    left, right = 0, len(s) - 1

    while left < right:
        # Skip non-alphanumeric characters
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1

        if s[left].lower() != s[right].lower():
            return False

        left += 1
        right -= 1

    return True

# "A man, a plan, a canal: Panama" → True
```

**TCS Tip:** TCS may ask "check palindrome without using built-in reverse." This two-pointer approach is exactly what they want!

**Quick Revision:** "Palindrome = Two Pointers from ends, skip non-alnum, compare lowercase."

---

### 7.8 Reverse String (LeetCode 344) — 🔴 HIGH Priority

```python
def reverseString(s):
    """
    Two Pointers: swap from both ends.
    TC: O(n), SC: O(1)
    """
    left, right = 0, len(s) - 1
    while left < right:
        s[left], s[right] = s[right], s[left]
        left += 1
        right -= 1
    return s
```

**TCS Tip:** Never use `s[::-1]` in TCS! They want to see the manual approach.

**Quick Revision:** "Reverse = Two Pointers swap from ends, move inward."

---

### 7.9 Merge Sorted Array (LeetCode 88) — 🔴 HIGH Priority

**Problem Understanding:** Merge nums2 into nums1 (which has extra space). Both are sorted.

**Key Insight:** Start from the END to avoid overwriting elements!

```python
def merge(nums1, m, nums2, n):
    """
    Merge from the end using two pointers.
    TC: O(m+n), SC: O(1)
    """
    p1 = m - 1     # Last real element in nums1
    p2 = n - 1     # Last element in nums2
    write = m + n - 1  # Where to write next (end of nums1)

    while p2 >= 0:
        if p1 >= 0 and nums1[p1] > nums2[p2]:
            nums1[write] = nums1[p1]
            p1 -= 1
        else:
            nums1[write] = nums2[p2]
            p2 -= 1
        write -= 1

    return nums1
```

**Quick Revision:** "Merge Sorted = Start from END, fill largest elements first."

---

### 7.10 Reverse Words in a String (LeetCode 151/557) — 🟡 MED Priority

```python
def reverseWords(s):
    """
    Split, reverse, join. For TCS: may need manual approach.
    TC: O(n), SC: O(n)
    """
    words = s.split()     # Split by whitespace (handles multiple spaces)
    words.reverse()       # Reverse the list of words
    return ' '.join(words)

# Manual approach (TCS-safe — no split/join):
def reverseWords_manual(s):
    """Manual reverse without built-in split."""
    # Step 1: Reverse entire string
    s_list = list(s.strip())
    s_list.reverse()

    # Step 2: Reverse each word individually
    result = []
    word = []
    for char in s_list:
        if char != ' ':
            word.append(char)
        else:
            if word:
                word.reverse()
                result.extend(word)
                result.append(' ')
                word = []
    if word:
        word.reverse()
        result.extend(word)

    return ''.join(result)
```

**Quick Revision:** "Reverse Words = reverse entire string, then reverse each word."

---

## 8. TCS-SPECIFIC NOTES for Two Pointers

1. **TCS loves in-place operations** — Two Pointers is your go-to
2. **Never use `sort()` for Sort Colors** — implement Dutch National Flag manually
3. **Never use `[::-1]` for reversing** — use the swap-based two-pointer approach
4. **Common edge cases TCS tests:** empty arrays, single element, all duplicates, already sorted

## 9. QUICK QUIZ — Two Pointers

1. **Q:** What's the key requirement for using collision two pointers to find a pair sum?
   **A:** The array must be **sorted**.

2. **Q:** In Sort Colors (Dutch National Flag), why don't we increment `mid` after swapping with `high`?
   **A:** Because the swapped element from `high` hasn't been examined yet.

3. **Q:** When would you choose Two Pointers over Hash Map for Two Sum?
   **A:** When the array is **sorted** and you need **O(1) space** instead of O(n).

---

# ═══════════════════════════════════════════════════
# PATTERN 2: SLIDING WINDOW
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** Sliding Window maintains a "window" (a contiguous subarray/substring) that slides across the data. Instead of recalculating from scratch for every possible window, we add the new element entering the window and remove the one leaving — making it O(n) instead of O(n²).

**Why does it exist?** Many problems ask about "the best subarray of size k" or "the smallest substring containing X." Without sliding window, you'd check every possible subarray — O(n²) or worse. Sliding window lets you process each element at most twice (once when entering the window, once when leaving).

**When to use in interviews?** Whenever the problem mentions **"contiguous subarray/substring"** with some **constraint on the window** (fixed size, or variable size with a condition). Keywords like "maximum sum of k elements," "longest substring without repeating," and "minimum window" all scream sliding window.

## 2. VISUAL EXPLANATION

**Analogy:** Imagine looking through a small rectangular frame (like a picture frame) at a long painting. You slide the frame left to right — at each position, you see a different portion. You never need to re-examine the entire painting; you just note what enters the frame on the right and what leaves on the left.

**Step-by-step (Max sum of subarray of size 3):**
```
Array: [2, 1, 5, 1, 3, 2]   k = 3

Window 1: [2, 1, 5] = 8       ← Initial window
Window 2: [1, 5, 1] = 7       ← Remove 2, Add 1
Window 3: [5, 1, 3] = 9       ← Remove 1, Add 3  ← MAX!
Window 4: [1, 3, 2] = 6       ← Remove 5, Add 2

Answer: 9
```

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Time | Space |
|-----------|------|-------|
| Fixed-size window | O(n) | O(1) |
| Variable-size window | O(n) | O(k) where k = unique elements |
| Brute force alternative | O(n×k) or O(n²) | — |

## 4. TEMPLATE CODE

### Template A: Fixed-Size Window
```python
def fixed_sliding_window(arr, k):
    """
    Template: Window of fixed size k.
    Use when: "maximum/minimum sum of k consecutive elements"
    """
    # Step 1: Calculate sum of first window
    window_sum = sum(arr[:k])
    max_sum = window_sum

    # Step 2: Slide the window
    for i in range(k, len(arr)):
        window_sum += arr[i]        # Add new element (right side)
        window_sum -= arr[i - k]    # Remove old element (left side)
        max_sum = max(max_sum, window_sum)

    return max_sum
```

### Template B: Variable-Size Window
```python
def variable_sliding_window(s):
    """
    Template: Window that grows/shrinks based on condition.
    Use when: "longest/shortest substring/subarray satisfying..."
    """
    left = 0
    best = 0
    window = {}  # Track window contents (e.g., character frequency)

    for right in range(len(s)):
        # EXPAND: Add s[right] to window
        window[s[right]] = window.get(s[right], 0) + 1

        # SHRINK: While window is invalid, remove from left
        while window_is_invalid(window):  # Define your condition
            window[s[left]] -= 1
            if window[s[left]] == 0:
                del window[s[left]]
            left += 1

        # UPDATE: Track the best valid window
        best = max(best, right - left + 1)

    return best
```

## 5. PATTERN CONNECTION

- **Connects to:** Pattern 5 (Hash Maps — often used inside the window), Pattern 1 (Two Pointers — the left and right of the window ARE two pointers)
- **Trigger phrases:**
  1. "Find the **longest/shortest substring/subarray** that..."
  2. "**Maximum sum** of k consecutive elements"
  3. "**Minimum window** containing all characters of..."

## 6. EASY WALKTHROUGH: Max Ascending Subarray Sum (LeetCode 1800)

**Problem:** Find the maximum sum of any ascending contiguous subarray.

```python
def maxAscendingSum(nums):
    """
    Track current ascending sum, reset when not ascending.
    TC: O(n), SC: O(1)
    """
    max_sum = nums[0]
    current_sum = nums[0]

    for i in range(1, len(nums)):
        if nums[i] > nums[i - 1]:
            current_sum += nums[i]  # Still ascending, extend window
        else:
            current_sum = nums[i]   # Reset window

        max_sum = max(max_sum, current_sum)

    return max_sum

# [10,20,30,5,10,50] → 10+20+30=60
```

## 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 7.1 Longest Substring Without Repeating Characters (LeetCode 3) — 🔴 HIGH Priority

**Problem:** Find the length of the longest substring without repeating characters.

**Key Observation:** Variable-size sliding window + hash set to track characters in current window.

**Approach 1: Brute Force**
```python
def lengthOfLongestSubstring_brute(s):
    """Check every substring. TC: O(n³), SC: O(n)"""
    max_len = 0
    for i in range(len(s)):
        for j in range(i, len(s)):
            substring = s[i:j+1]
            if len(set(substring)) == len(substring):  # All unique
                max_len = max(max_len, len(substring))
    return max_len
```

**Approach 2: Optimal (Sliding Window)**
```python
def lengthOfLongestSubstring(s):
    """
    Variable sliding window with hash set.
    TC: O(n), SC: O(min(n, 26)) — at most 26 lowercase letters
    """
    char_set = set()      # Characters in current window
    left = 0
    max_len = 0

    for right in range(len(s)):
        # Shrink window until no duplicate
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1

        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)

    return max_len

# "abcabcbb" → 3 ("abc")
# "bbbbb" → 1 ("b")
# "pwwkew" → 3 ("wke")
```

**Edge Cases:** Empty string, all same characters, all unique characters.

**Quick Revision:** "Longest unique substring = sliding window + set, shrink from left when duplicate found."

---

### 7.2 Minimum Size Subarray Sum (LeetCode 209) — implied in PDF

**Problem:** Find minimum length subarray with sum ≥ target.

```python
def minSubArrayLen(target, nums):
    """
    Variable sliding window: expand right, shrink left when sum >= target.
    TC: O(n), SC: O(1)
    """
    left = 0
    current_sum = 0
    min_len = float('inf')

    for right in range(len(nums)):
        current_sum += nums[right]

        while current_sum >= target:
            min_len = min(min_len, right - left + 1)
            current_sum -= nums[left]
            left += 1

    return min_len if min_len != float('inf') else 0
```

**Quick Revision:** "Min subarray with sum ≥ target = expand window, shrink when sum is enough."

---

### 7.3 Find All Anagrams in a String (LeetCode 438) — implied in PDF

```python
def findAnagrams(s, p):
    """
    Fixed sliding window of size len(p), compare character frequencies.
    TC: O(n), SC: O(1) — at most 26 chars
    """
    from collections import Counter
    if len(p) > len(s):
        return []

    p_count = Counter(p)
    window = Counter(s[:len(p)])
    result = []

    if window == p_count:
        result.append(0)

    for i in range(len(p), len(s)):
        # Add new char
        window[s[i]] += 1
        # Remove old char
        old_char = s[i - len(p)]
        window[old_char] -= 1
        if window[old_char] == 0:
            del window[old_char]

        if window == p_count:
            result.append(i - len(p) + 1)

    return result
```

**Quick Revision:** "Find Anagrams = fixed window of size len(p), compare frequency counts."

---

## 8. TCS-SPECIFIC NOTES for Sliding Window

1. TCS rarely asks "sliding window" by name — they describe it as "find longest/shortest subarray"
2. Always handle the empty string / empty array case first
3. For TCS, **Longest Substring Without Repeating** is the most commonly asked sliding window problem
4. Remember: sliding window only works for **contiguous** subarrays/substrings

## 9. QUICK QUIZ — Sliding Window

1. **Q:** What's the difference between fixed and variable sliding window?
   **A:** Fixed: window size is given (always k). Variable: window size changes based on a condition (grow right, shrink left).

2. **Q:** Can sliding window be used for non-contiguous subsequences?
   **A:** **No.** Sliding window only works for **contiguous** subarrays/substrings.

3. **Q:** What data structure do you typically pair with a variable sliding window?
   **A:** A **hash map** (or hash set) to track the contents of the current window.

---

# ═══════════════════════════════════════════════════
# PATTERN 3: FAST & SLOW POINTERS (Tortoise & Hare)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** Two pointers moving at different speeds through a data structure. The "slow" pointer moves one step at a time, the "fast" pointer moves two steps. If there's a cycle, fast will eventually catch up to slow (they'll meet). If there's no cycle, fast will reach the end.

**Why does it exist?** Detecting cycles in linked lists or arrays without using extra space (no hash set). Also perfect for finding the middle of a linked list in one pass.

**When to use?** Problems involving cycles (linked list cycle detection), finding the middle element, or detecting duplicates in specific array setups (like Floyd's algorithm for LeetCode 287).

## 2. VISUAL EXPLANATION

**Analogy:** Two runners on a circular track. One runs at 2x speed. If the track is circular (has a cycle), the fast runner will lap the slow runner — they'll meet. If the track is straight (no cycle), the fast runner reaches the finish line first.

```
Linked List with cycle:
1 → 2 → 3 → 4 → 5
              ↑       ↓
              7 ← 6

Slow: 1→2→3→4→5→6→7→4→5→6
Fast: 1→3→5→7→5→7→5→7...
They meet at node 5 or 7!
```

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Time | Space |
|-----------|------|-------|
| Cycle detection | O(n) | O(1) |
| Find middle | O(n) | O(1) |
| Find cycle start | O(n) | O(1) |

## 4. TEMPLATE CODE

```python
def has_cycle(head):
    """Template: Floyd's Cycle Detection."""
    slow = head
    fast = head

    while fast and fast.next:
        slow = slow.next          # Move 1 step
        fast = fast.next.next     # Move 2 steps
        if slow == fast:
            return True           # Cycle detected!

    return False  # Fast reached end → no cycle
```

```python
def find_middle(head):
    """When fast reaches end, slow is at middle."""
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # This is the middle node
```

## 5. PATTERN CONNECTION

- **Trigger phrases:**
  1. "Detect if there is a **cycle** in..."
  2. "Find the **middle** of a linked list"
  3. "Find the **duplicate** number (without modifying array)"

## 6 & 7. PROBLEMS — DETAILED SOLUTIONS

### Linked List Cycle (LeetCode 141) — 🔴 HIGH Priority

```python
def hasCycle(head):
    """
    Floyd's cycle detection.
    TC: O(n), SC: O(1)
    """
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

### Middle of the Linked List (LeetCode 876) — 🔴 HIGH Priority

```python
def middleNode(head):
    """
    When fast reaches end, slow is at middle.
    TC: O(n), SC: O(1)
    """
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

### Find the Duplicate Number (LeetCode 287) — 🟡 MED Priority

**Problem:** Array of n+1 integers in range [1, n]. Find the duplicate without modifying array.

**Key Insight:** Treat array indices as a linked list. Index i points to nums[i]. A duplicate means two indices point to the same node → cycle!

```python
def findDuplicate(nums):
    """
    Floyd's Cycle Detection on array (treat as linked list).
    TC: O(n), SC: O(1)
    """
    # Phase 1: Find meeting point
    slow = fast = nums[0]
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]
        if slow == fast:
            break

    # Phase 2: Find cycle entrance (= duplicate)
    slow = nums[0]
    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]

    return slow
```

### Palindrome Linked List (LeetCode 234) — 🟡 MED Priority

**Approach:** Find middle → reverse second half → compare both halves.

```python
def isPalindrome(head):
    """
    Fast/Slow to find middle, reverse second half, compare.
    TC: O(n), SC: O(1)
    """
    # Step 1: Find middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    # Step 2: Reverse second half
    prev = None
    curr = slow
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt

    # Step 3: Compare first and second halves
    first, second = head, prev
    while second:
        if first.val != second.val:
            return False
        first = first.next
        second = second.next

    return True
```

**Quick Revision:** "Palindrome LL = find middle (fast/slow) + reverse second half + compare."

## 8. TCS-SPECIFIC NOTES

- TCS may ask you to implement a linked list from scratch — know the Node class
- Always handle: empty list, single node, two nodes
- TCS loves "find middle" and "detect cycle" — these are guaranteed easy marks

## 9. QUICK QUIZ

1. **Q:** If fast moves 3x speed instead of 2x, does cycle detection still work?
   **A:** Yes, but they may take longer to meet. 2x is optimal.

2. **Q:** For an even-length list [1,2,3,4], which node does the slow pointer stop at?
   **A:** Node 3 (the second of the two middle nodes).

3. **Q:** What's the space complexity advantage of fast/slow over using a hash set for cycle detection?
   **A:** O(1) vs O(n). Fast/slow uses no extra space.

---

# ═══════════════════════════════════════════════════
# PATTERN 4: MATH & NUMBER THEORY (TCS Specials!)
# ═══════════════════════════════════════════════════

## 🚨 THIS IS THE MOST IMPORTANT PATTERN FOR TCS NQT! 🚨

## 1. CONCEPT INTRO

**What is it?** Problems based on mathematical properties: digit manipulation, prime numbers, GCD/LCM, number bases, special number types (palindrome, Armstrong, perfect, etc.). These don't need fancy data structures — just math logic.

**Why is this #1 for TCS?** TCS NQT almost ALWAYS has a math/number theory problem. It's their favorite category because it tests basic programming logic without requiring advanced DSA knowledge.

**When to use?** When the problem involves: digits of a number, checking properties (prime, palindrome, perfect), converting between bases (binary, octal, decimal), or computing GCD/LCM/factorial/fibonacci.

## 2. VISUAL EXPLANATION

**Digit Extraction (the most important skill):**
```
Number: 1234

Step 1: 1234 % 10 = 4  (last digit)    1234 // 10 = 123
Step 2: 123 % 10 = 3   (last digit)    123 // 10 = 12
Step 3: 12 % 10 = 2    (last digit)    12 // 10 = 1
Step 4: 1 % 10 = 1     (last digit)    1 // 10 = 0  → STOP

% 10 = extract last digit
// 10 = remove last digit
```

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Time | Formula |
|-----------|------|---------|
| Extract digits | O(d) where d = # of digits | `n % 10`, `n // 10` |
| Check prime | O(√n) | Check divisibility up to √n |
| GCD | O(log(min(a,b))) | Euclidean algorithm |
| Factorial | O(n) | Loop or recursion |
| Fibonacci | O(n) | Iterative with two variables |

## 4 & 7. TEMPLATE CODE + ALL PROBLEMS

### 4.1 Reverse Integer (LeetCode 7) — 🔴 HIGH Priority

```python
def reverse(x):
    """
    Extract digits from end, build reversed number.
    TC: O(d) where d = number of digits, SC: O(1)

    ⚠️ TCS TIP: Handle negative numbers and overflow!
    """
    sign = -1 if x < 0 else 1
    x = abs(x)
    reversed_num = 0

    while x > 0:
        digit = x % 10          # Extract last digit
        reversed_num = reversed_num * 10 + digit  # Append to result
        x = x // 10             # Remove last digit

    reversed_num *= sign

    # Check 32-bit integer overflow
    if reversed_num < -2**31 or reversed_num > 2**31 - 1:
        return 0

    return reversed_num

# 123 → 321, -123 → -321, 120 → 21
```

### 4.2 Palindrome Number (LeetCode 9) — 🔴 HIGH Priority

```python
def isPalindrome(x):
    """
    Reverse the number and compare. Negative numbers are NOT palindromes.
    TC: O(d), SC: O(1)
    """
    if x < 0:
        return False  # Negative numbers are never palindromes

    original = x
    reversed_num = 0

    while x > 0:
        reversed_num = reversed_num * 10 + x % 10
        x //= 10

    return original == reversed_num

# 121 → True, -121 → False, 10 → False
```

**TCS Tip:** Don't convert to string! TCS wants the mathematical approach.

### 4.3 Armstrong Number — 🔴 HIGH Priority

**An Armstrong number of n digits:** sum of each digit raised to power n equals the number itself.
Example: 153 = 1³ + 5³ + 3³ = 1 + 125 + 27 = 153 ✓

```python
def isArmstrong(num):
    """TC: O(d), SC: O(1)"""
    original = num
    n_digits = len(str(num))  # Count digits
    total = 0
    temp = num

    while temp > 0:
        digit = temp % 10
        total += digit ** n_digits
        temp //= 10

    return total == original

# Without str():
def count_digits(num):
    count = 0
    while num > 0:
        count += 1
        num //= 10
    return count
```

### 4.4 Count Primes / Sieve of Eratosthenes (LeetCode 204) — 🔴 HIGH Priority

```python
def countPrimes(n):
    """
    Sieve of Eratosthenes: mark multiples of each prime as non-prime.
    TC: O(n log log n), SC: O(n)
    """
    if n <= 2:
        return 0

    is_prime = [True] * n
    is_prime[0] = is_prime[1] = False  # 0 and 1 are not prime

    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            # Mark all multiples of i as not prime
            for j in range(i*i, n, i):
                is_prime[j] = False

    return sum(is_prime)

# Simple prime check for a single number:
def is_prime(n):
    """TC: O(√n)"""
    if n <= 1:
        return False
    if n <= 3:
        return True
    if n % 2 == 0 or n % 3 == 0:
        return False
    i = 5
    while i * i <= n:
        if n % i == 0 or n % (i + 2) == 0:
            return False
        i += 6
    return True
```

**TCS Tip:** They love asking "print all primes up to N" or "check if N is prime." Know BOTH the sieve and the simple check.

### 4.5 GCD & LCM — 🔴 HIGH Priority

```python
def gcd(a, b):
    """
    Euclidean Algorithm.
    TC: O(log(min(a,b))), SC: O(1)
    """
    while b:
        a, b = b, a % b
    return a

def lcm(a, b):
    """LCM = (a × b) / GCD(a, b)"""
    return (a * b) // gcd(a, b)

# GCD(12, 8) = 4
# LCM(12, 8) = 24
```

**⚠️ TCS TIP:** Don't use `math.gcd()` — implement it manually!

### 4.6 Binary ↔ Decimal Conversion — 🔴 HIGH Priority

```python
def decimal_to_binary(n):
    """Convert decimal to binary string WITHOUT bin()."""
    if n == 0:
        return "0"
    result = ""
    while n > 0:
        result = str(n % 2) + result
        n //= 2
    return result

def binary_to_decimal(binary_str):
    """Convert binary string to decimal WITHOUT int()."""
    result = 0
    for bit in binary_str:
        result = result * 2 + int(bit)
    return result

# decimal_to_binary(13) → "1101"
# binary_to_decimal("1101") → 13
```

### 4.7 Factorial & Fibonacci — 🟡 MED Priority

```python
def factorial(n):
    """TC: O(n), SC: O(1)"""
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

def fibonacci(n):
    """Return nth Fibonacci number. TC: O(n), SC: O(1)"""
    if n <= 1:
        return n
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b

# fibonacci(6) → 8 (0,1,1,2,3,5,8)
```

### 4.8 Power of Two/Three (LeetCode 231/326) — 🟡 MED Priority

```python
def isPowerOfTwo(n):
    """A power of 2 has exactly one bit set: n & (n-1) == 0"""
    return n > 0 and (n & (n - 1)) == 0

def isPowerOfThree(n):
    """Keep dividing by 3."""
    if n <= 0:
        return False
    while n % 3 == 0:
        n //= 3
    return n == 1
```

### 4.9 Plus One (LeetCode 66) — 🟡 MED Priority

```python
def plusOne(digits):
    """
    Add 1 to number represented as array of digits.
    TC: O(n), SC: O(1) or O(n) if new digit needed
    """
    carry = 1
    for i in range(len(digits) - 1, -1, -1):
        total = digits[i] + carry
        digits[i] = total % 10
        carry = total // 10
        if carry == 0:
            break

    if carry:
        digits.insert(0, 1)

    return digits

# [1,2,9] → [1,3,0]
# [9,9,9] → [1,0,0,0]
```

### 4.10 Add Binary (LeetCode 67) — 🟡 MED Priority

```python
def addBinary(a, b):
    """
    Add two binary strings. TC: O(max(m,n)), SC: O(max(m,n))
    """
    result = []
    carry = 0
    i, j = len(a) - 1, len(b) - 1

    while i >= 0 or j >= 0 or carry:
        total = carry
        if i >= 0:
            total += int(a[i])
            i -= 1
        if j >= 0:
            total += int(b[j])
            j -= 1
        result.append(str(total % 2))
        carry = total // 2

    return ''.join(reversed(result))

# addBinary("11", "1") → "100"
```

### 4.11 Happy Number (LeetCode 202) — implied

```python
def isHappy(n):
    """
    Sum of squares of digits repeatedly. If reaches 1 → happy.
    Use fast/slow to detect cycle!
    TC: O(log n), SC: O(1)
    """
    def digit_sum_sq(num):
        total = 0
        while num > 0:
            digit = num % 10
            total += digit ** 2
            num //= 10
        return total

    slow = n
    fast = digit_sum_sq(n)

    while fast != 1 and slow != fast:
        slow = digit_sum_sq(slow)
        fast = digit_sum_sq(digit_sum_sq(fast))

    return fast == 1
```

### 4.12 Roman to Integer (LeetCode 13) — 🟡 MED Priority

```python
def romanToInt(s):
    """
    If smaller value comes before larger, subtract it.
    TC: O(n), SC: O(1)
    """
    roman = {'I': 1, 'V': 5, 'X': 10, 'L': 50,
             'C': 100, 'D': 500, 'M': 1000}
    result = 0

    for i in range(len(s)):
        if i + 1 < len(s) and roman[s[i]] < roman[s[i + 1]]:
            result -= roman[s[i]]  # Subtract (e.g., IV = -1 + 5)
        else:
            result += roman[s[i]]

    return result

# "MCMXCIV" → 1994
```

## 8. TCS-SPECIFIC NOTES

1. **Number Theory appears in EVERY TCS NQT exam** — master this first!
2. **Never use built-in functions:** No `bin()`, `math.gcd()`, `math.factorial()` — implement manually
3. **Overflow handling:** Python handles big integers, but TCS may ask for 32-bit range checks
4. **Common TCS asks:** "Is N prime?", "Reverse a number", "Convert binary to decimal", "Find GCD"
5. **Edge cases TCS loves:** n = 0, n = 1, negative numbers, very large numbers

## 9. QUICK QUIZ

1. **Q:** What's the time complexity of checking if a number is prime using trial division?
   **A:** O(√n) — only check divisibility up to the square root.

2. **Q:** Why is n & (n-1) == 0 a valid check for power of 2?
   **A:** Powers of 2 have exactly one bit set (1000...). n-1 flips all bits after that (0111...). AND gives 0.

3. **Q:** What's the GCD of 0 and 5?
   **A:** 5. GCD(0, n) = n.

---

*Continue to Part 2 for Patterns 5-8: Hash Maps, Binary Search, Stack, and Dynamic Programming...*
