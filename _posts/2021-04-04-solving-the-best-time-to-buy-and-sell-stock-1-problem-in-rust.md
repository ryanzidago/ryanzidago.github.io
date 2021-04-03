You can find the problem [here](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/).

Let's try to find a solution for a very simple example:
```
[6, 1, 2, 3, 4, 2, 1]
```

Here, when we buy at 1 and sell at 4, we can make a profit of 3, which is the maximum profit for the given array.

For a given day, `i`, we will compute the difference between the selling price of the stock on this day and the buying price of this stock on the day before, so `prices[i] - price[i - 1]`.

```rust
fn main() {
  let prices: Vec<i32> = vec![6, 1, 2, 3, 4, 2, 1];
  max_profit(prices);
}

fn max_profit(prices: Vec<i32>) {
  for i in 1..prices.len() {
    let stock_value_on_selling_day = prices[i];
    let stock_value_on_buying_day = prices[i - 1];
    println!("i - (i - 1) = {}", stock_value_on_selling_day - stock_value_on_buying_day);
  }
}
```

Now run the program and you would see the following output:
```
$ cargo run
i - (i - 1) = -5
i - (i - 1) = 1
i - (i - 1) = 1
i - (i - 1) = 1
i - (i - 1) = -2
i - (i - 1) = -1
```

A known pattern starts to emerge. We can see here that the maximum profit 3 can be achieved by computing the sum of the maximum subarray! This is a problem that we learned to solve [here](2021/04/03/solving-the-maximum-subarray-problem-in-rust.html), with [Kadane's algorithm](https://en.wikipedia.org/wiki/Maximum_subarray_problem#Kadane's_algorithm). The difference here is that the `current_profit` __cannot be negative, because we do not want to sell at lost__, so we will need to reset the `current_profit` to `0` if we ever reach in the negative, to simulate the fact that we ignore this transaction.

```rust
use std::cmp;

fn main() {
  let prices: Vec<i32> = vec![6, 1, 2, 3, 4, 2, 1];
  max_profit(prices);
}

fn max_profit(prices: Vec<i32>) {
  let mut current_profit: i32 = 0;

  for i in 1..prices.len() {
    current_profit += prices[i] - prices[i - 1];
    current_profit = cmp::max(0, current_profit);
    println!("current_profit = {}", current_profit);
  }
}
```

If you run the code above, you should see the following output:
```bash
$ cargo run
current_profit = 0
current_profit = 1
current_profit = 2
current_profit = 3
current_profit = 1
current_profit = 0
```

Now we already know how to keep track of the maximum ever encountered sum. Let's create a `maximum_profit` variable. The `maximum_profit` will be equal to whatever is the maximum between the `maximum_profit` and the `current_profit`. In other words, if the `current_profit` is greater than the `maximum_profit`, then the `maximum_profit` will be equal to the `current_profit`. Otherwise, the `maximum_profit` will stay the same.

```rust
use std::cmp;

fn main() {
  let prices: Vec<i32> = vec![6, 1, 2, 3, 4, 2, 1];
  println!("{}", max_profit(prices));
}

fn max_profit(prices: Vec<i32>) -> i32 {
  let mut current_profit: i32 = 0;
  let mut maximum_profit: i32 = 0;

  for i in 1..prices.len() {
    current_profit += prices[i] - prices[i - 1];
    current_profit = cmp::max(0, current_profit);
    maximum_profit = cmp::max(maximum_profit, current_profit);
  }
  return maximum_profit;
}
```

Here are some tests, to make sure that we have found the solution:
```rust
#[cfg(test)]
mod test {
    use super::*;

    #[test]
    fn max_profit_test() {
        assert_eq!(3, max_profit(vec![6, 1, 2, 3, 4, 2, 1]));
        assert_eq!(5, max_profit(vec![7, 1, 5, 3, 6, 4]));
        assert_eq!(1, max_profit(vec![1, 2]));
        assert_eq!(0, max_profit(vec![5, 4, 3, 2, 1]));
        assert_eq!(3, max_profit(vec![1, 2, 3, 4]));
        assert_eq!(1, max_profit(vec![1, 2, 1, 2]));
        assert_eq!(3, max_profit(vec![1, 2, 3, 4, 3, 2]));
    }
}
```
