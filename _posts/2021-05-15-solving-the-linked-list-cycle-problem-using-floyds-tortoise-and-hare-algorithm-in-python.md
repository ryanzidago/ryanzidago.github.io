The problem statement can be found [here](https://leetcode.com/problems/linked-list-cycle/).

To solve this problem, we can use [Floyd's tortoise and hare algorithm](https://en.wikipedia.org/wiki/Cycle_detection#Floyd's_tortoise_and_hare). As noted from Wikipedia:
> Floyd's cycle-finding algorithm is a pointer algorithm that uses only two pointers, which move through the sequence at different speeds. It is also called the "tortoise and the hare algorithm", alluding to Aesop's fable of The Tortoise and the Hare.

> The algorithm thus maintains two pointers into the given sequence, one (the tortoise) at xi, and the other (the hare) at x2i. At each step of the algorithm, it increases i by one, moving the tortoise one step forward and the hare two steps forward in the sequence, and then compares the sequence values at these two pointers.

So for the following linked list containing a cycle where -4 points to 2:
```
3 -> 2 -> 0 -> -4
    ^-----------|
```

We can create two pointers, one `tortoise`, which will be the slow pointer moving only one step forward, and another one `hare`, which will be the fast pointer moving two steps at a time. If the linked list contains a cycle, then the `tortoise` and the `hare` pointers will reach the same node at some point.


```python
def has_cycle(head):
    if head is None:
        return false

    tortoise = head
    hare = head

    while hare.next is not None and hare.next.next is not None:
      tortoise = tortoise.next
      hare = hare.next.next
      if tortoise == hare:
        return True

    return False
```

So at first we have:
```
tortoise = 3
hare = 3
```

Then we iterate for as long as `hare` has a next node and `hare`'s next node also has a next node.

Then we move `tortoise` one step forward and `hare` to steps forward:
```
tortoise = 2
hare = 0
```

Since `tortoise` != `hare`, we keep on incrementing both pointers at their respective rate:
```
tortoise = 0
hare = 2
```

`tortoise` and `hare` aren't yet equals, so we keep going on:
```
tortoise = -4
hare = -4
```

And now we reached the point where `tortoise` == `hare`.

Notice that if there were no cycle in the linked list, we would have break out of the loop because of the condition in the while statement `while hare.next is not None and hare.next.next is not None:`
