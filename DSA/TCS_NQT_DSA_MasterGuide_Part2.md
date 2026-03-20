# 🧠 TCS NQT 2026 — DSA Master Guide — PART 2
# Patterns 5-8: Hash Maps, Binary Search, Stack, Dynamic Programming

> **Continued from Part 1** (Patterns 1-4)

---

# ═══════════════════════════════════════════════════
# PATTERN 5: HASH MAPS (Frequency & Lookup)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** A Hash Map (dictionary in Python) stores key-value pairs with O(1) average lookup, insertion, and deletion. It uses a hash function to convert keys into array indices, allowing near-instant access.

**Why does it exist?** Searching in a list is O(n). With a hash map, you can check "does this element exist?" or "how many times does this appear?" in O(1). This turns many O(n²) brute force solutions into O(n).

**When to use in interviews?** Whenever you need to:
- **Count frequency** of elements ("how many times does each appear?")
- **Find pairs/complements** ("is there an element that completes this?")
- **Remove duplicates** or **group elements**
- The words "frequency," "count," "unique," or "lookup" appear in the problem

## 2. VISUAL EXPLANATION

**Analogy:** A hash map is like a phone book. You know someone's name (key) and instantly find their number (value). You don't scan page by page — you jump straight to the right letter.

```
Array: [1, 2, 2, 3, 3, 3]

Building frequency map:
{} → {1: 1} → {1: 1, 2: 1} → {1: 1, 2: 2} → {1: 1, 2: 2, 3: 1}
→ {1: 1, 2: 2, 3: 2} → {1: 1, 2: 2, 3: 3}

Lookup "how many 3s?" → map[3] = 3, done in O(1)!
```

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Hash Map | List/Array |
|-----------|----------|-----------|
| Lookup | O(1) avg | O(n) |
| Insert | O(1) avg | O(1) append, O(n) insert |
| Delete | O(1) avg | O(n) |
| Space | O(n) | O(n) |

**Why use hash map instead of list for lookup?** List search is O(n), hash map is O(1). The tradeoff is extra O(n) space.

## 4. TEMPLATE CODE

```python
# Template: Frequency Counter
def frequency_count(arr):
    """Count how many times each element appears."""
    freq = {}
    for item in arr:
        freq[item] = freq.get(item, 0) + 1
    return freq

# Template: Two Sum Pattern (find complement)
def find_pair(arr, target):
    """Find if any two elements sum to target."""
    seen = {}  # value → index
    for i, num in enumerate(arr):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

# Template: Grouping by key
def group_by_key(items, key_func):
    """Group items by a computed key."""
    groups = {}
    for item in items:
        key = key_func(item)
        if key not in groups:
            groups[key] = []
        groups[key].append(item)
    return groups
```

## 5. PATTERN CONNECTION

- **Connects to:** Pattern 2 (Sliding Window — hash map tracks window contents), Pattern 1 (Two Pointers — hash map is an alternative when array is unsorted)
- **Trigger phrases:**
  1. "Find **how many times** each element appears"
  2. "Check if there **exists** a pair/complement"
  3. "**Group** elements by some property"

## 6 & 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 5.1 Two Sum (LeetCode 1) — 🔴 HIGH Priority

*(Already covered in Pattern 1, but the optimal solution uses Hash Map)*

```python
def twoSum(nums, target):
    """
    Hash Map: for each number, check if complement exists.
    TC: O(n), SC: O(n)
    """
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

**Quick Revision:** "Two Sum = Hash Map, store number→index, check if (target-num) exists."

### 5.2 Contains Duplicate (LeetCode 217) — 🔴 HIGH Priority

**Problem:** Return True if any value appears at least twice.

**Brute Force:** Compare every pair → O(n²)

**Optimal:**
```python
def containsDuplicate(nums):
    """
    Use a set (simplified hash map). If we try to add a duplicate, it already exists.
    TC: O(n), SC: O(n)
    """
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False

# Even simpler:
def containsDuplicate_v2(nums):
    return len(nums) != len(set(nums))
```

**TCS Tip:** The one-liner `len(nums) != len(set(nums))` is elegant but TCS may want the loop version to check your logic.

**Quick Revision:** "Contains Duplicate = Set. If element already in set → duplicate found."

### 5.3 Valid Anagram (LeetCode 242) — 🔴 HIGH Priority

**Problem:** Check if string t is an anagram of string s.

```python
def isAnagram(s, t):
    """
    Compare character frequency maps.
    TC: O(n), SC: O(1) — at most 26 lowercase letters
    """
    if len(s) != len(t):
        return False

    freq = {}
    for char in s:
        freq[char] = freq.get(char, 0) + 1

    for char in t:
        if char not in freq or freq[char] == 0:
            return False
        freq[char] -= 1

    return True

# "anagram" and "nagaram" → True
# "rat" and "car" → False
```

**TCS Tip:** Don't use `sorted(s) == sorted(t)` — that's O(n log n). Hash map is O(n).

**Quick Revision:** "Anagram = same character frequencies. Use hash map to compare."

### 5.4 First Unique Character in a String (LeetCode 387) — 🔴 HIGH Priority

```python
def firstUniqChar(s):
    """
    Count frequency, then find first char with count 1.
    TC: O(n), SC: O(1) — at most 26 chars
    """
    freq = {}
    for char in s:
        freq[char] = freq.get(char, 0) + 1

    for i, char in enumerate(s):
        if freq[char] == 1:
            return i

    return -1

# "leetcode" → 0 (first 'l' is unique)
# "aabb" → -1
```

**Quick Revision:** "First Unique Char = frequency map, then scan for first count==1."

### 5.5 Majority Element (LeetCode 169) — 🟡 MED Priority

**Problem:** Find the element that appears more than ⌊n/2⌋ times.

```python
# Hash Map approach:
def majorityElement(nums):
    """TC: O(n), SC: O(n)"""
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1
        if freq[num] > len(nums) // 2:
            return num

# Boyer-Moore Voting Algorithm (optimal):
def majorityElement_optimal(nums):
    """TC: O(n), SC: O(1)"""
    candidate = nums[0]
    count = 1

    for i in range(1, len(nums)):
        if count == 0:
            candidate = nums[i]
            count = 1
        elif nums[i] == candidate:
            count += 1
        else:
            count -= 1

    return candidate
```

**Quick Revision:** "Majority Element = Boyer-Moore: candidate + count. When count=0, switch candidate."

### 5.6 Group Anagrams (LeetCode 49) — 🟡 MED Priority

```python
def groupAnagrams(strs):
    """
    Group by sorted string as key.
    TC: O(n * k log k) where k = max string length, SC: O(n * k)
    """
    groups = {}
    for s in strs:
        key = ''.join(sorted(s))  # "eat" → "aet", "tea" → "aet"
        if key not in groups:
            groups[key] = []
        groups[key].append(s)

    return list(groups.values())

# ["eat","tea","tan","ate","nat","bat"]
# → [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```

**Quick Revision:** "Group Anagrams = sort each string as key, group by that key."

### 5.7 Intersection of Two Arrays (LeetCode 349/350) — 🟡 MED Priority

```python
def intersection(nums1, nums2):
    """TC: O(m+n), SC: O(min(m,n))"""
    return list(set(nums1) & set(nums2))

# With duplicates (LeetCode 350):
def intersect(nums1, nums2):
    """TC: O(m+n), SC: O(min(m,n))"""
    from collections import Counter
    count = Counter(nums1)
    result = []
    for num in nums2:
        if count.get(num, 0) > 0:
            result.append(num)
            count[num] -= 1
    return result
```

### 5.8 Ransom Note (LeetCode 383) — 🟡 MED Priority

```python
def canConstruct(ransomNote, magazine):
    """
    Check if magazine has enough characters for ransomNote.
    TC: O(m+n), SC: O(1) — 26 chars max
    """
    freq = {}
    for char in magazine:
        freq[char] = freq.get(char, 0) + 1

    for char in ransomNote:
        if freq.get(char, 0) == 0:
            return False
        freq[char] -= 1

    return True
```

### 5.9 Isomorphic Strings (LeetCode 205) — 🟡 MED Priority

```python
def isIsomorphic(s, t):
    """
    Each char in s maps to exactly one char in t and vice versa.
    TC: O(n), SC: O(1)
    """
    if len(s) != len(t):
        return False

    s_to_t = {}
    t_to_s = {}

    for c1, c2 in zip(s, t):
        if c1 in s_to_t and s_to_t[c1] != c2:
            return False
        if c2 in t_to_s and t_to_s[c2] != c1:
            return False
        s_to_t[c1] = c2
        t_to_s[c2] = c1

    return True

# "egg" & "add" → True, "foo" & "bar" → False
```

### 5.10 Word Pattern (LeetCode 290) — 🟡 MED Priority

```python
def wordPattern(pattern, s):
    """Same logic as isomorphic strings but with words."""
    words = s.split()
    if len(pattern) != len(words):
        return False

    p_to_w = {}
    w_to_p = {}

    for p, w in zip(pattern, words):
        if p in p_to_w and p_to_w[p] != w:
            return False
        if w in w_to_p and w_to_p[w] != p:
            return False
        p_to_w[p] = w
        w_to_p[w] = p

    return True
```

### 5.11 Longest Common Prefix (LeetCode 14) — 🔴 HIGH Priority

```python
def longestCommonPrefix(strs):
    """
    Compare character by character across all strings.
    TC: O(n * m) where m = shortest string length, SC: O(1)
    """
    if not strs:
        return ""

    prefix = strs[0]
    for s in strs[1:]:
        while not s.startswith(prefix):
            prefix = prefix[:-1]  # Shorten prefix
            if not prefix:
                return ""

    return prefix

# ["flower","flow","flight"] → "fl"
```

**Quick Revision:** "LCP = start with first string as prefix, shorten until all strings match."

## 8. TCS-SPECIFIC NOTES

1. Hash map problems are the **easiest to score** — master frequency counting
2. TCS favors: Two Sum, Contains Duplicate, Anagram Check, First Unique Char
3. Always handle **empty inputs** and **case sensitivity** (TCS may not specify)
4. Don't import Counter — build frequency maps manually for TCS

## 9. QUICK QUIZ

1. **Q:** Why is hash map O(1) lookup on average but O(n) worst case?
   **A:** Collisions. If many keys hash to the same bucket, lookup degrades to O(n). In practice, this almost never happens.

2. **Q:** How do you check if two strings are anagrams in O(n) time?
   **A:** Build frequency maps for both and compare. Or build one, decrement with the other.

3. **Q:** What's the space complexity of a frequency map for lowercase English letters?
   **A:** O(1) — at most 26 entries, which is constant.

---

# ═══════════════════════════════════════════════════
# PATTERN 6: BINARY SEARCH
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** Binary Search repeatedly halves the search space. Instead of checking every element (O(n)), you check the middle element. If the target is smaller, search the left half. If larger, search the right half. Each step eliminates half the data → O(log n).

**Why does it exist?** Linear search is O(n) — fine for small data but too slow for large sorted datasets. Binary search is O(log n) — searching 1 billion elements takes only ~30 steps!

**When to use?** Whenever the data is **sorted** (or has a monotonic property) and you need to find a specific element or boundary.

## 2. VISUAL EXPLANATION

```
Array: [1, 3, 5, 7, 9, 11, 13]   Target: 7

Step 1: left=0, right=6, mid=3 → arr[3]=7 → FOUND!

If Target was 5:
Step 1: mid=3 → arr[3]=7 > 5  → search left half, right=2
Step 2: left=0, right=2, mid=1 → arr[1]=3 < 5 → search right, left=2
Step 3: left=2, right=2, mid=2 → arr[2]=5 → FOUND!
```

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Time | Space |
|-----------|------|-------|
| Standard binary search | O(log n) | O(1) |
| Recursive binary search | O(log n) | O(log n) call stack |
| Linear search comparison | O(n) | O(1) |

## 4. TEMPLATE CODE

```python
def binary_search(arr, target):
    """
    Standard Binary Search Template.
    Returns index of target, or -1 if not found.
    """
    left, right = 0, len(arr) - 1

    while left <= right:
        mid = left + (right - left) // 2  # Avoid overflow (important in C/Java)

        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1      # Target is in right half
        else:
            right = mid - 1     # Target is in left half

    return -1  # Not found

# Template: Find leftmost/first occurrence
def find_first(arr, target):
    """Find the FIRST occurrence of target."""
    left, right = 0, len(arr) - 1
    result = -1

    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            result = mid        # Found, but keep searching LEFT for earlier occurrence
            right = mid - 1
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return result
```

## 5. PATTERN CONNECTION

- **Trigger phrases:**
  1. "Given a **sorted** array, find..."
  2. "Find the **first/last** position of..."
  3. "**Minimum/maximum** value that satisfies..."

## 6 & 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 6.1 Binary Search (LeetCode 704) — 🔴 HIGH Priority

```python
def search(nums, target):
    """
    Standard binary search.
    TC: O(log n), SC: O(1)
    """
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

### 6.2 Search Insert Position (LeetCode 35) — 🟡 MED Priority

**Problem:** Find target or where it should be inserted to maintain sorted order.

```python
def searchInsert(nums, target):
    """
    Binary search — if not found, 'left' is the insert position.
    TC: O(log n), SC: O(1)
    """
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return left  # Insert position

# [1,3,5,6], target=5 → 2
# [1,3,5,6], target=2 → 1
```

**Quick Revision:** "Search Insert = Binary Search. If not found, return `left` — that's the insert position."

### 6.3 First Bad Version (LeetCode 278) — 🟡 MED Priority

```python
def firstBadVersion(n):
    """
    Binary search for the boundary between good and bad.
    TC: O(log n), SC: O(1)
    """
    left, right = 1, n

    while left < right:
        mid = left + (right - left) // 2
        if isBadVersion(mid):
            right = mid       # Mid could be the first bad version
        else:
            left = mid + 1    # First bad is after mid

    return left
```

### 6.4 Search in Rotated Sorted Array (LeetCode 33) — 🟡 MED Priority

**Problem:** Array was sorted then rotated. Find target in O(log n).

**Key Insight:** One half is always sorted. Determine which half and check if target is in that range.

```python
def search(nums, target):
    """
    Modified binary search: one half is always sorted.
    TC: O(log n), SC: O(1)
    """
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid

        # Left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1   # Target is in sorted left half
            else:
                left = mid + 1    # Target is in right half
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1    # Target is in sorted right half
            else:
                right = mid - 1   # Target is in left half

    return -1

# [4,5,6,7,0,1,2], target=0 → 4
```

**Quick Revision:** "Rotated Array = one half always sorted. Check which, then decide."

### 6.5 Find First and Last Position (LeetCode 34) — 🟡 MED Priority

```python
def searchRange(nums, target):
    """
    Two binary searches: one for first, one for last occurrence.
    TC: O(log n), SC: O(1)
    """
    def findFirst(nums, target):
        left, right = 0, len(nums) - 1
        result = -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                result = mid
                right = mid - 1  # Keep searching left
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        return result

    def findLast(nums, target):
        left, right = 0, len(nums) - 1
        result = -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                result = mid
                left = mid + 1  # Keep searching right
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        return result

    return [findFirst(nums, target), findLast(nums, target)]
```

### 6.6 Valid Perfect Square (LeetCode 367) — 🟡 MED Priority

```python
def isPerfectSquare(num):
    """
    Binary search for the square root.
    TC: O(log n), SC: O(1)
    """
    left, right = 1, num

    while left <= right:
        mid = left + (right - left) // 2
        square = mid * mid

        if square == num:
            return True
        elif square < num:
            left = mid + 1
        else:
            right = mid - 1

    return False
```

## 8. TCS-SPECIFIC NOTES

1. **TCS often gives sorted array problems** — Binary Search should be your first instinct
2. **Don't use `mid = (left+right)//2`** in C/Java (overflow risk). Use `mid = left + (right-left)//2`
3. In Python, no overflow issue, but still good practice
4. Edge cases: empty array, single element, target not present, target at boundaries

## 9. QUICK QUIZ

1. **Q:** What's the time complexity of binary search? How many steps to search 1 million elements?
   **A:** O(log n). log₂(1,000,000) ≈ 20 steps.

2. **Q:** Why do we use `left + (right-left)//2` instead of `(left+right)//2`?
   **A:** To prevent integer overflow in languages like C/Java where `left+right` might exceed max int.

3. **Q:** In "Find First Bad Version," why is the condition `left < right` instead of `left <= right`?
   **A:** Because we want `left` and `right` to converge to the same point (the answer). Using `<=` could cause an infinite loop.

---

# ═══════════════════════════════════════════════════
# PATTERN 7: STACK (LIFO)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** A Stack is a linear data structure following Last-In-First-Out (LIFO). Think of a stack of plates — you can only add/remove from the top. The last plate placed is the first one taken off.

**Why does it exist?** Stacks naturally model operations where you need to "go back" — like undo in a text editor, browser back button, or matching opening/closing brackets. They're also crucial for expression evaluation and function call tracking.

**When to use?** Whenever you see **matching/nesting** (brackets, tags), **reversing** order, **monotonic** sequences, or **expression** evaluation. The word "valid" + "parentheses/brackets" is a dead giveaway.

## 2. VISUAL EXPLANATION

```
Valid Parentheses: "({[]})"

Stack operations:
'(' → push → Stack: ['(']
'{' → push → Stack: ['(', '{']
'[' → push → Stack: ['(', '{', '[']
']' → pop '[' → matches! → Stack: ['(', '{']
'}' → pop '{' → matches! → Stack: ['(']
')' → pop '(' → matches! → Stack: []

Stack empty at end → VALID! ✓
```

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Time | Space |
|-----------|------|-------|
| Push (add to top) | O(1) | — |
| Pop (remove from top) | O(1) | — |
| Peek (view top) | O(1) | — |
| Is Empty | O(1) | — |
| Total space | — | O(n) |

**In Python:** Use a list as a stack. `append()` = push, `pop()` = pop, `[-1]` = peek.

## 4. TEMPLATE CODE

```python
# Python stack = list
stack = []
stack.append(item)     # Push
top = stack.pop()      # Pop (and return)
top = stack[-1]        # Peek (without removing)
is_empty = len(stack) == 0  # Check empty

# Template: Matching brackets
def is_valid(s):
    """Template for bracket/parentheses matching."""
    stack = []
    matching = {')': '(', '}': '{', ']': '['}

    for char in s:
        if char in matching.values():  # Opening bracket
            stack.append(char)
        elif char in matching:         # Closing bracket
            if not stack or stack[-1] != matching[char]:
                return False
            stack.pop()

    return len(stack) == 0
```

## 5. PATTERN CONNECTION

- **Trigger phrases:**
  1. "Check if **parentheses/brackets** are valid"
  2. "**Simplify** a path/expression"
  3. "**Next greater/smaller** element"

## 6 & 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 7.1 Valid Parentheses (LeetCode 20) — 🔴 HIGH Priority

```python
def isValid(s):
    """
    Use stack: push opening, pop and match closing.
    TC: O(n), SC: O(n)
    """
    stack = []
    matching = {')': '(', '}': '{', ']': '['}

    for char in s:
        if char in matching:  # Closing bracket
            if stack and stack[-1] == matching[char]:
                stack.pop()
            else:
                return False
        else:  # Opening bracket
            stack.append(char)

    return len(stack) == 0

# "()" → True, "()[]{}" → True, "(]" → False
```

**Edge Cases:** Empty string (True), single bracket (False), nested brackets, only opening/only closing.

**TCS Tip:** This is one of the most commonly asked TCS problems! Know it cold.

**Quick Revision:** "Valid Parentheses = Push opening, pop on matching closing. Stack must be empty at end."

### 7.2 Valid Parentheses (extended with strings) — implied

```python
def is_balanced(s):
    """Check if string has balanced brackets — ignore other characters."""
    stack = []
    brackets = {')': '(', '}': '{', ']': '['}

    for char in s:
        if char in '({[':
            stack.append(char)
        elif char in ')}]':
            if not stack or stack[-1] != brackets[char]:
                return False
            stack.pop()

    return len(stack) == 0
```

### 7.3 Simplify Path (LeetCode 71) — 🟢 LOW Priority

```python
def simplifyPath(path):
    """
    Use stack to process directory names.
    TC: O(n), SC: O(n)
    """
    stack = []
    parts = path.split('/')

    for part in parts:
        if part == '' or part == '.':
            continue             # Skip empty and current directory
        elif part == '..':
            if stack:
                stack.pop()      # Go up one directory
        else:
            stack.append(part)   # Add directory name

    return '/' + '/'.join(stack)

# "/a/./b/../../c/" → "/c"
```

### 7.4 Backspace String Compare (implied)

```python
def backspaceCompare(s, t):
    """
    '#' means backspace. Build final strings using stack.
    TC: O(n), SC: O(n)
    """
    def build(string):
        stack = []
        for char in string:
            if char == '#':
                if stack:
                    stack.pop()
            else:
                stack.append(char)
        return ''.join(stack)

    return build(s) == build(t)

# "ab#c" and "ad#c" → True (both become "ac")
```

## 8. TCS-SPECIFIC NOTES

1. **Valid Parentheses is a TCS favorite** — appears frequently in NQT
2. Implement stack using a list, NOT import deque (keep it simple for TCS)
3. Edge case TCS loves: empty string, unbalanced brackets, only closing brackets
4. TCS may ask stack-based expression evaluation — know postfix notation

## 9. QUICK QUIZ

1. **Q:** What happens if you try to pop from an empty stack?
   **A:** In Python, `list.pop()` raises IndexError. Always check `if stack` before popping.

2. **Q:** Can you use a stack to reverse a string?
   **A:** Yes! Push all characters, then pop them all — they come out in reverse order.

3. **Q:** What's the difference between a stack and a queue?
   **A:** Stack is LIFO (last in, first out). Queue is FIFO (first in, first out).

---

# ═══════════════════════════════════════════════════
# PATTERN 8: DYNAMIC PROGRAMMING (DP)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** Dynamic Programming solves complex problems by breaking them into smaller overlapping subproblems and storing their solutions (memoization/tabulation) to avoid redundant computation. It's essentially recursion + caching.

**Why does it exist?** Many recursive solutions recompute the same subproblems exponentially (e.g., naive Fibonacci is O(2ⁿ)). DP stores results so each subproblem is solved only once, reducing time to O(n) or O(n²).

**When to use?** When the problem has:
1. **Optimal substructure:** Optimal solution can be built from optimal solutions of subproblems
2. **Overlapping subproblems:** Same subproblems are solved multiple times

**Keywords:** "minimum cost," "maximum value," "count the number of ways," "can you reach...?"

## 2. VISUAL EXPLANATION

**Analogy:** Imagine climbing stairs. To reach step 5, you must have come from step 4 or step 3. And to reach step 4, you came from step 3 or step 2. Notice step 3 appears twice? Instead of recalculating, we save it!

```
Fibonacci WITHOUT DP (exponential — so much waste!):
                    fib(5)
                   /      \
              fib(4)      fib(3)       ← fib(3) computed TWICE
             /    \       /    \
         fib(3) fib(2) fib(2) fib(1)   ← fib(2) computed THREE times!

Fibonacci WITH DP (linear — each computed ONCE):
dp[0] = 0, dp[1] = 1
dp[2] = dp[1] + dp[0] = 1
dp[3] = dp[2] + dp[1] = 2
dp[4] = dp[3] + dp[2] = 3
dp[5] = dp[4] + dp[3] = 5   ← Computed in 5 steps, not 15!
```

## 3. CORE OPERATIONS & COMPLEXITY

| DP Approach | Time | Space | When to Use |
|-------------|------|-------|-------------|
| Top-Down (Memoization) | Varies | O(n) + stack | When recursion is intuitive |
| Bottom-Up (Tabulation) | Varies | O(n) | When iterative is clearer |
| Space-Optimized | Varies | O(1) | When only previous 1-2 states needed |

## 4. TEMPLATE CODE

```python
# Template: Top-Down (Memoization)
def solve_memo(n, memo={}):
    if n in memo:
        return memo[n]
    if n <= 1:          # Base case
        return n
    memo[n] = solve_memo(n-1) + solve_memo(n-2)  # Recurrence
    return memo[n]

# Template: Bottom-Up (Tabulation)
def solve_tab(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[0], dp[1] = 0, 1          # Base cases
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]  # Recurrence
    return dp[n]

# Template: Space-Optimized
def solve_optimal(n):
    if n <= 1:
        return n
    prev2, prev1 = 0, 1
    for i in range(2, n + 1):
        curr = prev1 + prev2     # Only need last 2 values
        prev2, prev1 = prev1, curr
    return prev1
```

**The DP recipe:**
1. **Define the state:** What does dp[i] represent?
2. **Find the recurrence:** How does dp[i] relate to previous states?
3. **Set base cases:** What are dp[0], dp[1]?
4. **Determine order:** Fill from smaller → larger
5. **Optimize space** if possible

## 5. PATTERN CONNECTION

- **Trigger phrases:**
  1. "**Count the number of ways** to..."
  2. "Find the **minimum/maximum** cost/value"
  3. "**Can you reach** the destination?"
  4. "Find the **longest/shortest** subsequence"

## 6 & 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 8.1 Climbing Stairs (LeetCode 70) — 🟡 MED Priority

**Problem:** You can climb 1 or 2 steps at a time. How many distinct ways to reach step n?

**Key Insight:** This IS Fibonacci! `ways(n) = ways(n-1) + ways(n-2)`

```python
def climbStairs(n):
    """
    DP: dp[i] = number of ways to reach step i.
    dp[i] = dp[i-1] + dp[i-2] (come from 1 step or 2 steps below)
    TC: O(n), SC: O(1)
    """
    if n <= 2:
        return n

    prev2, prev1 = 1, 2  # dp[1]=1, dp[2]=2

    for i in range(3, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr

    return prev1

# n=3 → 3 ways: [1+1+1, 1+2, 2+1]
# n=5 → 8 ways
```

**Quick Revision:** "Climbing Stairs = Fibonacci. ways(n) = ways(n-1) + ways(n-2)."

### 8.2 Min Cost Climbing Stairs (LeetCode 746) — 🟡 MED Priority

```python
def minCostClimbingStairs(cost):
    """
    dp[i] = min cost to reach step i.
    dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])
    TC: O(n), SC: O(1)
    """
    n = len(cost)
    prev2, prev1 = 0, 0  # dp[0]=0, dp[1]=0 (free to start at step 0 or 1)

    for i in range(2, n + 1):
        curr = min(prev1 + cost[i - 1], prev2 + cost[i - 2])
        prev2, prev1 = prev1, curr

    return prev1

# cost=[10,15,20] → 15 (start at index 1, pay 15, jump 2 steps)
```

### 8.3 House Robber (LeetCode 198) — 🟡 MED Priority

**Problem:** Rob houses along a street. Can't rob two adjacent houses. Maximize money.

**Key Insight:** For each house, choose: rob it (+ skip previous) OR skip it (+ keep previous best).

```python
def rob(nums):
    """
    dp[i] = max money robbing houses 0..i
    dp[i] = max(dp[i-1], dp[i-2] + nums[i])
    TC: O(n), SC: O(1)
    """
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]

    prev2, prev1 = 0, nums[0]

    for i in range(1, len(nums)):
        curr = max(prev1, prev2 + nums[i])
        prev2, prev1 = prev1, curr

    return prev1

# [2,7,9,3,1] → 12 (rob house 0=2, house 2=9, house 4=1)
```

**Quick Revision:** "House Robber = at each house, max(skip it, rob it + prev_prev_best)."

### 8.4 Coin Change (LeetCode 322) — 🟡 MED Priority

**Problem:** Given coin denominations, find minimum coins to make target amount. Return -1 if impossible.

```python
def coinChange(coins, amount):
    """
    dp[i] = minimum coins to make amount i.
    dp[i] = min(dp[i - coin] + 1) for each coin.
    TC: O(amount × #coins), SC: O(amount)
    """
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0  # 0 coins needed for amount 0

    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i and dp[i - coin] != float('inf'):
                dp[i] = min(dp[i], dp[i - coin] + 1)

    return dp[amount] if dp[amount] != float('inf') else -1

# coins=[1,5,10], amount=11 → 2 (10+1)
# coins=[2], amount=3 → -1
```

**Quick Revision:** "Coin Change = dp[amount] = min coins. For each amount, try all coins."

### 8.5 Longest Increasing Subsequence (LeetCode 300) — 🟢 LOW Priority

```python
def lengthOfLIS(nums):
    """
    dp[i] = length of LIS ending at index i.
    dp[i] = max(dp[j] + 1) for all j < i where nums[j] < nums[i].
    TC: O(n²), SC: O(n)
    """
    if not nums:
        return 0

    dp = [1] * len(nums)  # Every element is a subsequence of length 1

    for i in range(1, len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)

# [10,9,2,5,3,7,101,18] → 4 ([2,3,7,101])
```

### 8.6 Pascal's Triangle (LeetCode 118) — 🟡 MED Priority

```python
def generate(numRows):
    """
    Each cell = sum of two cells above it.
    TC: O(n²), SC: O(n²)
    """
    triangle = []

    for i in range(numRows):
        row = [1] * (i + 1)  # Fill with 1s
        for j in range(1, i):
            row[j] = triangle[i-1][j-1] + triangle[i-1][j]
        triangle.append(row)

    return triangle

# numRows=5 → [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

## 8. TCS-SPECIFIC NOTES

1. TCS DP problems are usually **Easy-Medium** — Climbing Stairs, House Robber level
2. **Coin Change** appears occasionally — know the template
3. TCS may ask Fibonacci-style problems disguised differently (e.g., "how many paths on a grid")
4. Always start with recursion → add memoization → optimize to tabulation → optimize space
5. **Edge cases:** n=0, n=1, empty input, impossible cases (return -1)

## 9. QUICK QUIZ

1. **Q:** What are the TWO conditions needed for a problem to be solvable with DP?
   **A:** **Optimal substructure** and **overlapping subproblems.**

2. **Q:** What's the difference between memoization (top-down) and tabulation (bottom-up)?
   **A:** Memoization uses recursion + cache. Tabulation fills a table iteratively from base cases up.

3. **Q:** For Climbing Stairs, why can we reduce space from O(n) to O(1)?
   **A:** Because dp[i] only depends on dp[i-1] and dp[i-2] — we only need the last 2 values.

---

*Continue to Part 3 for Patterns 9-12: BFS/DFS, Bit Manipulation, Backtracking, Merge Intervals...*
