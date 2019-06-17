# Example WT 1

## Table of contents

[TOC]

## Question 1

Usually, the execution of a MapReduce application involves a master and several workers (e.g. the driver and the executors in Spark). Briefly define the role of the master and of the workers.

### Solution

At the beginning of a MapReduce computation multiple processes are created:

- A single *master* process which has the responsibility of assigning tasks to the worker processes.
- Several *workers* processes, that execute the map and reduce operations. Every worker process can be *idle*, *in-progress* and *completed*.

Furthermore the master process has to monitor the progress of the workers, thus, if a worker fails, master has to reschedule that task.



## Question 2

With reference to the k-means clustering problem:

1. Point out the negative aspects of the classical Lloyd’s algorithm;
2. Briefly say how algorithm k-means++ improves upon Lloyd’s algorithm.

### Solution

1. The classical Lloyd's algorithm:
  
   1. Starts with $k$ centers selected randomly from the set of points;
   2. Iteratively assign every point to the closest center;
   3. Computes the new center of each cluster;
   4. Stops when there is no substantial change in the value of k-means objective function from the previous iteration.
   
   While the algorithm always terminates, it can do quite a lot of iterations before stopping. It can be proved a lower bound on the number of iteration as $2^{\Omega(\sqrt{N})}$ in the worst case. When $k$ and $D$ (i.e. the dimensionality of input space) are small it has been proven an upper bound of $O(N^{kD})$.
   
   Aside from the number of iterations the main drawback of Lloyd's algorithm is its inability to always converge to the optimal solution, since it can be trapped into a local optimum, whose value of objective function can be larger than the real optimum.
   
2. The quality of Lloyd's algorithm's solution and speed of convergence strongly depend on the choice of the initial set of centers.

   K-means++ is an initialization procedure that returns the $k$ centers not too far from the optimal ones. It iterates $k$ times and each time it selects a point (as center) from the points in set $P$ choosing it with probability proportional to the distance of the point from the rest of centers. 

   Lloyd's algorithm can then improve its performance by using a set of centers retrieved via a k-means++ algorithm execution.
   
   

## Question 3

Consider the mining of frequent itemsets with respect to:

* A dataset $T$ of transactions;

* The set of items $I$;

* A threshold $s$.

For a given $\epsilon> 0$, define the notion of $\epsilon$-approximation of the set of frequent itemsets and their supports, and briefly say why it was introduced.

### Solution

Let:

- $T$ be a dataset of transactions over the set of items $I$;
- $minsup\in(0,1]$ be a support threshold;
- $\epsilon>0$ be a suitable parameter.

A set $C$ of pairs $(X, s_X) $ s.t. $X \subseteq I$ and $s_X \in (0,1]$ is an $\epsilon$ - approximation of the set of frequent itemsets and their supports if the following conditions are satisfied:

- For each $X \in F_{T, minsup}$ there exists a pair $(X, s_X) \in C$. This condition ensures that the approximated set $C$ comprises all true frequent itemsets;

- For each $(X, s_X) \in C$:

- - $Supp(X) \geq minsup - \epsilon$. This ensures that $C$ does not contain itemsets of very low support;
  - $|Supp(X)-s_X| \leq \epsilon$ . This ensures that for each itemset  $X$ such that  $(X, s_X) \in C$, $s_X$ is a good estimate of its support.

The concept of  $\epsilon$ - approximation was introduce in the context of sampling, as it shows that the frequent itemsets from a small sample of $T$ can provide a suitable approximation to the exact set.



## Question 4

Consider the MapReduce algorithm presented in class for computing a Minimum Spanning Forest (MSF) of a graph $G = (V, E)$ with $n$ nodes and $m = n^{1+c}$ edges, with $c > 0$ constant. The algorithm partitions $E$ into $\ell$ subsets $E_1,...,E_\ell$ of $m/\ell$ edges each, computes a MSF $F_i$ independently in each $E_i$, and then computes the final MSF on $∪_i F_i$. Analyze the local space required by the algorithm making a suitable choice for $\ell$.

### Solution

Note that if the input graph has $m=n^{1+c}$ edges, $E$ may be partitioned into $\ell=n^{c/2}$ subsets $E_1, E_2, ... , E_\ell$ of (approximately) the same size.

Let us now analyze the space requirements:

- **Round 1**: each reducer needs local space $M_L=O(m/\ell)=O(n^{1+c/2})$ to store an $E_i$ and compute $F_i$;
- Round 2: since the MST of a connected graph with $n$ nodes has exactly $n-1$ edges, the only active reducer requires local space $M_L=O(n\ell)=O(n^{1+c/2})$.

## Exercise 1

### Description

Let $P$ be a set of $N$ bi-coloured points from a metric space, partitioned into $k$ clusters $C_1 , C_2 , . . . , C_k$.

Each point $x ∈ P$ is initially represented by the key-value pair $(ID_x , (x, i_x , γ_x))$, where:

* $ID_x$ is a distinct key in $[0, N − 1]$;

* $i_x$ is the index of the cluster which $x$ belongs to;

* $\gamma_x ∈ \{0, 1\}$ is the colour of $x$.

1. Design an efficient 2-round MapReduce algorithm that, for each cluster $C_i$, checks whether all points of $C_i$ have the same colour.

   The output of the algorithm must be the $k$ pairs $(i, b_i)$, with $1 ≤ i ≤ k$, where:

   * $b_i = −1$ if $C_i$ contains points of different colours, otherwise

   * $b_i$ is the colour common to all points of $C _i$.

2. Analyze the local and aggregate space required by your algorithm (for full score, your algorithm must require $o(N)$ local space and $O(N)$ aggregate space).

### Solution

**Round 1**

- *Map*:

  - $(ID_x , (x, i_x , γ_x)) \longrightarrow (ID_x \mod \sqrt{N}, (x, i_x, \gamma_x))$

- *Reduce*: 
  
  - Gather by key the at most $\sqrt{N}$ pairs in each subset $P^j$;
  - For each point in the same cluster $i$ produce a single pair $(i, p_i)$ where:
    -  $p_i=\begin{cases}~~~0\iff \gamma_x=0~\forall~x\in P^j_i\\~~~1 \iff \gamma_x=1~\forall~x\in P^j_i\\ -1 \iff \exists x\in P^j_i,~\exists y\in P^j_i.(x\ne y~\and~\gamma_x \ne \gamma_y)\end{cases}$ ;
    - $P_i^j$ is the subset of points in $P^j$ belonging to the same cluster $i$.
  
  Note that after this phase there will be at most $\sqrt{N}$ points, with every key $i$ for $1 ≤ i ≤ k$ (since every subset yields at most a pair with key $i$). Each of at most $\sqrt{N}$ subset will be constituted of at most $k$ pairs, one for each possible cluster index in the subset. 

**Round 2**

- *Map*:
  - Identity.
- *Reduce*:
  - Gather by key the at most $\sqrt{N}$ pairs in each subset $P^i$;
  - Produce a single pair $(i, b_i)$ where $b_i = \begin{cases}~~~0 \iff p_i=0~~\forall ~ (i, p_i)\in P^i \\~~~1 \iff p_i=1~~\forall ~ (i, p_i)\in P^i \\-1 \iff \exists (i, p_j)\in P^i,~\exists (i, p_k)\in P^i.(j\ne k~\and~p_j \ne p_k) \\ \end{cases}$

**Analysis**

The $M_L=O(\sqrt{N}) = o(N)$ since every reduce deals with at most $\sqrt{N}$ pairs and $M_A=O(N)$.



## Exercise 2

### Description

Let $T$ be a dataset of $N$ transactions over a set $I$ of $d$ items and suppose that every itemset $X ⊆ I$ is closed (i.e. every superset $Y ⊃ X$ has smaller support).

Let $X_1, X_2, . . .$ be the sequence of itemsets by non-increasing support, including the empty itemset ($X_1$) which has support $1$. For a given $k> 1$, let $s = Supp_T(X_k)$ and $s' = Supp_T(X_{k+1})$ and assume that $1 > s > s'> 0$.

1. Show that for every itemset $X_j$ of support $s'$ (hence $j ≥ k + 1$), and for every item $a ∈ X_j$, the itemset $X_j \setminus \{a\}$ belongs to $\{X_1, X_2, . . . , X_k\}$.

2. Derive an upper bound to the number of itemsets of support exactly $s'$ and, from this, an upper bound to the number of Top-$(k + 1)$ frequent itemsets.
   
   (Hint: note that the previous point implies that any itemset of support $s'$ is obtained by adding an item to some $X_i$ with $1 ≤ i ≤ k$).

### Solution $\checkmark$

1. By definition of closed itemset, $X_j \supset X_j \setminus \{a\} \implies Supp_T(X_j) < Supp_T(X_j \setminus \{a\}) ~\forall~j  > k,~\forall~a \in X_j$. Since the sequence of itemsets $X_1, X_2, ...$ has non-increasing support, then $X_j \setminus \{a\}$ must precede $X_j$ in this sequence. We know that $s'=Supp_T(X_j)$ ans so $j \ge k+1$ $(Supp(X_j \setminus \{a\}) \ge s > s')$ , so the itemset $X_j \setminus \{a\} \in \{X_1, X_2, . . . , X_k\}$.
2. It's the number of itemsets with support $\ge s$, which are $k$ and the ones with support $s' \ge Supp \ge s$, which are $k \cdot d$. So the number is $k + k \cdot d$.
