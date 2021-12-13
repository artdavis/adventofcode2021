# Day 13: Transparent Origami

https://adventofcode.com/2021/day/13

## Part 1

**How many dots are visible after completing just the first fold instruction on your transparent paper?**


```python
from IPython.display import Markdown
import numpy as np
from matplotlib import pyplot as plt
```


```python
#infile = 'test_input.txt'
infile = 'input.txt'

ijlist = list()
foldcmds = list()
fbool = False # Flag indicating time to read fold commands
with open(infile, 'r') as fid:
    for line in fid:
        lstr = line.strip()
        if '' == lstr:
            fbool = True
            continue
        if not fbool:
            j, i = lstr.split(',')
            ijlist.append((int(i), int(j)))
        else:
            # Isolate x= or y= cmd
            eq = lstr.rpartition(' ')[-1].split('=')
            if 'x' == eq[0]:
                # Vertical line at j = eq[1]
                foldcmds.append((0, int(eq[1])))
            elif 'y' == eq[0]:
                # Horizontal line a i = eq[1]
                foldcmds.append((int(eq[1]), 0))
            else:
                raise ValueError("Unhandled eq: {}".format(eq))
ijlist = np.array(ijlist, dtype=np.int)
foldcmds = np.array(foldcmds, dtype=np.int)
#display(ijlist)
#display(foldcmds)
```


```python
def makeplot(dat, cmap='gray'):
    fig, ax = plt.subplots(figsize=(10, 10))
    ax.imshow(dat, cmap=cmap)

```


```python
# Create array from bounds of ijlist
dat = np.zeros((ijlist[:,0].max() + 1, ijlist[:,1].max() + 1), dtype=np.bool)
# Turn on dots
for i, j in ijlist:
    dat[i, j] = True
#makeplot(dat)
```


```python
# Run the folds
dat1 = dat.copy()
visdotlist = list()
for i, j in foldcmds:
    if 0 == i:
        # Vertical line at j
        # The new data area is:
        dat2 = dat1[:, :j]
        # Fold data:
        fdat = dat1[:, j+1:]
        # Flip fdat left-right to get its reflection
        fdat = np.fliplr(fdat)
    elif 0 == j:
        # Horizontal line at i
        dat2 = dat1[:i, :]
        # Fold data
        fdat = dat1[i+1:, :]
        # Flip fdat up-down to get its reflection
        fdat = np.flipud(fdat)
    # Merge folded data with data
    dat1 = np.logical_or(dat2, fdat)
    # Count vidible dots
    visdotlist.append(np.sum(dat1))
#makeplot(dat1)
```


```python
#Markdown("After first fold instruction **{}** dots are visible".format(visdotlist[0]))
```

## Part Two

**What code do you use to activate the infrared thermal imaging camera system?**


```python
#makeplot(dat1)
```


```python

```
