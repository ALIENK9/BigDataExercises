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

### Solution

Anti monotonicity of support for rules states that for $0 \ne Y' \subset Y \subset Z$ we have $\cfrac{Supp(Z)}{Supp(Z\setminus Y)} \le \cfrac{Supp(Z)}{Supp(Z\setminus Y')}$.

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

### Solution

Consider two itemsets X, Y such that $X \subseteq Y$. By definition $Conf(x_i → X \setminus \{x_i\})=\cfrac{Supp(X)}{Supp(x_i)}$. 

However we don't know what the support of $x_i$ would be with respect to $Y$. If $Y$ contains more itemsets that contain $x_i$ the support would increase, but if $Supp_{\{Y \setminus X\}}(x_i)=0$ the support in $Y$ would decrease compared to the support in $X$. Therefore $\zeta$ depends on the specific element that distinguish $X$ from $Y$ and so there is no arbitrary relation of monotonicity. **TO CHECK**



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

### Solution

1. $X \subseteq Y \implies Supp_T(X) \ge Supp_T(Y)$. 

   That is if $X$ is a substring of $Y$ the support of $X$ can't be less than the support of $Y$.

2. I'll prove that each frequent itemset of length $k$ must be in $C_k$ (i.e. $X \in F_k \implies X \in C_k$ which implies $F_k \subseteq C_k$).

   Proof by induction on $k$. 

   - Basis ($k=1$). $C_1=\{a \in \mathcal{E}\} \supseteq F_1$ (by definition, since there's no prefix).

   - Induction ($k > 1$).  Assume by induction hypothesis that the property holds up to index $k-1$ ($F_{i} \subseteq C_{i}~~\forall~i \le k-1$). Then consider an arbitrary _frequent_ itemset of size $k$: $X=X[1]X[2]\cdots X[k-1]X[k]$. Note that $X$ is frequent so $X \in F_k$. 
     This itemset contains two sub-itemsets: 

     - $X_1=X[1]X[2]\cdots  X[k-2]X[k-1]$
     - $X_2=X[1]X[2]\cdots X[k-2]X[k]$

     Clearly $X=X_1 \cup X_2$, and by anti-monotonicity of support they must be frequent (since $X$ is frequent). This means that $X_1 \in F_k$ and $X_2 \in F_k$. Therefore $X \in C_k$ by the definition above. $\square$

