---
layout: post
title: Resistance distance
tags: [math]
---

A while ago, I worked on a small project to help a company solve a neat algorithmic problem.  Here's what it is.

Let $$ G=(V,E)$$ be an undirected graph with positive edge weights, so each edge $$ e \in E$$ has a predetermined weight $$ w(e) > 0$$.  Assume $$ G$$ has no loops (that is, no edge connects a vertex with itself).  Now imagine constructing an electrical circuit that looks exactly like $$ G$$ -- it has wires where $$ G$$ has edges, and the resistance (in ohms) of each wire is equal to the weight of the edge corresponding to it.  Given two vertices $$ v_+,v_- \in V$$, the <em>resistance distance</em> between $$ v_+$$ and $$ v_-$$ is the reciprocal of the current that would flow from $$ v_+$$ to $$ v_-$$ if the positive and negative terminals of a 1-Volt battery were connected to those locations in the circuit.  This can be calculated using <a href="http://en.wikipedia.org/wiki/Kirchhoff%27s_circuit_laws">Kirchoff's laws</a>.

For example, in the graph <a href="/visualizations/">here</a>, the resistance distance between $$ v_+$$ and $$ v_-$$ is $$ ((5+3)^{-1}+2^{-1})^{-1}=8/5$$.

There are many applications in which resistance distance could plausibly be a useful notion.  For example, consider a graph representing a crime network, with a vertex for each criminal, an edge of weight 2 for each phone call between two criminals, and an edge of weight 1 for each monetary transaction between two criminals.  Then the resistance distance between two criminals seems like a somewhat reasonable measure of how well they might know each other.

Anyway, the interesting question from my perspective is how to compute the resistance distance quickly, and how to update it quickly when an edge weight is changed.  Note that changing one edge weight generally changes all the resistance distances.

This problem was solved fairly recently in a <a href="http://www.isid.ac.in/~rbb/paper8.dvi">1999 paper of Bapat</a>.  Bapat deduces a very concise formula for the resistance-distance matrix $$R$$, the $$\vert V\vert \times \vert V\vert $$ matrix whose $$ (i,j)$$-entry is the resistance distance between the $$ i$$th and $$ j$$th vertices.  The formula is as follows.

First define the Laplacian matrix $$ L$$ of the graph $$ G$$.  $$ L$$ is a $$ \vert V\vert \times \vert V\vert $$ matrix whose $$ ij$$th entry is 0 if vertices $$ i \neq j$$ and $$ i$$ and $$ j$$ are not adjacent and $$ -w(i,j)^{-1}$$ if vertices $$ i$$ and $$ j$$ are adjacent.  The diagonal entries are then set to make the rows of $$ L$$ sum to zero.  Let $$ J$$ be the $$ \vert V\vert \times \vert V\vert $$ matrix of ones.  Then Bapat's result states that the matrix $$ L+\frac{1}{\vert V\vert }J$$ is invertible, and, setting $$ X=\left(L+\frac{1}{\vert V\vert }J\right)^{-1}$$, the $$ ij$$-entry of the resistance-distance matrix is given by $$ R_{ij}=X_{ii}+X_{jj}-2X_{ij}$$.  In other words, computing the resistance distance matrix amounts to inverting a $$ \vert V\vert \times \vert V\vert $$ matrix, plus a constant amount of extra work.

Now here's the great part.  Suppose we update just one edge weight and want to know the new resistance-distance matrix.  Recomputing it from scratch would in general require $$ O(\vert V\vert ^3)$$ calculations -- that's the worst-case time required to invert a $$ \vert V\vert \times \vert V\vert $$ matrix.  But, as it turns out, we don't have to recompute the whole matrix!  If we change $$ w(i,j)$$, the matrix we have to invert, $$ L+\frac{1}{\vert V\vert }J$$, can only possibly change in entries $$ ii$$, $$ ij$$, $$ ji$$, and $$ jj$$.  The <a href="http://en.wikipedia.org/wiki/Sherman%E2%80%93Morrison_formula"> Sherman-Morrison Formula</a> states that if $$ A$$ is an $$ n \times n$$ matrix and $$ D$$ is a rank-one $$ n \times n$$ matrix, then $$ (A+D)^{-1}$$ can be computed from $$ A^{-1}$$ in $$ O(n^2)$$ operations.  Since we have made only four rank-one perturbations to $$ L+\frac{1}{\vert V\vert }J$$ by changing an edge weight, we can update the resistance-distance matrix in $$ O(\vert V\vert ^2)$$ operations.
