```
"""
Definition of Node
class Node:
    def _init_(self,val):
        self.data = val
        self.left = None
        self.right = None
"""

class Solution:
    def paths(self, root):
        # code here
        result = []
        path = []
        def dfs(node):
            if not node:
                return []
            
            path.append(node.data)
             
            if not node.left and not node.right:
                result.append(path.copy())

            
            
            if node.left:
                dfs(node.left)
                
            if node.right:
                dfs(node.right)
                
            path.pop()
           
        dfs(root)

```
        return result
