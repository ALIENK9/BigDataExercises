# Example WT 2

## Table of contents

[TOC]



## Question 1

Consider a MapReduce algorithm that partitions $N$ objects into $\sqrt{N}$ subsets by assigning to each object a random integer key in [0,$\sqrt{N}$) chosen with uniform probability and independently of the other objects.

For every integer $x ∈ [0, \sqrt{N})$, let $m_x$ be the number of objects with assigned key $x$:

1. For an arbitrary $x ∈ [0, \sqrt{N})$, give an upper bound to:

   $Pr(m_x ≥ 6$$\sqrt{N})$

   **Hint**: use the Chernoff bound that states that, for a binomal random variable $U$ of expectation $µ$ and for every $δ≥ 5$ , it holds that $Pr(U≥ (1 +  δ)µ) ≤ 2^{-(1+δ)µ} $.

2. Using the previous point, show that with high probability $m_x \in O(\sqrt{N})  \forall x \in [0, \sqrt{N})$.

### Solution

1. Let $X$ be a binomial random variable over $N$ elements with:

   * Probability $p=1/\sqrt{N}$;
   * Expectation $µ=E(X) = np = N * 1/\sqrt{N} = \sqrt{N}$.

   Applying the Chernoff bound with $δ = 5$ we obtain: 

   $Pr( m_x ≥ 6\sqrt{N}) ≤ 2^{-6\sqrt{N}} $

2. Using $Pr( m_x ≥ 6\sqrt{N}) ≤ 2^{-6\sqrt{N}} =1/2^{6\sqrt{N}} ≤ 1/N^6$ because exponential functions are asymptotically greater than polynomials. 

   Hence $Pr( m_x <  6\sqrt{N}) ≤ 1 - 1/N^6$, and when $N \gg 1$ this probability is almost equal to 1.

   Therefore $m_x = O(\sqrt{N})$  with 【﻿ｈｉｇｈ　ｐｒｏｂａｂｉｌｉｔｙ】.

## Question 2

Briefly describe a generic iteration of the PAM algorithm for the k-median clustering problem.

### Solution

If the stopping criteria is not met yet, for each point $p \in P/C$ (i.e. the set of points except for the chosen centers) try to swap each center $c$ with $p$ and for each swap calculate the new value of the k-median partition.

If the swap leads to an improvement of the objective function, perform it and iterate again, otherwise keep searching until all pairs (point-center) are tested.

## Question 3

Consider a dataset $T$ of transactions over a set $I$ of items:

1. Define the notion of closed itemset with respect to $T$.

2. Explain briefly, but precisely, in what sense the set of frequent closed itemsets with respect to $T$ and to a support threshold $minsup$, provide a succinct and lossless representation of the frequent itemsets with respect to $T$ and $minsup$.

### Solution

1. Let $X \subseteq I$ be an itemset w.r.t. a dataset $T$.

   Then $X$ is closed iff $Supp(Y)<Supp(X) \forall Y \supset X$. In other words, $X$ is closed iff as soon as a new item is added to it its support decreases.

2. Note that for each $X \subseteq I$, $Supp(X)=max\{Supp(Y) : Y \supseteq X \land Y \in CLO\}$ (i.e. $Y$ is closed).

   Each (frequent) closed itemset $Y$ can be regarded as a representative of all those (possibly many) itemsets $X$ such that $Closure(X)=\bigcup_{t \in T_X} t=Y$.

   From the frequent closed itemsets and their supports one can derive all frequent itemsets and their supports. In this sense, frequent closed itemsets and their supports provide a lossless representation of the frequent itemsets and their supports. 

## Question 4

Let $G$ be a connected undirected graph. Give the definition of neighborhood function $N_G(t)$ and express the diameter of $G$ in terms of $N_G(t)$.

### Solution

The neighborhood function $N_G(t)$ is defined as follows:

* $N_G(0)=0$
* $\forall t \geq 1$, $N_G(t)$ is the number of pairs of nodes $(u,v)=(v,u)$ with $u,v \in V_G$ s.t. $dist(u,v) \leq t$.

Then the notion of diameter of a graph can be expressed as follows:

$diameter(G)=min\{t \geq 1:N_G(t+1)=N_G(t)\}$.

## Exercise 1

### Description

Let $P$ be a set of $N$ points in a metric space $(M, d)$, and let $C = (C_1 , C_2 ,..., C_k ; c_1 , c_2 ,..., c_k)$ be a k-clustering of $P$.

Initially, each point $q ∈ P$ is represented by a pair $(ID(q), (q, c(q)))$, where:

* $ID(q)$ is a distinct key in $[0, N − 1]$, and
* $c(q) ∈ \{c_1 ,..., c_k\}$ is the center of the cluster of $q$.

1. Design a 2-round *MapReduce* algorithm that for each cluster center $c_i$ determines the most distant point among those belonging to the cluster $C_i$ (ties can be broken arbitrarily);
2. Analyze the local and aggregate space required by your algorithm (for full score, your algorithm must require $o(N)$ local space and $O(N)$ aggregate space).

### Solution

**Round 1**

- *Map*:

  - $(ID_q, (q, c(q))) \longrightarrow (ID_q \mod \sqrt{N}, (q, c_q, d(q, c_q)))$ where:
    - $d$ is the distance function, and
    - $c_q=c(q)$.

- *Reduce*:
  
  - Gather by key the at most $\sqrt{N}$ pairs in each subset $P^j$;
  - Of all points in the same cluster (with the same center $c_q$) produce a single pair $(c_p, (p, d_p))$ where $p$ is the point with the maximum distance from $c_p$ between all points in $P^j$ that are in the same cluster (break ties arbitrarily). Formally:
    - $p=\arg\!\max_{q\in P^j_{c_q}}\{d(q,c_q)\}$, and
    - $d_p=d(p, c_p)$ where $P_{c_q}^j$ is the subset of points in $P^j$ belonging to the same cluster with center $c_q$.
  
  Note that after this phase there will be at most $\sqrt{N}$ points for each key $c_i$ for $1 \le i \le k$ (since every subset yields at most a pair with key $c_i$).

**Round 2**

- *Map*:

  - Identity.

- *Reduce*:
  
  - Gather the at most $\sqrt{N}$ in each subset $P^i$ by key (i.e. cluster);
  - For each cluster $i$, produce a single pair $(0, (m_i, c_i, d_{\max}))$ where:
    - $m_i=\arg\!\max_{q\in P^i}\{d_q\}$, and
    - $d_\max=d(m_i, c_i)$ (ties broken arbitrarily). 
  
  Then $m_i$ is the most distant point among the points belonging to cluster $C_i$.

**Analysis**

The $M_L=O(\sqrt{N}) = o(N)$ since every reduce deals with at most $\sqrt{N}$ pairs and $M_A=O(N)$.



## Exercise 2

### Description

See Exercise 5 pag 49 in AssociationAnalysis1 file.

### Solution