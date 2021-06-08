# Leetcode Notable Tree problems

## Q: “max sum of the path in a binary tree”

__Solution:__

1. Keep a global max
2. Make a recursive function to calculate “max gain” for a node from bottom up.
    1. 0 on base case
    2. `maxLeftGain = max(0 , gain(left))` - __max is important here__ to determine whether left branch to be chosen.
    3. `maxRightGain = max(0, gain(right))` - again __max is important here!__
    4. `newMax = val + maxLeftGain + maxRightGain`
    5. Update max
    6. Return `val + max(maxLeftGain, maxRightGain)` - __to choose one path and not having the path in only one subtree__.
3. Call recursive method and return global max.

## Q: Diameter of a binary tree / “Longest path in a binary tree”

> Similar to max sum in a binary tree.

## Q: min sum binary tree path

__Solution:__

1. `dp[i][j] = (first row or first column) ? max(top, left) : min(top, left)`.
2. Answer = `dp[L][W]`.

## Q: Construct binary tree from pre-order and in-order given

__Solution:__

1. `Root = preorder[0]`
2. `Inorder = [ left-inorder, Root, right-inorder ]`
3. Do this recursively

```java
private TreeNode buildTreeUtil(int[] preorder, int[] inorder, int pi, int il, int ir) {
    if (il > ir || il < 0 || ir >= inorder.length || pi >= preorder.length) return null;

    int i = il;
    while (i <= ir) {
        if (inorder[i] == preorder[pi]) {
            break;
        }
        i++;
    }

    return new TreeNode(
        preorder[pi],
        buildTreeUtil(preorder, inorder, pi+1, il, i-1),
        buildTreeUtil(preorder, inorder, pi+i-il+1, i+1, ir)
    );
}

public TreeNode buildTree(int[] preorder, int[] inorder) {
    return buildTreeUtil(preorder, inorder, 0, 0, inorder.length-1);
}
```

## Q: Kth smallest number in BST - same with inorder recursion (standard iterative approach)

> use a stack instead of recursion

```java
  public int kthSmallest(TreeNode root, int k) {
    LinkedList<TreeNode> stack = new LinkedList<TreeNode>();

    while (true) {
      while (root != null) {
        stack.add(root);
        root = root.left;
      }
      root = stack.removeLast();
      if (--k == 0) return root.val;
      root = root.right;
    }
  }
```

## Q: Populate next right pointer in each node

__Solution:__

1. level-order-traversal
2. Using previously populated “next” pointers (amazing)

```java
public Node connect(Node root) {
        if (root == null) return root;

    // left most of current level - starting point
    Node leftmost = root;

    // Once we reach the final level, we are done
    while (leftmost.left != null) {

        // Iterate the "linked list" starting from the head
        // node and using the next pointers, establish the
        // corresponding links for the next level
        Node head = leftmost;

        while (head != null) {

            // CONNECTION 1
            head.left.next = head.right;

            // CONNECTION 2
            if (head.next != null) {
                head.right.next = head.next.left;
            }

            // Progress along the list (nodes on the current level)
            head = head.next;
        }

        // Move onto the next level
        leftmost = leftmost.left;
    }

    return root;
}
```

## Q: Delete nodes and return forest - all the partial tree formed after deleting linking nodes

```java
public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
    Set<Integer> del = new HashSet<>();
    for (int i : to_delete) {
        del.add(i);
    }
    List<TreeNode> res = new ArrayList<>();
    solve(root, true, del, res);
    return res;
}

private TreeNode solve(TreeNode root, boolean isroot,
                        Set<Integer> toDelete, List<TreeNode> res) {
    if (root == null) return null;
    boolean deleted = toDelete.contains(root.val);

    if (isroot && !deleted) {
        res.add(root);
    }
    root.left = solve(root.left, deleted, toDelete, res);
    root.right = solve(root.right, deleted, toDelete, res);

    return (deleted) ? null : root;
}
```
