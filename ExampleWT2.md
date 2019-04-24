# Example WT 2

## Table of contents

[TOC]



## Question 1

### Solution



## Question 2

### Solution



## Question 3

### Solution



## Question 4

### Solution



## Exercise 1

### Description

Let $P$ be a set of $N$ points in a metric space $(M, d)$, and let $C = (C_1 , C_2 ,..., C_k ; c_1 , c_2 ,..., c_k)$ be a k-clustering of $P$. Initially, each point $q ∈ P$ is represented by a pair $(ID(q), (q, c(q)))$, where $ID(q)$ is a distinct key in $[0, N − 1]$ and $c(q) ∈ \{c_1 ,..., c_k\}$ is the center of the cluster of $q$.

1. Design a 2-round *MapReduce* algorithm that for each cluster center $c_i$ determines the most distant point among those belonging to the cluster $C_i$ (ties can be broken arbitrarily).
2. Analyze the local and aggregate space required by your algorithm. (For full score, your algorithm must require $o(N)$ local space and $O(N)$ aggregate space).

### Solution

**Round 1**

- *Map*: $(ID_q, (q, c(q))) \longrightarrow (ID_q \mod \sqrt{N}, (q, c_q, d(q, c_q)))$ where $d$ is the distance function and $c_q=c(q)$.
- *Reduce*: gather by key the at most $\sqrt{N}$ pairs in each subset $P^j$ and for each point in the same cluster (with the same center $c_q$) produce a single pair $(c_p, (p, d_p))$ where  $p$ is the point with the maximum distance from $c_p$ between all points in $P^j$ that are in the same cluster (break ties arbitrarily). 
  Formally $p=\arg\!\max_{q\in P^j_{c_q}}\{d(q,c_q)\}$ and  $d_p=d(p, c_p)$ where $P_{c_q}^j$ is the subset of points in $P^j$ belonging to the same cluster with center $c_q$.
  Note that after this phase there will be at most $\sqrt{N}$ points with each key $c_i$ for $1 \le i \le k$ (since every subset yields at most a pair with key $c_i$).

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