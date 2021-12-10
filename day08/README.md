# Day 8: Seven Segment Search

## Part 1

You barely reach the safety of the cave when the whale smashes into the cave mouth, collapsing it. Sensors indicate another exit to this cave at a much greater depth, so you have no choice but to press on.

As your submarine slowly makes its way through the cave system, you notice that the four-digit seven-segment displays in your submarine are malfunctioning; they must have been damaged during the escape. You'll be in a lot of trouble without them, so you'd better figure out what's wrong.

Each digit of a seven-segment display is rendered by turning on or off any of seven segments named a through g:
```
  0:      1:      2:      3:      4:
 aaaa    ....    aaaa    aaaa    ....
b    c  .    c  .    c  .    c  b    c
b    c  .    c  .    c  .    c  b    c
 ....    ....    dddd    dddd    dddd
e    f  .    f  e    .  .    f  .    f
e    f  .    f  e    .  .    f  .    f
 gggg    ....    gggg    gggg    ....

  5:      6:      7:      8:      9:
 aaaa    aaaa    aaaa    aaaa    aaaa
b    .  b    .  .    c  b    c  b    c
b    .  b    .  .    c  b    c  b    c
 dddd    dddd    ....    dddd    dddd
.    f  e    f  .    f  e    f  .    f
.    f  e    f  .    f  e    f  .    f
 gggg    gggg    ....    gggg    gggg
```
So, to render a 1, only segments c and f would be turned on; the rest would be off. To render a 7, only segments a, c, and f would be turned on.

The problem is that the signals which control the segments have been mixed up on each display. The submarine is still trying to display numbers by producing output on signal wires a through g, but those wires are connected to segments randomly. Worse, the wire/segment connections are mixed up separately for each four-digit display! (All of the digits within a display use the same connections, though.)

So, you might know that only signal wires b and g are turned on, but that doesn't mean segments b and g are turned on: the only digit that uses two segments is 1, so it must mean segments c and f are meant to be on. With just that information, you still can't tell which wire (b/g) goes to which segment (c/f). For that, you'll need to collect more information.

For each display, you watch the changing signals for a while, make a note of all ten unique signal patterns you see, and then write down a single four digit output value (your puzzle input). Using the signal patterns, you should be able to work out which pattern corresponds to which digit.

For example, here is what you might see in a single entry in your notes:

acedgfb cdfbe gcdfa fbcad dab cefabd cdfgeb eafb cagedb ab |
cdfeb fcadb cdfeb cdbaf

(The entry is wrapped here to two lines so it fits; in your notes, it will all be on a single line.)

Each entry consists of ten unique signal patterns, a | delimiter, and finally the four digit output value. Within an entry, the same wire/segment connections are used (but you don't know what the connections actually are). The unique signal patterns correspond to the ten different ways the submarine tries to render a digit using the current wire/segment connections. Because 7 is the only digit that uses three segments, dab in the above example means that to render a 7, signal lines d, a, and b are on. Because 4 is the only digit that uses four segments, eafb means that to render a 4, signal lines e, a, f, and b are on.

Using this information, you should be able to work out which combination of signal wires corresponds to each of the ten digits. Then, you can decode the four digit output value. Unfortunately, in the above example, all of the digits in the output value (cdfeb fcadb cdfeb cdbaf) use five segments and are more difficult to deduce.

For now, focus on the easy digits. Consider this larger example:
```
be cfbegad cbdgef fgaecd cgeb fdcge agebfd fecdb fabcd edb |
fdgacbe cefdb cefbgd gcbe
edbfga begcd cbg gc gcadebf fbgde acbgfd abcde gfcbed gfec |
fcgedb cgb dgebacf gc
fgaebd cg bdaec gdafb agbcfd gdcbef bgcad gfac gcb cdgabef |
cg cg fdcagb cbg
fbegcd cbd adcefb dageb afcb bc aefdc ecdab fgdeca fcdbega |
efabcd cedba gadfec cb
aecbfdg fbg gf bafeg dbefa fcge gcbea fcaegb dgceab fcbdga |
gecf egdcabf bgf bfgea
fgeab ca afcebg bdacfeg cfaedg gcfdb baec bfadeg bafgc acf |
gebdcfa ecba ca fadegcb
dbcfg fgd bdegcaf fgec aegbdf ecdfab fbedc dacgb gdcebf gf |
cefg dcbef fcge gbcadfe
bdfegc cbegaf gecbf dfcage bdacg ed bedf ced adcbefg gebcd |
ed bcgafe cdgba cbgef
egadfb cdbfeg cegd fecab cgb gbdefca cg fgcdab egfdb bfceg |
gbdfcae bgc cg cgb
gcafb gcf dcaebfg ecagb gf abcdeg gaef cafbge fdbac fegbdc |
fgae cfgab fg bagce
```
Because the digits 1, 4, 7, and 8 each use a unique number of segments, you should be able to tell which combinations of signals correspond to those digits. Counting only digits in the output values (the part after | on each line), in the above example, there are 26 instances of digits that use a unique number of segments (highlighted above).

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

Through a little deduction, you should now be able to determine the remaining digits. Consider again the first example above:
```
acedgfb cdfbe gcdfa fbcad dab cefabd cdfgeb eafb cagedb ab |
cdfeb fcadb cdfeb cdbaf
```
After some careful analysis, the mapping between signal wires and segments only make sense in the following configuration:
```
 dddd
e    a
e    a
 ffff
g    b
g    b
 cccc
```
So, the unique signal patterns would correspond to the following digits:
```
    acedgfb: 8
    cdfbe: 5
    gcdfa: 2
    fbcad: 3
    dab: 7
    cefabd: 9
    cdfgeb: 6
    eafb: 4
    cagedb: 0
    ab: 1
```
Then, the four digits of the output value can be decoded:
```
    cdfeb: 5
    fcadb: 3
    cdfeb: 5
    cdbaf: 3
```
Therefore, the output value for this entry is 5353.

Following this same process for each entry in the second, larger example above, the output value of each entry can be determined:
```
    fdgacbe cefdb cefbgd gcbe: 8394
    fcgedb cgb dgebacf gc: 9781
    cg cg fdcagb cbg: 1197
    efabcd cedba gadfec cb: 9361
    gecf egdcabf bgf bfgea: 4873
    gebdcfa ecba ca fadegcb: 8418
    cefg dcbef fcge gbcadfe: 4548
    ed bcgafe cdgba cbgef: 1625
    gbdfcae bgc cg cgb: 8717
    fgae cfgab fg bagce: 4315
```
Adding all of the output values in this larger example produces 61229.

For each entry, determine all of the wire/segment connections and decode the four-digit output values. **What do you get if you add up all of the output values?**


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
