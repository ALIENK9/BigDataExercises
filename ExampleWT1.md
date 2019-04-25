# Example WT 1

## Table of contents

[TOC]



## Question 1

Usually, the execution of a MapReduce application involves a master and several workers (e.g., the driver and the executors in Spark). Briefly define the role of the master and of the workers.

### Solution

At the beginning of a MapReduce computation several processes are created:

- A single *master* process which has the responsibility of assigning tasks to the worker processes.
- Several *workers* processes, that execute the map and reduce operations. Every worker process can be *idle*, *in-progress* and *completed*.

Furthermore the master process has to monitor the progress of the workers, thus, if a worker fails, master has to reschedule that task.



## Question 2

With reference to the k-means clustering problem:

1. Point out the negative aspects of the classical Lloyd’s algorithm.
2. Briefly say how algorithm k-means++ improves upon Lloyd’s algorithm.

### Solution

1. Classical Lloyd's algorithm starts with $k$ centers selected randomly fro the set of points and iteratively assign every point to the closest center and then computes the new centers of each cluster. It stops when there is no substantial change in the value of k-means objective function from the previous iteration.
   While the algorithm always terminates it can do quite a lot of iterations before stopping. It can be proved a lower bound on the number of iteration as $2^{\Omega(\sqrt{N})}$ in the worst case. When $k$ and $D$ (dimensionality of input space) are small it has been proven an upper bound of $O(N^{kD})$.
   Aside from the number of iterations the main drawback of Lloyd's algorithm is its inability to always converge to the optimal solution, since it can stop on a local optimum, whose value of objective function can be larger than the real optimum.
2. The quality of Lloyd's algorithm's solution and speed of convergence strongly depend on the choice of the initial set of centers. K-means++ is an initialization procedure that returns the $k$ centers not too far from the optimal ones. It iterates $k$ times and each time it selects a point (as center) from the point in set $P$ choosing each point with probability proportional to the distance of the point from the rest of centers. Lloyd's algorithm can then improve the solution.



## Question 3

Consider the mining of frequent itemsets with respect to a dataset $T$ of transactions over the set of items $I$ and a support threshold $s$. For a given $\epsilon> 0$, define the notion of $\epsilon$-approximation of the set of frequent itemsets and their supports, and briefly say why it was introduced.

### Solution

Not yet covered



## Question 4

Consider the MapReduce algorithm presented in class for computing a Minimum Spanning Forest (MSF) of a graph $G = (V, E)$ with $n$ nodes and $m = n^{1+c}$ edges, with $c > 0$ constant. The algorithm partitions $E$ into $\ell$ subsets $E_1,...,E_\ell$ of $m/\ell$ edges each, computes a MSF $F_i$ independently in each $E_i$, and then computes the final MSF on $∪_i F_i$. Analyze the local space required by the algorithm making a suitable choice for $\ell$.

### Solution

Not yet covered



## Exercise 1

### Description

Let $P$ be a set of $N$ bi-colored points from a metric space, partitioned into $k$ clusters $C_1 , C_2 , . . . , C_k$. Each point $x ∈ P$ is initially represented by the key-value pair $(ID_x , (x, i_x , γ_x))$, where $ID_x$ is a distinct key in $[0, N − 1]$, $i_x$ is the index of the cluster which $x$ belongs to, and $\gamma_x ∈ \{0, 1\}$ is the color of $x$.

1. Design an efficient 2-round MapReduce algorithm that for each cluster $C_i$ checks whether all points of $C_i$ have the same color. The output of the algorithm must be the $k$ pairs $(i, b_i)$, with $1 ≤ i ≤ k$, where $b_i = −1$ if $C_i$ contains points of different colors, otherwise $b_i$ is the color common to all points of $C _i$.
2. Analyze the local and aggregate space required by your algorithm. (For full score, your algorithm must require $o(N)$ local space and $O(N)$ aggregate space.)

### Solution

**Round 1**

- *Map*: $(ID_x , (x, i_x , γ_x)) \longrightarrow (ID_x \mod \sqrt{N}, (x, i_x, \gamma_x))$
- *Reduce*: gather by key the at most $\sqrt{N}$ pairs in each subset $P^j$ and for each point in the same cluster $i$ produce a single pair $(i, p_i)$ where $p_i=\begin{cases}~~~0\iff \gamma_x=0~\forall~x\in P^j_i\\~~~1 \iff \gamma_x=1~\forall~x\in P^j_i\\ -1 \iff \exists x\in P^j_i,~\exists y\in P^j_i.(x\ne y~\and~\gamma_x \ne \gamma_y)\end{cases}$ where $P_i^j$ is the subset of points in $P^j$ belonging to the same cluster $i$.
  Note that after this phase there will be at most $\sqrt{N}$ points with (for?) each key $i$ for $1 ≤ i ≤ k$ (since every subset yields at most a pair with key $i$).

**Round 2**

- *Map*: identity.
- *Reduce*: gather the at most $\sqrt{N}$ in each subset $P^i$ and produce a single pair $(i, b_i)$ where $b_i = \begin{cases}~~~0 \iff p_i=0~~\forall ~ (i, p_i)\in P^i \\~~~1 \iff p_i=1~~\forall ~ (i, p_i)\in P^i \\-1 \iff \exists (i, p_j)\in P^i,~\exists (i, p_k)\in P^i.(j\ne k~\and~p_j \ne p_k) \\ \end{cases}$

**Analysis**

The $M_L=O(\sqrt{N}) = o(N)$ since every reduce deals with at most $\sqrt{N}$ pairs and $M_A=O(N)$.



## Exercise 2

### Description

Not yet covered

### Solution