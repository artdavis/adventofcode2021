# Day4: Giant Squid

https://adventofcode.com/2021/day/4

## Part 1

**What will your final score be if you choose that board?**


```python
import numpy as np
from IPython.display import Markdown
```


```python
boardlist = list()
with open('input.txt', 'r') as fid:
    drawnums = np.array([int(x) for x in fid.readline().strip().split(',')])
    # Throw away next line which is blank
    fid.readline()
    # Read in the boards
    board = list()
    for line in fid:
        if '' == line.strip():
            # End of this board. At to list and start next board
            boardlist.append(np.array(board))
            board=list()
        else:
            board.append(np.array(line.strip().split(), dtype=np.int))

#drawnums
# Make a 3D array of all the boards
boardarr = np.array(boardlist)
#boardarr: dimension 0 indexes the individual boards
#          dimension 1 indexes the rows
#          dimension 2 indexes the columns
```


```python
def check_win():
    win = False
    # Check for a winning row (sum over the columns)
    rowwin = np.any(hitarr.sum(axis=2) >= 5, axis=1)
    if np.any(rowwin):
        # ROW BINGO
        win = True
        return win, boardarr[rowwin], hitarr[rowwin]
    # Check for a winning col (sum over the rows)
    colwin = np.any(hitarr.sum(axis=1) >= 5, axis=1)
    if np.any(colwin):
        # COL BINGO
        win = True
        return win, boardarr[colwin], hitarr[colwin]
    # No BINGO yet
    return win, None, None

```


```python
# Initialize hitarray to no hits
hitarr = np.zeros_like(boardarr, dtype=np.bool)
for num in drawnums:
    # Toggle hit on board when a number comes up
    hitarr = np.logical_or(hitarr, boardarr == num)
    win, bwin, hwin = check_win()
    if win:
        break
```


```python
# Calculate score from winning board
# Sum of all umarked numbers times winning number
winscore = bwin[np.logical_not(hwin)].sum() * num
#Markdown("Score for first winning board is: **{}**".format(winscore))
```

## Part Two

**Once it wins, what would its final score be?**


```python
def check_win2():
    # Find all winning boards
    rowwin = np.any(hitarr.sum(axis=2) >= 5, axis=1)
    colwin = np.any(hitarr.sum(axis=1) >= 5, axis=1)
    allwin = np.logical_or(rowwin, colwin)
    return allwin
```


```python
# Initialize hitarray to no hits
hitarr = np.zeros_like(boardarr, dtype=np.bool)
# Keep track of when winning starts happening
startedwinning = False

for num in drawnums:
    #print("Board: {}".format(num))
    # Toggle hit on board when a number comes up
    hitarr = np.logical_or(hitarr, boardarr == num)
    winboards = check_win2()
    #print(winboards)
    if np.any(winboards):
        #print('... STARTED WINNING')
        startedwinning = True
    if startedwinning and np.all(winboards):
        print("All boards are just now winners")
        break
    # Cache this round in case it's the last round
    winboards0 = winboards
    num0 = num

#display(winboards)
#display(winboards0)
#display(num)
#display(num0)
```

    All boards are just now winners
    


```python
# Get the final winning board
lastboard = boardarr[np.logical_not(winboards0)]
lasthit = hitarr[np.logical_not(winboards0)]
lastscore = lastboard[np.logical_not(lasthit)].sum() * num
#Markdown("Score for the final winning board was: **{}**".format(lastscore))
```


```python

```
