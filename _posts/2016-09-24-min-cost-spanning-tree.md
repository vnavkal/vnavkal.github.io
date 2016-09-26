---
layout: post
title: Min cost spanning trees
tags: [math]
---

I recently learned a bit about algorithms that find a minimum-cost spanning tree in an undirected graph with weighted edges.  These include two elegant and simple greedy algorithms -- Kruskal's algorithm and Prim's algorithm -- both of which run very fast, but neither of which is optimal.  I'll describe Kruskal's algorithm below.  I'll also explain the current state of the problem.  The best known upper and lower bounds for the complexity class of this algorithms are unbelievably close, but not quite equal.

Precisely, we are seeking an algorithm that takes as input a finite undirected graph $$G=(V,E)$$ together with a weight $$ w(e) \in \mathbb{Z} $$ for each edge $$ e \in E $$.  The output of the algorithm should be a subgraph $$ T=(V_T,E_T) $$ of $$ G $$ such that

* $$ T $$ <em>spans</em> $$ G $$, i.e. $$ V_T=V $$,

* $$ T $$ is acyclic, and

* for any other subgraph $$ S=(V_S,E_S) $$ of $$ G $$ which is acyclic and spans $$ G $$, $$ \sum _{e \in E_S} w(e) \geq \sum _{e \in E_T} w(e). $$

Kruskal's algorithm is surprisingly simple.  It works as follows.  First, initialize $$ E_T^0=\{\} $$.  Let $$ e_1,\ldots,e_n $$ be the edges of $$ G $$, sorted in increasing order of weight.  For $$ i=1,\ldots ,n $$, if $$ E_T^{i-1} \cup \{e_i\} $$ is acyclic, set $$ E_T^i:=E_T^{i-1} \cup \{e_i\} $$; otherwise, set $$ E_T^{i}=E_T^{i-1} $$.  Then $$ T=(V,E_T^n) $$ is a minimum-cost spanning tree.  That's all there is to it!

Why is this algorithm correct?  Well, suppose $$ S=(V,E_S) $$ is an acyclic subgraph of $$ G $$ of minimal weight.  Let $$ i \in \{1,\ldots ,n\} $$ be minimal such that $$ e_i \in E_T $$ and $$ e_i \not \in E_S $$.  Note that for $$ j < i $$, if $$ e_j \in E_S $$ then $$ e_j \in E_T $$; otherwise, by the construction of $$ E_T $$, $$ E_T^{i-1} \cup \{e_j\} $$ has a cycle, contradicting that $$ E_T^{i-1} \cup \{e_j\} \subset E_S $$.  Now, $$ E_S \cup \{e_i\} $$ must contain a cycle.  This cycle must include an edge $$ e_j $$ that is not in $$ E_T $$.  By the previous argument, $$ j > i $$.  Let $$ S' $$ now be the graph with edges $$ E_{S'}=(E_S \setminus \{e_j\}) \cup \{e_i\} $$.  The weight of $$ S' $$ is at most that of $$ S $$, and $$ S' $$ now agrees with $$ T $$ on edges $$ e_1, \ldots ,e_i $$.  Iterating this procedure, one can construct an acyclic subgraph that agrees with $$ T $$ on all edges (i.e., is the same graph as $$ T $$) and whose weight is at most that of $$ S $$.

Moreover, it's extremely fast.  If $$ G $$ has $$ n $$ vertices and $$ m $$ edges, it takes $$ O(m\, \mathrm{log} m) $$ time to sort the edges.  Since $$ m \leq n^2 $$, $$ \mathrm{log}\, m \leq 2\mathrm{log}\, n $$ so that $$ O(m\, \mathrm{log}\, m) $$ is the same as $$ O(m\, \mathrm{log}\, n) $$.  Subsequently, at each step of the iteration, the algorithm checks whether an edge creates a cycle.  This sounds like it would take long to check, but the check can be made in constant time if the right union-find data structure is used (for example, each vertex always maintains a pointer to a distinguished "leader" vertex of the subtree containing it; an edge creates a cycle if and only if the two vertices it connects point to the same leader vertex).  If such a union-find data structure is used, adding an edge is nontrivial -- the leader pointers must be updated.  I'll skip the details here, but this can be done in such a way that the total number of updates a single vertex's pointer undergoes is at worst $$ \Theta (\mathrm{log}\, n) $$.  Therefore the total work done in the iteration step is $$ O(m\,\mathrm{log}\, n) $$.  So, the worst-case performance for Kruskal's algorithm is $$ O(m\,\mathrm{log}\, n) $$!

Surprisingly, this is still not optimal!  The fastest known algorithm, published in a 1999 paper of Bernard Chazelle, has $$ O(m\, \alpha (n)) $$ worst-case running time.  Here $$ \alpha $$ is the inverse of the <a href="http://en.wikipedia.org/wiki/Ackermann_function">Ackermann function</a>.  It grows more slowly than the $$ \mathrm{log}^* $$ function -- the inverse of the function that sends $$ x $$ to $$ 2 $$ raised to itself $$ x $$ times.  Nevertheless, $$ \lim _{x \to \infty} \alpha (m) = \infty $$, so $$ O(m\,\alpha (m)) $$ is not the same as $$ O(m) $$, though it's unbelievably close.  But, as you've probably guessed, no one yet knows whether there are deterministic algorithms solving this problem in $$ O(m) $$ time!
