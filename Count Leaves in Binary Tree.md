```
class Solution:
    def countLeaves(self, root):
        self.leaf_count = 0

        def inorder(node):
            if node is None:
                return

            # Visit Left
            inorder(node.left)

            # Visit Root
            if node.left is None and node.right is None:
                self.leaf_count += 1

            # Visit Right
            inorder(node.right)

        inorder(root)

        return self.leaf_count
```

#######################################
```
class Solution:
    def countLeaves(self, root):
        if root is None:
            return 0

    # Current node is a leaf
        if root.left is None and root.right is None:
            return 1

    # Count leaves in left and right subtrees
        left = self.countLeaves(root.left)
        right = self.countLeaves(root.right)

        return left + right
```
