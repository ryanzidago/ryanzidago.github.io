You can find the problem statement [here](https://leetcode.com/problems/top-k-frequent-words/).

> Given a non-empty list of words, return the k most frequent elements.
> Your answer should be sorted by frequency from highest to lowest. If two words have the same frequency, then the word with the lower alphabetical order comes first.

So we need to sort words by frequency, and then, if numerous words have the same frequency, then we should order those words alphabetically. The hardest part here is the sorting logic.

Let's just go through the problem once and then, we improve on our findings.
First, we will need a hashmap where each key will be the `word` itself and each value will be the `frequency` at which the word appeared in the `words` vector.
To populate the hashmap, we simply iterate over the `words` vector. If the hashmap contains a value for the `word` key, we simply increment this value by one. Otherwise, we insert the key `word` into the hashmap with a frequency of `1`:

```rust
use std::collections::HashMap;

fn main() {
  let words = vec![
            "the".to_string(),
            "day".to_string(),
            "is".to_string(),
            "sunny".to_string(),
            "the".to_string(),
            "the".to_string(),
            "the".to_string(),
            "sunny".to_string(),
            "is".to_string(),
            "is".to_string(),
        ];
  let k = 4;

  top_k_frequent(words, k);
}

fn top_k_frequent(words: Vec<String>, k: i32) {
  let mut map: HashMap<String, usize> = HashMap::new();

  for word in words {
    if let Some(frequency) = map.get_mut(&word) {
      *frequency += 1;
    } else {
      map.insert(word, 1);
    }
  }

  println!("{:#?}", map);

}
```

Running the code gives us the following output:
```
{
    "day": 1,
    "is": 3,
    "the": 4,
    "sunny": 2,
}
```

We successfully counted the number of frequencies per word. Now the tricky part is to sort them. Let's first sort them by frequency, because it is the first criteria from which we should sort the data (remember, first frequency, then alphabetically).

To automatically get the word with the highest frequency, we can use a [max BinaryHeap](https://doc.rust-lang.org/std/collections/struct.BinaryHeap.html). In this heap, we can store a tuple, whose first element is the frequency and second element is the word itself. The heap will only sort each node by the frequency.

```rust
use std::collections::{HashMap, BinaryHeap};

# ...

fn top_k_frequent(words: Vec<String>, k: i32) {
  let mut map: HashMap<String, usize> = HashMap::new();
  let mut heap: BinaryHeap<(usize, String)> = BinaryHeap::new();

  for word in words {
    if let Some(frequency) = map.get_mut(&word) {
      *frequency += 1;
    } else {
      map.insert(word, 1);
    }
  }

  println!("{:#?}", map);

  for (word, frequency) in map {
    heap.push((frequency, word));
  }

  println!("{:#?}", heap);

}
```
The output for the heap looks more or less like this:
```
[
    (
        4,
        "the",
    ),
    (
        2,
        "sunny",
    ),
    (
        3,
        "is",
    ),
    (
        1,
        "day",
    ),
]
```
The most important thing right now is that we have the most frequent node as the root node of the heap.

To finalize our first iteration, we can then pop `k` elements from our `heap`, store them in a `result` vector, and sort them alphabetically. The problem is that in this case `is` will come before `the` because we will disrecard the initial frequency numbers.


To deal with this complexity, we can create a struct `FrequentWord` that holds a `word` as well as a `frequency`. Then, we can implement the `PartialOrd` trait on this struct to cover the sorting logic. If we look at the documentation for the [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html) trait:

> Trait for values that can be compared for a sort-order.

In [_How can I implement `PartialOrd`?_](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html#how-can-i-implement-partialord), the documentation informs us that we only need to implement the `partial_cmp` method:

> PartialOrd only requires implementation of the partial_cmp method, with the others generated from default implementations.

First, we create our struct `FrequentWord`:
```rust
#[derive(Eq, Ord, PartialEq)]
struct FrequentWord {
    frequency: usize,
    word: String,
}
```

Then we implement the `PartialOrd` trait for our `FrequentWord` struct. Basically what we do say here is that if we have an equal ordering for `self` and `other`, then compare `self` and `other` based on their `word` field:
```rust
impl PartialOrd for FrequentWord {
    fn partial_cmp(&self, other: &FrequentWord) -> Option<Ordering> {
        Some(match self.frequency.cmp(&other.frequency) {
            Ordering::Equal => other.word.cmp(&self.word),
            ordering => ordering,
        })
    }
}
```

Instead of pushing a two-element tuple in our heap, we will push our newly created `FrequentWord` struct:
```rust
fn top_k_frequent(words: Vec<String>, k: i32) {
  let mut map: HashMap<String, usize> = HashMap::new();
  let mut heap: BinaryHeap<(usize, String)> = BinaryHeap::new();

  for word in words {
    if let Some(frequency) = map.get_mut(&word) {
      *frequency += 1;
    } else {
      map.insert(word, 1);
    }
  }

  println!("{:#?}", map);

  for (word, frequency) in map {
    let frequent_word = FrequentWord { word, frequency };
    heap.push(frequent_word);
  }

  println!("{:#?}", heap);

}
```

Then we pop as many nodes from our heap as the value `k` and only put the `word` field of our `FrequentWord` struct into a `result` vector and return the result:
```rust
let mut result: Vec<String> = Vec::new();

# ...

for _ in 0..k {
  let word: String = heap.pop().unwrap().word;
  result.push(word);
}

return result;
```

This is the whole code:
```rust
use std::cmp::Ordering;
use std::collections::{BinaryHeap, HashMap};

fn main() {}

#[derive(Eq, Ord, PartialEq)]
struct FrequentWord {
    frequency: usize,
    word: String,
}

impl PartialOrd for FrequentWord {
    fn partial_cmp(&self, other: &FrequentWord) -> Option<Ordering> {
        Some(match self.frequency.cmp(&other.frequency) {
            Ordering::Equal => other.word.cmp(&self.word),
            ordering => ordering,
        })
    }
}

fn top_k_frequent(words: Vec<String>, k: i32) -> Vec<String> {
    let mut map: HashMap<String, usize> = HashMap::new();
    let mut heap: BinaryHeap<FrequentWord> = BinaryHeap::with_capacity(k as usize);
    let mut result: Vec<String> = vec![];

    for word in words {
        if let Some(frequency) = map.get_mut(&word) {
            *frequency += 1;
        } else {
            map.insert(word, 1);
        }
    }

    for (word, frequency) in map {
        let frequent_word = FrequentWord { word, frequency };
        heap.push(frequent_word);
    }

    for _ in 0..k {
        let word: String = heap.pop().unwrap().word;
        result.push(word);
    }

    return result;
}
```

Here is a short test:
```rust
#[cfg(test)]
mod test {
    use super::*;

    #[test]
    fn top_k_frequent_test() {
        let words = vec![
            "the".to_string(),
            "day".to_string(),
            "is".to_string(),
            "sunny".to_string(),
            "the".to_string(),
            "the".to_string(),
            "the".to_string(),
            "sunny".to_string(),
            "is".to_string(),
            "is".to_string(),
        ];
        let k = 4;

        let expected = vec![
            "the".to_string(),
            "is".to_string(),
            "sunny".to_string(),
            "day".to_string(),
        ];

        let output = top_k_frequent(words, k);

        assert_eq!(expected, output);
    }
}
```
