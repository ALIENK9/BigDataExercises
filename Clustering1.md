# Clustering 1 - exercises

## Table of contents

[TOC]

## Exercise 2 pag 26

### Description

Let $|P| = N$ and suppose that each point $x ∈ P$ is represented as a key-value pair $(ID_x , (x, f ))$, where:

* $ID_x​$ is a distinct integer in $[0, N)​$, and
* $f$ is a binary flag which is $1$ if $x ∈ S$ and $0$ otherwise.

Design a 1-round *MapReduce* algorithm that implements $Partition(P, S)$ with:

* $M_L = O (k)$;
* $M_A = O (N)$.

(Assume that $k​$ and $N​$ are known).

### Solution

Partition algorithm to perform clustering from a set $S$ of $k$ centers and a set $P$ of points where $S \subseteq P$ has the following pseudocode:

**Partition(P, S)**

Let $S = {c_1 , c_2 , . . . , c_k } ⊆ P​$

*for*  $i ← 1$   *to*   $k$  *do*  $C_i ← {c_i}$ 	*// Initialize each cluster with its center*
*for* *each*  $p ∈ P \setminus S$  *do*	    	 *// For each point that is not a center* 
	$\ell← argmin_{i=1,k}~\{d(p, c_i )\}$    *// Get the index of the cluster whose center is closer to $p$ (ties broken* arbitrarily)
	$C_\ell \leftarrow C_\ell ∪ \{p\}$			 *// Assign $p$ to the cluster of the closest center* 
$C ← (C_1 , C_2 , . . . , C_k ; c_1 , c_2 , . . . , c_k)$	
*return* $C$					     *// Return the clustering*

The map reduce algorithm is the following:

* **Input**:  $(ID_x , (x, f ))$ where $ID_x \in [0,N)$ and $f = 1 \iff x\in S$;
* **Output**: $(ID_x, (x,\ell))$ where $\ell$ is the number of the cluster which $x$ was assigned to.

**Round 1**

- *Map*: 
  - Map each point $x \in P\setminus S$ in $k$ different pairs representing the distance between $x$ and each of the $k$ different centers. This means:  $(ID_x , (x, f=0)) \longrightarrow (ID_x, (x, c_i, d(x,c_i)))~~~\forall i\in1..k$;
  - Add $k$ pairs $(ID_{c_i}, (c_i,c_i,0))$ one for each of the centers $c_i~\forall i\in1..k$  (distance is obviously $0$  because a center is at distance $0​$ from itself);
  - At the end of the round there will be $(N-k)\cdot k + k$ different pairs: $k$ pairs for each of the $N-k$ points that are not centers, plus one pair for each of the $k$ centers.
- *Reduce*:
  - Gather by key $ID_x$, obtaining subsets of at most $k$ pairs each (precisely of either 1 or $k$ pairs);
  - Output the pairs $(ID_x, (x, \ell))$ where $\ell = \arg\!\min_{i=1..k}\{d(x,c_i)\}$.

**Analysis**

The size of the output of the map phase doesn't count in the analysis on $M_L​$, since the pairs need not to be stored in the memory of the worker in charge of this computation. 

For the reduce phase only $k$ pairs must be handled at the same time, thus $M_L=O(k)$.

The reduce phase has to deal with $(N-k)\cdot k+k$ subsets of at most $k$ pairs so: $M_A=O(\cfrac{(N-k)\cdot k+k}{k})=O(N)$.



## Exercise 2 pag 37

### Description

Show that the Farthest-first traversal algorithm can be implemented to run in $O(N · k)$ time.

**Hint**: make sure that in each iteration *i* of the first for-loop each point $p \in P \setminus S$ knows its closest center among $c_1, c_2, . . . , c_{i−1}$ and the distance from such a center.

### Solution

Farthest First Traversal algorithm has the following pseudo-code, assuming $| P| = N$.

$S ← \{c_1\}$ 	// $c_1 ∈ P$ arbitrary point
*for*  $i ← 2$  *to*  $k$  *do*
	Find the point $c_i ∈ P \setminus S$ that maximizes $d(c_i, S)$
	$S ← S ∪ \{c_i\}$
*return* ​$Partition(P, S)$

Step of finding point not in $S$ that maximizes distance from set of centers at iteration $i$ would require computation of $(i-1) * (N-i+1)$ distances, one for each possible pair of points in $S$ and $P \setminus S$. This would take the following number of computations: 

$\sum \limits_{i=2}^{k}[(i-1) * (N-i+1)] = \sum \limits_{i=1}^{k-1}i(N-i) =-\frac{1}{6}(k-1)\cdot k\cdot(2k-3N-1) = O(k^2N)$.

Suppose that each point knows its closest center and knows the distance between them. In this way the algorithm already knows $d(c_i, S)~\forall i \in 1...k$ and the complexity of the selection operation for a single iteration becomes $N-i+1 = O(N)$ since at every iteration $i$ the algorithm need to find the maximum $d(c_i, S)~\forall c_i\in P\setminus S$ and $|P\setminus S| = N-i+1$.

The final complexity for the for cycle then becomes:

 $\sum\limits_{i=2}^{k}(N-i+1) = \sum\limits_{i=1}^{k-1} (N-i)=\sum\limits_{i=1}^{k-1}N - \sum\limits_{i=1}^{k-1}i=(k-1)N-k(k-1)\cdot\frac{1}{2}=\frac{1}{2}\cdot (k-1)(2N-k)=O(kN)$.

The final call to $Partition(P,S)$ runs in $O(kN)$ time, so overall time complexity is still $O(kN)$.

To keep track of the distance of each point from the set of centers wee need to update at each iteration a data structure. The pseudo-code becomes: 

$S ← \{c_1\}$ 								// $c_1 ∈ P$ arbitrary point
$D ← Compute~d(c_i, c_1)$ $\forall~c_i \in P, i \ne 1$ 	   // $O(N)$ 
*for*  $i ← 2$  *to*  $k$  *do*
	$c_i \leftarrow$ Search in $D$ for point with max distance $d(c_i, S)$ and return such point $c_i$ 	// $O(N)$ 
	$S ← S ∪ \{c_i\}$
	Compute distance $\forall~p \in P\setminus S$ from $c_i$ and update $D$ if $c_i$ is closer to $p$ than previous center    // $O(N)$
*return* $Partition(P, S)$	// $O(N)$

Distances are store in a structure $D$  where:

* $D[i].closest$ returns the closest center to point $c_i$, and
* $D[i].distance$ returns the distance from the closest center.



## Exercise 3 pag 50

### Description

Let $P$  be a set of points in a metric space $(M, d)$, and let $T ⊆ P$. 

For any $k < |T |, |P|$, show that  $\phi^{opt}_{kcenter}(T , k) ≤ 2\phi^{opt}_{kcenter} (P, k)$. Is the bound tight?

### Solution

Let $|k| + 1$ points be $T=\{c_1,c_2, ... , c_k, q\}⊆ P$ , where: 

* $c_1,c_2, ... , c_k$ are the centers, and
* $q$ is the external point.

For each distinct pair of points $(x,y)$ in this set: 

* If $x=q \veebar y=q​$, then $d(x,y) = \phi^{opt}_{kcenter}(T , k)​$ and the inequality $d(x,y) ≥ \phi^{opt}_{kcenter}(T , k)​$  stands for $\phi^{opt}_{kcenter}​$ definition;
* If both are the chosen points are centers, than the inequality $d(x,y) ≥ \phi^{opt}_{kcenter}(T , k)$ stands because otherwise it could be possible to obtain a better clustering by choosing $q$ as a center, and group $x$ and $y$ in the same cluster.

Therefore for every distinct pair in $T$, $d(x,y) ≥ \phi^{opt}_{kcenter}(T , k)$.

Since $T$ is composed of more than $k$ points, at least two of them (say $x, y$) must belong to the same cluster of  $\phi^{opt}_{kcenter} (P, k)$. Let $c$ be the center of this cluster, then, for the triangular inequality: 

$\phi^{opt}_{kcenter}(T,k) ≤ d(x,y) ≤ d(x,c) + d(c,y)​$ .

Since $c​$ is a center of $\phi^{opt}_{kcenter} (P, k)​$:

$d(x,c) , d(c,y) ≤ \phi^{opt}_{kcenter} (P, k)$.

Therefore: $\phi^{opt}_{kcenter}(T,K) ≤ \phi^{opt}_{kcenter} (P, k) + \phi^{opt}_{kcenter} (P, k) ≤  2\phi^{opt}_{kcenter} (P, k)$.

Note that the bound is tight. For example, consider $P$ with three points: $a,b,m$, where:

* $m$ is the mean point of $a, b$, and
* $k=1$.

Then the optimal cluster uses $m$ as the center, and $\phi^{opt}_{kcenter} (P, k) = d(a,b)/2$. Given set $T = \{a,b\}$, $\phi^{opt}_{kcenter}(T , k) = d(a,b)$ with either $a$ or $b$ as centers. Therefore in this particular case $\phi^{opt}_{kcenter}(T , k) = 2\phi^{opt}_{kcenter} (P, k)$, so no bound can be tighter.

## Exercise 5 pag 51

### Description

Let $P$ be a set of $N$ points in a metric space $(M, d)$, and let $C = (C_1 , C_2 , . . . , C_k ; c_1 , c_2 , . . . , c_k)$ be a k-clustering of $P$. Initially, each point $q ∈ P$ is represented by a pair $(ID(q), (q, c(q)))$, where $ID(q)$ is a distinct key in $[0, N − 1]$ and $c(q) ∈ \{c_1 , . . . , c_k\}$ is the center of the cluster of $q$.

1. Design a 2-round *MapReduce* algorithm that for each cluster center $c_i​$ determines the most distant point among those belonging to the cluster $C_i​$ (ties can be broken arbitrarily).
2. Analyze the local and aggregate space required by your algorithm. Your algorithm must require $o(N)$ local space and $O (N)$ aggregate space.

### Solution

**Round 1**
- *Map*:

  - Map each point $(ID(q), (q, c(q))) \longrightarrow (ID(q)\mod\sqrt{N}, (q,c(q)))$.

- *Reduce*:

  - Gather by key the subsets $P^j$ and for each different $c(q)$ produce the pair $(c(q_j), (q_j, d(c(q_j),q_j)))$ where $q_j=\arg\!\max_{q_j}\{d(c(q_j),q_j)~~\forall~q_j\in P^j\}​$.

    Note that new key $c(q_j) \in [1, k]$ and each subset $P^j$ produces at most one pair with key $c(q_j)$. Since we have $\sqrt{N}$ subset after this phase there are at most $\sqrt{N}$ element with same key.

**Round 2**

- *Map*:
  - Identity.
- *Reduce*:
  - Gather by key the at most $\sqrt{N}$ pairs in the form $(c(q), (q, d(c(q),q)))​$;
  - For each subset $P^k$ produce the pair $(c(q_{max}), q_{max})$ where $q_{max}=\arg\!\max_{q} \{d(c(q), q)~~\forall~q\in P^k\}$.

**Analysis**
At each round at most  $\sqrt{N}$ pairs are kept in the same subset so $M_L=O(\sqrt{N})$. Clearly $M_L \in o(N)$ as required.

The first reduce phase has to deal with $\sqrt{N}$ subsets of $\sqrt{N}$ pairs each, so $M_A=O(N)$. 



## Exercise 6 pag 52

### Description

Let $P$ be a set of $N$ bi-colored points, partitioned in $k$ clusters $C_1,C_2,...,C_k$. Design a 2 round algorithm in MR to check whether every cluster $C_i$ is solid (i.e. whether a cluster contains all points of the same color).
Analyze the local and aggregate space required by your algorithm. Your algorithm must require $o(N)$ local space and $O(N)$ aggregate space.

* **Input**: $\{(ID_x, (x,i_x,\gamma_x))~~~\forall x\in P\}$. Where:
  * $ID_x\in [0,N)$;
  * $i_x \in[1,k]$ is the index of the cluster a point belongs to;
  * $\gamma_x\in \{0,1\}​$ is the color.

* **Output**: $\{(i,b_i)~~~\forall~1 \le i \le k\}​$. Where:
  *  $b_i=\begin{cases}
    -1 \iff \text{there exist two points $\in C_i$ with different color}\\~~~0 \iff \text{every point $\in C_i$ have color 0}\\~~~1 \iff \text{every point $\in C_j$ have color 1} \end{cases}$ 

### Solution $\checkmark$

**Round 1**

- *Map*:

  - $(ID_x, (x,i_x,\gamma_x)) \longrightarrow (ID_x\mod \sqrt{N}, (x,i_x,\gamma_x))$.

- *Reduce*: 

  - $\forall~\ell \in [0,\sqrt{N})$ gather by key pairs $(\ell, (x,i_x,\gamma_x))$.

  - Considering each partition $\ell$, reduce the pairs belonging to the same cluster $C_j = \{\text{pairs with}~i_x=j\}$ to a single pair $(j, b_j(\ell))$, where:

    $b_j(\ell)=\begin{cases}
    -1 \iff \text{points of $C_j$ in partition $\ell$ have different color}\\~~~0 \iff \text{every point of $C_j$ in partition $\ell$ have color 0}\\~~~1 \iff \text{every point of $C_j$ in partition $\ell$ have color 1} \end{cases}$

    Note that reduce phase produce at most $\sqrt{N}$ pairs for each cluster (with same key $j$).

**Round 2**

- *Map*:
  - Identity
- *Reduce*:
  - $\forall~1\le j \le k$  gather the at most $\sqrt{N}$ pairs $(j, b_j(\ell))$ with $0 \le \ell < \sqrt{N}$ ;
  - Output the pair $(j, b_j)$ where $b_j=\begin{cases}
    -1\iff \exists~\ell' \and \ell''.(b_j(\ell')=0~\and~b_j(\ell'')=1)\\~~~0 \iff \forall~\ell.b_j(\ell)=0\\~~~1 \iff \forall~\ell.b_j(\ell)=1 \end{cases}$

**Analysis**
Input pairs for any phase are at most $\sqrt{N}​$, so  $M_L=O(\sqrt{N})=o(N)​$ and we have to process $\sqrt{N}​$ partitions of $\sqrt{N}​$ pairs each, thus $M_A=O(N)​$.