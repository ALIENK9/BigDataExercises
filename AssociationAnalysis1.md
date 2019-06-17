# Association analysis 1

## Table of contents

[TOC]

## Exercise 1 pag 47

### Description

Argue rigorously that given a family $F$ of itemsets of the same length, represented as sorted arrays of items, function $APRIORI-GEN(F)$ does not generate the same itemset twice.

### Solution

APRIORI-GEN, in order to generate the new set of candidates of size $\ell$, called $C_\ell$, considers each pairs of frequent itemset in $F_{\ell-1}$ (all of size $\ell-1$) that share a prefix of size $\ell-2$. Thus the algorithm looks for pairs of itemsets that differ for the last element only and the only way it could generate twice the same itemset is by taking two different itemsets with the prefix and the last element in common, so by taking a pair of identical itemsets. This is not possible, since the algorithm doesn't generate the same element twice.

A way to formally prove this is by induction on the length of the itemsets in $F$, which is $\ell-1$. The thesis is that $\forall~\ell \ge 2~~C_\ell$ doesn't contain duplicates.

- Base: $\ell-1 = 1$. $F_1$ is a set of singletons and cannot contains duplicates by construction. Therefore taking distinct pair of itemsets only once it's not possible to generate duplicates in $C_2$.
- Induction: $\ell -1 > 1$. By induction hypothesis $C_{\ell-1}$ contains no duplicates. Then, since $F_{\ell-1} \subseteq C_{\ell-1}$, also $F_{\ell-1}$ cannot contain duplicates. Since the only way to generate a duplicate would be to have a duplicate in $F_{\ell-1}$, as argued before, we conclude that $C_\ell$ doesn't contain duplicates. $\square$



## Exercise 2 pag 47

### Description

Consider two association rules $r_1 : A → B$  and  $r_2 : B → C$ , and suppose that both satisfy support and confidence requirements. Is it true that also $r_3 : A → C$ satisfies the requirements? If so, prove it, otherwise show a counterexample.

### Solution $\checkmark$

 No, it's not true. Take, for instance, an initial dataset $\{AB, BC, ABD, BCE\}$ and a bound *minconf$=$minsup*$=1/2$. Then:

- $Supp(A\rightarrow B) = Supp(\{AB\}) = 1/2$
- $Supp(B \rightarrow C) = 1/2$
- $Conf(A\rightarrow B) = Supp(A\rightarrow B) / Supp(A)=\cfrac{1/2}{1/2}=1$
- $Conf(B \rightarrow C) = Supp(B \rightarrow C) / Supp(B)= 1/2$

Then the constraints on support and confidence are satisfied. But this is not true for $r_3$:

- $Supp(A \rightarrow C) = 0 <$ *minsup*
- $Conf(A \rightarrow C) = 0 <$ *minconf*.



## Exercise 3 pag 48

### Description

Let

- $c_1 = Conf(A → B)$
- $c_2 = Conf(A → BC )$
- $c_3 = Conf(AC → B)$

What relationships do exist among the $c_i$'s?

### Solution $\checkmark$

Anti monotonicity of support for rules states that for $0 \ne Y' \subset Y \subset Z$ we have $\cfrac{Supp(Z)}{Supp(Z\setminus Y)} \le \cfrac{Supp(Z)}{Supp(Z\setminus Y')}$. 

Thus we can say the following.

$c_2=\cfrac{Supp(ABC)}{Supp(A)} \le \cfrac{Supp(ABC)}{Supp(AC)} = c_3$ 

Then by anti monotonicity of support we know that $Supp(ABC) \le Supp(AB)$ and thus

$c_2=\cfrac{Supp(ABC)}{Supp(A)} \le \cfrac{Supp(AB)}{Supp(A)} = c_1$

We also know that $Supp(AC) \le Supp(A)$ but it's not possible to compare $c_1$ and $c_3$.
The relations are:

- $c_2 \le c_3$
- $c_2 \le c_1$



## Exercise 4 pag 48

### Description

For a given itemset $X = \{x_1 , x_2 , . . . , x_k\}$, define the measure: $\zeta(X ) =\min \{Conf(x_i → X \setminus \{x_i\}) : 1 ≤ i ≤ k\}$. 

Say whether $\zeta$ is monotone, anti-monotone or neither one. Justify your answer.

### Solution $\checkmark$

Consider two itemsets $X$,$Y$ such that $X \subseteq Y$. By definition $Conf(x_i → X \setminus \{x_i\})=\cfrac{Supp(X)}{Supp(\{x_i\})}$. 

Then, if $x_i$ is the item minimizing the confidence of the rule, we know that $\zeta(X) = Conf(\{x_i\}\rightarrow X\setminus \{x_i\})=\cfrac{Supp(X)}{Supp(\{x_i\})} \ge \cfrac{Supp(Y)}{Supp(\{x_i\})}$ by anti monotonicity of support. We don't know if $x_i$ is exactly the item minimizing confidence of the rule in $Y$, but we know by definition that $Conf(\{x_i\}\rightarrow Y\setminus \{x_i\}) = \cfrac{Supp(Y)}{Supp(\{x_i\})} \ge \zeta(Y)~ \forall~i$ since $\zeta$ returns the minimum confidence.

Then $X \subseteq Y \implies \zeta(Y) \le \zeta(X)$, so $\zeta$ is anti-monotone.



## Exercise 5 pag 49

### Description

Let $T$ be a dataset of $N$ strings over an alphabet $\mathcal{E}$. Given a string $t ∈ T$ of length $t$ and a generic string $X$ over $\mathcal{E}$ of length $\ell_X ≤ \ell_t$ , we say that $X$ is a substring of $t$ if there is an index $i ∈ [0, \ell_t − \ell_X]$ such that $X[0]X[1] \cdots X [\ell_X − 1] = t[i]t[i + 1] \cdots t[i + \ell_X − 1]$. (Strings are viewed
as vectors with indexes starting from $0$). 

Define the support of a string $X$ with respect to $T$ as

- $Supp_T(X)=\cfrac{|\{t \in T : X~\text{is a substring of}~t \}|}{N}$.

That is, the fraction of strings of $T$ that contain $X$ as substring. Do the following:

1. Identify a suitable anti-monotonicity property for $Supp_T (·)$;

2. Given a support threshold $minsup ∈ (0, 1)$ let $F_ k$ be the set of strings of length $k$ of support $≥ minsup$. Define:

   - $C_{k+1} = \{Xa : X \in F_k, a \in \mathcal{E}~\and~X[1] \cdots X [k − 1]a \in F_k\}$.

   Show that $F_{k+1} \subseteq C_{k+1}$.
   
3. Find an upper bound for the number of candidates $|C_{k+1}|$.

### Solution $\checkmark$

1. $X \subseteq Y \implies Supp_T(X) \ge Supp_T(Y)$. 

   That is, if $X$ is a substring of $Y$, the support of $X$ can't be less than the support of $Y$.

2. I'll prove that each frequent itemset of length $k$ must be in $C_k$ (i.e. $X \in F_k \implies X \in C_k$ which implies $F_k \subseteq C_k$).

   Consider an arbitrary _frequent_ itemset of size $k+1$:

   - $Z=Z[0]Z[1]\cdots Z[k]Z[k+1]$. 

   Note that $Z$ is frequent so $Z \in F_{k+1}$. This itemset contains two sub-itemsets:
   
   - $Z_1=Z[0]Z[1]\cdots  [k-1]Z[k]Z$ (where $Z_1a=Z$ and $a = Z[k]$)
   - $Z_2=Z[1]Z[2]\cdots Z[k]Z[k+1]$  (where $bZ_2=Z$ and $b=Z[0]$)
   
   Clearly $Z=Z_1 \cup Z_2$, and by anti-monotonicity of support (described in point 1) they must be frequent (since they are substrings of $Z$ and $Z$ is frequent). This means that $Z_1 \in F_{k}$ and $Z_2 \in F_{k}$. Therefore $Z \in C_{k+1}$ by the definition of $C_k$ above. $\square$
   
3. An upper bound to $|C_{k+1}|$ is $|F_k| \times |\mathcal{E}|$, which are all the possible combinations of strings and final characters. That's because $C_{k+1}$ is obtained appending a character of the alphabet to a string in $F_k$.

## Correctness of mining rules algorithm (pag 34)

### Proof of AP-GENRULES

Bu induction on $m$. Assume that the algorithm is correct and computes all the rules of length $ < m$ for some value $m$.

- Base case: $m=1$, then $H_{Z, 1}$ is trivially correct, since it contains all association rules over $Z$ considering subsets of length 1.
- Induction: by induction hypothesis $H_{Z, m-1}$ is the correct set of rules towards subsets of length $m-1$. Then `Apriori-gen` generates the correct candidates of length $m$. This itemset is refined and only frequent rules are kept. So the algorithm is correct.

### Proof of main algorithm

The algorithm to mine association rules iterates over the set of frequent itemsets calculated using APRIORI algorithm. It calls the AP-GENRULES algorithm on each frequent itemset and return the union of all rules generated from all frequent itemsets. Then it must generate all possible rules of confidence $> minconf$. It's not possible that a rule generated by a non frequent itemset is frequent.

## Exercise pag 39

### Description

Each itemset frequent in dataset $T$ of $n$ items, must be frequent in some $T_j$ (partitions of dataset) each with $O(n/k)$ items.

### Solution

Let $T$ be a dataset of $n$ items, $k$ a number of partitions, and $minsup$ the support threshold. Then each partition contains $O(n/k)$ items. Take a frequent itemset $X$, with absolute support $s$ (w.r.t $T$), and support $\cfrac{s}{n} \ge minsup$.
As a consequence dividing the dataset in $k$ partition there will be at least one partition where $X$ will have absolute support at least $\bigg\lceil \cfrac{s}{k} \bigg\rceil$. 
This mean that the support in at least one partition would be $\bigg\lceil \cfrac{s}{k} \bigg\rceil \cdot \cfrac{k}{n} \ge \cfrac{s}{n}$, and so $X$ will still be frequent.