# Pattern 2: Two Pointers

## Mental Model
Use two index variables to scan the array instead of one.
Avoid nested loops (O(n²)) by moving both pointers intelligently based on a condition.

## When to Recognize It
- "find a pair that sums to target" in a sorted array
- "remove duplicates in place"
- "reverse something"
- "check if palindrome"
- "container with most water"
- anything with "left and right" or "start and end"

## Core Templates

### Template 1 — Opposite Ends
Start one pointer at the beginning, one at the end. Move them toward each other.

```python
def two_pointers(arr):
    left = 0
    right = len(arr) - 1

    while left < right:
        if # condition met:
            return # answer
        elif # too small:
            left += 1
        else:
            right -= 1
```

### Template 2 — Same Direction (Fast/Slow)
Both pointers start at the beginning. One moves faster than the other.

```python
def fast_slow(arr):
    slow = 0

    for fast in range(len(arr)):
        if # condition met:
            arr[slow] = arr[fast]
            slow += 1

    return slow
```

---

## Problem List

**Easy**
1. Valid Palindrome
2. Two Sum II (sorted array)
3. Remove Duplicates from Sorted Array
4. Move Zeroes
5. Reverse String

**Medium**
1. Container With Most Water
2. 3Sum
3. Trapping Rain Water

---

## Solutions

### 1. Valid Palindrome
```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        newstr = ""
        for ch in s:
            if ch.isalnum():
                newstr += ch.lower()

        length = len(newstr)
        left = 0
        right = length - 1

        while left <= right:
            if newstr[left] == newstr[right]:
                left += 1
                right -= 1
            else:
                return False
        return True
```

### 2. Two Sum II (sorted array)
```python
class Solution:
    def twoSum(self, num: List[int], t: int) -> List[int]:
        left = 0
        right = len(num) - 1
        while left < right:
            total = num[left] + num[right]
            if total < t:
                left += 1
            elif total > t:
                right -= 1
            else:
                return [left + 1, right + 1]
```

### 3. Remove Duplicates from Sorted Array
```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        slow = 0
        for fast in range(1, len(nums)):
            if nums[slow] != nums[fast]:
                slow += 1
                nums[slow] = nums[fast]
        return slow + 1
```

### 4. Move Zeroes
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        left = 0
        for el in range(0, len(nums)):
            if nums[el] != 0:
                nums[left], nums[el] = nums[el], nums[left]  # swapping
                left += 1
```

### 5. Reverse String
```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        left = 0
        right = len(s) - 1
        while left < right:
            s[left], s[right] = s[right], s[left]
            left += 1
            right -= 1
```
