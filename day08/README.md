# Day 8: Seven Segment Search

https://adventofcode.com/2021/day/8

## Part 1

**In the output values, how many times do digits 1, 4, 7, or 8 appear?**


```python
from IPython.display import Markdown
import numpy as np
```


```python
#infile = 'test_input.txt'
infile = 'input.txt'
dat_nums = list()
dat_disp = list()
with open(infile, 'r') as fid:
    for line in fid:
        nums, disp = line.strip().split('|')
        dat_nums.append(nums.split())
        dat_disp.append(disp.split())
#display(dat_nums, dat_disp)
```


```python
# Convert output values to sets and get length to determine
# how many segments per digit are called for
dat1_nsegs = np.array([[len(set(y)) for y in x] for x in dat_disp])
#dat1_nsegs
```


```python
# 1 uses 2 segments
# 4 uses 4 segments
# 7 uses 3 segments
# 8 uses 7 segments
ones = dat1_nsegs == 2
fours = dat1_nsegs == 4
sevens = dat1_nsegs == 3
eights = dat1_nsegs == 7
uniques = ones.sum() + fours.sum() + sevens.sum() + eights.sum()
#Markdown('The digits 1, 4, 7 or 8 appear: **{}** times'.format(uniques))
```

## Part Two

**What do you get if you add up all of the output values?**


```python
outlist = list()
#nums = dat_nums[0]
#disp = dat_disp[0]
for nums, disp in zip(dat_nums, dat_disp):
    seglens = list(map(lambda x: len(x), nums))
    ones = nums[seglens.index(2)]
    fours = nums[seglens.index(4)]
    sevens = nums[seglens.index(3)]
    eights = nums[seglens.index(7)]
    #ones, fours, sevens, eights

    a_val = (set(sevens) - set(ones)).pop()
    #a_val

    # 2,3,5 displays have 5 segments
    cands_235 = [nums[i] for i, x in enumerate(seglens) if x == 5]
    #cands_235

    # If we subtract out the segments that make the 4 display
    # the set with 3 left over is the two display
    twos = cands_235[[len(set(x) - set(fours)) for x in cands_235].index(3)]
    #twos

    # Subtract out the fours and the other 3,5 candidates
    # to get the unique e_val
    cands_35 = list(set(cands_235) - {twos})
    #cands_35

    e_val = set(twos)
    for d in set(cands_35) | {fours}:
        e_val -= set(d)
    e_val = e_val.pop()
    #e_val

    # Subtracting off the segments for 2 of the 3,5 displays
    # will yield 2 for the 5 display and 1 for the 3 display
    threes = cands_35[[len(set(x) - set(twos)) for x in cands_35].index(1)]
    fives = cands_35[[len(set(x) - set(twos)) for x in cands_35].index(2)]
    #threes, fives

    # The c segment is unique to 3 vs 5
    c_val = (set(threes) - set(fives)).pop()
    #c_val

    # The b segment is unique to 5 vs 3
    b_val = (set(fives) - set(threes)).pop()
    #b_val

    # The f segment is what is not the c segment of 1
    f_val = (set(ones) - {c_val}).pop()
    #f_val

    # The d segment is what is not b,c,f of 4
    d_val = (set(fours) - set([b_val, c_val, f_val])).pop()
    #d_val

    # The g segment is what is not a,c,d,e of 2
    g_val = (set(twos) - set([a_val, c_val, d_val, e_val])).pop()
    #g_val

    # All segments are identified, just need to decode
    # digits 0, 6, 9
    # Zero has a,b,c,e,f,g
    # Use '^' operator for letters in both sets but not both
    # The empty set then is the match for that digit
    zeros = nums[[len(set(x) ^ set(a_val + b_val + c_val + e_val + f_val + g_val))
     for x in nums].index(0)]
    #zeros

    # Six has a,b,d,e,f,g
    sixes = nums[[len(set(x) ^ set(a_val + b_val + d_val + e_val + f_val + g_val))
     for x in nums].index(0)]
    #sixes

    # Nine has a,b,c,d,f,g
    nines = nums[[len(set(x) ^ set(a_val + b_val + c_val + d_val + f_val + g_val))
     for x in nums].index(0)]
    #nines

    codes = [zeros, ones, twos, threes, fours,
             fives, sixes, sevens, eights, nines]
    nums_decode = [nums.index(x) for x in codes]
    #nums_decode

    def get_digit(readout):
        for i, s in enumerate(codes):
            if len(set(s) ^ set(readout)) == 0:
                return str(i)
            
    output = int(''.join([get_digit(x) for x in disp]))
    outlist.append(output)
#display(outlist)
```


```python
outsum = sum(outlist)
#Markdown("The sum of the output values is: **{}**".format(outsum))
```


```python

```
