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

**Sliding Window (bridge from Two Pointers)**
1. Minimum Size Subarray Sum 
2. Minimum Contiguous Houses (multi-test-case variant) 

**Bonus (different pattern — Prefix Sum)**
1. Range Sum Query 

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

### 6. Container With Most Water
```python
class Solution:
    def maxArea(self, h: List[int]) -> int:
        left = 0
        right = len(h) - 1
        max_a = 0
        while left < right:
            min_h = min(h[left], h[right])
            current_area = min_h * (right - left)
            max_a = max(current_area, max_a)
            if h[left] < h[right]:
                left += 1
            else:
                right -= 1
        return max_a
```
Key rule: move only the pointer at the **shorter** height. Moving the taller one can never improve the area — width shrinks and the limiting height can't increase.

### 7. Minimum Size Subarray Sum
```python
n, t = map(int, input().split())
arr = list(map(int, input().strip().split()))

left = 0
right = 0
sum_t = 0
count = float('inf')

while right < n:
    sum_t += arr[right]
    right += 1
    while sum_t >= t:
        curr_len = right - left
        count = min(count, curr_len)
        sum_t -= arr[left]
        left += 1

print(0 if count == float('inf') else count)
```
Sliding window: `right` expands the window, `left` shrinks it once the sum condition is met. `curr_len = right - left` (not `+1`) since `right` has already moved past the last included index by the time the check runs.

### 8. Minimum Contiguous Houses (multi-test-case variant)
```python
n = int(input())
arr = list(map(int, input().strip().split()))
tc = int(input())

for i in range(tc):
    target = int(input())

    left = 0
    right = 0
    sum_t = 0
    count = float('inf')

    while right < n:
        sum_t += arr[right]
        right += 1
        while sum_t >= target:
            curr_len = right - left
            count = min(count, curr_len)
            sum_t -= arr[left]
            left += 1

    print(0 if count == float('inf') else count)
```
Same sliding window as Min Size Subarray Sum, but reset `left`, `right`, `sum_t`, `count` fresh for **each** test case's target.

### 9. 3Sum
```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        nums.sort()
        result = []

        for i in range(len(nums)):
            if i > 0 and nums[i] == nums[i-1]:
                continue

            left = i + 1
            right = len(nums) - 1
            target = -nums[i]

            while left < right:
                total = nums[left] + nums[right]
                if total < target:
                    left += 1
                elif total > target:
                    right -= 1
                else:
                    result.append([nums[i], nums[left], nums[right]])
                    left += 1
                    right -= 1
                    while left < right and nums[left] == nums[left - 1]:
                        left += 1
                    while left < right and nums[right] == nums[right + 1]:
                        right -= 1
        return result
```
Sort first, then fix one element (`nums[i]`) and run Two Sum II on the rest for `target = -nums[i]`. Skip duplicate `i` values, and skip duplicate `left`/`right` values after a match to avoid repeated triplets.

### 10. Trapping Rain Water
```python
class Solution:
    def trap(self, h: List[int]) -> int:
        l = 0
        r = len(h) - 1
        lmax = 0
        rmax = 0
        s = 0

        while l < r:
            lmax = max(lmax, h[l])
            rmax = max(rmax, h[r])

            if lmax < rmax:
                s += lmax - h[l]
                l += 1
            else:
                s += rmax - h[r]
                r -= 1

        return s
```
Water at a position is bounded by `min(left_max, right_max) - height`. Whichever side has the smaller max so far is guaranteed correct regardless of unseen walls in between — process only that side, one position per iteration, then move only that pointer.

### Bonus: Range Sum Query (Prefix Sum — different pattern)
```python
n, q = map(int, input().split())
arr = list(map(int, input().strip().split()))
pre = [0] * n
pre[0] = arr[0]
for i in range(1, n):
    pre[i] = pre[i - 1] + arr[i]

for i in range(q):
    l, r = map(int, input().split())
    l -= 1
    r -= 1
    if l == 0:
        curr_sum = pre[r]
    else:
        curr_sum = pre[r] - pre[l - 1]
    print(curr_sum)
```
`pre[i]` = sum of `arr[0..i]` inclusive. For 1-indexed query `[L, R]`: answer = `pre[R-1] - pre[L-2]`, i.e. `pre[r] - pre[l-1]` after converting `l, r` to 0-indexed — special-cased when `l == 0` since `pre[-1]` would wrap around incorrectly.

---

## Key Takeaways
- **Check-before-store vs store-before-check:** check-before-store when looking for something seen in a *previous* iteration (Two Sum complement, Contains Duplicate). Store-before-check when checking the *current* element's own running count (element appearing >2 times, Top K, Valid Anagram).
- **Swap vs overwrite:** when the displaced value still matters (e.g. Move Zeroes), swap — don't just overwrite, or you lose data.
- Two pointers moving in the *same* direction (slow/fast) vs *opposite* ends are two distinct templates — pick based on whether the array is sorted and what relationship you're checking.
