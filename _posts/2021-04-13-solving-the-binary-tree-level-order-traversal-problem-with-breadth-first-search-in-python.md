The problem statement can be found [here](https://leetcode.com/problems/binary-tree-level-order-traversal/).

We are dealing here with a tree as a data structure. Since a [tree](https://en.wikipedia.org/wiki/Tree_(graph_theory)) is also a graph, more specifically _a connected acyclic undirected graph_, we can use graph algorithms such as _[breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search)_ and _[depth-first search](https://en.wikipedia.org/wiki/Depth-first_search)_ to traverse the tree.

Let's take a look at what _breadth-first search (BFS)_ does:
> Breadth-first search (BFS) is an algorithm for traversing or searching tree or graph data structures. It starts at the tree root (or some arbitrary node of a graph, sometimes referred to as a 'search key'[1]), and explores all of the neighbor nodes at the present depth prior to moving on to the nodes at the next depth level.

It seems exactly what we need to help us solve our problem.

Here is the pseudocode for BFS:
```rust
1  procedure BFS(G, root) is
2      let Q be a queue
3      label root as discovered
4      Q.enqueue(root)
5      while Q is not empty do
6          v := Q.dequeue()
7          if v is the goal then
8              return v
9          for all edges from v to w in G.adjacentEdges(v) do
10              if w is not labeled as discovered then
11                  label w as discovered
12                  Q.enqueue(w)
```
* `G` stands for graph
* `root` references the root node in our tree
* `Q` is the queue where vertices will be enqueued.
* `adjacentEdges` references the edges that are connected to our `vertex`. Since we deal with a binary tree, it means that our `vertex` can have at most two `adjacent edges`.

According to BFS, we will need to:
* create a `queue`
* have a way to mark `nodes` as discovered, (keeping track of which nodes were visited, in order to avoid infinite looping in case of a cyclic connected graph - but this does not concern us since our graph is acyclic).
* put the `root` node in the `queue`
* then, for as long as the queue is not empty (for as long as there is at least one element in the queue):
  * pick up the `node` at the front of the queue
  * do something with this `node` (print it, return it, etc ...)
  * then, for all `adjacent edges` of the current `node`:
    * mark each one of these nodes as discovered
    * enqueue them

Python has a [built-in queue](https://docs.python.org/3/library/queue.html) class that we can use to solve our problem.

We use the following definition of a binary tree node:
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

First, let's simply print each node in the level order:
```python
import queue

def level_order(root):
    q = queue.Queue()

    if root:
        q.put(root)

    while q.qsize():
        for _ in range(q.qsize()):
            node = q.get()
            print(node)

            if node.left:
                q.put(node.left)
            if node.right:
                q.put(node.right)
```

We will use the binary tree from the example:
```python
binary_tree = TreeNode(
   3,
   left_child=TreeNode(
       9
   ),
   right_child=TreeNode(
       20,
       left_child=TreeNode(
           15
       ),
       right_child=TreeNode(
           7
       )
   )
)
```

Now if you call our function:
```
level_order(binary_tree)
```

You should see the following output:
```
3
9
20
15
7
```

Great!

Now we just need to put each nodes belonging to the same level into the same array, so we can obtain the output `[[3],[9,20],[15,7]]`. To do this, we can create a `level` variable. Each nodes belonging to the same level will be put in the `level` variable. Then, we will put each `level` into a `result` list.

```python
def level_order(root):
    q = queue.Queue()
    result = []

    if root:
        q.put(root)

    while q.qsize():
        level = []
        for _ in range(q.qsize()):
            node = q.get()
            level.append(node.val)

            if node.left:
                q.put(node.left)
            if node.right:
                q.put(node.right)
        result.append(level)
    return result
```

Now if you re-run the function using the same `binary_tree` as before, you should see the expected return value:
```
[[3],[9,20],[15,7]]
```
