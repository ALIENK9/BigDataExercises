# Clustering 2 - exercises

## Table of contents

[TOC]

## Exercise 1 pg 20

### Description

Show how to implement algorithm *k-means++* in *MapReduce* using $O(k)​$ rounds, local space $M_L = O(\sqrt{N})​$ and aggregate space $M_A = O (N)​$.

### Solution

Input: $(ID_x, x)$ 

To do.

**Round 1**

- Map: $(ID, x_i) \longrightarrow (d_{ij},x_i)$ where $d_{ji}=d_{ij}=\max{\{i,j\}}+\min{\{i,j\}}\cdot N$.
- Reduce:

## Exercise 2 pg 20

###Description

Show how to implement one iteration of Lloyd’s algorithm in MapReduce using a constant number of rounds, local space $M_L = O(N)$ and aggregate space $M_A = O(N)$. Assume that at the beginning of the iteration a set $S$ of $k$ centers and a current estimate $\phi$ of the objective function are available. The iteration must compute the partition of $P$ around $S$, the new set $S'$ of $k$ centers, and it must update $\phi​$, unless it is the last iteration.

### Solution

To do.

## Exercise 3 pg 47

### Description

Let $\mathcal{C}_{alg} = Partition(P, S)​$ be the k-clustering of $P​$ induced by the set $S​$ of centers returned by *k-means++*. 
Using Markov’s inequality determine a value $α > 1​$ such that $Pr (\phi_{kmeans}(\mathcal{C}_{alg} ) ≤ \alpha · \phi^{opt}_{kmeans}(k)) \geq 1/2​$.
Recall that for a real-valued nonnegative random variable $X​$ with expectation $E[X]​$ and a value $a > 0​$, the Markov’s inequality states that $Pr(X ≥ a) ≤ \cfrac{E[X]}{a}​$.

### Solution

From theorem in slide 18 we know that   $E[\phi_{kmeans} (\mathcal{C}_{alg})] ≤ 8(ln(k) + 2) · \phi^{opt}_{kmeans}(k)​$.
Using Markov inequality: $Pr(\phi_{kmeans} (\mathcal{C}_{alg}) ≥ a) ≤ \cfrac{E[\phi_{kmeans} (\mathcal{C}_{alg})]}{a}​$ .
Substituting the expectation from theorem above we get:
$Pr(\phi_{kmeans} (\mathcal{C}_{alg}) ≥ a) ≤ \cfrac{E[\phi_{kmeans} (\mathcal{C}_{alg})]}{a} \le \cfrac{8(ln(k) + 2) · \phi^{opt}_{kmeans}(k)}{a}​$. 
Putting $a = \alpha · \phi^{opt}_{kmeans}(k)​$  we obtain:
$Pr(\phi_{kmeans}(\mathcal{C}_{alg})\ge \alpha\cdot \phi^{opt}_{kmeans}(k)) \le \cfrac{8(ln(k) + 2) · \phi^{opt}_{kmeans}(k)}{\alpha\cdot \phi^{opt}_{kmeans}(k)}=\cfrac{8(ln(k)+2)}{\alpha}​$.
Solving the equation we get:
$\cfrac{8(\ln{(k)}+2)}{\alpha} =\cfrac{1}{2} \implies \alpha=16(\ln{k}+2)​$. Note that $\alpha \ge 32​$ for $k\ge 1​$.
This means:
$Pr(\phi_{kmeans}(\mathcal{C}_{alg})\ge \alpha\cdot \phi^{opt}_{kmeans}(k)) \le \cfrac{1}{2}​$    for    $\alpha=16(\ln{k}+2)​$.
And the reverse probability is: $1 - Pr(\phi_{kmeans}(\mathcal{C}_{alg})\ge \alpha\cdot \phi^{opt}_{kmeans}(k)) = Pr(\phi_{kmeans}(\mathcal{C}_{alg})\le \alpha\cdot \phi^{opt}_{kmeans}(k))​$. 
So $Pr(\phi_{kmeans}(\mathcal{C}_{alg})\le \alpha\cdot \phi^{opt}_{kmeans}(k))\ge \cfrac{1}{2}​$   for    $\alpha=16(\ln{k}+2)​$.

## Exercise 4 pg 48

### Description

(This exercise shows how to make the approximation bound of algorithm *k-means++* to hold with high probability rather than in expectation.)
Let $\mathcal{C}_{alg} = Partition(P, S)​$ be the k-clustering of $P​$ induced by the set $S​$ of centers returned by an execution of *k-means++*. Suppose that we know that for some value $α > 1​$ the following probability bound holds:
$Pr(\phi_{kmeans} (\mathcal{C}_{alg}) ≤ \alpha · \phi^{opt}_{kmeans}(k)) ≥ 1/2​$.
Show that the same approximation ratio, but with probability at least $1 − 1/N​$, can be ensured by running several independent instances of *k-means++*, computing for each instance the clustering around the returned centers, and returning at the end the best clustering found (call it $\mathcal{C}_{best}​$), i.e., the one with smallest value of the objective function.

### Solution

Suppose we run a certain number of consecutive *k-means++* and partitioning and each time we test whether the objective function $\phi_{kmeans}$ respect the inequality  $\phi_{kmeans} (\mathcal{C}_{alg}) ≤ \alpha · \phi^{opt}_{kmeans}(k)$, for a known $\alpha >1$. We define a random variable $Y=0 \iff$ the inequality is not respected and 1 otherwise. The probability to have a 1 is $\ge 1/2$.
This defines a Bernoulli distribution $Ber(p\ge1/2)$.
Define then a random variable $X=$("number of $Y=1$ in a series of $M$ trials"). This defines a binomial distribution $Bin(M,p\ge\frac{1}{2})$.
Since at every run of *k-means++* we save the best clustering we need just one run to satisfy the inequality to prove the bound.
Then $Pr(X\ge 1) = 1-Pr(X=0)\ge 1-\binom{M}{0}\cdot(\frac{1}{2})^0\cdot(1-\frac{1}{2})^M=1-\cfrac{1}{2^M}$.
So with at least $M=\lceil\log_2{N}\rceil$ trials the bound is satisfied with probability $\ge 1-\cfrac{1}{N}$.

## Exercise 5 pg 48

### Description

Show that the *PAM* algorithm always terminates.

### Solution

To do.





