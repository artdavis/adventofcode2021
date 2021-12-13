# Day 6: Lanternfish

https://adventofcode.com/2021/day/6

## Part 1

**How many lanternfish would there be after 80 days?**


```python
import numpy as np
from IPython.display import Markdown
```


```python
infile = 'input.txt'
ndays = 80
#infile = 'test_input.txt'
#ndays = 18
with open(infile, 'r') as fid:
    # Lanternfish initial condition:
    f0 = [int(x) for x in fid.readline().strip().split(',')]
# Each element of f0 represents a fish and its internal timer value
f0 = np.array(f0, dtype=np.uint64)
#f0
```


```python
def getfish(ndays):
    # Generate the lanternfish count histogram for initial condition
    hist, bins = np.histogram(f0, bins=range(8))
    #hist, bins = np.histogram([0,1,1,2,3,4,5,6,6,6], bins=range(8))
    # The INDEX of the hist list represents the fish countdown timer
    # value and the VALUE represents the number of fish at that countdown
    # value. So for example if hist = [0, 1, 1, 2, 1, 0, 0], the
    # number of fish at 0 timer value is hist[0]=0, the number of
    # fish at 1 timer value is hist[1]=1 ... etc.
    #hist, bins

    # Each day every fish's timer value ticks down one.
    # This can be represented by simply shifting hist left
    # by one. The fishes at index 2 go to index 1, index 1
    # goes to 0 and index zero wraps around to index 6 as their
    # timer resets

    # Get the number of Lanternfish after ndays
    # Initialize the new fishes array [7count, 8count, NEWFISH]
    histnew = np.zeros(3, dtype=np.uint64)
    for i in range(ndays):
        hist = np.roll(hist, -1)
        # Add the number of 7 count fishes to the number of 6 count fishes
        hist[6] += histnew[0]
        # The number of 8 count fishes become the number of 7 count fishes
        histnew[0] = histnew[1]
        # The number of new fishes become the number of 8 count fishes
        histnew[1] = histnew[2]
        # The number of 0 count fishes becomes the number of new fishes
        histnew[2] = hist[0]
        #print("Day {}: {}, {}".format(i+1, hist, histnew))

    # Sum to get the total number of fishes
    totalfish = hist.sum() + histnew[:2].sum()
    return totalfish

totalfish = getfish(ndays)
#Markdown("After {} days there are **{}** total fishes.".format(ndays, totalfish))
```

## Part Two

**How many lanternfish would there be after 256 days?**


```python
ndays2 = 256
totalfish2 = getfish(ndays2)
#Markdown("After {} days there are **{}** total fishes.".format(ndays2, totalfish2))
```


```python

```
