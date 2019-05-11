# Association analysis 1

## Table of contents

[TOC]

## Exercise 1 pag 47

### Description

Argue rigorously that given a family $F$ of itemsets of the same length, represented as sorted arrays of items, function $APRIORI-GEN(F)$ does not generate the same itemset twice.

### Solution

APRIORI-GEN, in order to generate the new set of candidates of size $\ell$, called $C_\ell$, considers each pairs of frequent itemset in $F_{\ell-1}$ (all of size $\ell-1$) that share a prefix of size $\ell-2$. Thus the algorithm looks for pairs of itemsets that differ for the last element only and the only way it could generate twice the same itemset is by taking two different itemsets with the prefix and the last element in common, so by taking a pair of identical itemsets. This is not possible, since the algorithm doesn't generate the same element twice.

A way to formally prove this is by induction on the length of the itemsets in $F$, which is $\ell-1$. The thesis is that $\forall~\ell \ge 2~~C_\ell$ doesn't contain duplicates.  

- Base: $\ell-1 = 1$. $F_1$ is a set of singletons and cannot contains duplicates by construction. Therefore taking distinct pair of items only once it's not possible to generate duplicates in $C_2$.
- Induction: $\ell -1 > 1$. By induction hypothesis $C_{\ell-1}$ contains no duplicates. Then, since $F_{\ell-1} \subseteq C_{\ell-1}$, also $F_{\ell-1}$ cannot contain duplicates. Since the only way to generate a duplicate would be to have a duplicate in $F_{\ell-1}$, as argued before, we conclude that $C_\ell$ doesn't contain duplicates. $\square$

## Exercise 2 pag 47

### Description

Consider two association rules $r_1 : A → B$  and  $r_2 : B → C$ , and suppose that both satisfy support and confidence requirements. Is it true that also $r_3 : A → C$ satisfies the requirements? If so, prove it, otherwise show a counterexample.

### Solution

 No, it's not true. Take, for instance, a initial itemset $\{AB, BCD, ABD, BCE\}$ and a bound *minconf$=$minsup*$=1/2$. Then: 

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

For a given itemset $X = \{x_1 , x_2 , . . . , x_k\}$, define the measure: $\zeta(X ) =\min \{Conf(x_i → X − \{x_i\}) : 1 ≤ i ≤ k\}$. 

Say whether $\zeta$ is monotone, anti-monotone or neither one. Justify your answer.

