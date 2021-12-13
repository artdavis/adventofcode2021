# Day 5: Hydrothermal Venture

https://adventofcode.com/2021/day/5

## Part 1

**At how many points do at least two lines overlap?**


```python
import numpy as np
from IPython.display import Markdown
```


```python
test_input = '''0,9 -> 5,9
8,0 -> 0,8
9,4 -> 3,4
2,2 -> 2,1
7,0 -> 7,4
6,4 -> 2,0
0,9 -> 2,9
3,4 -> 1,4
0,0 -> 8,8
5,5 -> 8,2
'''
```


```python
xpts0 = []; ypts0 = []
xpts1 = []; ypts1 = []
with open('input.txt', 'r') as fid:
    for line in fid:
#for line in test_input.split('\n'):
        if '' == line:
            break
        (x0, y0), (x1, y1) = [x.split(',') for x in line.strip().split(' -> ')]
        xpts0 += [int(x0)]
        ypts0 += [int(y0)]
        xpts1 += [int(x1)]
        ypts1 += [int(y1)]
```


```python
xpts0 = np.array(xpts0)
xpts1 = np.array(xpts1)
ypts0 = np.array(ypts0)
ypts1 = np.array(ypts1)

xmin = np.min([xpts0.min(), xpts1.min()])
ymin = np.min([ypts0.min(), ypts1.min()])
xmax = np.max([xpts0.max(), xpts1.max()])
ymax = np.max([ypts0.max(), ypts1.max()])
xmin, xmax, ymin, ymax
```




    (10, 989, 10, 989)




```python
cloudgrid = np.zeros((xmax + 1, ymax + 1))

for i in range(len(xpts0)):
    x0 = np.min([xpts0[i], xpts1[i]])
    x1 = np.max([xpts0[i], xpts1[i]])
    y0 = np.min([ypts0[i], ypts1[i]])
    y1 = np.max([ypts0[i], ypts1[i]])
    if x0 == x1:
        # Vertical line
        #print("Vertical line: ({}, {}), ({}, {})".format(x0, y0, x1, y1))
        cloudgrid[x0, y0:y1 + 1] += 1
    elif y0 == y1:
        # Horizontal line
        #print("Horizontal line: ({}, {}), ({}, {})".format(x0, y0, x1, y1))
        cloudgrid[x0:x1 + 1, y0] += 1

#cloudgrid.T
```


```python
overlap = np.sum(cloudgrid >= 2)
#Markdown("Regions of overlap: **{}**".format(overlap))
```

## Part Two

**At how many points do at least two lines overlap?**


```python
cloudgrid = np.zeros((xmax + 1, ymax + 1))

for i in range(len(xpts0)):
    x0 = xpts0[i]
    x1 = xpts1[i]
    y0 = ypts0[i]
    y1 = ypts1[i]
    if x0 > x1:
        xstep = -1
    else:
        xstep = 1
    if y0 > y1:
        ystep = -1
    else:
        ystep = 1
    if x0 == x1:
        # Vertical line
        #print("Vertical line: ({}, {}), ({}, {})".format(x0, y0, x1, y1))
        cloudgrid[x0, y0:y1 + ystep:ystep] += 1
    elif y0 == y1:
        # Horizontal line
        #print("Horizontal line: ({}, {}), ({}, {})".format(x0, y0, x1, y1))
        cloudgrid[x0:x1 + xstep:xstep, y0] += 1
    else:
        # Diagonal line
        #print("Diagonal line: ({}, {}), ({}, {})".format(x0, y0, x1, y1))
        for x, y in zip(range(x0, x1 + xstep, xstep),
                        range(y0, y1 + ystep, ystep)):
            #print(x,y)
            cloudgrid[x, y] += 1
#cloudgrid.T
```


```python
overlap = np.sum(cloudgrid >= 2)
#Markdown("Regions of overlap: **{}**".format(overlap))
```


```python

```
