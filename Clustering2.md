# Clustering 2 - exercises

## Table of contents

[TOC]

## Exercise 1 pag 20

### Description

Show how to implement algorithm *k-means++* in *MapReduce* using $O(k)$ rounds, local space $M_L = O(\sqrt{N})$ and aggregate space $M_A = O (N)$.

### Solution

Algorithm *k-means++* computes a set $S$ of $k$ centers for $P$ using the following procedure (note that $S ⊆ P$):
$c_1 ←$ random point chosen from $P$ with uniform probability
$S ← \{c_1\}$
for $i ← 2$ to $k$ do
	$c_i ←$ random point chosen from $P \setminus S$, where a point $p$ is chosen with probability $d(p, S)^2 / \sum\limits_{q∈P\setminus S} (d(q, S))^2$
	$S ← S \cup \{c_i\}$
return $S$

To prove the exercise I need to prove that it is possible to do a single iteration of *kmeans++* in $O(1)$ rounds with above space constraints. I'll split the MapReduce *kmeans++* procedure in two parts:

- A MapReduce implementation of *Probability(P,S)* that does the computation of probability value for each point
- An implementation of a single iteration of *kmeans++*

If both the above algorithms can run in $M_L=O(\sqrt{N})$, $M_A=O(N)$ and $O(1)$ rounds then the complete algorithm can run with the same space requirements and $O(k)$ rounds.

#### Probability(P, S)

This procedure aims at efficiently computing the probability used by *kmeans++* for each point in the set.
*Input*: $(i, (x_i, f_i))$ where $i$ is a unique number $\in [0, N)$ and $f_i=\begin{cases}0 \iff x_i \in P\setminus S\\1 \iff x_i \in S \end{cases}$ and $|S|=m\le k$. 
Note that $m=O(N)$.
*Output*: $(i, (x_i, p))$ where $p$ is the probability of $x_i$ to be chosen as a center computed as described above.

**Round 1**

Compute squared distances

- *Map*: $(i, x_i) \longrightarrow (x_i, d(x_i, c_j))~\forall~c_j\in S, \forall ~x_i\in P\setminus S$ where $d(x_i, c_j)$ is the distance between center $c_j$ and $x_i$.
  Note that $m\cdot N$ pairs will be generated.
- *Reduce*: gather the $O(m)$ pairs and produce a single pair containing the square of the smallest distance: $(x_i, d(x_i, c_j)) \forall ~c_j\in S \longrightarrow (i, (x_i, sd_i))$, where $i$ is an index assigned incrementally in $[0, N-m)$ and $sd_i = d(x_i, S)^2$ (square distance).

**Round 2**

Compute partial sum of distances

- *Map*: $(i,(x_i, sd_i)) \longrightarrow (i\mod \sqrt{N}, (x_i,sd_i))$.
- *Reduce*: gather by key the at most $\sqrt{N}$ pairs in each subset $P^j$ and output the same input pairs with a new pair $(n, sum_j)$ where $n\ge \sqrt{N}$ (need to be different from all other indexes) and $sum_j=\sum\limits_{i\in{P^j}} sd_i$. 
  At this point there will be the same pairs as before in addition of $\sqrt{N}$ pairs representing the partial sum of squared distances.

**Round 3**

Compute sum of distances

- *Map*: identity.
- *Reduce*: gather by key and leave unaltered all subsets, aside from the one with all the pairs of key $n$. Reduce that set in a single pair $(n, \sum\limits_{j=0}^{\sqrt{N}} sum_j)$ which represent the final sum of squared distances for all points in $P\setminus S$.
  At this point there are $N-m+1$ pairs, one for each point in $P\setminus S$ plus the total sum.

**Round 4**

Compute probability

- *Map*: map each pair dividing by the total sum of squared distances, producing the final probability: $(i,(x_i, sd_i)) \longrightarrow (i, (x_i, \cfrac{sd_i}{sum}))$.
- *Reduce*: identity function.

**Analysis**

The algorithm uses 4 rounds, and each round needs a maximum of $\sqrt{N}$ pairs at the same time so $M_L=O(\sqrt{N})$. Reduce phase on round 1 has to deal with $N$ pairs overall, so $M_A=O(N)$. 

#### Kmeans++

The following MapReduce procedure represent how to obtain center $c_n$ for iteration $n$ of *kmeans++*. Running this procedure $k$ times every time with the updated set of centers $S$ allows to obtain $k$ center.

**Round 1**

- *Map*: $(i, x_i) \longrightarrow (i\mod \sqrt{N}, x_i)$.
- *Reduce*: gather by key the subsets $P^j$ where $|P^j|\le \sqrt{N}~\forall~j$ such that $0\le j < \sqrt{N}$ and compute $\pi_i=Probability(P^j, S)$ for each pair in subset: $(i, x_i) \longrightarrow (i, (x_i, \pi_i))$
  Note that $\forall j\in[0, \sqrt{N})~~~\sum\limits_{i\in P^j}\pi_i=1$ so $\pi_i$ define a probability distribution, as required.

**Round 2**

- *Map* : identity.

- *Reduce*: gather by key the at most $\sqrt{N}$ pairs and produce a single pair representing the selected center in subset $P^j$ chosen with probability calculated in previous round: $ (i, (x_i, \pi_i)) ~\forall i \in P^j \longrightarrow (i, x_i)$.

  Note that the produced pair represent the chosen center between points in $P^j$ and it's not yet a center of $P$. To do that we need another round.
  At this point there will be at most $\sqrt{N}$ pairs in total with keys $\in [0, \sqrt{N})$.

**Round 3**

- *Map*: for each of the $\sqrt{N}$ pairs (lets call the whole subset $P^{ref}$) and compute $\pi_i=Probability(P^{ref}, S)$ mapping each pair to its probability of being a final center: $(i, x_i )\longrightarrow (0, (x_i, \pi_i))$.
- *Reduce*: reduce the $\sqrt{N}$ pairs to a single pair $(0, x_i)$ choosing each pair from the set with probability computed in map phase.

**Analysis**

Clearly only $\sqrt{N}$ pairs need to be stored in memory every time so $M_L=O(\sqrt{N})$ and there are $\sqrt{N}$ subsets of $\sqrt{N}$ pairs so $M_A=O(N)$. This analysis assumes that $Probability(P,S)$ runs efficiently in a constant number of rounds, as stated before.
Repeating this procedure $k$ times results in the complete *kmeans++* algorithm with $3\cdot k = O(k)$ rounds.



## Exercise 2 pag 20

###Description

Show how to implement one iteration of Lloyd’s algorithm in MapReduce using a constant number of rounds, local space $M_L = O(\sqrt{N})$ and aggregate space $M_A = O(N)$. Assume that at the beginning of the iteration a set $S$ of $k$ centers and a current estimate $\phi$ of the objective function are available. The iteration must compute the partition of $P$ around $S$, the new set $S'$ of $k$ centers, and it must update $\phi$, unless it is the last iteration.

### Solution

For simplicity I'll use three global variables $\mathcal{C}$ (clusters and centroids), $\phi$ (optimal value of obj function), and $S$ (set of centroids). It is always possible to code them as special pairs that are never duplicated and only updated at every iteration.

**Round 1**

Apply `Partition(P,S)` primitive like explained in ex 2 pag 26 of Clustering 1 exercise file. Space requirements are: $M_L=O(k)$, $M_A=O(N)$ and only one round.

**Round 2**

Input of this round are pairs $(ID_x, (x, \ell))$ where $\ell$ is the number of the cluster which point $x$ was assigned to. $ID_x \in[0, N-1]$. A copy of this pairs must be kept untouched for later.
The goal of the next two rounds is computing the new centroids $\{c_1, .., c_k\}$ for the partitioning returned by the previous round.

- *Map*:  $(ID_x, (x, \ell)) \longrightarrow (ID_x \mod \sqrt{N}, (x, \ell))$.
- *Reduce*: gather by key every subset $P^i$ and produce a pair for each different $\ell$: $(\ell, s_x)$ where $s_x=\sum \limits_{x\in P^i}x$ (component wise sum).

**Round 3**

- *Map*: identity.
- *Reduce*: gather the at most $\sqrt{N}$ in every subset $P^\ell$ (pairs with key equal to $\ell$) and compute the value of the centroid: $(\ell, \cfrac{1}{N}\sum\limits_{x\in P^\ell}s_x)$. Then assign this centers to $\mathcal{C}$.

**Round 4**

Compute value of objective function. We have the (copy of the) original points in pairs $(ID_x, (x, \ell))$ plus the new centroids previously computed as pairs $(\ell, c_\ell)$.

- *Map*: $(ID_x, (x, \ell)) \longrightarrow (ID_x \mod \sqrt{N}, (x, \ell, d(x, c_\ell)))$ where $d$ is the distance function and $c_\ell$ is the newly computed centroid for cluster $\ell$. Previously computed pairs with centroids are discarded.
- *Reduce*: gather by key the at most $\sqrt{N}$ pairs in each set $P^i$. For each different cluster $\ell$ produce a single pair $(\ell, d_i)$ where $d_i= \sum\limits_{x\in P^i_\ell}d(x, c_\ell)$, where $P^i_\ell$ is the set of points of $P^i$ assigned to cluster $\ell$.
  Note that for each $P^i$ this phase produces at most one pair for every different $\ell$ in $P^i$.

**Round 5**

- *Map*: identity.
- *Reduce*: gather by key the at most $\sqrt{N}$ pairs and produce a pair $(\ell, d_j)$ where $d_j=\sum d_i$.
  A pair with sum of squared distance for each cluster will be generated ($1\le j \le k$).

**Round 6**

- *Map*: identity.
- *Reduce*: gather all the pairs (at most $\sqrt{N}$) and compute the final value of the objective function $(0, \phi'=\sum\limits_{j=1}^k d_j)$.
  Then compare it to the previous value of objective function $\phi$ and if better assign $\phi=\phi'$ and $S = \{c_1, ...,c_k\} $ (taking it from $\mathcal{C}$).



**Analysis**

The procedure uses a constant number of rounds. Of course $M_L=O(\sqrt{N})$ and $M_A=O(N)$.



## Exercise 3 pag 47

### Description

Let $\mathcal{C}_{alg} = Partition(P, S)$ be the *k-clustering* of $P$ induced by the set $S$ of centers returned by *k-means++*. 
Using Markov’s inequality determine a value $α > 1$ such that $Pr (\phi_{kmeans}(\mathcal{C}_{alg} ) ≤ \alpha · \phi^{opt}_{kmeans}(k)) \geq 1/2$.
Recall that for a real-valued nonnegative random variable $X$ with expectation $E[X]$ and a value $a > 0$, the Markov’s inequality states that $Pr(X ≥ a) ≤ \cfrac{E[X]}{a}$.

### Solution $\checkmark$

From *theorem* in slide 18 we know that  $E[\phi_{kmeans} (\mathcal{C}_{alg})] ≤ 8(ln(k) + 2) · \phi^{opt}_{kmeans}(k)$.

Then we obtain $Pr(\phi_{kmeans}(\mathcal{C}_{alg})\le \alpha\cdot \phi^{opt}_{kmeans}(k)) \ge Pr(\phi_{kmeans}(\mathcal{C}_{alg})< \alpha\cdot \phi^{opt}_{kmeans}(k)) = 1-Pr(\phi_{kmeans}(\mathcal{C}_{alg})\ge \alpha\cdot \phi^{opt}_{kmeans}(k)) $

Then using Markov inequality and the *theorem* we have
$1-Pr(\phi_{kmeans}(\mathcal{C}_{alg})\ge \alpha\cdot \phi^{opt}_{kmeans}(k)) \stackrel{\text{Markov}}{\ge} 1-\cfrac{E[\phi_{kmeans}(\mathcal{C_{alg}})]}{\alpha\cdot \phi^{opt}_{kmeans}(k)} \stackrel{\text{Theorem}}{\ge} 1-\cfrac{8(ln(k) + 2) · \phi^{opt}_{kmeans}(k)}{\alpha\cdot \phi^{opt}_{kmeans}(k)}=\\=1-\cfrac{8(ln(k)+2)}{\alpha}$

So putting the pieces together we have  $Pr (\phi_{kmeans}(\mathcal{C}_{alg} ) ≤ \alpha · \phi^{opt}_{kmeans}(k)) \ge 1-\cfrac{8(ln(k)+2)}{\alpha}$
And we want the quantity on the right to be  $\ge \cfrac{1}{2}$. Thus we want $\cfrac{8(ln(k)+2)}{\alpha} \le \cfrac{1}{2}$.
Solving the inequality we see that the bound is true with any  $\alpha \ge 16(\ln{k}+2)$.



## Exercise 4 pag 48

### Description

(This exercise shows how to make the approximation bound of algorithm *k-means++* to hold with high probability rather than in expectation.)
Let $\mathcal{C}_{alg} = Partition(P, S)$ be the k-clustering of $P$ induced by the set $S$ of centers returned by an execution of *k-means++*. Suppose that we know that for some value $α > 1$ the following probability bound holds:
$Pr(\phi_{kmeans} (\mathcal{C}_{alg}) ≤ \alpha · \phi^{opt}_{kmeans}(k)) ≥ 1/2$.
Show that the same approximation ratio, but with probability at least $1 − 1/N$, can be ensured by running several independent instances of *k-means++*, computing for each instance the clustering around the returned centers, and returning at the end the best clustering found (call it $\mathcal{C}_{best}$), i.e., the one with smallest value of the objective function.

### Solution $\checkmark$

Suppose we run a certain number of consecutive *k-means++* and partitioning and each time we test whether the objective function $\phi_{kmeans}$ respect the inequality  $\phi_{kmeans} (\mathcal{C}_{alg}) ≤ \alpha · \phi^{opt}_{kmeans}(k)$, for a known $\alpha >1$. At every run we save the objective function and update $\mathcal{C}_{best}=argmin~\phi(\mathcal{C}_i)$, the best clustering found. Every run is independent from the other as long as the first point is selected at random every time.
We define a random variable $Y=0 \iff$ the inequality is not respected and 1 otherwise. The probability to have a 1 is $\ge 1/2$.
This defines a Bernoulli distribution $Ber(p\ge1/2)$.
Define then a random variable $X=$("number of $Y=1$ in a series of $M$ trials"). This defines a binomial distribution $Bin(M,p\ge\frac{1}{2})$.
Since at every run of *k-means++* we save the best clustering we need just one run to satisfy the inequality to prove the bound.
Then $Pr(X\ge 1) = 1-Pr(X=0)\ge 1-\binom{M}{0}\cdot(\frac{1}{2})^0\cdot(1-\frac{1}{2})^M=1-\cfrac{1}{2^M}$.
So with at least $M=\lceil\log_2{N}\rceil$ trials the bound is satisfied with probability $\ge 1-\cfrac{1}{N}$.



## Exercise 5 pg 48

### Description

Show that the *PAM* algorithm always terminates.

### Solution $\checkmark$

**PAM Algorithm pseudocode**

Input: Set $P$ of $ N$ points from $(M,d)$, integer $k > 1$
Output: *k-clustering* $C = (C_1 , C_2 , . . . , C_k ; c_1 , c_2 , . . . , c_k )$ of $P$ with small $\phi_{kmedian}(C)$ 

$ S ← \{c_1 , c_2 , . . . , c_k\}$ (arbitrary set of $k$ points of $P$)
$C ← Partition(P, S)$
stopping-condition $← false$
while ($!$stopping-condition​) do
	stopping-condition $← true$
	for each $p ∈ P − S$ do
		for each $c ∈ S$ do $S_0 ← (S \setminus \{c\}) ∪ \{p\}$
			$C_0 ← Partition(P, S_0)$
			if $\phi_{kmedian} (C_0) < \phi_{kmedian} (C)$ then
				stopping-condition $← false$
				$C ← C_0$
				$S ← S_0$
				exit both for-each loops
return $ C$

**Reason why it stops**

At any moment the function $\phi(\mathcal{C})$ is the minimum value of objective function for *k-median* encountered to that moment. At every iteration the two for each loops consider all possible clustering over all possible combination of points. If inside these loops is not possible to find a better solution than the current optimal, the algorithm will never enter the inner *if* and the *stopping-condition* will remain set to $true$.
Additionally the value of the $\phi_{kmedian} (C)$ must decrease in every iteration to allow the algorithm to do another *while* iteration, since the *if* statement requires a strict inequality to set *stopping-condition* to $false$.
Thus the PAM algorithm surely stops.