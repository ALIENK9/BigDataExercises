# Clustering 3 - exercises

## Table of contents

[TOC]

## Exercise 1 pag 4

### Description

Show that by setting $t ∈ o(N)$, $H(P)$ (Hopkins statistic) can be efficiently computed in *MapReduce*.

### Solution

Input: $N$ pairs in the form $(i, (p_i, f))$ where $i$ is a unique integer $1 \le i < N$ and

- $f=1 \iff p_i\in X$, $X=\{x_0,x_1,...x_{t-1}\}$
- $f=2 \iff p_i\in Y$, $Y=\{y_0,y_1,...y_{t-1}\}$
- $f=0 \iff p_i\in P\setminus X$

**Round 1**

- *Map*: map each pair with $f=0$ in $t$ different pairs $(t\cdot j +(i\mod{t}), (d(x_j, p_i),d(y_j,p_i)))~\forall~j\in [0,t)$.
  Now we have the distance between each point in $X$ and $Y$ and each point in $P$ and $N\cdot t$ pairs.

- *Reduce*: gather by key every subset $P^j$ and produce a single pair $(k, (d_{min}(x_j), d_{min}(y_j)))$, where $d_{min}(x_i)=d(x_i,P^j\setminus \{x_i\})=\min{\{d(x_i,p_i)~~\forall~p_i\in P^j\setminus \{x_i\}\}}$ and $d_{min}(y_i)=d(y_i,P^j\setminus \{y_i\})=\min{\{d(y_i,p_i)~~\forall~p_i\in P^j\setminus \{x_i\}\}}$.
  Note that $|P^j|\le t$.

**Round 2**
At this point we have $t$ sets of $t$ pairs.

- *Map*: map each pair in $(k \div t, (d_{min}(x_j), d_{min}(y_j))$ where $\div$ is the integer division.
- *Reduce*: gather by key the at most $t$ pairs and produce a single pair with the minimum distance for both $x_j$ and $y_j$:  $(k, (d(x_j,P\setminus \{x_j\}), d(y_j,P\setminus \{y_j\})))=(k, (w_j, u_j))$.

**Round 3**
Now we have $t$ pairs containing the distance between each point in $X$ and $P$ and between each point in $Y$ and $P$. All we have to do is to sum these components and compute $H(P)$.

- Map: identity.
- Reduce: gather all the pairs and produce a single pair $\left (0, \dfrac{\sum\limits_{j=0}^{t-1}u_i}{\sum\limits_{j=0}^{t-1}u_i+\sum\limits_{j=0}^{t-1}w_i}\right)$.

**Analysis**

At each round the input is at most $t$ pairs. So $M_L=O(t)$ and aggregate space $O(t\cdot N)$ since totally we have $t$ sets of $N/t$ pairs, each set replicated $t$ times.
Setting $t=o(N)$, like $t=\sqrt{N}$ ensures good solution.



## Exercise 2 pag 13

### Description

Let $P$ be a pointset of $N$ points in a metric space $(M, d)$ and let $C ⊆ P$ be a subset of $P$ of size $n$. Show how to compute $\tilde{d}_{sum} (p, C , t)$ efficiently in *MapReduce*, for all $p \in P$. Assume that

- $t$ and $n$ are known;
- $t ∈ O(\log{N})$;
- each point of $P$ is provided with an *ID* in $[0, N − 1]$ and the points of $C$ are those with *IDs* $\in \{0, 1, . . . , n− 1\}$.
  Note that $n$ can be very large.

### Solution

**Round 1**

- *Map*: extract for each point in $C$ a number in $\{0,1\}$ where $p(1) =\cfrac{t}{n}$. If the extracted number is $0$ discard the pair, otherwise keep it.
  Note that, on average, only $t$ pairs will be kept (it's a binomial distribution with expectation $n\cdot \cfrac{t}{n} = t$).
Then map each of the extracted point in this way: $(i, x_i) \longrightarrow (i, d(p, x_i))$, where $i$ is an index in $\{1, t\}$ and $d$ is the distance function.
  
- *Reduce*: gather all the pairs, which are at most $O(t)=O(\log N)$, and compute the weighted sum obtaining a single pair $(0, \cfrac{n}{t}\sum\limits_{i=1}^t d(p, x_i))$.

**Analysis**
The strategy uses only one round. Reduce phase deals with just $O(t)$ pairs so for the assumption in the task description $M_L=O(\log N)$. Aggregate space $M_A=O(n)$ since map phase has to map each of the initial pairs.

