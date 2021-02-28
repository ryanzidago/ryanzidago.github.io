This is a solution that I have seen from the Elixir track of Exercism.
To make the following test pass:
```elixir
test "more keys" do
  old = %{1 => ["APPLE", "ARTICHOKE"], 2 => ["BOAT", "BALLERINA"]}

  expected = %{
    "apple" => 1,
    "artichoke" => 1,
    "boat" => 2,
    "ballerina" => 2
  }

  assert ETL.transform(old) == expected
end
```

one can use a list comprehension with multiple generators:
```elixir
defmodule ETL do
  def transform(input) do
    for {score, words} <- input, word <- words, into: %{}, do: {String.downcase(words), score}
  end
end
```

Step by step:
```elixir
for {score, words} <- input,
```
For each key-value pair in the map, give me the key, and its corresponding value.

```elixir
word <- words,
```
Then, since each value is a list, we can also extract each element of the list.

```elixir
into: %{},
```
Put the whole result in a map.

```elixir
do: {String,downcase(word), score}
```
Create a new map with the key being the downcased word and the value being the score.
