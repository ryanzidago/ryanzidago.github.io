# What is a tensor?

A tensor is an array-like data structure that comes with some *super powers*.

A tensor with 0 dimension (basically an integer or a float) is called a scalar:
```python
1
```

A tensor with 1 dimension is called a vector:
```python
[1, 2, 3]
```

A tensor with 2 dimensions is called a matrix:
```python
[
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9], 
]
```

A tensor with 3+ dimensions is called a tensor:
```python
[
    [
        [1, 2, 3],
        [4, 5, 6],
    ],
    [
        [7, 8, 9],
        [10, 11, 12],
    ],
]
```

Not that a tensor must be *balanced*: each subcomponents of the tensor must be of the same length. The following ins't a valid tensor:
```python
[
    [1, 2, 3],
    4,
    [5, 6, 7, 8, 9],
]
```

A tensor is defined by it's *number of dimensions*, *shape* and *length*:
- __number of dimensions__: to know how many dimensions a tensor has, ask yourself how many indexes are needed for accessing an element of the tensor:

```python
100
# 0 index needed 

[1, 2, 3]
# 1 index needed 

[
    [1, 2, 3],
]
# two indexes needed

[
    [
        [1, 2, 3]
    ],
]
# three indexes needed 
# and so on
```

- __shape__: the shape of a tensor is the length of each dimension:

```python
0
#

[1, 2, 3]
# (3)

[
    [1, 2, 3]
]
# (1, 3)

[
    [
        [1, 2, 3]
    ]
]
# (1, 1, 3)

[
    [
        [1, 2, 3],
        [4, 5, 6],
    ]
]
# (1, 2, 3)

[
    [1],
    [2],
    [3],
]
# (3, 1)
```

- __length__: the length of the tensor is the number of element within the tensor:

```python
[1, 2, 3]
# 3

[
    [1, 2, 3]
]
# 3

[
    [1],
    [2],
    [3],
]
# 3

[
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
# 9
```