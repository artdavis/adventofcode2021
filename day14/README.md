# Day 14: Extended Polymerization

https://adventofcode.com/2021/day/14

## Part 1

The incredible pressures at this depth are starting to put a strain on your submarine. The submarine has polymerization equipment that would produce suitable materials to reinforce the submarine, and the nearby volcanically-active caves should even have the necessary input elements in sufficient quantities.

The submarine manual contains instructions for finding the optimal polymer formula; specifically, it offers a polymer template and a list of pair insertion rules (your puzzle input). You just need to work out what polymer would result after repeating the pair insertion process a few times.

For example:
```
NNCB

CH -> B
HH -> N
CB -> H
NH -> C
HB -> C
HC -> B
HN -> C
NN -> C
BH -> H
NC -> B
NB -> B
BN -> B
BB -> N
BC -> B
CC -> N
CN -> C
```
The first line is the polymer template - this is the starting point of the process.

The following section defines the pair insertion rules. A rule like AB -> C means that when elements A and B are immediately adjacent, element C should be inserted between them. These insertions all happen simultaneously.

So, starting with the polymer template NNCB, the first step simultaneously considers all three pairs:
```
    The first pair (NN) matches the rule NN -> C, so element C is inserted between the first N and the second N.
    The second pair (NC) matches the rule NC -> B, so element B is inserted between the N and the C.
    The third pair (CB) matches the rule CB -> H, so element H is inserted between the C and the B.
```
Note that these pairs overlap: the second element of one pair is the first element of the next pair. Also, because all pairs are considered simultaneously, inserted elements are not considered to be part of a pair until the next step.

After the first step of this process, the polymer becomes NCNBCHB.

Here are the results of a few steps using the above rules:
```
Template:     NNCB
After step 1: NCNBCHB
After step 2: NBCCNBBBCBHCB
After step 3: NBBBCNCCNBBNBNBBCHBHHBCHB
After step 4: NBBNBNBBCCNBCNCCNBBNBBNBBBNBBNBBCBHCBHHNHCBBCBHCB
```
This polymer grows quickly. After step 5, it has length 97; After step 10, it has length 3073. After step 10, B occurs 1749 times, C occurs 298 times, H occurs 161 times, and N occurs 865 times; taking the quantity of the most common element (B, 1749) and subtracting the quantity of the least common element (H, 161) produces 1749 - 161 = 1588.

Apply 10 steps of pair insertion to the polymer template and find the most and least common elements in the result. **What do you get if you take the quantity of the most common element and subtract the quantity of the least common element?**


```python
from IPython.display import Markdown
```


```python
#infile = 'test_input.txt'
infile = 'input.txt'

rules = dict()
with open(infile, 'r') as fid:
    # First line is polymer template
    polym0 = fid.readline().strip()
    # Ignore the next blank line
    fid.readline()
    # Read the pair insertion rules into a dictionary
    for line in fid:
        k, v = line.strip().split(' -> ')
        rules[k] = v
        
#print(polym0)
#print(rules)
```


```python
def get_polym(nsteps):
    # Initialize the polymer
    polym = polym0
    # String to hold resulting polymer
    polym1 = str()

    for n in range(nsteps):
        print("ROUND {}...".format(n+1))
        for i in range(len(polym) - 1):
            pair = polym[i:i+2]
            #print('{} -> {}'.format(pair, rules[pair]))
            # Append the first element and inserted element
            polym1 += pair[0] + rules[pair]
        # Append the last element
        polym1 += pair[1]
        polym1
        # Assign the current polymer to initial polymer for the next step
        polym = polym1
        polym1 = str()
        #print("After step {}: {}".format(n+1, polym))
    return polym
```


```python
polym = get_polym(10)
len(polym)
```

    ROUND 1...
    ROUND 2...
    ROUND 3...
    ROUND 4...
    ROUND 5...
    ROUND 6...
    ROUND 7...
    ROUND 8...
    ROUND 9...
    ROUND 10...
    




    19457




```python
# Unique elements
els = set(polym)
els
```




    {'B', 'C', 'F', 'H', 'K', 'N', 'O', 'P', 'S', 'V'}




```python
def get_ecount(polym):
    # Get element counts from polymer string
    # Element Counts
    ecount = dict()
    for e in els:
        ecount[e] = polym.count(e)
        #print('{} occurs {} times'.format(e, polym.count(e)))
    return ecount

ecount = get_ecount(polym)
print(ecount)
```

    {'B': 1490, 'O': 570, 'F': 2711, 'V': 3056, 'H': 3978, 'S': 577, 'P': 2643, 'C': 972, 'K': 1378, 'N': 2082}
    


```python
# Most common - least common element
elspan = max(ecount.values()) - min(ecount.values())
#Markdown("Most common minus least common element quantity is **{}**".format(elspan))
```

## Part Two

The resulting polymer isn't nearly strong enough to reinforce the submarine. You'll need to run more steps of the pair insertion process; a total of 40 steps should do it.

In the above example, the most common element is B (occurring 2192039569602 times) and the least common element is H (occurring 3849876073 times); subtracting these produces 2188189693529.

Apply 40 steps of pair insertion to the polymer template and find the most and least common elements in the result. **What do you get if you take the quantity of the most common element and subtract the quantity of the least common element?**


```python
# After round 20 the get_polym() function time per round just explodes.
# Brute forcing out to round 40 is not tenable.
#polym = get_polym(40)
```


```python
def get_pairs(polym):
    # Return the pairdict {pair: count}
    # from supplied polym string
    pairdict = dict()
    for i in range(len(polym) - 1):
        pair = polym[i:i+2]
        if pair in pairdict:
            pairdict[pair] += 1
        else:
            pairdict[pair] = 1
    return pairdict
#pairdict = get_pairs(polym0)
#print(pairdict)
```


```python
def get_paircount(pairdict, nsteps):
    """
    Create pairdict histogram dictionary for nsteps
    """
    pairdict0 = pairdict.copy()
    pairdict1 = pairdict0.copy()
    for n in range(nsteps):
        #print("... ROUND {}...".format(n+1))
        pairdict0 = pairdict1.copy()
        #print("AT START OF ROUND...")
        #print(pairdict0)
        # Next increment any newly formed pairs
        for k, v in pairdict0.items():
            if v > 0:
                # Only if pair count is non-zero
                # Assemble new pair
                pair1 = k[0] + rules[k]
                #print("New pair1: {}".format(pair1))
                if pair1 in pairdict1:
                    pairdict1[pair1] += v
                else:
                    pairdict1[pair1] = v
                pair2 = rules[k] + k[1]
                #print("New pair2: {}".format(pair2))
                if pair2 in pairdict1:
                    pairdict1[pair2] += v
                else:
                    pairdict1[pair2] = v
                # Since we've also split up k here, reduce its count
                pairdict1[k] -= v
        # AFTER ROUND
        #print("ROUND COMPLETED...")
        #print(pairdict1)
        #print('-'*40)
    return pairdict1

def get_ecount_from_pairs(pairdict, polym0=polym0):
    """
    Get the element count from the pairdict histogram
    """
    ecount = dict()
    # Offset the first and last element because they
    # don't pair past the end of the chain
    ecount[polym0[0]] = 1
    ecount[polym[-1]] = 1
    for k, v in pairdict.items():
        #print(k)
        if k[0] in ecount:
            ecount[k[0]] += v
        else:
            ecount[k[0]] = v
        if k[1] in ecount:
            ecount[k[1]] += v
        else:
            ecount[k[1]] = v
    # Now divide the count for each element
    # by 2 to account for having been double
    # counted in the pairs
    ecount = {k: v // 2 for k, v in ecount.items()}
    return ecount

```


```python
# TESTING
pairdict = get_pairs(polym0)

nsteps = 10
pairdict1 = get_paircount(pairdict, nsteps)
#pairdict1

polym_steps = {0: 'NNCB',
               1: 'NCNBCHB',
               2: 'NBCCNBBBCBHCB',
               3: 'NBBBCNCCNBBNBNBBCHBHHBCHB',
               4: 'NBBNBNBBCCNBCNCCNBBNBBNBBBNBBNBBCBHCBHHNHCBBCBHCB',
               10: polym}
pairdict_test = get_pairs(polym_steps[nsteps])

#print('=-'*40)
#for k, v in pairdict1.items():
#    print("{}:{}".format(k, v), end='')
#    if k in pairdict_test:
#        print("/{}".format(pairdict_test[k]))
#    else:
#        print()
        
test_pairdict = get_pairs(polym)
ecount2 = get_ecount_from_pairs(test_pairdict)
#print(ecount)
#print(ecount2)
for k, v in ecount.items():
    assert(ecount2[k] == v)
print('TESTS PASS')
```

    TESTS PASS
    


```python
# RUN FOR REAL
pairdict = get_pairs(polym0)
nsteps = 40
pairdict40 = get_paircount(pairdict, nsteps)
ecount40 = get_ecount_from_pairs(pairdict40)
#ecount40
```


```python
# Most common - least common element
elspan40 = max(ecount40.values()) - min(ecount40.values())
#Markdown("Most common minus least common element quantity is **{}**".format(elspan40))
```
