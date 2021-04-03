The problem is stated [here](https://www.codewars.com/kata/57b06f90e298a7b53d000a86).

To solve this problem, we can use a min [heap](https://en.wikipedia.org/wiki/Heap_%28data_structure%29).
Each supermarket checkout will be an element in our heap. More specifically, each time that a customer visits a specific checkout, the time it took the customer at the checkout will be added to the checkout's total time.

A customer always go to the _fastest checkout_, i.e the less busy checkout. Since we are using a min heap then, every time we will push a customer to a checkout, the customer will land in this _fastest checkouts_ (the checkout with the smallest total time).

By default, Rust uses a [max heap](https://doc.rust-lang.org/std/collections/struct.BinaryHeap.html). However, the documentation also inform us on how to construct a [min heap](https://doc.rust-lang.org/std/collections/struct.BinaryHeap.html#min-heap) (with the [Reverse](https://doc.rust-lang.org/std/cmp/struct.Reverse.html) struct).

First, let's create the `queue_time` function:

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

fn queue_time(customers: Vec<i32>, n: i32) -> i32 {

}
```

Now, we need to create as many elements in the heap as there are checkouts. So if there is 5 checkouts, then our heap will contains 5 nodes. We will then later add customers' time to those nodes:

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

fn queue_time(customers: Vec<i32>, n: i32) -> i32 {
  let mut checkouts: BinaryHeap<Reverse<i32>> = BinaryHeap::new();

  for _ in 0..n {
    checkouts.push(Reverse(0));
  }
}
```

Let's iterate over every customer's time and add their respective time to the fastest checkout. We do this by:
- popping the root node of the checkouts heap (which is in our case, the node with the smallest value),
- add the customer's time to the checkout's time
- push the new checkout's time (that includes the customer's time) to the min heap (if this checkout's time is not the smallest checkout's time anymore, another node with the smallest value will become the root node)

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

fn queue_time(customers: Vec<i32>, n: i32) -> i32 {
  let mut checkouts: BinaryHeap<Reverse<i32>> = BinaryHeap::new();

  for _ in 0..n {
    checkouts.push(Reverse(0));
  }

  for customer in customers {
    let Reverse(time) = checkouts.pop().unwrap();
    checkouts.push(Reverse(time + customer));
  }
}
```

Then, we need to get the node with the largest value. To do this, we can convert the heap into a sorted vector, and fetch its very first element. This element represents the busiest checkout (the checkout with the largest customer's time):

```rust
fn queue_time(customers: Vec<i32>, n: i32) -> i32 {
    let mut checkouts: BinaryHeap<Reverse<i32>> = BinaryHeap::new();

    for _ in 0..n {
        checkouts.push(Reverse(0));
    }

    for customer in customers {
        let Reverse(time) = checkouts.pop().unwrap();
        checkouts.push(Reverse(time + customer));
    }

    let checkouts = checkouts.into_sorted_vec();
    let Reverse(max_time_in_checkout) = checkouts.first().unwrap();
    *max_time_in_checkout
}
```

Here are some tests:
```rust

#[cfg(test)]
mod test {
    use super::*;

    #[test]
    fn queue_time_test() {
        assert_eq!(0, queue_time(vec![], 1));
        assert_eq!(5, queue_time(vec![5], 1));
        assert_eq!(2, queue_time(vec![2], 5));
        assert_eq!(15, queue_time(vec![1, 2, 3, 4, 5], 1));
        assert_eq!(5, queue_time(vec![1, 2, 3, 4, 5], 100));
        assert_eq!(9, queue_time(vec![2, 2, 3, 3, 4, 4], 2));
        assert_eq!(
            106,
            queue_time(
                vec![45, 25, 2, 5, 46, 4, 50, 27, 34, 22, 12, 9, 29, 7, 1, 8, 49, 37, 22, 18],
                5
            )
        );
    }
}
```

The time complexity for both heap insertion and deletion is `O(log n)`([_Source_](https://forum.devtalk.com/t/a-common-sense-guide-to-data-structures-and-algorithms-second-edition-pragprog/418)).
