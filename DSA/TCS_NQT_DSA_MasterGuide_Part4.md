# 🧠 TCS NQT 2026 — DSA Master Guide — PART 4
# Patterns 13-15: Kadane's, Prefix Sums, Heaps + Sorting + Linked Lists + Strings + Revision

> **Continued from Part 3** (Patterns 9-12)

---

# ═══════════════════════════════════════════════════
# PATTERN 13: KADANE'S ALGORITHM (Greedy/Subarray)
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** Kadane's Algorithm finds the **maximum sum contiguous subarray** in O(n). The idea: at each position, decide whether to extend the previous subarray or start a new one. If the running sum becomes negative, it's better to start fresh.

**Why does it exist?** The brute force approach to finding the max subarray sum is O(n²) or O(n³). Kadane's does it in a single pass.

**When to use?** Whenever you see "maximum subarray sum," "best contiguous sum," or "largest sum of consecutive elements."

## 2. VISUAL EXPLANATION

```
Array: [-2, 1, -3, 4, -1, 2, 1, -5, 4]

Step through:
i=0: current_sum = max(-2, -2) = -2, max_sum = -2
i=1: current_sum = max(1, -2+1) = max(1, -1) = 1, max_sum = 1
i=2: current_sum = max(-3, 1-3) = max(-3, -2) = -2, max_sum = 1
i=3: current_sum = max(4, -2+4) = max(4, 2) = 4, max_sum = 4
i=4: current_sum = max(-1, 4-1) = max(-1, 3) = 3, max_sum = 4
i=5: current_sum = max(2, 3+2) = max(2, 5) = 5, max_sum = 5
i=6: current_sum = max(1, 5+1) = max(1, 6) = 6, max_sum = 6  ← MAX!
i=7: current_sum = max(-5, 6-5) = max(-5, 1) = 1, max_sum = 6
i=8: current_sum = max(4, 1+4) = max(4, 5) = 5, max_sum = 6

Answer: 6 (subarray [4, -1, 2, 1])
```

## 3. TEMPLATE CODE

```python
def kadane(nums):
    """
    Kadane's Algorithm: Maximum Subarray Sum.
    At each position: extend previous OR start new.
    TC: O(n), SC: O(1)
    """
    max_sum = nums[0]
    current_sum = nums[0]
    
    for i in range(1, len(nums)):
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)
    
    return max_sum
```

## 4. ALL PROBLEMS

### 13.1 Maximum Subarray (LeetCode 53) — 🔴 HIGH Priority

**Problem Understanding:** Find the contiguous subarray with the largest sum.

**Approach 1: Brute Force**
```python
def maxSubArray_brute(nums):
    """Check every subarray. TC: O(n²), SC: O(1)"""
    max_sum = float('-inf')
    for i in range(len(nums)):
        current = 0
        for j in range(i, len(nums)):
            current += nums[j]
            max_sum = max(max_sum, current)
    return max_sum
```

**Approach 2: Kadane's (Optimal)**
```python
def maxSubArray(nums):
    """
    Kadane's: extend or restart at each element.
    TC: O(n), SC: O(1)
    """
    max_sum = current_sum = nums[0]
    
    for i in range(1, len(nums)):
        # Should I extend the previous subarray or start fresh?
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)
    
    return max_sum

# [-2,1,-3,4,-1,2,1,-5,4] → 6
```

**Edge Cases:** All negative numbers (return the least negative), single element, all positive.

**TCS Tip:** TCS may ask to also return the subarray itself, not just the sum. Track start and end indices.

**Quick Revision:** "Max Subarray = Kadane's. current = max(element, current + element). Track global max."

### 13.2 Best Time to Buy and Sell Stock (LeetCode 121) — 🔴 HIGH Priority

**Problem:** Given prices, find max profit from one buy and one sell.

**Key Insight:** This IS Kadane's in disguise! Or simpler: track minimum price so far, compute profit at each step.

```python
def maxProfit(prices):
    """
    Track min price seen so far, compute max profit at each step.
    TC: O(n), SC: O(1)
    """
    min_price = prices[0]
    max_profit = 0
    
    for price in prices[1:]:
        profit = price - min_price
        max_profit = max(max_profit, profit)
        min_price = min(min_price, price)
    
    return max_profit

# [7,1,5,3,6,4] → 5 (buy at 1, sell at 6)
# [7,6,4,3,1] → 0 (prices only decrease, don't buy)
```

**Edge Cases:** Descending prices (return 0), single day, two days.

**Quick Revision:** "Buy/Sell Stock = track min_price, profit = current - min_price, track max_profit."

### 13.3 Max Ascending Subarray Sum (LeetCode 1800) — 🟡 MED Priority

*(Already covered in Sliding Window section — cross-pattern problem!)*

## 5. TCS-SPECIFIC NOTES

1. **Maximum Subarray and Buy/Sell Stock are TCS favorites** — almost guaranteed to appear
2. The Kadane logic is simple but easy to mess up — practice the edge cases
3. Remember: for Buy/Sell Stock, you can't sell before you buy (left to right only)

## 6. QUICK QUIZ

1. **Q:** In Kadane's, what does `current_sum = max(nums[i], current_sum + nums[i])` mean intuitively?
   **A:** "Should I extend the previous subarray by including this element, or is it better to start a new subarray from here?"

2. **Q:** What does Kadane's return if all numbers are negative?
   **A:** The **least negative** number (the maximum single element).

3. **Q:** How is Buy/Sell Stock related to Kadane's?
   **A:** If you compute the array of daily price changes, then max subarray of those changes = max profit.

---

# ═══════════════════════════════════════════════════
# PATTERN 14: PREFIX SUMS
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** A prefix sum array stores cumulative sums: `prefix[i] = sum(arr[0..i])`. This lets you compute the sum of any subarray in O(1) using: `sum(arr[l..r]) = prefix[r] - prefix[l-1]`.

**Why does it exist?** Computing subarray sums naively is O(n) each time. With prefix sums, you precompute once in O(n), then answer any range query in O(1).

**When to use?** "Sum of elements from index i to j," "find pivot index," "running sum," "subarray sum equals K."

## 2. VISUAL EXPLANATION

```
Array:      [1, 2, 3, 4, 5]
Prefix Sum: [1, 3, 6, 10, 15]

Sum from index 1 to 3 = prefix[3] - prefix[0] = 10 - 1 = 9
Verify: 2 + 3 + 4 = 9 ✓
```

## 3. TEMPLATE CODE

```python
def build_prefix_sum(arr):
    """Build prefix sum array."""
    prefix = [0] * (len(arr) + 1)  # Extra 0 at start for easier math
    for i in range(len(arr)):
        prefix[i + 1] = prefix[i] + arr[i]
    return prefix

def range_sum(prefix, left, right):
    """Sum of arr[left..right] in O(1)."""
    return prefix[right + 1] - prefix[left]
```

## 4. ALL PROBLEMS

### 14.1 Running Sum of 1d Array (LeetCode 1480) — 🟡 MED Priority

```python
def runningSum(nums):
    """
    Running sum = prefix sum.
    TC: O(n), SC: O(1) if modifying in-place
    """
    for i in range(1, len(nums)):
        nums[i] += nums[i - 1]
    return nums

# [1,2,3,4] → [1,3,6,10]
```

### 14.2 Find Pivot Index (LeetCode 724) — 🟡 MED Priority

**Problem:** Find index where left sum equals right sum.

```python
def pivotIndex(nums):
    """
    Pivot: left_sum == total_sum - left_sum - nums[pivot]
    TC: O(n), SC: O(1)
    """
    total = sum(nums)
    left_sum = 0
    
    for i in range(len(nums)):
        right_sum = total - left_sum - nums[i]
        if left_sum == right_sum:
            return i
        left_sum += nums[i]
    
    return -1

# [1,7,3,6,5,6] → 3 (left sum = 1+7+3 = 11, right sum = 5+6 = 11)
```

**Quick Revision:** "Pivot Index = left_sum == total - left_sum - current. Scan left to right."

### 14.3 Product of Array Except Self (LeetCode 238) — 🔴 HIGH Priority

**Problem:** Return array where each element is the product of all other elements. No division allowed!

```python
def productExceptSelf(nums):
    """
    Build left products and right products, multiply them.
    TC: O(n), SC: O(1) extra (output doesn't count)
    """
    n = len(nums)
    result = [1] * n
    
    # Left pass: result[i] = product of all elements to the LEFT of i
    left_product = 1
    for i in range(n):
        result[i] = left_product
        left_product *= nums[i]
    
    # Right pass: multiply by product of all elements to the RIGHT of i
    right_product = 1
    for i in range(n - 1, -1, -1):
        result[i] *= right_product
        right_product *= nums[i]
    
    return result

# [1,2,3,4] → [24,12,8,6]
# result[0] = 2*3*4 = 24, result[1] = 1*3*4 = 12, etc.
```

**Quick Revision:** "Product Except Self = left pass (prefix products) × right pass (suffix products)."

## 5. TCS-SPECIFIC NOTES

1. Prefix sums are conceptually simple but powerful — TCS uses them in disguised forms
2. **Product of Array Except Self** is a classic — know the two-pass approach
3. The idea of "precompute to answer quickly" is the essence of prefix sums

## 6. QUICK QUIZ

1. **Q:** What's the time to compute sum of a subarray with prefix sums?
   **A:** O(1) — just one subtraction: `prefix[right+1] - prefix[left]`.

2. **Q:** Why can't we use division for Product of Array Except Self?
   **A:** The problem explicitly forbids it. Also, division fails if any element is 0.

3. **Q:** What's the prefix sum of an array of all zeros?
   **A:** An array of all zeros.

---

# ═══════════════════════════════════════════════════
# PATTERN 15: HEAPS / QUICKSELECT
# ═══════════════════════════════════════════════════

## 1. CONCEPT INTRO

**What is it?** A Heap is a tree-based data structure where the parent is always smaller (min-heap) or larger (max-heap) than its children. It efficiently supports finding and removing the min/max element. QuickSelect is an algorithm to find the Kth smallest/largest in O(n) average.

**Why does it exist?** When you need to repeatedly access the minimum or maximum element efficiently. Sorting gives O(n log n) for finding the Kth element. A heap can do better.

**When to use?** "Find Kth largest/smallest," "top K elements," "median of stream," "K closest points."

## 2. VISUAL EXPLANATION

```
Min-Heap (smallest on top):
        1
       / \
      3   5
     / \
    7   9

After extracting min (1):
        3
       / \
      7   5
     /
    9
```

## 3. TEMPLATE CODE

```python
import heapq

# Min-heap operations in Python
heap = []
heapq.heappush(heap, value)      # Add element: O(log n)
min_val = heapq.heappop(heap)     # Remove & return smallest: O(log n)
min_val = heap[0]                  # Peek smallest: O(1)

# For MAX-heap: negate values!
heapq.heappush(heap, -value)      # Push negative
max_val = -heapq.heappop(heap)    # Pop and negate back
```

## 4. ALL PROBLEMS

### 15.1 Kth Largest Element in an Array (LeetCode 215) — 🟡 MED Priority

**Problem:** Find the Kth largest element (not Kth distinct).

**Approach 1: Sort**
```python
def findKthLargest_sort(nums, k):
    """TC: O(n log n), SC: O(1)"""
    nums.sort(reverse=True)
    return nums[k - 1]
```

**Approach 2: Min-Heap of size K**
```python
def findKthLargest(nums, k):
    """
    Maintain a min-heap of size k. The top is the Kth largest.
    TC: O(n log k), SC: O(k)
    """
    import heapq
    heap = nums[:k]
    heapq.heapify(heap)
    
    for num in nums[k:]:
        if num > heap[0]:     # Bigger than current Kth largest
            heapq.heapreplace(heap, num)  # Pop smallest, push new
    
    return heap[0]  # Top of min-heap = Kth largest

# [3,2,1,5,6,4], k=2 → 5
```

**Quick Revision:** "Kth Largest = min-heap of size k. Heap top is the answer."

### 15.2 Merge K Sorted Lists (implied)

```python
def mergeKLists(lists):
    """Use min-heap to efficiently merge. TC: O(n log k), SC: O(k)"""
    import heapq
    heap = []
    
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst.val, i, lst))
    
    dummy = ListNode(0)
    current = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        current.next = node
        current = current.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next
```

## 5. TCS-SPECIFIC NOTES

1. Heaps are **rarely** asked in TCS NQT — but Kth Largest appears occasionally
2. Know that `heapq` in Python is a **min-heap** by default
3. For max-heap, negate values (`push -x, pop -x`)
4. The sorting approach for Kth largest is acceptable for TCS (O(n log n) is fine)

---

# ═══════════════════════════════════════════════════
# 🔧 BONUS: MANUAL SORTING ALGORITHMS (TCS MUST-KNOW!)
# ═══════════════════════════════════════════════════

## ⚠️ TCS CRITICAL: Never use .sort() or sorted()! Implement manually!

### Bubble Sort — TC: O(n²), SC: O(1)
```python
def bubble_sort(arr):
    """
    Repeatedly swap adjacent elements if in wrong order.
    Largest element "bubbles" to end each pass.
    """
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        if not swapped:  # Already sorted, stop early
            break
    return arr
```

### Selection Sort — TC: O(n²), SC: O(1)
```python
def selection_sort(arr):
    """
    Find the minimum element and place it at the beginning.
    Repeat for remaining unsorted portion.
    """
    n = len(arr)
    for i in range(n):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    return arr
```

### Insertion Sort — TC: O(n²), SC: O(1)
```python
def insertion_sort(arr):
    """
    Build sorted portion from left to right.
    Insert each element into its correct position.
    """
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]  # Shift larger elements right
            j -= 1
        arr[j + 1] = key  # Insert at correct position
    return arr
```

### Quick Sort — TC: O(n log n) avg, SC: O(log n)
```python
def quick_sort(arr, low=0, high=None):
    """
    Pick pivot, partition around it, recurse on both halves.
    """
    if high is None:
        high = len(arr) - 1
    
    if low < high:
        pivot_idx = partition(arr, low, high)
        quick_sort(arr, low, pivot_idx - 1)
        quick_sort(arr, pivot_idx + 1, high)
    
    return arr

def partition(arr, low, high):
    """Lomuto partition: elements < pivot go left, >= go right."""
    pivot = arr[high]
    i = low - 1
    
    for j in range(low, high):
        if arr[j] < pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1
```

### Merge Sort — TC: O(n log n), SC: O(n)
```python
def merge_sort(arr):
    """
    Divide array in half, sort each half, merge them.
    """
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)

def merge(left, right):
    """Merge two sorted arrays."""
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

### Sorting Comparison Table

| Algorithm | Best TC | Avg TC | Worst TC | SC | Stable? | TCS Priority |
|-----------|---------|--------|----------|-----|---------|-------------|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes | 🔴 HIGH |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No | 🔴 HIGH |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes | 🔴 HIGH |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | No | 🟡 MED |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | 🟡 MED |

---

# ═══════════════════════════════════════════════════
# 🔗 BONUS: LINKED LIST DEEP DIVE (TCS Favorite!)
# ═══════════════════════════════════════════════════

### The Node Class (Know This Cold!)
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### Reverse Linked List (LeetCode 206) — 🔴 HIGH Priority
```python
def reverseList(head):
    """
    Three pointers: prev, curr, next_temp.
    TC: O(n), SC: O(1)
    """
    prev = None
    curr = head
    
    while curr:
        next_temp = curr.next  # Save next
        curr.next = prev       # Reverse pointer
        prev = curr            # Move prev forward
        curr = next_temp       # Move curr forward
    
    return prev  # New head
```

**Visual:**
```
1 → 2 → 3 → None

Step 1: prev=None, curr=1
        None ← 1   2 → 3 → None

Step 2: prev=1, curr=2
        None ← 1 ← 2   3 → None

Step 3: prev=2, curr=3
        None ← 1 ← 2 ← 3

Return prev (3) → 3 → 2 → 1 → None
```

**Quick Revision:** "Reverse LL = three pointers. Save next, reverse current arrow, advance both."

### Merge Two Sorted Lists (LeetCode 21) — 🔴 HIGH Priority
```python
def mergeTwoLists(l1, l2):
    """
    Compare heads, pick smaller, advance that pointer.
    TC: O(n+m), SC: O(1)
    """
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
    
    current.next = l1 or l2  # Attach remaining
    return dummy.next
```

### Delete Node in a Linked List (LeetCode 237) — 🟡 MED Priority
```python
def deleteNode(node):
    """
    Copy next node's value, then skip next node.
    TC: O(1), SC: O(1)
    """
    node.val = node.next.val
    node.next = node.next.next
```

### Remove Nth Node From End (LeetCode 19) — 🟡 MED Priority
```python
def removeNthFromEnd(head, n):
    """
    Two pointers: fast goes n steps ahead, then both move until fast reaches end.
    TC: O(L), SC: O(1) where L = list length
    """
    dummy = ListNode(0, head)
    fast = slow = dummy
    
    for _ in range(n + 1):  # n+1 to position slow before the target
        fast = fast.next
    
    while fast:
        fast = fast.next
        slow = slow.next
    
    slow.next = slow.next.next  # Skip the nth node
    return dummy.next
```

### Add Two Numbers (LeetCode 2) — 🟡 MED Priority
```python
def addTwoNumbers(l1, l2):
    """
    Add digit by digit with carry, like elementary addition.
    TC: O(max(m,n)), SC: O(max(m,n))
    """
    dummy = ListNode(0)
    current = dummy
    carry = 0
    
    while l1 or l2 or carry:
        total = carry
        if l1:
            total += l1.val
            l1 = l1.next
        if l2:
            total += l2.val
            l2 = l2.next
        
        carry = total // 10
        current.next = ListNode(total % 10)
        current = current.next
    
    return dummy.next
```

### Intersection of Two Linked Lists (LeetCode 160) — 🟡 MED Priority
```python
def getIntersectionNode(headA, headB):
    """
    Two pointers: when one reaches end, switch to other list's head.
    They meet at intersection (or both reach None).
    TC: O(m+n), SC: O(1)
    """
    a, b = headA, headB
    while a != b:
        a = a.next if a else headB
        b = b.next if b else headA
    return a
```

---

# ═══════════════════════════════════════════════════
# 📝 BONUS: REMAINING STRING PROBLEMS
# ═══════════════════════════════════════════════════

### String to Integer — atoi (LeetCode 8) — 🔴 HIGH Priority
```python
def myAtoi(s):
    """
    Parse string to integer: strip spaces, handle sign, read digits, clamp to 32-bit.
    TC: O(n), SC: O(1)
    """
    s = s.strip()
    if not s:
        return 0
    
    sign = 1
    i = 0
    result = 0
    
    # Handle sign
    if s[0] == '-':
        sign = -1
        i = 1
    elif s[0] == '+':
        i = 1
    
    # Read digits
    while i < len(s) and s[i].isdigit():
        result = result * 10 + int(s[i])
        i += 1
    
    result *= sign
    
    # Clamp to 32-bit range
    INT_MIN, INT_MAX = -2**31, 2**31 - 1
    return max(INT_MIN, min(INT_MAX, result))

# "   -42" → -42, "4193 with words" → 4193
```

**TCS Tip:** TCS loves string parsing problems. This is a classic!

### Implement strStr() (LeetCode 28) — 🟡 MED Priority
```python
def strStr(haystack, needle):
    """
    Find first occurrence of needle in haystack.
    TC: O(n*m), SC: O(1)
    """
    if not needle:
        return 0
    
    for i in range(len(haystack) - len(needle) + 1):
        if haystack[i:i + len(needle)] == needle:
            return i
    
    return -1
```

### Multiply Strings (LeetCode 43) — 🟡 MED Priority
```python
def multiply(num1, num2):
    """
    Grade-school multiplication without using int() for full conversion.
    TC: O(m*n), SC: O(m+n)
    """
    if num1 == "0" or num2 == "0":
        return "0"
    
    m, n = len(num1), len(num2)
    result = [0] * (m + n)
    
    for i in range(m - 1, -1, -1):
        for j in range(n - 1, -1, -1):
            mul = int(num1[i]) * int(num2[j])
            p1, p2 = i + j, i + j + 1
            total = mul + result[p2]
            
            result[p2] = total % 10
            result[p1] += total // 10
    
    # Remove leading zeros
    result_str = ''.join(map(str, result)).lstrip('0')
    return result_str or "0"
```

### Add Strings (LeetCode 415) — 🟡 MED Priority
```python
def addStrings(num1, num2):
    """Add two number strings without int() conversion."""
    result = []
    carry = 0
    i, j = len(num1) - 1, len(num2) - 1
    
    while i >= 0 or j >= 0 or carry:
        total = carry
        if i >= 0:
            total += ord(num1[i]) - ord('0')
            i -= 1
        if j >= 0:
            total += ord(num2[j]) - ord('0')
            j -= 1
        result.append(str(total % 10))
        carry = total // 10
    
    return ''.join(reversed(result))
```

### Longest Palindromic Substring (LeetCode 5) — 🟡 MED Priority
```python
def longestPalindrome(s):
    """
    Expand around center for each position.
    TC: O(n²), SC: O(1)
    """
    def expand(left, right):
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return s[left + 1:right]
    
    result = ""
    for i in range(len(s)):
        # Odd length palindrome
        odd = expand(i, i)
        # Even length palindrome
        even = expand(i, i + 1)
        
        longer = odd if len(odd) > len(even) else even
        if len(longer) > len(result):
            result = longer
    
    return result
```

### Excel Sheet Column Number/Title (LeetCode 171/168) — 🟡 MED Priority
```python
def titleToNumber(columnTitle):
    """A=1, B=2, ..., Z=26, AA=27, AB=28..."""
    result = 0
    for char in columnTitle:
        result = result * 26 + (ord(char) - ord('A') + 1)
    return result

def convertToTitle(columnNumber):
    """Reverse of above."""
    result = ""
    while columnNumber > 0:
        columnNumber -= 1  # Make 0-indexed
        result = chr(columnNumber % 26 + ord('A')) + result
        columnNumber //= 26
    return result
```

---

# ═══════════════════════════════════════════════════
# 🏆 ULTIMATE REVISION SHEET — ALL 15 PATTERNS
# ═══════════════════════════════════════════════════

| # | Pattern | One-Line Summary | Template Key Line | Must-Do Problem |
|---|---------|-----------------|-------------------|-----------------|
| 1 | Two Pointers | Two indices moving toward each other or same direction | `while left < right` | Sort Colors (75) |
| 2 | Sliding Window | Expandable/shrinkable window across data | `while window_invalid: shrink left` | Longest Substr No Repeat (3) |
| 3 | Fast & Slow | Two pointers at different speeds | `slow = slow.next; fast = fast.next.next` | Linked List Cycle (141) |
| 4 | Math/Numbers | Digit extraction, primes, GCD | `digit = n % 10; n //= 10` | Reverse Integer (7) |
| 5 | Hash Maps | O(1) lookup with key-value pairs | `if complement in seen` | Two Sum (1) |
| 6 | Binary Search | Halve search space each step | `mid = left + (right-left)//2` | Binary Search (704) |
| 7 | Stack | LIFO for matching/nesting | `stack.append(x); stack.pop()` | Valid Parentheses (20) |
| 8 | DP | Store subproblem results | `dp[i] = dp[i-1] + dp[i-2]` | Climbing Stairs (70) |
| 9 | BFS/DFS | Level-by-level / depth-first | `queue = deque([root])` / recursion | Max Depth (104) |
| 10 | Bit Manipulation | XOR, AND, shift operations | `result ^= num` | Single Number (136) |
| 11 | Backtracking | Try all, undo bad choices | `choose → explore → un-choose` | Subsets (78) |
| 12 | Merge Intervals | Sort + merge overlapping ranges | `if curr[0] <= last[1]: merge` | Merge Intervals (56) |
| 13 | Kadane's | Max contiguous subarray sum | `curr = max(num, curr + num)` | Max Subarray (53) |
| 14 | Prefix Sums | Precomputed cumulative sums | `prefix[i] = prefix[i-1] + arr[i]` | Product Except Self (238) |
| 15 | Heaps | Efficient min/max tracking | `heapq.heappush/heappop` | Kth Largest (215) |

---

# 🎯 TCS NQT EXAM DAY STRATEGY

| Step | What to Do | Time |
|------|-----------|------|
| 1 | **Read BOTH questions** completely | 5 min |
| 2 | **Identify patterns** for each question | 2 min |
| 3 | **Solve Q1** (easier one first) | 35-40 min |
| 4 | **Test Q1** with edge cases | 5 min |
| 5 | **Solve Q2** (harder one) | 30-35 min |
| 6 | **Test Q2** with edge cases | 5 min |
| 7 | **Review** both solutions | 3 min |

### Key TCS Reminders:
1. ❌ Don't use `.sort()`, `sorted()`, `bin()`, `math.gcd()` — implement manually
2. ❌ Don't use string slicing tricks like `s[::-1]` — use loops
3. ✅ Focus on: **Arrays, Strings, Math — they cover 80% of TCS questions**
4. ✅ **Partial marks exist** — even if you can't solve optimally, solve the brute force!
5. ✅ Handle edge cases: empty input, single element, all same elements, negative numbers
6. ✅ Use meaningful variable names and add comments — readability matters

### Top 20 Must-Solve Problems for TCS NQT:
1. Reverse Integer (7)
2. Palindrome Number (9)
3. Two Sum (1)
4. Best Time to Buy/Sell Stock (121)
5. Maximum Subarray — Kadane's (53)
6. Valid Parentheses (20)
7. Reverse String (344)
8. Binary Search (704)
9. Sort Colors (75)
10. Move Zeroes (283)
11. Contains Duplicate (217)
12. Count Primes (204)
13. GCD (manual implementation)
14. Binary ↔ Decimal Conversion
15. Bubble/Selection/Insertion Sort (manual)
16. Linked List Cycle (141)
17. Reverse Linked List (206)
18. Valid Anagram (242)
19. First Unique Character (387)
20. String to Integer — atoi (8)

---

*🚀 You have everything you need, Krish. Master these patterns, practice the problems, and ace TCS NQT 2026!*
*Remember: Patterns > Individual Problems. Learn the pattern, solve ANY variation!*
