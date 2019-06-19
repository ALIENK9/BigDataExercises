# Exam simulation 5-06-2019

## Table of contents

[TOC]

## Exercise 1

### Description

Let $P$ be a set of $N$ bi-colored points from a metric space, partitioned into $k$ clusters $C_1 , C_2 , . . . , C_k$. Each point $x ∈ P$ is initially represented by the key-value pair $(ID_x , (x, i_x , γ_x ))$, where $ID_x$ is a distinct key in $[0, N − 1]$, $i_x$ is the index of the cluster which $x$ belongs to, and $γ_x ∈ \{0, 1\}$ is the color of $x$.

1. Design an efficient 2-round MapReduce algorithm that for each cluster $C_i$ checks whether all points of $C_i$ have the same color. The output of the algorithm must be the $k$ pairs $(i, b_i)$, with $1 ≤ i ≤ k$, where $b_i = −1$ if $C_i$ contains points of different colors, otherwise $b_i$ is the color common to all points of $C_i$.
2. Analyze the local and aggregate space required by your algorithm. (For full score, your algorithm must require $o(N)$ local space and $O(N)$ aggregate space).

### Solution

1. MR algorithm:

   - **Round 1**

     - *Map phase*: $(ID_x , (x, i_x , γ_x )) \longrightarrow (ID_x \mod \sqrt{N} , (x, i_x , γ_x ))$

     - *Reduce phase*: gather by key each set $P^j$ of $O(\sqrt{N})$ key value pairs. Call $P^j_i$ the set of points $\in P^j$  belonging to the same cluster $i$ ($i_x = i$). 

       Output a pair for each cluster $(i, b_i)$, where $i$ is the index of the cluster $\in [1, k]$ and $b_i = -1$ if $P^j_i$ contained points of different color, otherwise $b_i$ is the color of all the points in $P^j_i$.

       Note that for each partition at most $\sqrt{N}$ pairs will be generated, everyone with a different $i$.

   - **Round 2**

     - Map phase: nothing.
     - Reduce phase: gather by key (cluster index) the at most $\sqrt{N}$ pairs referring to the same cluster $C_i$, and call this set $P_i$. Output a new pair $(i, b_i)$ where $b_i = -1$ if $P_i$ contained points of different color or one of the key value pairs had $b_j = -1$. Otherwise $b_i$ is the color of all points in $P_i$.

2. The algorithm deals at the same time with at most $\sqrt{N}$ pairs, so $M_L=O(\sqrt{N})$. Overall, the first reduce phase must handle $\sqrt{N}$ partitions with $O(\sqrt{N})$ pairs in each, thus $M_A=O(N)$.



## Exercise 2

### Description

Let $T$ be a dataset of $N$ transactions over a set of items $I$, and let $\epsilon > 0$ be a parameter. For each itemset $X$ let $s_X$ be an approximation of its true support $Supp_T(X)$ such that

- $Supp_T(X) − \epsilon ≤ s_X ≤ Supp_T(X) + \epsilon$ 

Consider an ordering of all itemsets $X_1 , X_2 , X_3 , . . .$ such that $s_{X_1} ≥ s_{X_2} ≥ s_{X_3} . . .$, and let $K < K'$ be two positive indexes for which $s_{X_K} > s_{X_{K'}} + 2\epsilon$.

1. Show that for each pair of indexes $i, j ≥ 1$, with $i ≤ K < K' ≤ j$, we have $Supp_T(X_i) > Supp_T(X_j)$
2. Based on the previous points, show that the set $\{X_1 , X_2 , . . . , X_{K'}\}$ contains the Top-K frequent itemsets with respect to the true support.

### Solution

1. Let $s_{X_i}$ and $s_{X_j}$ be the approximated supports for $X_i, X_j$ respectively. Since the itemsets are ordered by support it is true that $s_{X_i} \ge s_{X_K} \ge s_{X_{K'}} \ge s_{X_j}$. In the worst case $s_{X_i}$ could be overestimated by a factor of $\epsilon$ and  $s_{X_K}$ could be equal to $Supp_T(X_K) - \epsilon$. Note that the approximation still guarantees that $Supp_T(X_i) \ge Supp_T(X_K)$.

   Moreover, let's say that $s_{X_K} = Supp_T(X_K) +  \epsilon$ and $s_{X_{K'}} = Supp_T(X_{K'}) - \epsilon$, which is the worst case that could happen. Then the two indexes $K$ and $K'$ ensure that $Supp_T(X_K) +  \epsilon > Supp_T(X_{K'}) - \epsilon + 2\epsilon = Supp_T(X_{K'}) + \epsilon$, which imply $Supp_T(X_{K}) > Supp_T(X_{K'})$.

   This means the following is true: $Supp_T(X_i) \ge Supp_T(X_K) > Supp_T(X_{K'}) \ge Supp_T(X_j)$. $\square$

2. Suppose that it is not true, so it doesn't contain the Top-K frequent itemsets. Then it must exists an index $\ell > K'$ such that $\exists i \in \{1,...,K'\}. Supp_T(X_\ell) > Supp_T(X_i)$. Since the supports are non-increasing this means that $Supp_T(X_\ell) > Supp_T(X_{K'})$. Thus (by point 1) we infer that $\ell \in \{1,...,K\}$, which gives the contradiction (we said in hypothesis that $\ell > K'$).

