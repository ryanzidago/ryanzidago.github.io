The problem statement can be found [here](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/).

The difference between this problem and the one that we [previously solved](https://ryanzidago.github.io/2021/04/04/solving-the-best-time-to-buy-and-sell-stock-1-problem-in-rust.html) is that here, we can buy and sell more than one stock. We still cannot engage in simultaneous transactions, but we can sell a stock on one day and buy it on the same day.

Let's take the following stock prices:
```
[1, 2, 3, 4, 1, 2]
```

The maximum profit we can make is 4, if we buy the stock at 1, sell at 3, buy again at 1 and sell at 2. Since, from the previous problem, we know that computing the difference between two points i and i + n is the same as (i + 0) + (i + 1) + .. (i + n), then we just need to compute the sums of all subarrays, whose sum is positive. In other words, we will need to compute all transactions for i - (i - 1), and ignore all transactions whose sum is negative.

```rust
use std::cmp;

fn max_profit(prices: Vec<i32>) -> i32 {
  let mut max_sum: i32 = 0;

  for i in 1..prices.len() {
    max_sum += cmp::max(0, prices[i] - prices[i - 1]);
  }

  return max_sum;
}
```

With `max_sum += cmp::max(0, prices[i] - prices[i - 1])`, we ignore all transactions whose result is not positive.

Here are some additional tests to verify our solution:

```rust
#[cfg(test)]
mod test {
    use super::*;

    #[test]
    fn max_profit_00_test() {
        assert_eq!(4, max_profit(vec![1, 2, 3, 4, 1, 2]));
        assert_eq!(7, max_profit(vec![7, 1, 5, 3, 6, 4]));
        assert_eq!(4, max_profit(vec![1, 2, 3, 4, 5]));
        assert_eq!(0, max_profit(vec![7, 6, 4, 3, 1]));
        assert_eq!(3, max_profit(vec![1, 2, 3, 4]));
        assert_eq!(2, max_profit(vec![1, 2, 1, 2]));
    }
}
```
