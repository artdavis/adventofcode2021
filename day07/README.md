# Day 7: The Treachery of Whales

https://adventofcode.com/2021/day/7

## Part 1

**How much fuel must they spend to align to that position?**


```python
import numpy as np
from IPython.display import Markdown
```

The problem is not easily reduced because the movement distance can be positive
or negative. If the movement distance was assured positive then the following
would hold true:
$$
|x_1 - d_0| + |x_2 - d_0| + |x_3 - d_0| + ... + |x_n - d_0| = n\cdot d_0\sum_{i=1}^{n} x_i
$$

But they're not, so it won't.
Go ahead and power through with the manual approach for Part 1... expecting that we'll
have to come up with something more clever still for Part 2.


```python
#infile = 'test_input.txt'
infile = 'input.txt'
pos = np.loadtxt(infile, delimiter=',', dtype=np.int)
#pos
```


```python
pmin = pos.min()
pmax = pos.max()
locs = np.arange(pmin, pmax+1, dtype=np.int)
#locs
```


```python
def get_dist(loc):
    return np.abs(pos - loc).sum()
```


```python
dists = np.array(list(map(get_dist, locs)), dtype=np.int)
#dists
```


```python
minfuel = dists.min()
#Markdown("The minimum fuel spent to align is: **{}**".format(minfuel))
```

## Part Two

**How much fuel must they spend to align to that position?**


```python
def sum_to_one(val):
    return np.arange(val + 1, dtype=np.int).sum()

def get_fuel(loc):
    # Get the distances
    dists = np.abs(pos - loc)
    # Each element needs to be summed from its value down to
    # 1 to get the fuel consumption
    return np.sum(list(map(sum_to_one, dists)), dtype=np.int)

#get_fuel(2)
```


```python
fuel = np.array(list(map(get_fuel, locs)), dtype=np.int)
#fuel
```


```python
minfuel2 = fuel.min()
#Markdown("The minimum fuel spent to align is: **{}**".format(minfuel2))
```


```python

```
