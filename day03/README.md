# Binary Diagnostic

https://adventofcode.com/2021/day/3

## Part 1

**What is the power consumption of the submarine?**


```python
from IPython.display import Markdown
import numpy as np
```


```python
test_input = [
    '00100',
    '11110',
    '10110',
    '10111',
    '10101',
    '01111',
    '00111',
    '11100',
    '10000',
    '11001',
    '00010',
    '01010']
```


```python
bitsum = np.zeros(12, dtype=np.int)

nlines = 0
with open('input.txt', 'r') as fid:
    for lnum, line in enumerate(fid):
        bitstr = line.strip()
        bitarr = np.array([int(x) for x in line.strip()], dtype=np.int)
        bitsum += bitarr
nlines = lnum + 1
```


```python
# Wherever bitsum exceeds nlines // 2 there are more 1's than zeros
gamma_bin = ''.join([str(x) for x in np.array(bitsum > nlines // 2, dtype=np.int).tolist()])
gamma = int(gamma_bin, base=2)
#display(gamma)

# For the least common bit we just need to invert gamma_bin
epsilon_bin = ''.join([str(y) for y in map(lambda x: int(not(int(x))), gamma_bin)])
epsilon = int(epsilon_bin, base=2)
#display(epsilon)

#Markdown("The power consumption is: **{}**".format(gamma * epsilon))
```

## Part Two

**What is the life support rating of the submarine?**


```python
# Get the data all into memory in a list for easier processing
dat = list()
with open('input.txt', 'r') as fid:
    for line in fid:
        dat.append(line.strip())
#dat
```


```python
def get_common(strlist, bitpos, mode):
    # For the supplied list of bit strings, return the
    # 'most' or 'least' common bit at bitpos depending on the mode
    bitsum = 0
    nlines = len(strlist)
    for bitstr in strlist:
        #print(bitstr[bitpos])
        bitsum += int(bitstr[bitpos])
    #print('bitsum: {}; nlines / 2.: {}'.format(bitsum, nlines / 2.))
    if -1e-5 < bitsum - nlines / 2.:
        # 1 is most common or 0's and 1's are equally common
        if 'most' == mode:
            #print('1 is most common')
            return 1
        elif 'least' == mode:
            #print('0 is least common')
            return 0
        else:
            raise ValueError("Unhandled mode: {}",format(mode))
    else:
        # 0 is most common
        if 'most' == mode:
            #print('0 is most common')
            return 0
        elif 'least' == mode:
            #print('1 is least common')
            return 1
        else:
            raise ValueError("Unhandled mode: {}",format(mode))


def filter_for(bitlist, bitval, bitpos):
    # Return list for bitstrs of bitlist with bitval at bitpos
    return [x for x in bitlist if str(x[bitpos]) == str(bitval)]

def get_rating(bitlist, mode):
    # Return rating of bitlist for mode of 'most' or 'least'
    niters = len(bitlist[0])
    for i in range(niters):
        bitval = get_common(bitlist, i, mode=mode)
        #print("{}: {}".format(bitval, bitlist))
        bitlist = filter_for(bitlist, bitval, i)
        if 2 > len(bitlist):
            break
    #bitlist
    rating = int(bitlist[0], base=2)
    return rating
```


```python
#get_common(test_input, 1, mode='most')
#filter_for(test_input, 0, 1)
```


```python
oxrating = get_rating(dat, mode='most')
cdioxrating = get_rating(dat, mode='least')
lifesupport = oxrating * cdioxrating
#Markdown("Life support rating is: **{}**".format(lifesupport))
```


```python

```
