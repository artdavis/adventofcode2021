# Day 9: Smoke Basin

## Part 1

These caves seem to be lava tubes. Parts are even still volcanically active; small hydrothermal vents release smoke into the caves that slowly settles like rain.

If you can model how the smoke flows through the caves, you might be able to avoid it and be that much safer. The submarine generates a heightmap of the floor of the nearby caves for you (your puzzle input).

Smoke flows to the lowest point of the area it's in. For example, consider the following heightmap:
```
2199943210
3987894921
9856789892
8767896789
9899965678
```
Each number corresponds to the height of a particular location, where 9 is the highest and 0 is the lowest a location can be.

Your first goal is to find the low points - the locations that are lower than any of its adjacent locations. Most locations have four adjacent locations (up, down, left, and right); locations on the edge or corner of the map have three or two adjacent locations, respectively. (Diagonal locations do not count as adjacent.)

In the above example, there are four low points, all highlighted: two are in the first row (a 1 and a 0), one is in the third row (a 5), and one is in the bottom row (also a 5). All other locations on the heightmap have some lower adjacent location, and so are not low points.

The risk level of a low point is 1 plus its height. In the above example, the risk levels of the low points are 2, 1, 6, and 6. The sum of the risk levels of all low points in the heightmap is therefore 15.

Find all of the low points on your heightmap. **What is the sum of the risk levels of all low points on your heightmap?**


```python
from IPython.display import Markdown
import numpy as np
from matplotlib import pyplot as plt
from scipy import ndimage
```


```python
#infile = 'test_input.txt'
infile = 'input.txt'

lines = list()
with open(infile, 'r') as fid:
    for line in fid:
        lines.append([int(x) for x in line.strip()])
dat = np.array(lines, dtype=np.int)
#dat
```


```python
# Strategy. Go number by number. Check neighbors.
# If any neighbor is less, this is not a local min
# otherwise it is.
mask = np.zeros_like(dat, dtype=np.bool)
#mask

# Iterate over every element
for i, j in np.ndindex(dat.shape):
    minval = dat[i, j]
    # Check above
    if 0 < i and dat[i-1, j] < minval:
        # This value is not a minima
        #print("ABOVE:",minval, end=',')
        continue
    # Check below
    if dat.shape[0] - 1 > i and dat[i+1, j] < minval:
        # This value is not a minima
        #print("BELOW:",minval, end=',')
        continue
    # Check left
    if 0 < j and dat[i, j-1] < minval:
        # This value is not a minima
        #print("LEFT:",minval, end=',')
        continue
    # Check right
    if dat.shape[1] - 1 > j and dat[i, j+1] < minval:
        #print("RIGHT",minval, end=',')
        continue
    # 9 is NEVER a minima
    if dat[i, j] > 8:
        #print("GOT A 9 at {}, {}".format(i, j))
        continue
    # If we're still here, minval IS a minima
    mask[i, j] = True
    #print("...", dat[i, j], minval)
#display(dat[:20,:20])
#display(mask.astype(np.int)[:20,:20])
```


```python
risks = dat[mask] + 1
#risks
risksum = risks.sum()
#Markdown("Sum of the risk levels is: **{}**".format(risksum))
```

## Part Two

Next, you need to find the largest basins so you know what areas are most important to avoid.

A basin is all locations that eventually flow downward to a single low point. Therefore, every low point has a basin, although some basins are very small. Locations of height 9 do not count as being in any basin, and all other locations will always be part of exactly one basin.

The size of a basin is the number of locations within the basin, including the low point. The example above has four basins.

The top-left basin, size 3:
```
2199943210
3987894921
9856789892
8767896789
9899965678
```
The top-right basin, size 9:
```
2199943210
3987894921
9856789892
8767896789
9899965678
```
The middle basin, size 14:
```
2199943210
3987894921
9856789892
8767896789
9899965678
```
The bottom-right basin, size 9:
```
2199943210
3987894921
9856789892
8767896789
9899965678
```
Find the three largest basins and multiply their sizes together. In the above example, this is 9 * 14 * 9 = 1134.

**What do you get if you multiply together the sizes of the three largest basins?**


```python
# Find basin by taking each low point and finding
# how many points there are bounded by 9's
```


```python
# Lets make some plot's to improve understanding
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(dat, cmap='gray')
```




    <matplotlib.image.AxesImage at 0x1e8e8afac88>




    
![png](README_files/README_7_1.png)
    



```python
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(mask, cmap='gray')
```




    <matplotlib.image.AxesImage at 0x1e8e8de64e0>




    
![png](README_files/README_8_1.png)
    



```python
dat9 = dat == 9
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(dat9, cmap='gray')
```




    <matplotlib.image.AxesImage at 0x1e8e8e42518>




    
![png](README_files/README_9_1.png)
    



```python
mask9 = mask*2 + dat9
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(mask9, cmap='gray')
```




    <matplotlib.image.AxesImage at 0x1e8e91b99e8>




    
![png](README_files/README_10_1.png)
    



```python
# Every boundary with 9's defines a basin with a minima inside
# Create a map where all 9's are zeros and all non-9's are 1's
dat2 = dat != 9
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(dat2, cmap='gray')
```




    <matplotlib.image.AxesImage at 0x1e8e9227da0>




    
![png](README_files/README_11_1.png)
    



```python
# Now label all the features that are non zero to get all basins
labels, nfeatures = ndimage.label(dat2)
```


```python
# nfeatures notably contains the number of minima from part 1
assert(mask.sum() == nfeatures)
```


```python
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(labels, cmap='jet')
```




    <matplotlib.image.AxesImage at 0x1e8e9284f98>




    
![png](README_files/README_14_1.png)
    



```python
# Now just count the labels using a histogram to get the
# basin sizes
count, bins = np.histogram(labels, bins=np.arange(nfeatures+2))
```


```python
itop = np.argpartition(count, -4)
# Exclude count of zeros in top 3 basins
top3 = count[itop[-4:]]
top3 = top3[top3 != count.max()]
```


```python
basinmx = top3[0] * top3[1] * top3[2]
#Markdown("The product of the 3 largest basin sizes is: **{}**".format(basinmx))
```


```python

```
