# Preliminaries

- **data structure** - black box containing some **data** and providing certain **operations** on top of it
- **queries** - non-altering operations returning some data state
- **updates** - altering operations changing data
- **static** data structure - once built, no longer updated
- **dynamic** data structure - can be updated at any time
- structure **interface** - set of methods for accessing the data structure
- structure **implementation** - internal layout of data in memory and procedures handling operations

# Ammortized analysis

**Amortized complexity** "is the total expense per operation, evaluated over a sequence of operations."

In other words, amortized complexity says what's the upper bound of operation cost in a larger sequence of operations.

- consider the worst time per operation $T$
- meaning $k$ operations take time at most $kT$
- however, if we use amortized complexity, we can improve the upper bound estimate
- if we speculate an **amortized cost** per operation $A$, then it holds that $k$ operations take time at most $kA \le kT$ (amortized cost often substantially better than worst cost)

- another way to look at it is that amortized cost $A$ "piles up" when performing easy operations and gets used up on long operations.

## Flexible arrays

![FlexArray](res/flex_array.png)

- dynamic array which can expand/shrink once it passes certain thresholds
- has to be reallocated and copied when expanding/shrinking

### Complexity analysis

We claim that **amortized cost** $A = 3$ ($k$ operations take $\le 3k$ time).
Further, the **real cost** $R$ is at most $A$.

## Weight-balanced tree

- Define:
  - $l(v)$ - left child of vertex $v$
  - $r(v)$ - right child of vertex $v$
  - $s(v)$ - number of vertices in subtree with root $v$ (incl. $v$)
- Then:
  - tree is $\alpha$-weight-balanced iff $\forall v:s(l(v)) \le \alpha s(v) \wedge s(r(v)) \le \alpha s(v)$
- we consider case $\alpha = \frac{2}{3}$
- depth $d \le \log_{2/3}\frac{1}{n} = \log_{3/2}n \in O(\log n)$
  - because:
    - consider path from root to some leaf
    - at maximum, each next step goes into subtree of size $\frac{2}{3}s(v)$
    - in such case in leaf we reach subtree of size $\left(\frac{2}{3}\right)^d n = 1$

## Splay trees

## (a,b)-trees

- $O(n)$ štepení pri $n$ insertoch do prázdneho stromu
- ak $b \ge 2a$, tak počet štepení/zlievaní pri $m$ insertoch a $l$ deletoch je $O(m + l)$

$\Phi = 2 \cdot \#a + 1 \cdot \#(a+1) + 2 \cdot \#(b-1) + 4 \cdot \#b$,  
    kde $\#a =$ počet vrcholov majúcich $a$ potomkov v strome

### Insert

![](res/ab_insert.png)

- máme $t$ vrcholov s $b$ synmi, kt. sa rozštipia na $2t$ vrcholov, aspoň $t$ z nich bude mať $\ge a+1$ a $\le b-1$ synov.
- teda:
  - zanikne **určite** $t$ vrcholov v $\#b$
    - $-4t$
  - vznikne **najviac** $t$ vrcholov v $\#a$
    - $+2t$
  - vznikne **najviac** $t$ vrcholov v $\#(a+1)$
    - $+1t$
  - vznikne **možno** $1$ vrchol (koreň) v $\#b$
    - $+4$
- $\Delta\Phi \le - 4t + 2t + 1t + 4 = 4 - t$
- $R = 1 + t$ (insert + splity)
- $A \le 5$

### Delete

![](res/ab_delete.png)

- máme $s$ dvojíc vrcholov s $a$ synmi, kt. sa spoja do $s$ vrcholov s $2a-1$ synmi
- teda:
  - zanikne **určite** $2s$ vrcholov v $\#a$
    - $-2 \cdot 2s$
  - vznikne **najviac** $s$ vrcholov v $\#(b-1)$ (z $2a-1$)
    - $+2s$
  - vznikne **možno** $1$ vrchol v $\#b$ ($p'$ z $p$)
    - $+4$
  - vznikne **možno** $1$ vrchol v $\#b$ ($q'$ z $q$)
    - $+4$
- $\Delta\Phi \le -2 \cdot 2s + 2s + 4 + 4 = 8 - 2s$
- $R = 1 + 2s$ (insert + merge)
- $A \le 9$

# Caching

![](res/cache.png)

- complexity is measured in number of **blocks read** from memory needed to complete the algorithm.

- 2 types of memory:
  - **External** - potentially infinite in size, contains arbitrary items in fixed-size blocks
  - **Internal** - for $M$ items, organized in $B$-sized blocks

- types of cache models:
  - **Cache-aware (CA)** - knows parameters of cache, can optimize algorithm
  - **Cache-oblivious (CO)** - doesn't know parameters so tries to "work for every cache"

## Scanning an array

- sequential scan
- CA algo takes $\lceil N/B \rceil \le N/B + 1$
- CO algo can take an additional read (e.g. array of size on the edge of 2 blocks).

## Mergesort

Time complexity:
- we pass each $\lceil \log N \rceil$ times (sort sets of 2, 4, 8...)
- each pass takes $\Theta(N)$ time
- reaching overall complexity of $\Theta(N \log N)$

I/O complexity:
- a single pass requires $O(T/B + 1)$ reads, where 
  - $T$ is number of items
  - $B$ is number of items per block
- since we need $\log N$ passes, we reach I/O complexity of $O(T/B\cdot \log N + \log N)$
- and since "$+1$" is only significant when $N < B$, we can fit it in cache in one go and don't need the $\log$ passes, so we get $O(T/B\cdot \log N + 1)$

## Multi-way Mergesort

Time complexity:
- K-way mergesort combines $K$ runs at the same time, decreasing passes to $\lceil \log_K N \rceil = \lceil \log N / \log K \rceil$
- in each step a minimum of $K$ items has to be found. Can be done in heap, reaching $\Theta(\log K)$ for each _Min()_.
- that takes $\Theta(T \log K)$ for $T$ items
- making Mergesort run in $\Theta(N \log K \cdot \log N / \log K) = \Theta(N \log N)$ for any $K$

I/O complexity:
- if cache is large enough, I/O complexity is $O(T/B + K)$. $K$ is for when all runs are in separate blocks. It can be run in continuous memory getting $O(T/B + 1)$.
- with $\log_K N$ passes we get total I/O complexity $O(N/B\cdot \log N / \log K + 1)$

- how high can $K$ go?
- Need $K+1$ blocks for scan (+ result block)
- $K-1$ blocks are enough for the heap
- so we need $M \ge 2BK$
- if we know $M$ and $B$ in advance, we set $K = \frac{M}{2B}$ for best efficiency
- that way we get I/O complexity $O(N/B\cdot \log N / \log \frac{M}{B} + 1) =$

## Matrix transposition

- usually stored in row-major mode
- read by rows then takes $O(N^2/B+1)$ reads
- read by cols can reach $\Theta(N^2)$
- transposing by triangles is no good then, as we access the other by columns

### Tiling algorithm

- split matrix to $d \times d$ tiles, possibly rectangular
- we get $\lceil N/d \rceil^2 \le (N/d + 1)^2 \in O(N^2/d^2 + 1)$ tiles
- if $B | N$, we can align matrix to block, so every row starts with new block
- if we set $d = B$, each row of block is contained in a block too, meaning we could process tile in $O(B)$ I/O ops for large enough cache
- for $N^2/B^2$ blocks that means $O(N^2/B)$ reads

Requirements:
- we need to work with 2 tiles at once, so $M \ge 2B^2$, referred to as **tall-cache property**
- more generally tall cache has $M \in \Omega(B^2)$
- for any constant in $\Omega$ we can simply split the tile to a small enough subtiles and algo won't change asymptotically.

Non-aligned:
- if $B \nmid N$, we lose alignment but it's still fine
- if e.g. $B \times B$ block's row spans 2 blocks, that's $2B$ I/O ops, still $\in O(B)$
- cache has to contain $4B^2$ items this way

### Cache-oblivious

![](res/matrix_trans_co.png)

- using **divide and conquer** strategy
- recursively split matrix into 4 submatrices, transpose individually
- altogether $n^2$ operations
- altogether $O(n^2/B)$ I/O ops
- assumes **tall-cache property**

#### Proof

![](res/matrix_trans_co_proof.png)
- at some point we reach submatrices which can fit into $B$ blocks
- there is $\frac{N}{B/2} \times \frac{N}{B/2} = 4 \frac{N^2}{B^2}$ such submatrices
- resulting in $4\frac{N^2}{B^2} \cdot B = 4\frac{N^2}{B}$ I/O ops

## Saving tree in memory

![](res/matrix_trans_co_tree.png)
- saved row-wise like a normal heap
- traversing path takes $\log \frac{N}{B}$ I/O transfers

## Van Emde Boas tree

![](res/veb_tree.png)

- each tree recursively branching into $\sqrt{n}$ subtrees of size $\sqrt{n}$
- each subtree is saved in continuously in memory
- tree of size $\le B$ saved in $O(1)$ continuous blocks

### Traversing

![](res/veb_tree_traverse.png)

- path goes through $\le \log N / \log \sqrt{B}$ subtrees
- altogether $O(\frac{\log N}{\log B})$ I/O transfers

### Sorting

- Mergesort takes $O(\frac{n}{B} \log n)$ I/O transfers
- K-way mergesort takes $O(\frac{n}{B} \log_K n)$ I/O transfers
- Funnelsort takes $O(\frac{n}{B} \log_{M/B} n)$ I/O transfers

# Cache management

- which block to throw out from cache when full?
  - we don't know the future
  - asociative cache

## Sleator-Tarjan (1985)

- **LRU strategy**
  - throw out block not used the longest
- **OPT strategy**
  - throw out block which will not be needed the longest
  - requires knowing the future

For sequence of accesses $s_1,\dots,s_N$:
$$
    C_{LRU} \le \frac{M_{LRU}}{M_{LRU}-M_{OPT}} \cdot C_{OPT} + M_{LRU}
$$
where $M$ is size of the cache (in blocks), $C$ is number of cache misses.

Proof:

- split sequence $s_1,\dots,s_N$ to $K$ groups, such that each group requires $M_{LRU}$ unique blocks (except for the last one if there's not enough accesses).
- LRU has at most $M_{LRU}$ misses on each group
  - has to load all $M_{LRU}$ unique blocks, but might some have loaded already
- OPT has at least $M_{LRU} - M_{OPT}$ misses on each group
  - it might have prepared for as many reads as possible, but still can't contain all blocks if its size is smaller than $M_{LRU}$
- $C_{LRU} \le M_{LRU} \cdot K$
  - at worst it missed each of $M_{LRU}$ unique blocks in each of $K$ groups
  - but it probably had some stored from previous runs by luck
- $C_{OPT} \ge (M_{LRU} - M_{OPT}) \cdot (K - 1)$
  - because of the 3rd point
  - $K-1$ just because last group isn't guaranteed to have $M_{LRU}$ unique blocks (that's basically why M_{LRU} is added in the inequality itself)
- putting together we get:
$$
    \frac{C_{LRU}}{M_{LRU}} \le K = \frac{C_{OPT}}{(M_{LRU}-M_{OPT})} + 1
$$

# Hashing

## Dictionary problem

- universe $U$, $S \subseteq U$, $|S|=n$
- we want to represent $S$
- supported ops:
  - Find(x), Member(x)
  - Insert(x)
  - Delete(x)

### Naive solution

- array of size $|U|$
- info about $x$ at index $x$.

### Better solution

- hashing in array of size $m \ge n$
- $h: U \rightarrow \{1,\dots,m\}$ hashing function
- $x$ stored at $h(x)$
- issue - **collision**: $x \neq y \wedge h(x) = h(y)$

## Solving collisions

### Separate hashing

![](res/hashing_chaining.png)

- hash tables elements are linked lists (or other types of data structures like BST)
- if collision happens, new item is appended at the end of that list

### Linear probing

- Insert(x) - upon collision insert at nearest free higher index
- Find(x) - search from h(x) to first empty space

### Double hashing

- Insert(x) - save at position $h_1(x) + i \cdot h_2(x)$
  - if collision, increment $i$ and repeat
  - $h_1, h_2$ must be independent

## Balls & Bins

- $n$ balls, $n$ bins, balls thrown randomly

---

$$
    Pr[b_i=0] = Pr[\textrm{"i-th bin is empty"}] = \left(1 - \frac{1}{n}\right)^n
$$
- as $n \rightarrow \infty$
$$
    Pr[b_i=0] \approx 1/e
$$

---

$$
    Pr[b_i=k] = Pr[\textrm{"i-th bin has k balls"}] = {n \choose k} \left(\frac{1}{n}\right)^k\left(1-\frac{1}{n}\right)^{n-k}
$$
  - if $k \ll n$
$$
    Pr[b_i=k] \approx \frac{n^k}{n!}(\cdot\frac{1}{n})^k\cdot\frac{1}{e}=\frac{1}{ek!}
$$

---

- for $k \in O\left(\frac{\log n}{\log\log n}\right)$
$$
    Pr[\exists i: b_i=k]Pr[\textrm{"exists bin with k balls"}] \le \frac{1}{n^2}
$$
- moreover with large probability each bin has $\Theta\left(\frac{\log n}{\log\log n}\right)$ balls
- because:
  - $k! \le k^k$
$$
    Pr[\exists i: b_i=k] \le n \cdot Pr[b_i=k] \le \frac{n}{ek!} \le \frac{n}{ek^k}
$$
and as
$$
    k^k = \left(\frac{\log n}{\log\log n}\right)^{\left(\frac{\log n}{\log\log n}\right)}
$$
$$
        = 2^{\log\left(\frac{\log n}{\log\log n}\right)\cdot\left(\frac{\log n}{\log\log n}\right)}
$$
$$
        \approx 2^{c \log n}
$$
$$
        = n^c
$$
we get
$$
    Pr[\exists i: b_i=k] \le \frac{n}{en^c} \in O\left(\frac{1}{n^{c-1}}\right)
$$

## Linear Probing - analysis

- define $P(k,s)$ that for inserted $x$
  - the first free position following $h(x)$ is at $h(x) + k$
  - the first free position preceding $h(x)$ is at $h(x) - s - 1$
$$
    P(k,s) = {n \choose k+s}\left(\frac{k+s}{m}\right)^{k+s}
$$
- in other words, $k+s$ items are stored at $k+s$ positions from all $n$ buckets

> Note:
> $$
>   {n \choose k} = \frac{n!}{k!(n-k)!}=\frac{n(n-1)\dots(n-k+1)}{k!} \le \frac{n^{k}}{k!}
> $$

$$
    P(k,s) \le \frac{n^{k+s}}{(k+s)!}\cdot\frac{(k+s)^{k+s}}{m^{k+s}} \\
$$
$$        
    = \left(\frac{n}{m}\right)^{k+s}\cdot\frac{(k+s)^{k+s}}{(k+s)!} \\
$$
> Note: Stirling's approximation
> $$
>    n! \sim \sqrt{2\pi n} \left(\frac{n}{e}\right)^n
> $$
> as $n \rightarrow \infty$
$$
    \approx \left(\frac{n}{m}\right)^{k+s}\cdot\frac{1}{\sqrt{k+s}}\cdot e^{k+s}\cdot\frac{1}{\sqrt{2\pi}}
$$
- if we set $m \ge 3n$
$$
    \le \left(\frac{e}{3}\right)^{k+s}\cdot\frac{1}{\sqrt{(k+s)2\pi}}
$$
- and then we can calculate **expected value of time of search**:
$$
    EX \le \sum_{k\ge 1} k \cdot Pr["\textrm{search takes time k}"]
$$
$$
    \le \sum_{k\ge 1} k \cdot \sum_{s \ge 0} \left(\frac{e}{3}\right)^{k+s}\cdot\frac{1}{\sqrt{(k+s)2\pi}}
$$
> Note:
> $$
>   \sum_{s \ge 0} \left(\frac{e}{3}\right)^{k+s}\cdot\frac{1}{\sqrt{(k+s)2\pi}}
>   \le \left(\frac{e}{3}\right)^k \sum_{s \ge 0} \left(\frac{e}{3}\right)^s
>   \le O\left(\left(\frac{e}{3}\right)^k\right)
> $$
> as $\frac{1}{\sqrt{(k+s)2\pi}} \le 1$ and $\sum_{s \ge 0} \left(\frac{e}{3}\right)^s$ is finite.

## Balls & Bins with choice

- $n$ balls, $n$ bins
- for each ball select $2$ bins and throw in the emptier
- expected bin fullness is $O(\log \log n)$

## Cuckoo hashing

![](res/hashing_cuckoo.png)

- 2 hashing functions $h_1, h_2$
```
Insert(x):
    if T[h1(x)] = x or T[h2(x)] = x then
        return

    pos = h1(x)
    loop n times:
        if T[pos] is empty then
            T[pos] = x
            return
        
        swap(x, T[pos])

        if pos = h1(x) then
            pos = h2(x)
        else
            pos = h1(x)
    
    rehash()
    insert(x)
```

- Find(x) $\in O(1)$ - worst case
- Delete(x) $\in O(1)$ - worst case
- Insert(x) $\in O(1)$ - average case
- works best for $m \ge 2n$
- rehashing can occur after as little as $6 \log n$ cuckoo inserts

## Iceberg hashing

- uses linear probing with **B-tree** as a complementary data structure
- sets $m = \frac{n}{\log^3 n}$
- uses 2 hash functions
  - $h_1: U \Rightarrow [m]$
    - maps items to buckets
  - $h_2: U \Rightarrow [\log^{10}n]$
    - used to organize within B-trees (buckets)
    - $h_2(x)$ has $\log(\log^{10}n) = 10\log\log n$ bits
    - that ensures each key in B-trees fit in $O(\log n)$ bits in memory
- for B-tree sets $B = \sqrt{\log n}$
- each B-tree can hold up to $\log^3 n + C \log^2 n$ for well chosen constant $C$
  - when keys are inserted into full tree, some are put into "**backyard**"
- $Pr[\textrm{"i-th bucket overflows"}] \le 1/n^{c'}$
  - where $c'$ is dependent on chosen $C$
  - via Chernoff bounds
- when collision happens within a B-tree, both items are put into **backyard
$$
  Pr[\textrm{"collision happens in i-th bucket"}] \\
  \le {\log^3 n + C \log^2 n \choose 2} \cdot \frac{1}{\log^{10} n} \\
  \le O\left(\frac{1}{\log^4 n}\right)
$$

# Working with strings

- working with strings
- $T \in \Sigma^m$ ... text
- $P \in \Sigma^n$ ... searched word
- Task: 
  - find all occurences of $P$ in $T$
- Naive solution: 
  - Aho-Corsick
    - construct FSA from $P$ in time $O(n+m)$
- Issue:
  - Usually $T$ is fixed and big
  - There are many $P$'s, ineffective

## Suffix array

- for string $T$ set of suffixes $T[0..m], T[1..m], \dots, T[m..m]$
- sorted lexicographically, giving function $S$, such that 
  - $S[i]=j$ means $i$-th suffix of $T$ is lexicographically ordered in $j$-th position
> Example:
> 
> $T = banana$  
> | i |  T[i..6]  |   S[i]    |
> |:-:|:---------:|:---------:|
> | 0 | banana    | $\lambda$ |
> | 1 | anana     | a         |
> | 2 | nana      | ana       |
> | 3 | ana       | anana     |
> | 4 | na        | banana    |
> | 5 | a         | na        |
> | 6 | $\lambda$ | nana      |

### Search

- binary search $P$ in sorted suffix array in time $O(n \log m)
  - $\log m$ pivots with at most $n$ chars comparison

### Budovanie suffixového poľa

- možno vybudovať pomocou **prefix doubling algoritmu** 
  - definujme zotriedenie $\gamma \le_k \delta \Leftrightarrow \gamma[0..k] \le \delta[0..k]$
    - analogicky pre rovnosť
    - teda sú k-rovné/menšie, práve keď ich prefixy dĺžky $k$ sú rovné/menšie
  - následne vykonáme $O(\log m)$ prechodov, v ktorých zoradíme sufixy podľa ich prefixov dĺžky $k = 2^i$
  - $i$-ty prechod teda usporiada sufixy podľa $\le_{2^i}$
  - v $0$. prechode zoradíme sufixy podľa ich prvého znaku pomocou nejakého sort algoritmu v čase $O(m \log m)$
  - v $i+1$. kroku máme sufixy zoradené podľa $\le_{2^i}$, chceme zoradiť podľa $\le_{2^{i+1}}$
    - tu možno použiť práve fakt, že slová na sebe závisia.
    - $
  a_i \le_{2k} a_j \Leftrightarrow (a_i <_k a_j) \vee ((a_i =_k a_j) \wedge (a_{i+k} \le_k a_{j+k}))$
    - teda, 2 nezoradené sufixy možno zoradiť rekurzívne, keďže bez prefixov sú to "iné sufixy" toho istého slova, ktoré sú už podľa $\le_k$ zoradené

> Example:  
> $T = tatar$  
> suffixes $= [tatar, atar, tar, ar, r, \lambda]$  
> - $i = 0$:
>   - $\lambda\le_1\{atar,ar\}\le_1r\le_1\{tatar, tar\}$
> - $i = 1$:
>   - $\lambda \le_2 ar \le_2 atar \le_2 r \le_2 \{tatar, tar\}$
>     - $ar \le_2 atar \Leftarrow (ar =_1 atar) \wedge (r \le_1 tar)$
> - $i = 2$:
>   - $\lambda \le_4 ar \le_4 atar \le_4 r \le_4 tar \le_4 tatar$
>     - $tar \le_4 tatar \Leftarrow (tar =_2 tatar) \wedge (r \le_2 tar)$

- teda potrebujeme $O(\log m)$ prechodov, v každom sortovať v čase $O(m \log m)$
- dokopy $O(m \log^2 m)$
- to možno vylepšiť pomocou [Karp Miller Rosenberg](https://dl.acm.org/doi/10.1145/800152.804905) bucket sortom
  - tým znížime čas sortovania na $O(m)$, teda celkovo na $O(m \log m)$

# Parallelism

- multi-processing environment

## Locks

### Race conditions

- issue caused by unfortunate ordering of operations of muiltiple processes
  ```
  proc GlobalInc
  1. t <- cnt
  2. t <- t + 1
  3. cnt <- t
  ```
  - for 2 processes A,B the order of operations (A1, B1, B2, A2, B3) results in B having `cnt` incremented by 2

### Mutex

- used to resolved race conditions
- also called **synchronization primitives**, common of which is **mutex** (mutual exclusion, or simply lock)
  - mutex supports operations:
    - Lock
      - if unlocked, lock
      - if already locked, first wait until unlocked (expected to be done by the "locker")
    - Unlock
      - if locked, unlock
      - if unlocked, crash (expected not to happen at all)
- typically an instance of data structure has its own mutex. This makes operations on top of it **atomic** - once initiated, cannot be interrupted by other processes

### Deadlock

- issue potentially caused by mutexes
- caused by specific sequences of atomic operations
  ```
  proc AtomicMove
  1. lock(A)
  2. lock(B)
  3. A.delete(x)
  4. B.insert(x)
  5. unlock(B)
  6. unlock(A)
  ```
- for 2 processes X,Y in which X wants to move from A to B, Y wants to move from B to A, the sequence (X1, Y1) results in the 2 processes blocking each other
- these can be recognized by **dependency graphs**
  - directed graphs
  - vertices are processes
  - edge $(i,j)$ show that process $i$ waiting for mutex locked by $j$
  - directed cycles here mean **deadlocks**
- we can **order** mutexes topologically to insure there's always at least one process not being blocked
  - this can not always be done for example if the edges can be set dynamically by the data (e.g. data contains its destination)

# Geometric Data Structures

- multi-dimensional items
  - points, lines, polygons... in $R^d$
- queries can ask for **region**
  - a single object - analogic to lookup
  - a range - $d$-dimensional box, potentially infinite in some directions
  - partial match - bounds only some dimensions (e.g. $x=3 \vee z=5$)
  - polygon
- queries can return all items or their count

## 1-D

### K-D trees

- binary search trees
- used to store $K$-dimensional data
- $K = 1$
  ![](res/kd_tree_1d.png)
  - each internal node sets a **split point** between its subtrees
  - range query again $O(\log n + k)$ in **balanced** tree
    - find **splitting points** $(x,x')$ such that $x$ not in range, $x'$ in range both for min and max in time $O(\log n)$
    - enumerate all nodes inbetween in time $O(k)$, taking advantage of paths to splitting points
- $K = 2$
  ![](res/kd_tree_2d.png)
  - assume no points share the same axis (e.g. we can rotate all or shift by small $\epsilon$)
  - internal nodes split space in the following way
    - **odd-level** nodes split items based on their position on $X$ axis
    - **even-level** nodes split items based on their position on $Y$ axis
  - tree height is $\log n \pm 1 \in O(\log n)$
  - **Construction**
    - need $O(\log n)$ passes
    - in each, find median for each group, summed in time $O(n)$
    - together construct in time $O(n \log n)$ in $O(n)$ space
  - **Find**
  ```
  Find(v : vertex, r : interval)
    In:
      v .. vertex of kd-tree
      r .. desired interval
    Out:
      all vertices lying in r from subtree of v

    if v is leaf then
      if v in r then
        return [v]
      else
        return []
    
    if all vertices of subtree of l(v) in r then
      return subtree of l(v)
    if some vertices of subtree of l(v) in r then
      return Find(l(v), r)

    if all vertices of subtree of r(v) in r then
      return subtree of r(v)
    if some vertices of subtree of r(v) in r then
      return Find(r(v), r)
  ```
    - time complexity
      - return whole subtree in $O(k)$ time
      - recursion in $O(\sqrt n + k)$ time
        - why $\sqrt n$ you ask
        - consider thick strip "avoiding" all leafs
        - each odd level takes one path
        - each even level takes both paths
        - at leaf level $t$ nothing gets returned
        - but we still branched in recursion by $2$ in every $2$-nd level, reaching $2^{t/2} = \sqrt{2^t} \approx \sqrt n$ leafs
        ![](res/kd_tree_2d_complexity.png)
        - generally, for $K \ge 2$ using similar range query (small for one dimension, avoiding all data), we branch in recursion at all but every $K$-th level of the tree, reaching $2^{t - t/K} \in O(n^{1-1/K})$ leafs.

### Range trees

- instead of alternating between axes, range tree sorts by the first axis with each vertex connecting to another tree which sorts its corresponding points according to their second dimension, etc.
![](res/range_tree.png)
- Search
  - $O(\log^d n)$
    - passes tree of size $n$ for each of $d$ dimensions
- Space
  - $O(n \log^{d-1} n)$
- Range
  - $O(\log^{d-1} n)