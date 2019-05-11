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

Consider two association rules $r_1 : A → B$, and $r_2 : B → C$ , and suppose that both satisfy support and confidence requirements. Is it true that also $r_3 : A → C$ satisfies the requirements? If so, prove it, otherwise show a counterexample.

### Solution

 