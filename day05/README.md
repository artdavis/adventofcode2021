# Day 5: Hydrothermal Venture

## Part 1

You come across a field of hydrothermal vents on the ocean floor! These vents constantly produce large, opaque clouds, so it would be best to avoid them if possible.

They tend to form in lines; the submarine helpfully produces a list of nearby lines of vents (your puzzle input) for you to review. For example:
```
0,9 -> 5,9
8,0 -> 0,8
9,4 -> 3,4
2,2 -> 2,1
7,0 -> 7,4
6,4 -> 2,0
0,9 -> 2,9
3,4 -> 1,4
0,0 -> 8,8
5,5 -> 8,2
```
Each line of vents is given as a line segment in the format x1,y1 -> x2,y2 where x1,y1 are the coordinates of one end the line segment and x2,y2 are the coordinates of the other end. These line segments include the points at both ends. In other words:

* An entry like 1,1 -> 1,3 covers points 1,1, 1,2, and 1,3.
* An entry like 9,7 -> 7,7 covers points 9,7, 8,7, and 7,7.

For now, only consider horizontal and vertical lines: lines where either x1 = x2 or y1 = y2.

So, the horizontal and vertical lines from the above list would produce the following diagram:
```
.......1..
..1....1..
..1....1..
.......1..
.112111211
..........
..........
..........
..........
222111....
```
In this diagram, the top left corner is 0,0 and the bottom right corner is 9,9. Each position is shown as the number of lines which cover that point or . if no line covers that point. The top-left pair of 1s, for example, comes from 2,2 -> 2,1; the very bottom row is formed by the overlapping lines 0,9 -> 5,9 and 0,9 -> 2,9.

To avoid the most dangerous areas, you need to determine the number of points where at least two lines overlap. In the above example, this is anywhere in the diagram with a 2 or larger - a total of 5 points.

Consider only horizontal and vertical lines. **At how many points do at least two lines overlap?**


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

Unfortunately, considering only horizontal and vertical lines doesn't give you the full picture; you need to also consider diagonal lines.

Because of the limits of the hydrothermal vent mapping system, the lines in your list will only ever be horizontal, vertical, or a diagonal line at exactly 45 degrees. In other words:

* An entry like 1,1 -> 3,3 covers points 1,1, 2,2, and 3,3.
* An entry like 9,7 -> 7,9 covers points 9,7, 8,8, and 7,9.

Considering all lines from the above example would now produce the following diagram:
```
1.1....11.
.111...2..
..2.1.111.
...1.2.2..
.112313211
...1.2....
..1...1...
.1.....1..
1.......1.
222111....
```
You still need to determine the number of points where at least two lines overlap. In the above example, this is still anywhere in the diagram with a 2 or larger - now a total of 12 points.

Consider all of the lines. **At how many points do at least two lines overlap?**


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
