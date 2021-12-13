# Day 11: Dumbo Octopus

https://adventofcode.com/2021/day/11

## Part 1

**How many total flashes are there after 100 steps?**


```python
from IPython.display import Markdown
import numpy as np
from scipy import ndimage
from matplotlib import pyplot as plt
from scipy import signal
```


```python
def makeplot(dat, cmap='gray'):
    fig, ax = plt.subplots(figsize=(10, 10))
    ax.imshow(dat, cmap=cmap)
    
def prdat(arr, note=None):
    if note is not None:
        print(note)
    print(np.array(arr, dtype=np.int))
```


```python
#infile = 'test_input2.txt'
#infile = 'test_input.txt'
infile = 'input.txt'

lines = list()
with open(infile, 'r') as fid:
    for line in fid:
        lines.append([int(x) for x in line.strip()])
dat0 = np.array(lines, dtype=np.int)
#dat0
```


```python
'''
# Prototype a single step
dat1 = dat0.copy()
# Step 1: Energy level of each octopus increases by 1
dat1 += 1
dat1

# Step 2: Any octopuses with an energy level of 10 will flash
# increasing the energy level of all adjacent octopuses by 1

# The centering structure needs to include diagonals
struct = np.ones((3,3), dtype=np.int)
struct

#makeplot(dat0)
flashpts = dat1 >= 10
#makeplot(flashpts)
flasharea = signal.convolve2d(flashpts, struct, mode='same')
# Any octopuses that have already flashed reset to zero
flasharea[flashpts] = 0
flasharea

dat2 = dat1 + flasharea
dat2

# Zero out the octopuses that already flashed
dat2[flashpts] = 0
dat2

# Handle any new octopus flashes
flashpts2 = dat2 >= 10
flasharea2 = signal.convolve2d(flashpts2, struct, mode='same')
flasharea2

# Any octopuses that already flashed won't flash again
flasharea2[flashpts] = 0
flasharea2[flashpts2] = 0
flasharea2

# Only if there's another octopus available
# to gain energy in the flash area do we continue
if np.any(flasharea2):
    dat3 = dat2 + flasharea2
else:
    dat3 = dat2
dat3[flashpts2] = 0
dat3
'''
pass
```


```python
# Model the whole process...
nflashes = 0
nrounds = 100
# The centering structure needs to include diagonals
struct = np.ones((3,3), dtype=np.int)

dat1 = dat0.copy()
for i in range(nrounds):
    # Run a step which includes accounting for
    # all chain reaction flashes
    dat1 = dat1 + 1
    # Mask for preventing octopuses that already
    # flashed from flashing again
    mask = np.zeros_like(dat1, dtype=np.bool)
    #prdat(dat1, note='Initial conditions of octopuses')
    while True:
        # Flash chain reaction
        flashpts = dat1 >= 10
        #prdat(flashpts, "Initial available flash points:")
        #prdat(mask, "Initial mask:")
        # Any ocotopus that already flashed may not flash again
        flashpts[mask] = False
        #prdat(flashpts, "Flash points after turning off already flashed:")
        # Get the area of flash
        flasharea = signal.convolve2d(flashpts, struct, mode='same')
        #prdat(flasharea, 'Raw flash area:')
        # Reset the octopuses that flashed and mask them for the future
        mask[flashpts] = True
        #prdat(mask, "Mask of flashed octopuses:")
        # Any octopus that flashed won't gain any more energy this round:
        flasharea[mask] = 0
        #prdat(flasharea, "Flash energy transferrance values:")
        # Zero out any octopuses that flashed
        dat1[mask] = 0
        #prdat(dat1, "Zero out flashed octopuses:")
        if np.any(flasharea):
            # An octopus flashed and can increase the energy of neighbor
            dat1 += flasharea
            # Mask the flashing octopus for the future
            mask[flashpts] = True
            #prdat(mask, "Mask after applying flasharea")
        else:
            break
        #print("CONTINUING CHAIN REACTION")
    #prdat(dat1, "After step {}:".format(i+1))
    # Count the number of zeros to get the number of flashes
    nflashes += np.sum(dat1 == 0)
    
#Markdown("There were **{}** flashes".format(nflashes))
```

## Part Two

**What is the first step during which all octopuses flash?**


```python
# Continue the process until all octopuses flash simultaneously
roundnum = 0
# The centering structure needs to include diagonals
struct = np.ones((3,3), dtype=np.int)

dat1 = dat0.copy()
while True:
    # Run a step which includes accounting for
    # all chain reaction flashes
    dat1 = dat1 + 1
    # Mask for preventing octopuses that already
    # flashed from flashing again
    mask = np.zeros_like(dat1, dtype=np.bool)
    #prdat(dat1, note='Initial conditions of octopuses')
    while True:
        # Flash chain reaction
        flashpts = dat1 >= 10
        #prdat(flashpts, "Initial available flash points:")
        #prdat(mask, "Initial mask:")
        # Any ocotopus that already flashed may not flash again
        flashpts[mask] = False
        #prdat(flashpts, "Flash points after turning off already flashed:")
        # Get the area of flash
        flasharea = signal.convolve2d(flashpts, struct, mode='same')
        #prdat(flasharea, 'Raw flash area:')
        # Reset the octopuses that flashed and mask them for the future
        mask[flashpts] = True
        #prdat(mask, "Mask of flashed octopuses:")
        # Any octopus that flashed won't gain any more energy this round:
        flasharea[mask] = 0
        #prdat(flasharea, "Flash energy transferrance values:")
        # Zero out any octopuses that flashed
        dat1[mask] = 0
        #prdat(dat1, "Zero out flashed octopuses:")
        if np.any(flasharea):
            # An octopus flashed and can increase the energy of neighbor
            dat1 += flasharea
            # Mask the flashing octopus for the future
            mask[flashpts] = True
            #prdat(mask, "Mask after applying flasharea")
        else:
            break
        #print("CONTINUING CHAIN REACTION")
    #prdat(dat1, "After step {}:".format(i+1))
    # Count the number of rounds
    roundnum += 1
    if np.any(dat1):
        # Not all octopuses flashed
        pass
    else:
        # All octopuses flashed!
        break
    
#Markdown("All octopuses flashed on round **{}**".format(roundnum))
```


```python

```
