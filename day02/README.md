# Day 2: Dive!

https://adventofcode.com/2021/day/2

## Part 1

**What do you get if you multiply your final horizontal position by your final depth?**


```python
from IPython.display import Markdown
```


```python
pos = 0
depth = 0
with open('input.txt', 'r') as fid:
    for line in fid:
        cmd, val = line.strip().split()
        if 'forward' == cmd:
            pos += int(val)
        elif 'down' == cmd:
            depth += int(val)
        elif 'up' == cmd:
            depth -= int(val)
        else:
            raise ValueError("Unhandled command: {}".format(cmd))

pdepth = pos * depth
#display(pos, depth, pdepth)
#Markdown("Position Depth product is: **{}**".format(pdepth))
```

## Part Two

**What do you get if you multiply your final horizontal position by your final depth?**



```python
pos = 0
depth = 0
aim = 0
with open('input.txt', 'r') as fid:
    for line in fid:
        cmd, val = line.strip().split()
        if 'forward' == cmd:
            pos += int(val)
            depth += int(val) * aim
        elif 'down' == cmd:
            aim += int(val)
        elif 'up' == cmd:
            aim -= int(val)
        else:
            raise ValueError("Unhandled command: {}".format(cmd))

pdepth = pos * depth
#display(pos, depth, pdepth)
#Markdown("Position Depth product is: **{}**".format(pdepth))
```


```python

```
