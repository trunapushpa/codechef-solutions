# [Problem link](https://www.codechef.com/problems/MMA)

## PROBLEM:

There are $N$ monsters, the $i$-th has health $H_i$.  
You start with a strength of $X$, and can kill the $i$-th monster only if $X > H_i$.  
After killing the $i$-th monster, your strength gets set to $H_i$.  

At most once, you can multiply your strength by $K$.  
Find the maximum number of monsters you can kill.

## EXPLANATION:

First, let’s solve the problem if the multiplication operation didn’t exist.

After killing a monster with health $H_i$, our strength gets set to $H_i$.  
This means the next monster we kill must have a strength that strictly less than $H_i$.  
The monsters we kill must thus have a strictly decreasing sequence of strengths.

The very first monster we kill must have a strength that’s less than $X$.  
So, the maximum number of monsters that can be killed is simply the number of distinct strengths less than $X$.

---

Now, let’s think about what the multiplication operation lets us do.  
There are two possibilities: either we multiply right at the start, or we kill a few monsters and then multiply.

The first case is trivial: multiplying right at the start gives us a strength of $X \cdot K$, after which we can kill one monster for each distinct strength that’s less than $X \cdot K$.

The second case requires a bit more thought.  
Suppose we multiply right after killing a monster with strength $H_i$, so that we’re now at a strength of $K \cdot H_i$.  
Then,

- We can certainly kill one monster for each distinct strength $< K \cdot H_i$.
- We can also kill one monster for each distinct strength that’s $\geq H_i$ and $< X$, on our initial path to reach $H_i$.
- There is one caveat, however: if there’s exactly one monster with strength $Y$, where both $Y < K \cdot H_i$ and $H_i \leq Y < X$, then it should be counted once and not twice (since we can only kill it once).

So, for a fixed $H_i$ being the last kill before multiplication, we want to know:

1. The number of distinct elements that are $< K \cdot H_i$.
2. The number of distinct elements that are $< X$ and $\geq H_i$.
3. The number of elements that appear exactly once, and are between $H_i$ and $\min(X, K \cdot H_i) - 1$.

For the hard version, all of these can be found quite easily using binary search: for the first two, store a sorted list of all distinct elements and binary search on that; for the third, store a sorted list of all elements that appear exactly once and binary search on that.

So, for a fixed $H_i$ the answer can be found in $O(\log N)$ time - we only perform a few binary searches.  
Repeat this for every possible $H_i < X$, and take the best answer among them.  
Don’t forget to also consider the case of taking all the distinct elements $< X \cdot K$.

**TIME COMPLEXITY:**

$O(N \log N)$
per testcase.

## CODE
```python
import bisect, collections

for _ in range(int(input())):
    n, x, k = map(int, input().split())
    a = list(map(int, input().split()))
    
    freq = collections.Counter(a)
    distinct, twice = [], []
    for y in freq.keys():
        distinct.append(y)
        if freq[y] > 1: twice.append(y)
    distinct = sorted(distinct)
    twice = sorted(twice)
    
    ans = 0
    for y in distinct:
        if y >= x: break

        # take till y -> multiply -> take remaining
        # one copy of everything that's < max(y*k, x)
        # another copy of everything that's < min(y*k, x) and >= y

        mn, mx = min(y*k, x), max(y*k, x)
        cur = bisect.bisect_left(distinct, mx)
        cur += bisect.bisect_left(twice, mn) - bisect.bisect_left(twice, y)
        ans = max(ans, cur)
    
    # also consider multiplying right at the start
    ans = max(ans, bisect.bisect_left(distinct, x*k))
    print(ans)

```
