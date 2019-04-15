# Clustering 1 - exercises

## Table of contents

[TOC]

## Exercise 2 pag 26

### Task

Let $|P| = N​$ and suppose that each point $x ∈ P​$ is represented as a key-value pair $(ID_x , (x, f ))​$, where $ID_x​$ is a distinct integer in $[0, N)​$, and $f​$ is a binary flag which is 1 if $x ∈ S​$ and 0 otherwise. 
Design a 1-round *MapReduce* algorithm that implements $Partition(P, S)​$ with $M_L = O (k)​$ and $M_A = O (N)​$.
(Assume that $k​$ and $N​$ are known.)

### Solution

Partition algorithm to perform clustering from a set $S$ of $k$ centers and a set $P$ of points where $S \subseteq P$ has the following pseudocode:

**Partition(P, S)**
Let $S = {c_1 , c_2 , . . . , c_k } ⊆ P$
*for*  $i ← 1$   *to*   $k$  *do*  $C_i ← {c_i}$ 	// Initialize each cluster with its centroid
*for* *each*  $p ∈ P \setminus S$  *do*	    	// For each point that is not a centroid 
	$\ell← argmin_{i=1,k}~\{d(p, c_i )\}$    // Get the index of the cluster whose centroid is closer to $p$ (ties broken arbitrarily)
	$C_\ell \leftarrow C_\ell ∪ \{p\}$			// Assign $p$ to the cluster of the closest centroid 
$C ← (C_1 , C_2 , . . . , C_k ; c_1 , c_2 , . . . , c_k)$	
*return* $C$					    // Return the clustering

The map reduce algorithm is the following:

Input:  $(ID_x , (x, f ))$ where $ID_x \in [0,N)$ and $f = 1 \iff x\in S$
Output: $(ID_x, (x,\ell))$ where $\ell$ is the number of the cluster which $x$ was assigned to.

**Round 1**
*Map*: map each point $x \in P\setminus S$ in $k$ different pair representing the distance between $x$ and each of the $k$ different centroids. This means:  $(ID_x , (x, f )) \longrightarrow (ID_x, (x, c_i, d(x,c_i)))~~~\forall i\in1..k$.
The add $k$ pairs $(ID_{c_i}, (c_i,c_i,0))$ for each of the centroids $c_i~\forall i\in1..k$.
At the end of the round there will be $(N-k)k + k$ different pairs, $k$ pairs for each of the $N-k$ points that are not centroids, plus one pair for each of the $k​$ centroids.

*Reduce*: gather by key $ID_x$, obtaining subsets of at most $k$ pairs each (precisely of either 1 or $k$ pairs), and output the pair $(ID_x, (x, \ell))$ where $\ell = argmin_{i=1..k}\{d(x,c_i)\}$.

**Analysis**
The size of the output of the map phase doesn't count in the analysis on $M_L$, since the pairs need not to be stored in the memory of the worker in charge of this computation. For the reduce phase only $k$ pairs must be handled at the same time, so $M_L=O(k)$ and $M_A=O(N)$.

## Exercise 2 pag 37

### Task

Show that the Farthest-first traversal algorithm can be implemented to run in $O(N · k)​$ time.
**Hint**: make sure that in each iteration *i* of the first for-loop each point $p \in P \setminus S​$ knows its closest center among $c_1, c_2, . . . , c_{i−1}​$ and the distance from such a center.

### Solution

Farthest First Traversal algorithm has the following pseudo-code, assuming $| P| = N$.

$S ← \{c_1\}$ 	// $c_1 ∈ P$ arbitrary point
*for*  $i ← 2$  *to*  $k$  *do*
	Find the point $c_i ∈ P \setminus S$ that maximizes $d(c_i, S)$
	$S ← S ∪ \{c_i\}$
*return* ​$Partition(P, S)​$

Step of finding point not in $S​$ that maximizes distance from set of centers at iteration $i​$ would require computation of $(i-1) * (N-i+1)​$ distances, one for each possible pair of points in $S​$ and $P \setminus S​$. This would be $\sum \limits_{i=2}^{k}[(i-1) * (N-i+1)] = \sum \limits_{i=1}^{k-1}i(N-i) =-\frac{1}{6}(k-1)\cdot k\cdot(2k-3N-1) = O(k^2N)​$  computations in total.

Suppose that each point knows its closer center and knows the distance between them. In this way the algorithm already knows $d(c_i, S)~\forall i \in 1...k​$ and the complexity of the selection operation for a single iteration becomes $N-i+1 = O(N)​$ since at every iteration $i​$ the algorithm need to find the maximum $d(c_i, S)~\forall c_i\in P\setminus S​$ and $|P\setminus S| = N-i+1​$. The final complexity for the for cycle becomes then: $\sum\limits_{i=2}^{k}(N-i+1) = \sum\limits_{i=1}^{k-1} (N-i)=\sum\limits_{i=1}^{k-1}N - \sum\limits_{i=1}^{k-1}i=(k-1)N-k(k-1)\cdot\frac{1}{2}=\frac{1}{2}\cdot (k-1)(2N-k)=O(kN)​$.
The final call to $Partition(P,S)​$ runs in $O(kN)​$ time, so overall time complexity is still $O(kN)​$.

To keep track of the distance of each point from the set of centers wee need to update at each iteration a data structure. The pseudo-code becomes: 

$S ← \{c_1\}$ 	// $c_1 ∈ P$ arbitrary point
$D ← Compute~d(c_i, c_1)$ $\forall~c_i \in P, i \ne 1$ 	// $O(N)$ time complexity
*for*  $i ← 2$  *to*  $k$  *do*
	$c_i \leftarrow$ Search in $D$ for point with max distance $d(c_i, S)$ and return such point $c_i$ 	// $O(N)$ as discussed before
	$S ← S ∪ \{c_i\}$
	Compute distance $\forall~p \in P\setminus S$ from $c_i$ and update $D$ if $c_i$ is closer to $p$ than previous center    // $O(N)$
*return* $Partition(P, S)$	// $O(N)$

Distances is a structure where $D[i].closest​$ returns the closest center to point $c_i​$, and $D[i].distance​$ returns the distance from the closest center.

## Exercise 3 pag 50

### Task

Let $P$ be a set of points in a metric space $(M, d)$, and let $T ⊆ P$. 
For any $k < |T |, |P|$, show that  $\phi^{opt}_{kcenter}(T , k) ≤ 2\phi^{opt}_{kcenter} (P, k)$. Is the bound tight?

### Solution

Boh.

## Exercise 5 pag 51

### Task

Let $P$ be a set of $N$ points in a metric space $(M, d)$, and let $C = (C_1 , C_2 , . . . , C_k ; c_1 , c_2 , . . . , c_k)$ be a k-clustering of $P$. Initially, each point $q ∈ P$ is represented by a pair $(ID(q), (q, c(q)))$, where $ID(q)$ is a distinct key in $[0, N − 1]$ and $c(q) ∈ \{c_1 , . . . , c_k\}$ is the center of the cluster of $q$.

1. Design a 2-round *MapReduce* algorithm that for each cluster center $c_i​$ determines the most distant point among those belonging to the cluster $C_i​$ (ties can be broken arbitrarily).
2. Analyze the local and aggregate space required by your algorithm. Your algorithm must require $o(N)$ local space and $O (N)$ aggregate space.

### Solution

**Round 1**
*Map*: map each point $(ID(q), (q, c(q))) \longrightarrow (ID(q)~\text{mod}~\sqrt{N}, (q,c(q)))$.
*Reduce*: gather by key the subsets $P^j$ and for each different $c(q)$ produce the pair $(ID, (c(q), q_j))$ where $q_j=argmax_{j}\{d(c(q_j),q_j)\}~\forall~q_j\in P^j$.
Note that $ID \in [0, \sqrt{N})$ and each subset $P^j$ produce with the reduce phase a number of pairs $\le |P^j|$.

**Round 2**
*Map*: map each point $(ID, (c(q), q)) \longrightarrow (c(q), (q, d(c(q), q)))$.
*Reduce*: gather by key and produce the pair $(c(q), q_{max})$ where $q_{max}=argmax_{q} \{d(c(q), q)\}$.

## Exercise 6 pag 

### Description

Set $P$ of $N$ points bi-colored. They are partitioned in $k$ clusters.
$\mathbb{D}_x\in [0,N)$, $i_x \in[1,k]$ index of the cluster they belong to, $\gamma_x\in \{0,1\}$ is the color.
Design a 2 round algorithm in MR to check whether every cluster $C_i$ is solid, i.e. that every cluster contains points of the same color.
Output: $\{(i,b_i)~\forall~1 \le i \le k\}$ where $b_i=-1/0/1$ depending on whether points of $C_i$ have different colors, points have color 0 or color 1.

### Solution $\checkmark$

**Round 1**

- Map: $(ID_x, (x,i_x,\gamma_x)) \longrightarrow (ID_x \mod \sqrt{N}, (x,i_x,\gamma_x))​$.
- Reduce: $\forall~\ell \in [0,\sqrt{N})$ gather by key pairs $(\ell, (x,i_x,\gamma_x))$. For each cluster $C_j$ compute $b_j(\ell)=-1/0/1$ if points of $C_j$ in partition $\ell$ have different colors or color 0 or 1. Produce for each cluster the pair $(j, b_j(\ell))$.

**Round 2**

- Map: identity
- Reduce: $\forall~1\le j \le k$ gather pairs $(j, b_j(\ell))$ with $0 \le \ell < \sqrt{N}$ and output the pair $(j, b_j)$ where $b_j = -1 \iff \exists~\ell' \and \ell''.(b_j(\ell')=0~\and~b_j(\ell'')=1)$, $b_j=0 \iff \forall~\ell.b_j(\ell)=0$, $b_j=1 \iff \forall~\ell.b_j(\ell)=1$.

**Analysis**
$M_L=O(\sqrt{N})$, $M_A=O(N)$.