# Pattern 3: Binary Search

## Mental Model
Whenever the search space is sorted (or has some monotonic property — a condition that flips from true to false at one point, or values that increase/decrease), you can cut the space in half each step instead of scanning linearly. O(log n) instead of O(n).

## When to Recognize It
- "sorted array" + "find target"
- "find the boundary where condition flips from true to false" (or vice versa)
- "minimize/maximize X such that condition holds" — search over the *answer*, not the array
- anything asking for O(log n) on a sorted or monotonic structure

## Core Template

```python
def binary_search(arr, target):
    left = 0
    right = len(arr) - 1

    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

**Boundary reasoning:**
- `left <= right`, not `<` — when `left == right`, there's still one element left to check; using `<` would exit the loop and skip it.
- `left = mid + 1` / `right = mid - 1`, not `mid` — `mid` has already been ruled out by the `if`/`elif` above, so it must be excluded from the new range. Leaving `mid` in (e.g. `left = mid`) risks an infinite loop when the range shrinks to two elements.

---

## Problem List

**Easy**
1. Binary Search (vanilla template)
2. Search Insert Position
3. First Bad Version
4. Sqrt(x)
5. Valid Perfect Square

**Medium**
1. Search in Rotated Sorted Array
2. Find Minimum in Rotated Sorted Array
3. Find First and Last Position of Element in Sorted Array
4. Koko Eating Bananas (binary search on the answer)
5. Capacity To Ship Packages Within D Days (binary search on the answer)

---

## Solutions

1. Binary Search (vanilla template)
```python3
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left<=right :
            mid = (left+right) // 2 
            if nums[mid] == target :
                return mid
            elif nums[mid] < target :
                left = mid+1
            else :
                right = mid -1

        return -1
        
```
2. Search Insert Position
```python3
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0 
        right = len(nums) -1
        while left <= right :
            mid = (left+right) // 2
            if nums[mid] == target :
                return mid 
            elif nums[mid] < target :
                left = mid +1
            else :
                right = mid -1
        return left 
```
3. First Bad Version

# The isBadVersion API is already defined for you.
# def isBadVersion(version: int) -> bool:
```python3
class Solution:
    def firstBadVersion(self, n: int) -> int:
        left = 1
        right = n
        while left < right :
            mid = (left+right) // 2
            if isBadVersion(mid) :
                right = mid 
            else :
                left = mid +1 
        return left 
```

4. Sqrt(x)
```python3
class Solution:
    def mySqrt(self, x: int) -> int:
      l = 1
      r = x
      if x == 0 :
        return 0
      while l<=r:
        m = (l+r) // 2
        if m*m == x:
            return m 
        elif m*m < x:
            l = m+1
        else :
            r = m-1
      return r
```
5. Valid Perfect Square
```python3
class Solution:
    def isPerfectSquare(self, num: int) -> bool:
            l = 1
            r = num
            while l<=r:
                m = (l+r) // 2
                if m*m == num:
                    return True
                elif m*m < num:
                    l = m+1
                else :
                    r = m-1
            return False
```
        
