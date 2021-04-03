The problem is stated [here](https://leetcode.com/problems/maximum-subarray/).

Let's try to find the maximum subarray with a simple example:

```
[1, 2, -5, 2]
```

Here, we can see that `[1, 2]` is the maximum subarray because `(1 + 2) > (2 + -5)` and `(1 + 2) > (-5 + 2)`. This example is suitable to approach the problem because here the sum of the maximum subarray is not the sum of the whole array and the array itself contains few elements.

While iterating over the array, let's see if we can identify the current sum of the subarray, regardless if it is the best sum or not. We will consider the first element the current sum, and iterate over all elements of the array exept the first one.  

```rust
use std::cmp;

fn main() {
  let nums: Vec<i32> = vec![1, 2, -5, 2]
  maximum_subarray(nums);
}

fn maximum_subarray(nums: Vec<i32>) {
  let mut current_sum: i32 = nums[0];

  for num in &nums[1..] {
    current_sum = cmp::max(*num, current_sum + *num);
    println!("current_sum = {}", current_sum);
  }
}
```

After running the program, we can see the following output:
```bash
$ cargo run
current_sum = 3
current_sum = -2
current_sum = 2
```

Great! In the array `[1, 2, -5, 2]`, we've identified the sum of the maximum subarray (`[1, 2]`), which is 3. How can we keep track of the best sum, while iterating over the array?

We can create another variable, `max_sum`. We will then compare the `max_sum` with the `current_sum`. If the `max_sum` is smaller than the `current_sum`, then the `max_sum` will be equal to the `current_sum`, otherwise, the `max_sum` will retain its value.

```rust
fn maximum_subarray(nums: Vec<i32>) -> i32 {
  let mut current_sum: i32 = nums[0];
  let mut max_sum: i32 = nums[0];

  for num in &nums[1..] {
    current_sum = cmp::max(*num, current_sum + *num);
    max_sum = cmp::max(max_sum, current_sum);
  }

  return max_sum;
}
```

If you run the above code on the same array input as before (`[1, 2, -5, 2]`), you will get 3, which is effectively the sum of the maximum subarray.

Here are some test:
```rust
#[cfg(test)]
mod test {
    use super::*;

    #[test]
    fn maximum_subarray_test() {
        assert_eq!(6, maximum_subarray(vec![-2, 1, -3, 4, -1, 2, 1, -5, 4]));
        assert_eq!(1, maximum_subarray(vec![1]));
        assert_eq!(23, maximum_subarray(vec![5, 4, -1, 7, 8]));
        assert_eq!(1, maximum_subarray(vec![-2, 1]));
        assert_eq!(3, maximum_subarray(vec![1, 2, -5, 2]));
    }
}
```

This algorithm is basically [Kadane's algorithm](https://en.wikipedia.org/wiki/Maximum_subarray_problem).
