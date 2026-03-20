# 🧠 TCS NQT 2026 — DSA Master Guide — PART 3
# Patterns 9-12: BFS/DFS, Bit Manipulation, Backtracking, Merge Intervals

> **Continued from Part 2** (Patterns 5-8)

---

# ═══════════════════════════════════════════════════
# PATTERN 9: BFS / DFS (Traversals)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** BFS (Breadth-First Search) explores level by level — like ripples in water spreading outward. DFS (Depth-First Search) dives as deep as possible before backtracking — like exploring a maze by always going forward until hitting a wall.

**Why does it exist?** Trees and graphs are non-linear. You need systematic ways to visit every node. BFS finds the shortest path in unweighted graphs. DFS is great for exploring all paths, detecting cycles, and solving recursion-based problems.

**When to use?**
- **BFS:** Shortest path, level-order traversal, finding nearest X
- **DFS:** Path existence, tree depths, connected components, topological sort

## 2. VISUAL EXPLANATION

```
Binary Tree:
        1
       / \
      2   3
     / \   \
    4   5   6

BFS (level-by-level): 1 → 2, 3 → 4, 5, 6
  → Uses QUEUE (FIFO)

DFS (go deep first):
  Preorder  (Root→Left→Right):  1, 2, 4, 5, 3, 6
  Inorder   (Left→Root→Right):  4, 2, 5, 1, 3, 6
  Postorder (Left→Right→Root):  4, 5, 2, 6, 3, 1
  → Uses STACK (or recursion)
```

**Analogy:** 
- **BFS** = You're looking for someone in a building. Check ALL rooms on floor 1, then ALL rooms on floor 2, etc.
- **DFS** = You pick a corridor and go all the way to the end, then backtrack and try the next corridor.

## 3. CORE OPERATIONS & COMPLEXITY

| Operation | Time | Space |
|-----------|------|-------|
| BFS traversal | O(V + E) | O(V) for queue |
| DFS traversal | O(V + E) | O(H) for stack/recursion (H = height) |
| Tree traversal | O(n) | O(n) worst case |

V = vertices (nodes), E = edges, n = number of nodes, H = tree height.

## 4. TEMPLATE CODE

### BFS Template (Level Order)
```python
from collections import deque

def bfs(root):
    """BFS: use a QUEUE. Process level by level."""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level)
    
    return result
```

### DFS Templates
```python
# Preorder: Root → Left → Right
def preorder(root):
    if not root:
        return
    print(root.val)       # Process root
    preorder(root.left)   # Go left
    preorder(root.right)  # Go right

# Inorder: Left → Root → Right (gives sorted order for BST!)
def inorder(root):
    if not root:
        return
    inorder(root.left)
    print(root.val)       # Process root
    inorder(root.right)

# Postorder: Left → Right → Root
def postorder(root):
    if not root:
        return
    postorder(root.left)
    postorder(root.right)
    print(root.val)       # Process root
```

## 5. PATTERN CONNECTION

- **Trigger phrases:**
  1. "**Level order** traversal" or "by levels" → BFS
  2. "Find the **depth/height**" → DFS
  3. "**Connected components** / number of islands" → BFS or DFS
  4. "**Shortest path** in unweighted graph" → BFS

## 6 & 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 9.1 Maximum Depth of Binary Tree (LeetCode 104) — 🟡 MED Priority

```python
def maxDepth(root):
    """
    DFS: depth = 1 + max(left_depth, right_depth)
    TC: O(n), SC: O(h) where h = height
    """
    if not root:
        return 0
    
    left_depth = maxDepth(root.left)
    right_depth = maxDepth(root.right)
    
    return 1 + max(left_depth, right_depth)
```

**Quick Revision:** "Max Depth = DFS. Return 1 + max(left_depth, right_depth). Base: null = 0."

### 9.2 Symmetric Tree (LeetCode 101) — 🟡 MED Priority

```python
def isSymmetric(root):
    """
    Check if left subtree mirrors right subtree.
    TC: O(n), SC: O(h)
    """
    def isMirror(left, right):
        if not left and not right:
            return True
        if not left or not right:
            return False
        return (left.val == right.val and
                isMirror(left.left, right.right) and
                isMirror(left.right, right.left))
    
    return isMirror(root.left, root.right) if root else True
```

### 9.3 Invert Binary Tree (LeetCode 226) — 🟡 MED Priority

```python
def invertTree(root):
    """
    Swap left and right children at every node.
    TC: O(n), SC: O(h)
    """
    if not root:
        return None
    
    root.left, root.right = root.right, root.left
    invertTree(root.left)
    invertTree(root.right)
    
    return root
```

**Quick Revision:** "Invert Tree = Swap left & right at each node recursively."

### 9.4 Same Tree (LeetCode 100) — 🟡 MED Priority

```python
def isSameTree(p, q):
    """TC: O(n), SC: O(h)"""
    if not p and not q:
        return True
    if not p or not q:
        return False
    return (p.val == q.val and 
            isSameTree(p.left, q.left) and 
            isSameTree(p.right, q.right))
```

### 9.5 Level Order Traversal (LeetCode 102) — 🟡 MED Priority

```python
def levelOrder(root):
    """
    BFS with queue. Process each level as a group.
    TC: O(n), SC: O(n)
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    
    return result
```

### 9.6 Validate BST (LeetCode 98) — 🟡 MED Priority

**Key Insight:** In a BST, every node must be within a valid range (min, max).

```python
def isValidBST(root):
    """
    DFS with range checking.
    TC: O(n), SC: O(h)
    """
    def validate(node, min_val, max_val):
        if not node:
            return True
        if node.val <= min_val or node.val >= max_val:
            return False
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))
    
    return validate(root, float('-inf'), float('inf'))
```

**Quick Revision:** "Validate BST = DFS with min/max range. Left child < root < right child."

### 9.7 Convert Sorted Array to BST (LeetCode 108) — 🟡 MED Priority

```python
def sortedArrayToBST(nums):
    """
    Pick middle as root, recurse on left and right halves.
    TC: O(n), SC: O(log n) stack
    """
    if not nums:
        return None
    
    mid = len(nums) // 2
    root = TreeNode(nums[mid])
    root.left = sortedArrayToBST(nums[:mid])
    root.right = sortedArrayToBST(nums[mid + 1:])
    
    return root
```

### 9.8 Number of Islands (LeetCode 200) — 🟢 LOW Priority

**Problem:** Given a 2D grid of '1's (land) and '0's (water), count the number of islands.

```python
def numIslands(grid):
    """
    DFS: when you find a '1', flood-fill it (mark as visited), count += 1.
    TC: O(m×n), SC: O(m×n) worst case for recursion
    """
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
        grid[r][c] = '0'  # Mark as visited
        dfs(r + 1, c)     # Down
        dfs(r - 1, c)     # Up
        dfs(r, c + 1)     # Right
        dfs(r, c - 1)     # Left
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                dfs(r, c)
                count += 1
    
    return count
```

**Quick Revision:** "Number of Islands = DFS flood-fill. Every new '1' starts a new island."

## 8. TCS-SPECIFIC NOTES

1. Tree problems are **medium priority** for TCS — arrays and math are more common
2. Know the **TreeNode class**: `class TreeNode: def __init__(self, val=0, left=None, right=None): self.val = val; self.left = left; self.right = right`
3. BFS needs `from collections import deque` — TCS usually allows imports
4. Most tree problems are recursive — master the pattern: base case → process node → recurse

## 9. QUICK QUIZ

1. **Q:** What data structure does BFS use? What about DFS?
   **A:** BFS uses a **queue** (FIFO). DFS uses a **stack** (or recursion).

2. **Q:** In a BST, inorder traversal gives what order?
   **A:** **Sorted ascending** order.

3. **Q:** What's the space complexity of DFS on a balanced binary tree of n nodes?
   **A:** O(log n) — the tree height. For a skewed tree, it's O(n).

---

# ═══════════════════════════════════════════════════
# PATTERN 10: BIT MANIPULATION (XOR)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** Bit manipulation uses binary operations (AND, OR, XOR, shift) directly on the binary representation of numbers. XOR is especially powerful: `a ^ a = 0` and `a ^ 0 = a`.

**Why does it exist?** Some problems can be solved in O(1) space and O(n) time using bit tricks that would otherwise need O(n) space with hash maps.

**When to use?** Single number (find the unique element), power of 2 checks, counting bits, swapping without temp variable.

## 2. VISUAL EXPLANATION

```
XOR Truth Table:
0 ^ 0 = 0
0 ^ 1 = 1
1 ^ 0 = 1
1 ^ 1 = 0

Key Properties:
a ^ a = 0    (same numbers cancel out!)
a ^ 0 = a    (XOR with 0 keeps the number)
a ^ b = b ^ a  (order doesn't matter)

Example: Find single number in [4, 1, 2, 1, 2]
XOR all: 4 ^ 1 ^ 2 ^ 1 ^ 2
       = 4 ^ (1 ^ 1) ^ (2 ^ 2)
       = 4 ^ 0 ^ 0
       = 4  ← The unique one!
```

## 3. CORE OPERATIONS

| Operation | Symbol | Use |
|-----------|--------|-----|
| AND (`&`) | `a & b` | Check if bit is set |
| OR (`\|`) | `a \| b` | Set a bit |
| XOR (`^`) | `a ^ b` | Toggle / find unique |
| NOT (`~`) | `~a` | Flip all bits |
| Left Shift (`<<`) | `a << n` | Multiply by 2ⁿ |
| Right Shift (`>>`) | `a >> n` | Divide by 2ⁿ |

## 4 & 7. ALL PROBLEMS — DETAILED SOLUTIONS

### 10.1 Single Number (LeetCode 136) — 🟡 MED Priority

**Problem:** Every element appears twice except one. Find it.

**Brute Force:** Use hash map to count frequencies → O(n) time, O(n) space.

**Optimal (XOR):**
```python
def singleNumber(nums):
    """
    XOR all numbers. Pairs cancel (a^a=0), leaving the unique one.
    TC: O(n), SC: O(1) ← This is the magic of XOR!
    """
    result = 0
    for num in nums:
        result ^= num
    return result

# [4,1,2,1,2] → 4^1^2^1^2 = 4
```

**Quick Revision:** "Single Number = XOR everything. Duplicates cancel, unique remains."

### 10.2 Missing Number (LeetCode 268) — 🟡 MED Priority

**Problem:** Array of [0, n] with one number missing. Find it.

```python
# Approach 1: Math (sum formula)
def missingNumber_math(nums):
    """TC: O(n), SC: O(1)"""
    n = len(nums)
    expected_sum = n * (n + 1) // 2
    actual_sum = sum(nums)
    return expected_sum - actual_sum

# Approach 2: XOR
def missingNumber_xor(nums):
    """XOR index with values. Everything cancels except missing."""
    result = len(nums)  # Start with n (since indices go 0 to n-1)
    for i, num in enumerate(nums):
        result ^= i ^ num
    return result

# [3,0,1] → missing is 2
```

**Quick Revision:** "Missing Number = expected_sum - actual_sum. Or XOR all indices with values."

### 10.3 Number of 1 Bits / Hamming Weight (implied)

```python
def hammingWeight(n):
    """Count number of 1-bits. TC: O(32)=O(1), SC: O(1)"""
    count = 0
    while n:
        count += n & 1  # Check last bit
        n >>= 1         # Shift right
    return count

# Faster approach using n & (n-1):
def hammingWeight_fast(n):
    """Each n & (n-1) removes the lowest set bit."""
    count = 0
    while n:
        n &= n - 1  # Remove lowest set bit
        count += 1
    return count
```

### 10.4 Power of Two (LeetCode 231) — 🟡 MED Priority

```python
def isPowerOfTwo(n):
    """
    Powers of 2 have exactly one bit set.
    n & (n-1) removes that bit → should be 0.
    TC: O(1), SC: O(1)
    """
    return n > 0 and (n & (n - 1)) == 0

# 8 = 1000 in binary, 7 = 0111. 1000 & 0111 = 0000 → True!
# 6 = 0110, 5 = 0101. 0110 & 0101 = 0100 ≠ 0 → False!
```

## 8. TCS-SPECIFIC NOTES

1. **Single Number and Missing Number** are common TCS problems
2. Know the math approach (sum formula) as a backup — easier to explain
3. XOR is tricky for beginners — practice understanding `a ^ a = 0`
4. Bit manipulation questions are usually **easy** in TCS NQT

## 9. QUICK QUIZ

1. **Q:** What is 5 ^ 5?
   **A:** 0. Any number XOR itself is 0.

2. **Q:** How do you check if the last bit of n is 1?
   **A:** `n & 1`. If result is 1, last bit is set.

3. **Q:** What does left shifting by 1 (`n << 1`) do?
   **A:** Multiplies n by 2.

---

# ═══════════════════════════════════════════════════
# PATTERN 11: BACKTRACKING
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** Backtracking systematically explores all possible solutions by building candidates incrementally, abandoning ("backtracking" from) a candidate as soon as it's determined to be invalid.

**Why does it exist?** Some problems require trying all combinations/permutations. Backtracking is smarter than brute force because it prunes invalid paths early.

**When to use?** Permutations, combinations, subsets, solving puzzles (Sudoku, N-Queens), generating all possible arrangements.

## 2. VISUAL EXPLANATION

```
Subsets of [1, 2, 3]:

                        []
                   /    |    \
                [1]    [2]   [3]
               / \      |
           [1,2] [1,3] [2,3]
             |
          [1,2,3]

Result: [[], [1], [2], [3], [1,2], [1,3], [2,3], [1,2,3]]
```

## 3. TEMPLATE CODE

```python
def backtrack(result, current, choices, start):
    """
    Generic backtracking template.
    result: final answer list
    current: current partial solution
    choices: available options
    start: where to begin choosing (avoids duplicates)
    """
    result.append(current[:])  # Add a copy of current solution
    
    for i in range(start, len(choices)):
        current.append(choices[i])     # CHOOSE
        backtrack(result, current, choices, i + 1)  # EXPLORE
        current.pop()                   # UN-CHOOSE (backtrack!)
```

## 4 & 7. ALL PROBLEMS

### 11.1 Subsets (LeetCode 78) — 🟢 LOW Priority

```python
def subsets(nums):
    """
    Generate all subsets using backtracking.
    TC: O(2^n), SC: O(n) recursion depth
    """
    result = []
    
    def backtrack(start, current):
        result.append(current[:])
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()
    
    backtrack(0, [])
    return result

# [1,2,3] → [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

### 11.2 Permutations (LeetCode 46) — 🟢 LOW Priority

```python
def permute(nums):
    """
    Generate all permutations.
    TC: O(n!), SC: O(n)
    """
    result = []
    
    def backtrack(current, remaining):
        if not remaining:
            result.append(current[:])
            return
        for i in range(len(remaining)):
            current.append(remaining[i])
            backtrack(current, remaining[:i] + remaining[i+1:])
            current.pop()
    
    backtrack([], nums)
    return result

# [1,2,3] → [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

## 8. TCS-SPECIFIC NOTES

1. Backtracking is **LOW priority** for TCS NQT — rarely asked
2. If it appears, it's usually Subsets or Permutations — know the template
3. Focus your time on Arrays, Strings, Math instead

## 9. QUICK QUIZ

1. **Q:** How many subsets does a set of n elements have?
   **A:** 2ⁿ. Each element is either included or not.

2. **Q:** How many permutations of n elements exist?
   **A:** n! (n factorial).

3. **Q:** What's the key operation that makes backtracking different from brute force?
   **A:** **Pruning** — abandoning invalid paths early instead of exploring them fully.

---

# ═══════════════════════════════════════════════════
# PATTERN 12: MERGE INTERVALS
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** The Merge Intervals pattern handles problems involving overlapping ranges/intervals. The key technique: sort by start time, then merge overlapping intervals.

**Why does it exist?** Scheduling, calendar, and range problems need efficient overlap detection. Sorting + linear scan gives O(n log n).

**When to use?** When you see words like "intervals," "meetings," "overlapping," "schedule."

## 2. VISUAL EXPLANATION

```
Intervals: [[1,3], [2,6], [8,10], [15,18]]

Sort by start: Already sorted!

Check overlaps:
[1,3] and [2,6]: 2 ≤ 3 → overlap! Merge to [1,6]
[1,6] and [8,10]: 8 > 6 → no overlap. Keep both.
[8,10] and [15,18]: 15 > 10 → no overlap. Keep both.

Result: [[1,6], [8,10], [15,18]]
```

## 4 & 7. PROBLEMS

### 12.1 Merge Intervals (LeetCode 56) — 🟡 MED Priority

```python
def merge(intervals):
    """
    Sort by start, merge overlapping intervals.
    TC: O(n log n) for sort, SC: O(n) for result
    """
    if not intervals:
        return []
    
    intervals.sort(key=lambda x: x[0])  # Sort by start time
    merged = [intervals[0]]
    
    for i in range(1, len(intervals)):
        current = intervals[i]
        last = merged[-1]
        
        if current[0] <= last[1]:  # Overlap!
            last[1] = max(last[1], current[1])  # Extend end
        else:
            merged.append(current)  # No overlap, add new
    
    return merged

# [[1,3],[2,6],[8,10],[15,18]] → [[1,6],[8,10],[15,18]]
```

**Quick Revision:** "Merge Intervals = Sort by start, if next.start ≤ current.end → merge (extend end)."

### 12.2 Insert Interval (implied)

```python
def insert(intervals, newInterval):
    """TC: O(n), SC: O(n)"""
    result = []
    i = 0
    
    # Add all intervals that come before newInterval
    while i < len(intervals) and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # Merge overlapping intervals
    while i < len(intervals) and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)
    
    # Add remaining intervals
    while i < len(intervals):
        result.append(intervals[i])
        i += 1
    
    return result
```

## 8. TCS-SPECIFIC NOTES

1. Merge Intervals is **occasionally** asked in TCS NQT
2. The key trick is always: **sort first**, then linear scan
3. Remember to use `max()` for the end when merging

## 9. QUICK QUIZ

1. **Q:** What's the first step in any interval merging problem?
   **A:** **Sort** the intervals by their start time.

2. **Q:** How do you check if two intervals [a,b] and [c,d] overlap (assuming a ≤ c)?
   **A:** They overlap if c ≤ b (the start of the second is before the end of the first).

3. **Q:** What's the time complexity of merging n intervals?
   **A:** O(n log n) — dominated by the sorting step.

---

*Continue to Part 4 for Patterns 13-15 (Kadane's, Prefix Sums, Heaps), Linked List Deep Dive, Sorting Algorithms, and the Ultimate Revision Sheet...*
