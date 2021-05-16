The problem is stated [here](https://leetcode.com/problems/swapping-nodes-in-a-linked-list/).

We'll explore two approach for solving this problem. The first approach uses a map to keep track of the visited notes as well as their respective positions. The second approach uses the two-pointers technique.

# 1. With a map

Let's start with the first approach.
We will have a map `nodes_with_pos` which will have an inter `position` as a key (representing the position of the node in the 1-indexed linked list), and a `node` as a value. The first node `head` is saved into a `current_node` variable.
```python
def swapNodes(head, k):
    nodes _with_pos = {}
    pos = 0
    current_node = head
```

Then, for as long as the `current_node` is not `None`, we will iterate over the list. While iterating, we will update the `nodes_with_pos` map with a given `pos` and a `node`. So the first node will have position 1, second node position 2 and so on.


```python
while current_node:
    pos += 1
    nodes_with_pos[pos] = current_node
    current_node = current_node.next
```

Once we're done with it, we have a map with each nodes as well as their relative position in the linked list!

Now, we need to get the `kth_node_from_beginning` of the linked list as well as the `kth_node_from_end`. Getting the kth node from beginning is easy, we just need to look into our map for a node at position `k`.
```python
kth_node_from_beginning = nodes_with_pos[k]
```

Getting the `kth_node_from_end` is a little bit trickier. We know that the last node is at position `pos` because the last time that `pos` was update was during the last iteration of our `while` loop (when we actually visited the last node).

So the node at position `kth` from the end where `kth` is 2 in a linked list `1 -> 2 -> 3 -> 4 -> 5` will be `pos - (k - 1)`, so `4` in our case:
```python
kth_node_from_end = nodes_with_pos[pos - (k - 1)]
```

Once we have found the two nodes, we can simply swap their values:
```python
kth_node_from_beginning.val, kth_node_from_end.val = kth_node_from_end.val, kth_node_from_beginning.val
```

Finally, the function expects us to return the head of the linked list (this is why we assigned the `head` to `current_node` in the first place, so that we can freely mutate `current_node` without losing track of the head of the linked list):
```python
return head
```

Here's the whole code for the first approach:
```python
def swapNodes(head, k):
    nodes_with_pos = {}
    pos = 0
    current_node = head

    while current_node:
        pos += 1
        nodes_with_pos[pos] = current_node
        current_node = current_node.next

    kth_node_from_beginning = nodes_with_pos[k]
    kth_node_from_end = nodes_with_pos[pos - (k - 1)]

    kth_node_from_beginning.val, kth_node_from_end.val = kth_node_from_end.val, kth_node_from_beginning.val
    return head
```

This approach is solves the problem by going only one time through the entire linked list. It's great but we need to allocate additional memory for storing all the visited nodes.

# 2. With two pointers

To avoid to store the entire list inside a map, one can use the _two pointers approach_.

- We will have two pointers `slow` and `fast`.
- `fast` will first find the kth node from the beginning of the list.
- Then, we will iterate through the list for as long as `fast` has a next node.
- While iterating, we will both move `slow` and `fast` one step forward.
- at the end of the iteration, `slow` will already be pointing at kth node from the end!

- So for the linked list `1 (slow, fast) -> 2 -> 3 -> 4 -> 5`:
- We move fast to two: `1 (slow) -> 2 (fast) -> 3 -> 4 -> 5`.
- Then, we traverse the list until `fast` has no next node. While traversing, we update both `fast` and `slow` from one step forward:
  - `1 -> 2 (slow) -> 3 (fast) -> 4 -> 5`
  - `1 -> 2 -> 3 (slow) -> 4 (fast) -> 5`
  - `1 -> 2 -> 3 -> 4 (slow) -> 5 (fast)`

Here's the complete solution:
```python
def swapNodes(head, k):
    slow = head
    fast = head

    for _ in range(k - 1):
      fast = fast.next

    kth_node_from_beginning = fast

    while fast.next:
      slow = slow.next
      fast = fast.next

    kth_node_from_end = slow

    kth_node_from_beginning.val, kth_node_from_end.val = kth_node_from_end.val, kth_node_from_beginning.val
    return head
```
