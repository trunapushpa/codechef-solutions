# [Problem link](https://www.codechef.com/problems/CXZ)

## PROBLEM:

There are $N$ monsters, the $i$-th has health $H_i$.  
You start with a strength of $X$, and can kill the $i$-th monster only if $X > H_i$.  
After killing the $i$-th monster, your strength gets set to $H_i$.  

At most once, you can multiply your strength by $K$.  
Find the maximum number of monsters you can kill.

## EXPLANATION:

First, let’s solve the problem if the multiplication operation didn’t exist.

After killing a monster with health $H_i$, our strength gets set to $H_i$.  
This means the next monster we kill must have a strength that is strictly less than $H_i$.  
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

In the easy version, the constraints on $K$, $H_i$, $X$ are fairly small ($\leq 500$), so you can compute these quantities by iterating over each value from 1 to 500 and checking which of the conditions it satisfies.  
All you need to store is the frequency of each element.

**TIME COMPLEXITY:**

$O(500 \cdot N)$
per testcase.

## CODE
```python
for _ in range(int(input())):
    n, x, k = map(int, input().split())
    h = list(map(int, input().split()))
    freq = [0]*501
    for y in h: freq[y] += 1

    ans = 0
    # consider multiplying once at the start
    for y in range(1, 501):
        if y < x*k and freq[y] > 0: ans += 1
    
    for i in range(n):
        # multiply after killing h[i]
        # one copy of everything < h[i]*k
        # one copy of everything >= h[i] and < x
        # careful about stuff between h[i] and min(x, h[i]*k)

        ct = 0
        if h[i] >= x: continue
        for y in range(1, 501):
            if freq[y] == 0: continue

            if y < h[i]*k: ct += 1
            if y >= h[i] and y < x: ct += 1
            if freq[y] == 1 and y >= h[i] and y < min(x, h[i]*k): ct -= 1
        ans = max(ans, ct)
    
    print(ans)
```
