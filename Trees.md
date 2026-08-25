# Trees — Pattern Reference
 
## Core idea
A binary tree is naturally recursive: a tree is a node + (smaller tree on the left) + (smaller tree on the right).
Same base-case/recursive-case principle as linear recursion, but now **two recursive calls per node** instead of one — the call stack becomes a branching tree, not a line (same shape as Fibonacci/Climbing Stairs).
 
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```
 
## Two families of tree problems
 
### Family 1: Traversal (visit every node in some order)
No combining — just decide *when* to "visit" (print/append) relative to going left/right.
 
- **Preorder**: visit → left → right
- **Inorder**: left → visit → right (gives sorted output on a BST specifically)
- **Postorder**: left → right → visit
- **BFS / Level order**: not recursive — uses a queue, visits level by level
### Family 2: Combine-style (use children's *returned answers* to compute something new)
The hard jump from Family 1. Mental model: **"ask my children a question, then use their answers to compute my own."**
You don't need to trace the whole call stack — just answer one question at each node, trusting recursion handles the rest (same "trust" principle as `sum(n-1)`, just applied twice per node now).
 
3-step skeleton, used in every combine-style problem below:
```python
def solve(root):
    if root is None:
        return <base value>
    left_result = self.solve(root.left)     # trust: fully solved already
    right_result = self.solve(root.right)    # trust: fully solved already
    return <combine left_result, right_result, root.val somehow>
```
 
---
 
## Traversal code
 
```python
class Solution:
    def inorderTraversal(self, root):
        res = []
        def inorder(root):
            if root is None:
                return
            inorder(root.left)
            res.append(root.val)
            inorder(root.right)
        inorder(root)
        return res
```
``` 
    def preorderTraversal(self, root):
        res = []
        def preorder(root):
            if root is None:
                return
            res.append(root.val)
            preorder(root.left)
            preorder(root.right)
        preorder(root)
        return res
 ```
```
    def postorderTraversal(self, root):
        res = []
        def postorder(root):
            if root is None:
                return
            postorder(root.left)
            postorder(root.right)
            res.append(root.val)
        postorder(root)
        return res
 ```
```
    def levelOrder(self, root):
        from collections import deque
        if root is None:
            return []
        result = []
        q = deque([root])
        while q:
            level_size = len(q)          # snapshot: how many nodes in THIS level
            current_level = []
            for _ in range(level_size):   # process exactly that many nodes
                node = q.popleft()
                current_level.append(node.val)
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            result.append(current_level)
        return result
```
 
**BFS key trick**: `level_size = len(q)` — freeze the queue's size *before* the for-loop starts. Children get pushed *during* the loop, but the loop only runs `level_size` times, so it stops exactly at the level boundary, before touching next-level nodes.
 
