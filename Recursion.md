# Recursion — Pattern Reference

## Core idea
A recursive function calls itself to solve a smaller version of the same problem.
Every recursive function needs:
- **Base case** — smallest input where you already know the answer, no more recursion needed.
- **Recursive case** — calls itself with a *smaller* input, then combines that result to solve the current input.

No base case, or input that never shrinks toward it → infinite recursion → stack overflow.

## Mental model: call stack
- Each call **pauses** and pushes a new call on top (like a stack of plates).
- Only the base case actually returns a value directly — everything else is "pending" until then.
- Two phases: **going down** (calls being made, nothing computed) and **coming back up** (returning + combining).

## Trace table format (use this every time)
Split into two clearly separated phases — never mix "pending" and "resolved" in the same line.

**Going down:**
| Call | Waiting on |
|---|---|
| fn(4) | fn(3) |
| fn(3) | fn(2) |
| ... | ... |
| fn(0) | base case |

**Coming back up:**
| Call | Computation | Returns |
|---|---|---|
| fn(0) | base case | value |
| fn(1) | combine with fn(0) | value |
| ... | ... | ... |

## Shapes covered (5 reps, Aug 2026)

### 1. Single recursive call, "build going up" (factorial / sum(n) style)
```python
def sum(n):
    if n == 0:
        return 0
    return n + sum(n-1)
```
- Base case value matters — it's what everything else adds/multiplies onto.
- Check if one base case already covers the "edge" case before adding a redundant second one.

### 2. Single recursive call, string/array shrink (reverseStr / isPalindrome style)
```python
def reverseStr(s):
    if s == "":
        return ""
    return reverseStr(s[1:]) + s[0]

def isPalindrome(s):
    if len(s) <= 1:
        return True
    if s[0] != s[-1]:
        return False
    return isPalindrome(s[1:-1])
```
- Some recursive returns "build" something (reverseStr concatenates), others just "pass a verdict through" (isPalindrome — no combining math, just propagates True/False).

### 3. Recursive call with transformed input (Add Digits style — LC 258)
```python
def sumdigits(n):
    if n == 0:
        return 0
    return n % 10 + sumdigits(n // 10)

class Solution:
    def addDigits(self, n: int) -> int:
        if n <= 9:
            return n
        return self.addDigits(sumdigits(n))
```
- Function calls itself with a *new computed value*, not just n-1 or n//10.
- Key insight: reuse the same function's "check + repeat" logic instead of writing a new loop.

### 4. Two recursive calls — branching (Fibonacci — LC 509)
```python
class Solution:
    def fib(self, n: int) -> int:
        if n == 0:
            return 0
        if n == 1:
            return 1
        return self.fib(n-1) + self.fib(n-2)
```
- Call stack becomes a **tree**, not a line — two branches per call.
- Same values get recomputed multiple times (fib(1), fib(0) computed repeatedly) — this is why Fibonacci is the classic memoization/DP intro example.
- **This branching shape is the direct precursor to Trees (left subtree + right subtree = same two-call pattern).**

### 5. Two recursive calls, "count paths" style (Climbing Stairs — LC 70)
```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n == 1 or n == 0:
            return 1
        return self.climbStairs(n-1) + self.climbStairs(n-2)
```
- Base case n=0 returns **1**, not 0 — represents "already at the top, one valid way (do nothing)."
- Common trap: patching a wrong base case with a hardcoded extra case (e.g. `if n==2: return 2`) instead of fixing the root value.

## Call trace diagrams (visual)

### sum(3) — single call, linear stack
```
sum(3)
  -> sum(2)
       -> sum(1)
            -> sum(0)
                 returns 0        [base case]
            returns 1 + 0 = 1
       returns 2 + 1 = 3
  returns 3 + 3 = 6
```

### reverseStr("abc") — single call, linear stack, builds on the way up
```
reverseStr("abc")
  -> reverseStr("bc")
       -> reverseStr("c")
            -> reverseStr("")
                 returns ""                [base case]
            returns "" + "c" = "c"
       returns "c" + "b" = "cb"
  returns "cb" + "a" = "cba"
```

### isPalindrome("abba") — single call, linear stack, verdict passes through
```
isPalindrome("abba")   a==a, ok
  -> isPalindrome("bb")   b==b, ok
       -> isPalindrome("")
            returns True             [base case]
       returns True                  (just passed up, nothing combined)
  returns True
```

### addDigits(199) — chained transform, restarts as a fresh call each time
```
addDigits(199)
  -> sumdigits(199) = 1+9+9 = 19        [helper, separate linear stack]
  -> addDigits(19)
       -> sumdigits(19) = 1+9 = 10
       -> addDigits(10)
            -> sumdigits(10) = 1+0 = 1
            -> addDigits(1)
                 returns 1                [base case: n<=9]
            returns 1
       returns 1
  returns 1
```

### fib(4) — two calls, branching tree
```
                          fib(4)
                         /      \
                    fib(3)      fib(2)
                   /      \      /    \
              fib(2)   fib(1) fib(1) fib(0)
             /      \
        fib(1)    fib(0)

Coming back up:
  fib(1)=1, fib(0)=0                     [base cases, leaves of the tree]
  fib(2) = fib(1)+fib(0) = 1+0 = 1       (computed twice, both =1)
  fib(3) = fib(2)+fib(1) = 1+1 = 2
  fib(4) = fib(3)+fib(2) = 2+1 = 3
```
Note: fib(1) is computed 3 times, fib(0) is computed 2 times — nothing is cached. This wasted recomputation is exactly what memoization/DP fixes later.

### climbStairs(3) — two calls, branching tree
```
                    climbStairs(3)
                   /               \
          climbStairs(2)         climbStairs(1)
           /          \                 |
   climbStairs(1)  climbStairs(0)    returns 1   [base case]
       |                |
   returns 1        returns 1
    [base case]     [base case]

Coming back up:
  climbStairs(2) = 1 + 1 = 2
  climbStairs(3) = 2 + 1 = 3
```

## Recurring bugs to watch for
- Forgetting `return` before a recursive call (silently returns None).
- `self.` required inside `class Solution:` methods; NOT required for standalone `def` functions outside a class.
- Redundant base cases — check if one base case (e.g. n==0) already covers the next value up (n==1) before adding a second check.
- Wrong base case *value* (not just wrong condition) — e.g. climbStairs(0) should be 1, not 0. Trace a known small example to catch this.
- Mixing "going down" and "coming back up" in one trace line — always separate them fully, especially once branching (two calls) is involved.


## Next in roadmap
Trees BFS/DFS — left/right subtree recursion uses the exact two-branch shape from Fibonacci/Climbing Stairs above.
