# Exercises on association analysis 2

## Exercise 1 pag 11

### Description

Prove the corollary:

For each $X \subseteq  I$, $Supp(X)=\max\{Supp(Y) : Y \supseteq X \and Y \in CLO\}$

### Solution

$CLO = \{Z \subseteq I : \forall~W \supset Z~\text{it holds}~Supp(W) < Supp(Z)\}$

By second property on slide 10 $Supp(Closure(X)) = Supp(X)$. By first property $X \subseteq Closure(X)$. Of course $Closure(X) \in CLO$ since surely by adding anything to $X$ support would go down (this follows by the definition of Closure).

$Closure(X)$ is the superset of $X$ with maximal support. Suppose for the purpose of contradiction that $Closure(X)$ is not the superset of $X$ with higher support.

It would mean that there exists another itemset $Z \supseteq X$ such that $Z \neq Closure(X)$, $Z \in CLO$ and $Supp(Z) > Supp(Closure(X))$. But this would imply that $Z \sub Closure(X)$, by anti-monotonicity of support. Then $Z \notin CLO$ (can't be since one of its superset is in $CLO$). So the contradiction. $\square$



## Exercise 4 pag 20

### Description

Let $T$ be a dataset of transaction over $I$. Let $X, Y \sube I$ be two closed itemsets and define $Z=X \cap Y$.

1. Find a relation among $T_X, T_Y$ and $T_Z$ (which are the set of transactions containing $X, Y$ and $Z$, respectively). Justify your answer.
2. Show that $Z$ is also closed.

### Solution

1. $T_Z \supe T_X \cup T_Y$. Because all transactions that contained $X$ must contains also $Z$, since $Z \sube X$. Same holds for $Y$.

2. $X, Y \in CLO$. Suppose $Z=X \cap Y$ is not closed. Then there exists an itemset $P \supset Z$ defined as $P=Z \cup \{i\}$ for some item $i \notin Z$, and $Supp(P) = Supp(Z)$ (cannot be greater by anti-monotonicity of support).

   This means that every transaction that contains $Z$ contains also $P$. Thus $T_Z = T_P$. By point 1 $T_P \supe T_X \cup T_Y$ and so $T_P \supe T_X$ and $T_P \supe T_Y$ (all transactions that contains $X$ and $Y$ must contain $P$).

   So $i \in X$ and $i \in Y$, since $X$ and $Y$ are closed. Were it not the case, since $X \cup \{i\}$ have the same support of $X$ the last couldn't be closed. 

   Then $i \in X \cap Y = Z$, which contradict the fact that  $i \notin Z$. $\square$



## Exercise 5 pag 20

### Description

Let 

- $I=\{a_1, a_2, ...,a_n\} \cup \{b_1, b_2, ...,b_n\}$ be a set of $2n$ items and 
- $T = \{t_1, t_2, ...,t_n\}$ be a dataset of $n$ transactions over $I$ where
  - $t_i = \{a_1, a_2, ..., a_n, b_i\}$ for $1 \le i \le n$.

For $minsup=1/n$, determine the number of frequent closed itemsets and the number of maximal itemsets.

### Solution

Let's make an example. Let $I = \{a_1, a_2, a_3, b_1, b_2, b_3\}$ where $n=3$. Then $T$ is made up of the following transactions:

- $t_1=\{a_1, a_2, a_3, b_1\}$
- $t_2=\{a_1, a_2, a_3, b_2\}$
- $t_3=\{a_1, a_2, a_3, b_3\}$



Every subset of $A=\{a_1,...,a_n\}$ has support $1$ (is present in all transactions). Instead every itemset composed of some subset of $A$ and one item of $B$ has support $1/n$. Every itemset with more than one element of $B$ has support $0$. So:

- $|CLO-F_{T, minsup}|=n + 1$. In particular $CLO-F_{T, minsup} = \{A, A \cup \{b_i\}~\forall~1\le i\le n\}$;
- $|MAX_{T, minsup}| = n$. In particular $MAX_{T, minsup} = \{A \cup \{b_i\}~\forall~1\le i\le n\}$.



## Exercise 6 pag 21

### Description

I don't have time for this.

### Solution

1. Let $Supp(X_j)=s'$ and for each item $a \in X_j$ the itemset $X_j \setminus \{a\}$ is a subset of $X_j$. Then $Supp(X_j \setminus \{a\}) > Supp(X_j)$ because every itemset, including $X_j$, is closed. 

   Furthermore, since $j \ge K+1$ and the sequence $X_1,X_2,...,X_j,..., X_n$ has nonincreasing support, itemset $X_j \setminus \{a\}$ must be an itemset with index $\le K$, so $X_j \setminus \{a\} \in \{X_1, ..., X_k\}$.

2. The itemsets of support exactly $s' (< s)$ can be obtained by adding one item to the itemsets of length $|X_K|$. The maximum number of such itemsets is of course $K$, so the upper bound to the number of itemsets of support exactly $s'$ is $K\cdot d$, where $d=|I|$. Note that that by adding two items instead of one, you would generated a superset of the itemsets with just one item added, so the support would be $< s'$, thus they must not be accounted for.

   The Top-(k+1) frequent itemsets are then all the itemsets of support $\ge s'$, thus there are $O(K+d\cdot K)$ of them (sum of the one with support $< s'$ and the one with support $=s'$).