# Day 1: Sonar Sweep

https://adventofcode.com/2021/day/1

## Part 1

**How many measurements are larger than the previous measurement?**


```python
import numpy as np
from IPython.display import Markdown
```


```python
dat = np.loadtxt('input.txt')
nlarger = np.sum(np.diff(dat) > 0)
```


```python
#display(Markdown("**{}** measurements are larger than the previous measurement.".format(nlarger)))
```

## Part Two

Consider sums of a three-measurement sliding window. **How many sums are larger than the previous sum?**


```python
# Roll the data to provide a 3 long window
dat1 = np.roll(dat, -1)
dat2 = np.roll(dat, -2)

# Sum up and ignore last 2 points since invalid
datsum = (dat + dat1 + dat2)[:-2]
nlarger3 = np.sum(np.diff(datsum) > 0)
```


```python
#display(Markdown("**{}** sums are larger than the previous sum.".format(nlarger3)))
```


```python

```
