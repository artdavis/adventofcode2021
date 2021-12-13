# Day 12: Passage Pathing

https://adventofcode.com/2021/day/12

## Part 1

**How many paths through this cave system are there that visit small caves at most once?**


```python
from IPython.display import Markdown
import copy
```


```python
#infile = 'test-input1.txt'
#infile = 'test-input2.txt'
#infile = 'test-input3.txt'
infile = 'input.txt'

# Represent connection graph as dict with
# each node as a key and all available neighbors
# in a set as the value
cavegraph = dict()
with open(infile, 'r') as fid:
    for line in fid:
        node, neigh = line.strip().split('-')
        # Never include 'start' as a neighbor since we
        # can never go back there
        if node in cavegraph:
            if 'start' != neigh:
                cavegraph[node].add(neigh)
        else:
            if 'start' != neigh:
                cavegraph[node] = {neigh}
        if neigh in cavegraph:
            if 'start' != node:
                cavegraph[neigh].add(node)
        else:
            if 'start' != node:
                cavegraph[neigh] = {node}
```


```python
#cavegraph
```


```python
def pr(val):
    print(val, end=',')
```


```python
def pathway(node, seen=set(), thisroute=list(), routes=list()):
    # Recursive function to traverse all valid cave paths
    # allowing for visiting each small cave at most once
    #print("=={}==".format(node))
    thisroute.append(node)
    #print("This route: {}".format(thisroute))
    if 'end' == node:
        routes.append(thisroute)
        #print('...Hit the end: {}'.format(routes))
        return routes
    if node in seen:
        # Already saw this small cave, not a valid route
        return routes
    if node == node.lower():
        # Small cave, add to seen
        seen.add(node)
    # From this node, try every neighbor
    #print("\n... Available neighbors: {}".format(cavegraph[node]))
    #print("... Seen: {}".format(seen))
    # Cache thisroute and seen before descent
    thisroute0 = thisroute.copy()
    seen0 = seen.copy()
    for neigh in cavegraph[node]:
        # Restore cached thisroute and seen for this descent
        thisroute1 = thisroute0.copy()
        seen1 = seen0.copy()
        #pr('({})'.format(neigh))
        #print('__{}__'.format(neigh))
        #print('thisroute1: {}'.format(thisroute1))
        routes = pathway(neigh, seen=seen1, thisroute=thisroute1, routes=routes)
    return routes
```


```python
routes = pathway('start')
#print()
#print('-='*20)
#for r in routes:
#    print(','.join(r))
```


```python
npaths = len(routes)
Markdown("The number of paths is **{}**".format(npaths))
```




The number of paths is **5104**



## Part Two

Given these new rules, **how many paths through this cave system are there?**


```python
known_paths = set('''start,A,b,A,b,A,c,A,end
start,A,b,A,b,A,end
start,A,b,A,b,end
start,A,b,A,c,A,b,A,end
start,A,b,A,c,A,b,end
start,A,b,A,c,A,c,A,end
start,A,b,A,c,A,end
start,A,b,A,end
start,A,b,d,b,A,c,A,end
start,A,b,d,b,A,end
start,A,b,d,b,end
start,A,b,end
start,A,c,A,b,A,b,A,end
start,A,c,A,b,A,b,end
start,A,c,A,b,A,c,A,end
start,A,c,A,b,A,end
start,A,c,A,b,d,b,A,end
start,A,c,A,b,d,b,end
start,A,c,A,b,end
start,A,c,A,c,A,b,A,end
start,A,c,A,c,A,b,end
start,A,c,A,c,A,end
start,A,c,A,end
start,A,end
start,b,A,b,A,c,A,end
start,b,A,b,A,end
start,b,A,b,end
start,b,A,c,A,b,A,end
start,b,A,c,A,b,end
start,b,A,c,A,c,A,end
start,b,A,c,A,end
start,b,A,end
start,b,d,b,A,c,A,end
start,b,d,b,A,end
start,b,d,b,end
start,b,end
'''.split())
#known_paths
```


```python
def rls(route):
    # Return route list as csv string
    return ','.join(route)
```


```python
#watchstr = 'start,A,b,A,b'
#watchstr = 'start,A,b,A,c,A,b,A,c'
#watchstr = 'start,A,b,A,b,A,c,A,c'
#watchstr = 'start,A,b,A'
watchstr = 'start,A,c,A,b,A,c,A'
def pathway2(node, seen=set(), seenbuffer=list(), smalldone=False,
             thisroute=list(), routes=list()):
    # Recursive function to traverse all valid cave paths
    # allowing for visiting a single small cave twice but all
    # other small caves at most once
    ###print("=={}==".format(node))
    thisroute.append(node)
    ###if rls(thisroute).startswith(watchstr):
    ###    print("*"*20)
    ###    print("WATCH... node: {}; seenbuffer: {}".format(node, seenbuffer))
    ###print("This route: {}; seen: {}; seenbuffer: {}, smalldone: {}".format(
    ###    rls(thisroute), rls(seen), rls(seenbuffer), smalldone))
    #print("WATCH... node: {}; seenbuffer: {}".format(node, seenbuffer))
    ###print("... Available neighbors: {}".format(cavegraph[node]))
    if 'end' == node:
        routes.append(thisroute)
        ###print('...HIT THE END')
        ###print('-'*40)
        return routes
    if node in seen:
        # Already saw this small cave, not a valid route
        ###print('... EXITING DUE TO {} in seen: {}'.format(node, rls(seen)))
        return routes
    #if node in seenbuffer and smalldone:
        # Already saw this small cave once and visited another
        # small cave twice... not a valid route
    #    return routes
    if node == node.lower():
        ###print('lower node: {}'.format(node))
        if smalldone:
            seen = seen.union(set(seenbuffer))
            seen.add(node)
        # If this is the first time we've seen a small cave, we might
        # come back to visit it a second time
        #if node not in seenbuffer and node not in {'start', 'end'}:
        #print("Small cave: {}".format(node))
        #if 0 == len(seenbuffer) and node not in {'start', 'end'}:
        elif node not in {'start', 'end'} and node not in seenbuffer:
            ###print("Buffering: {}".format(node))
            #seenbuffer.add(node)
            seenbuffer.append(node)
        #elif smalldone:
        #    # If smalldone is True, this must go in seen
        #    #seen.add(node)
        #    # Add everything in seenbuffer to seen
        #    seen = seen.union(set(seenbuffer))
        else:
            # Node WAS in the seenbuffer but was not in seen (but may be start/end)
            # If we've already visited this node before, flag that
            # we may no longer visit small caves after this
            ###print("smalldone: {}; node: {}; seen: {}; seenbuffer: {}".format(
            ###        smalldone, node, rls(seen), rls(seenbuffer)))
            #if smalldone:
            #    # smalldone was already set True (and this node was in
            #    # seenbuffer)
            #    print('ADDING {} to seen because smalldone was True and this node was in screenbuffer'.
            #         format(node))
            #    #seen.add(node)
            #    # Add everything in seenbuffer to seen
            #    seen = seen.union(set(seenbuffer))
            #else:
            # smalldone was False
            # Visiting small caves is herein forbidden
            if node not in {'start', 'end'}:
                smalldone = True
                ###print("Setting smalldone to TRUE")
                seen = seen.union(set(seenbuffer))
                
    # From this node, try every neighbor
    #print("... Available neighbors: {}".format(cavegraph[node]))
    #print("... Seen: {}".format(seen))
    # Cache this route and seen before descent
    thisroute0 = thisroute.copy()
    seen0 = seen.copy()
    seenbuffer0 = seenbuffer.copy()
    smalldone0 = copy.copy(smalldone)
    for neigh in cavegraph[node]:
        # Restore cached thisroute and seen for this descent
        thisroute1 = thisroute0.copy()
        seen1 = seen0.copy()
        seenbuffer1 = seenbuffer0.copy()
        smalldone1 = copy.copy(smalldone0)
        #pr('({})'.format(neigh))
        #print('__{}__'.format(neigh))
        #print('thisroute1: {}'.format(thisroute1))
        routes = pathway2(neigh, seen=seen1, seenbuffer=seenbuffer1,
                          smalldone=smalldone1,
                          thisroute=thisroute1, routes=routes)
    return routes

routes2 = pathway2('start')
routes2a = {','.join(x) for x in routes2}
npaths2 = len(routes2)
#display(Markdown("The number of paths is **{}**".format(npaths2)))
```


```python
# TESTING
# Missed routes
#miss_routes = known_paths - routes2a
#print('=-'*20)
#print("MISSED ROUTES:")
#for x in miss_routes:
#    print(x)

# Invalid routes
#invalid_routes = routes2a - known_paths
#print('=-'*20)
#print("INVALID ROUTES:")
#for x in invalid_routes:
#    print(x)

#print('=-'*20)
#print("FOUND ROUTES:")
#for r in routes2:
#    print(','.join(r))    
```


```python

```
