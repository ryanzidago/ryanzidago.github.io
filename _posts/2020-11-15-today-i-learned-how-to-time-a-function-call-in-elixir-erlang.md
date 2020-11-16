---
layout: post
title:  "Today I learned how to time a function call in Elixir/Erlang"
date:   2020-11-15 16:56:00 +0100
categories: elixir
---

```elixir
iex(1)> :timer.tc(fn -> :timer.sleep(100) end)
{100641, :ok}
iex(2)> :timer.tc(:timer, :sleep, 100)
{100641, :ok}
```

[:timer.tc](https://erlang.org/doc/man/timer.html#tc-1) returns a two-element tuple. The first element being the time it took to execute the function (in microseconds), the second element being the return value of the function itself.

Now I can use the `:timer.tc` instead of doing this:
```elixir
start = :erlang.system_time(:micro_seconds)
# some function
finish = :erlang.system_time(:micro_seconds)

time = finish - start
```
