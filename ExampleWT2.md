# Example WT 2

## Table of contents

[TOC]



## Question 1

Consider a MapReduce algorithm that partitions N objects into $\sqrt{N}​$ subsets by assigning to each object a random integer key in [0,$\sqrt{N}​$) chosen with uniform probability and independently of the other objects. For every integer x ∈ [0, $\sqrt{N}​$), let $m_x​$ be the number of objects with assigned key x.
(a) For an arbitrary x ∈ [0,$\sqrt{N}​$), give an upper bound to Pr( $m_x ≥ 6​$$\sqrt{N}​$). (Use the Chernoff bound that states that, for a Binomal random variable $U​$ of expectation $µ​$ and for every $δ​$ ≥ 5, it holds that                      Pr( $U​$ ≥ (1 + $δ​$)$µ​$) ≤ $2^{-(1+δ)µ} ​$.)
(b) Using the previous point, show that with high probability $m_x​$ ∈ O($\sqrt{N}​$) for every x ∈ [0,$\sqrt{N}​$).

### Solution

(a) Let $X​$ be binomial random variable over N elements with $1/\sqrt{N}​$ probability, with expectation $E(X) = np = N * 1/\sqrt{N} = \sqrt{N}​$. Applying the Chernoff bound with $δ = 5​$ we obtain: 

$Pr( m_x ≥ 6\sqrt{N}) ≤ 2^{-6\sqrt{N}} ​$

(b) Using $Pr( m_x ≥ 6\sqrt{N}) ≤ 1/2^{6\sqrt{N}} $ we obtain that  $Pr( m_x  ≥ 6\sqrt{N}) ≤ 1/N^6$ (Credo perchè esponenziale sempre maggiore di polinomiale) . Hence $Pr( m_x <  6\sqrt{N}) ≤ 1 - 1/N^6$, when $N >>= 1$ this probability is almost equal to 1, therefore $m_x$ = O($\sqrt{N}$)  with H I G H  P R O B A B I L I T Y.

## Question 2

Briefly describe a generic iteration of the PAM algorithm for the k-median clustering problem

### Solution

If the stopping criteria is not yet met, for each point $p$ in the point set except the chosen centers try to swap each center with $p$ and for each swap calculate the new value of the k-median partition. If the swap leads to an improvement of the objective function, perform it and iterate again, otherwise keep searching until all pairs (point-center) are tested.

## Question 3

### Solution



## Question 4

### Solution

GIGI

## Exercise 1

### Description

Let $P​$ be a set of $N​$ points in a metric space $(M, d)​$, and let $C = (C_1 , C_2 ,..., C_k ; c_1 , c_2 ,..., c_k)​$ be a k-clustering of $P​$. Initially, each point $q ∈ P​$ is represented by a pair $(ID(q), (q, c(q)))​$, where $ID(q)​$ is a distinct key in $[0, N − 1]​$ and $c(q) ∈ \{c_1 ,..., c_k\}​$ is the center of the cluster of $q​$.

1. Design a 2-round *MapReduce* algorithm that for each cluster center $c_i$ determines the most distant point among those belonging to the cluster $C_i$ (ties can be broken arbitrarily).
2. Analyze the local and aggregate space required by your algorithm. (For full score, your algorithm must require $o(N)$ local space and $O(N)$ aggregate space).

### Solution

**Round 1**

- *Map*: $(ID_q, (q, c(q))) \longrightarrow (ID_q \mod \sqrt{N}, (q, c_q, d(q, c_q)))​$ where $d​$ is the distance function and $c_q=c(q)​$.
- *Reduce*: gather by key the at most $\sqrt{N}​$ pairs in each subset $P^j​$ and for each point in the same cluster (with the same center $c_q​$) produce a single pair $(c_p, (p, d_p))​$ where  $p​$ is the point with the maximum distance from $c_p​$ between all points in $P^j​$ that are in the same cluster (break ties arbitrarily). 
  Formally $p=\arg\!\max_{q\in P^j_{c_q}}\{d(q,c_q)\}​$ and  $d_p=d(p, c_p)​$ where $P_{c_q}^j​$ is the subset of points in $P^j​$ belonging to the same cluster with center $c_q​$.
  Note that after this phase there will be at most $\sqrt{N}​$ points with each key $c_i​$ for $1 \le i \le k​$ (since every subset yields at most a pair with key $c_i​$).

**Round 2**

- *Map*: identity.
- *Reduce*: gather the at most $\sqrt{N}$ in each subset $P^i$ and produce a single pair $(0, (m_i, c_i, d_{\max}))$ where $m_i=\arg\!\max_{q\in P^i}\{d_q\}$ and $d_\max=d(m_i, c_i)$ (ties broken arbitrarily). 
  Then $m_i$ is the most distant point among the points belonging to cluster $C_i$.

**Analysis**

The $M_L=O(\sqrt{N}) = o(N)$ since every reduce deals with at most $\sqrt{N}$ pairs and $M_A=O(N)$.



## Exercise 2

### Description

Not yet covered

### Solution