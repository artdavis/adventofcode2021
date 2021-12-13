# Day 10: Syntax Scoring

https://adventofcode.com/2021/day/10

## Part 1

**What is the total syntax error score for those errors?**


```python
from IPython.display import Markdown
```


```python
#infile = 'test_input.txt'
infile = 'input.txt'
datlist = list()
with open(infile, 'r') as fid:
    for line in fid:
        datlist.append(line.strip())
#datlist
```


```python
def test_corrupt(navstr):
    brackpairs = {'(':')',
                  '[':']',
                  '{':'}',
                  '<':'>'}
    stack = list()
    for c in navstr:
        if c in '([{<':
            # Push open bracket onto stack
            stack.append(c)
        elif c in ')]}>':
            # Pop open bracket off stack
            brack = stack.pop()
            # Check that the popped open bracket matches
            # the close bracket
            if c != brackpairs[brack]:
                # navstr is corrupt
                return c
        else:
            raise ValueError('Unhandled character: {}'.format(c))
    # navstr is not corrupt
    return False
```


```python
scoredict = {')': 3,
             ']': 57,
             '}': 1197,
             '>': 25137}
score = 0
corrupted = list()
incomplete = list()
for navstr in datlist:
    bad_bracket = test_corrupt(navstr)
    if bad_bracket is not False:
        #corrupted.append(navstr)
        #print('Corrupt bracket: {}'.format(bad_bracket))
        score += scoredict[bad_bracket]
    else:
        incomplete.append(navstr)
#corrupted
#Markdown("Syntax error score is: **{}**".format(score))
```

## Part Two

**What is the middle score?**


```python
# We constructed the incomplete list in Part 1
#incomplete
```


```python
def make_complete(navstr):
    brackpairs = {'(':')',
                  '[':']',
                  '{':'}',
                  '<':'>'}
    stack = list()
    for c in navstr:
        if c in '([{<':
            # Push open bracket onto stack
            stack.append(c)
        elif c in ')]}>':
            # Pop open bracket off stack
            brack = stack.pop()
            # Check that the popped open bracket matches
            # the close bracket
            if c != brackpairs[brack]:
                raise ValueError('Data is corrupt')
    # Whatever's left on the stack needs to be closed
    return ''.join([brackpairs[x] for x in stack[::-1]])
```


```python
def get_score(brackstr):
    scoredict2 = {')': 1,
                  ']': 2,
                  '}': 3,
                  '>': 4}
    score = 0
    for c in brackstr:
        score = score * 5 + scoredict2[c]
    return score

#make_complete(datlist[2]) # Should fail corrupt
#make_complete(incomplete[0])
score_list = list()
for navstr in incomplete:
    brackstr = make_complete(navstr)
    score2 = get_score(brackstr)
    score_list.append(score2)
    #print('{}: {}'.format(brackstr, score2))
```


```python
score_list.sort()
#score_list
median_score = score_list[len(score_list) // 2]
#Markdown('The middle autocomplete score is: **{}**'.format(median_score))
```


```python

```
