# Pattern 4: Stack

## Mental Model
A stack is Last-In-First-Out (LIFO) — the last thing you pushed is the first thing you pop.
Useful whenever a problem needs you to "remember what came before, but only care about the most recent unmatched/unresolved thing."

## When to Recognize It
- "valid parentheses / matching brackets"
- "next greater/smaller element"
- anything with nested structure — expressions, HTML tags, function calls
- "evaluate this expression" (postfix/infix)
- "undo" style problems, or anything where you peel back the *most recent* unresolved item first

## Core Template

```python
def stack_pattern(arr):
    stack = []
    for item in arr:
        while stack and # some condition comparing item to stack[-1]:
            stack.pop()
        stack.append(item)
    return stack
```

**Vocabulary:**
- `stack.append(item)` — **push**, adds to the end of the list.
- `stack.pop()` — **pop**, removes and returns the last item.
- `stack[-1]` — **peek**, looks at the last item without removing it.

**Why a stack, not a queue, for nesting/matching problems:** closing a bracket must undo the *most recently opened*, still-unresolved one — that's LIFO behavior. A queue (FIFO) would try to resolve things in the order they were first opened, ignoring nesting, and gives wrong answers on cases like `"([)]"`.

---

## Problem List

**Easy**
1. Valid Parentheses 
2. Min Stack 
3. Baseball Game 
4. Implement Queue using Stacks 
5. Remove All Adjacent Duplicates in String 

**Medium**
1. Daily Temperatures 
2. Evaluate Reverse Polish Notation
3. Next Greater Element I / II
4. Decode String
5. Asteroid Collision

---

## Solutions

### 1. Valid Parentheses
```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        pairs = {")": "(", "]": "[", "}": "{"}

        for char in s:
            if char in pairs:
                if not stack or stack[-1] != pairs[char]:
                    return False
                else:
                    stack.pop()
            else:
                stack.append(char)
        return not stack
```
`pairs` dict does double duty: `char in pairs` checks "is this a closing bracket," and `pairs[char]` gives the opener it should match. Final `return not stack` (not `True`) catches leftover unmatched openers, e.g. `"((("`.

### 2. Min Stack
```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.minstack = []

    def push(self, value: int) -> None:
        self.stack.append(value)
        if not self.minstack or value <= self.minstack[-1]:
            self.minstack.append(value)
        else:
            self.minstack.append(self.minstack[-1])

    def pop(self) -> None:
        self.stack.pop()
        self.minstack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.minstack[-1]
```
A second "shadow" stack tracks the running minimum at each depth, in sync with the main stack. Popping both together automatically "rewinds" the minimum — no separate tracking variable needed (a single variable would go stale once the actual minimum gets popped).

### 3. Baseball Game
```python
class Solution:
    def calPoints(self, operations: List[str]) -> int:
        stack = []
        for i in operations:
            if i == "+":
                stack.append(stack[-1] + stack[-2])
            elif i == "D":
                stack.append(2 * stack[-1])
            elif i == "C":
                stack.pop()
            else:
                stack.append(int(i))
        return sum(stack)
```
Stack as running history of valid scores, not match-and-cancel. Numbers arrive as strings — must `int()` convert before pushing. Answer is `sum(stack)`, not just the last score.

### 4. Implement Queue using Stacks
```python
class MyQueue:
    def __init__(self):
        self.stackin = []
        self.stackout = []

    def push(self, x: int) -> None:
        self.stackin.append(x)

    def pop(self) -> int:
        if not self.stackout:
            while self.stackin:
                x = self.stackin.pop()
                self.stackout.append(x)
            return self.stackout.pop()
        else:
            return self.stackout.pop()

    def peek(self) -> int:
        if not self.stackout:
            while self.stackin:
                x = self.stackin.pop()
                self.stackout.append(x)
            return self.stackout[-1]
        else:
            return self.stackout[-1]

    def empty(self) -> bool:
        return not self.stackin and not self.stackout
```
Two stacks simulate FIFO using only LIFO operations. `stackin` collects new pushes. When `stackout` runs empty and something needs to come out, dump everything from `stackin` into `stackout` — this reverses the order, putting the oldest item on top. Only re-flip when `stackout` is empty again (amortized efficiency — don't flip on every operation).

### 5. Remove All Adjacent Duplicates in String
```python
class Solution:
    def removeDuplicates(self, s: str) -> str:
        stack = []
        for char in s:
            if not stack or stack[-1] != char:
                stack.append(char)
            else:
                stack.pop()
        return "".join(stack)
```
Same "compare to top, push or pop" shape as Valid Parentheses, but the "match" condition is just equality with the previous character, and a match means cancel (pop) rather than confirm-and-continue.

### 6. Daily Temperatures (in progress)
```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        stack = []
        result = [0] * len(temperatures)

        for i, temp in enumerate(temperatures):
            while stack and temp > temperatures[stack[-1]]:
                prev_index = stack.pop()
                result[prev_index] = i - prev_index
            stack.append(i)

        return result
```
**Monotonic stack** — first pattern where the stack holds *indices*, not values, because the answer needed is a distance (today's index minus the waiting day's index), not just a value. `stack[-1]` gives the index on top; `temperatures[stack[-1]]` looks up that day's actual temperature through the index. Every index gets pushed exactly once and popped at most once, so this is O(n) overall despite the nested loop appearance.

---

## Key Takeaways
- **Match-and-cancel stacks** (Valid Parentheses, Remove Adjacent Duplicates): compare current item to `stack[-1]`, push if no match, pop if match.
- **Shadow stacks** (Min Stack): a second stack tracked in perfect sync with the first, to get O(1) access to some derived property (min, max, running total) that would otherwise need an O(n) scan.
- **Two-stack simulation** (Implement Queue using Stacks): reversing order by fully draining one stack into another — only re-drain when necessary, not on every operation.
- **Monotonic stack** (Daily Temperatures): stack holds *indices*, not values, and maintains an implicit ordering (temperatures decrease going down the stack) by popping everything that violates it before pushing the new index.