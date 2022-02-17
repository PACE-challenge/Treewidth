# Treewidth solvers, instances, and tools

This document contains links to solvers, instance sets, and tools for treewidth computation.
It is a collection of products of the treewidth implementation challenges at
The Parameterized Algorithms and Computational Experiments Challenge
[PACE 2017](http://dx.doi.org/10.4230/LIPIcs.IPEC.2017.30) and [PACE 2016](https://doi.org/10.4230/LIPIcs.IPEC.2016.30).

### Table of Contents

  * [Problem description](#problem-description)
  * [List of Treewidth Solvers](#list-of-treewidth-solvers)
  * [List of Treewidth Instance Sets](#list-of-treewidth-instance-sets)
  * [Validity checker](#validity-checker)
  * [Input format](#input-format)
  * [Output format](#output-format)
  * [Mirrors](#mirrors)

## Problem description

The objective is to compute a tree-decomposition of minimum width.

- Input: An undirected graph.
- Output: A minimum-width tree-decomposition of the graph.

The theory and complexity of treewidth has been intensely study in graph minor theory and fixed-parameter tractability (FPT). Given a graph G and an integer k, it is NP-complete to determine whether the treewidth of G is at most k, but there is an n^(k+2)-time algorithm (Arnborg, Corneil, and Proskurowski 1987). The problem can also be solved in FPT-time exp(k^3) n (Bodlaender 1996), and a factor-5 approximation can be obtained in time exp(k) n (Bodlaender, Drange, Dregi, Fomin, Lokshtanov, and Pilipczuk 2013). It is unknown whether the problem has a polynomial-time approximation scheme (PTAS).

Treewidth implementations are used in various contexts. For example, compilers allocate registers by computing proper colorings on control flow graphs, which turn out to have small treewidth in practice (e.g., Thorup 1998). Data structures for shortest path queries can use tree decompositions in a preprocessing phase (e.g., Chatterjee, Ibsen-Jensen, and Pavlogiannis 2016). Graph theory can be guided by treewidth implementations when attempting to rigorously determine the treewidth of specific graph families (e.g., Kiyomi, Okamoto, and Otachi 2015). Finally, many problems in probabilistic inference use tree decompositions in a preprocessing phase (e.g., Otten, Ihler, Kask, and Dechter 2011).

## List of Treewidth Solvers

### Exact

- [twalgor](https://github.com/twalgor/tw) by Hiaso Tamaki (2022)
- [tamaki-2017](https://github.com/TCS-Meiji/PACE2017-TrackA) by Hisao Tamaki, Hiromu Ohtsuka, Takuto Sato, and Keitaro Makii
- [tdlib](https://github.com/freetdi/p17) by Lukas Larisch and Felix Salfelder
- [Jdrasil](https://github.com/maxbannach/Jdrasil) by Max Bannach, Sebastian Berndt, and Thorsten Ehlers
- [tamaki-2016](https://github.com/TCS-Meiji/treewidth-exact) by Hisao Tamaki

### Heuristic

- [Flow-Cutter](https://github.com/kit-algo/flow-cutter-pace17) by Ben Strasser
- [htd](https://github.com/mabseher/htd) by Michael Abseher, Nysret Musliu, and Stefan Woltran
- [tamaki-2017](https://github.com/TCS-Meiji/PACE2017-TrackA) by Tamaki, Hiromu Ohtsuka, Takuto Sato, and Keitaro Makii
- [Jdrasil](https://github.com/maxbannach/Jdrasil) by Max Bannach, Sebastian Berndt, and Thorsten Ehlers
- [Minfill_mrs](https://github.com/td-mrs/minfill_mrs.git) and
  [Minfillbg_mrs](https://github.com/td-mrs/minfillbg_mrs.git) by Philippe Jégou, Hanan Kanso, and Cyril Terrioux
- [tdlib](https://github.com/freetdi/p17) by Lukas Larisch and Felix Salfelder

## List of Treewidth Instance Sets

### Exact

- [PACE 2017 bonus instances](https://github.com/PACE-challenge/Treewidth-PACE-2017-bonus-instances) – this should be a fresh challenge even for the PACE 2017 winning solvers
- [PACE 2017 competition instances](https://github.com/PACE-challenge/Treewidth-PACE-2017-instances)
- [Named graphs](https://github.com/freetdi/named-graphs.git) contributed by Lukas Larisch
- [Control flow graphs](https://github.com/freetdi/CFGs.git) contributed by Lukas Larisch

### Heuristic

- [PACE 2017 competition instances](https://github.com/PACE-challenge/Treewidth-PACE-2017-instances)
- [UAI 2014 probabilistic inference competition graphs](https://github.com/PACE-challenge/UAI-2014-competition-graphs)
- [SAT competition graphs](http://people.mmci.uni-saarland.de/~hdell/pace17/SAT-competition-gaifman.tar)
- [Transit graphs](https://github.com/daajoe/transit_graphs) contributed by Johannes Fichte
- [Road graphs](https://github.com/ben-strasser/road-graphs-pace16) contributed by Ben Strasser
- [treedecomposition.com](https://treedecomposition.com/)

## Validity checker

The validity checker [td-validate](https://github.com/holgerdell/td-validate) verifies that a given tree decomposition (specified in the .td file format) is indeed a valid tree decomposition of a given graph (specified in the .gr format).

The validity checker also comes with an automatic tester `autotest-tw-solver.py` that can automatically test a given exact treewidth solver by running it on all graphs with min-degree 3 and at most 8 vertices.

## Input Format

We describe the file format .gr, which is similar to the format used by DIMACS challenges.

Lines are separated by the character ‘\n’. Each line that starts with the character c is considered to be a comment line. The first non-comment line must be a line starting with p followed by the problem descriptor tw and the number of vertices n and edges m (separated by a single space each time). No other line may start with p. Every other line indicates an edge, and must consist of two decimal integers from 1 to n separated by a space; moreover, graphs are considered undirected (though they may contain isolated vertices, multiple edges, and loops). For example, a path with four edges can be defined as follows:

    c This file describes a path with five vertices and four edges.
    p tw 5 4
    1 2
    2 3
    c we are half-way done with the instance definition.
    3 4
    4 5

## Output Format

Recall the definition of a tree decomposition of a graph G: It is a tree T such that every vertex x in V(T) has an associated bag B(x) that is a subset of V(G). Every edge e in E(G) must be a subset of at least one bag B(x). Moreover, for every vertex v in V(G), the set of tree vertices whose bags contain v induce a connected subtree of T. The width of T is the maximum size of its bags minus one. The goal is to compute a tree decomposition of minimum width.

We define the file format .td. As above, c lines are comments and can occur throughout the file. Instead of a p-line, we now expect a unique solution line s as the first non-comment line, which contains the string td, followed by the number N of bags of the tree decomposition, the width of the tree decomposition plus one (i.e., the largest bag size), as well as the number of vertices of the original input graph. The next non-comment lines we expect start with b and specify the contents of each bag; for example, b 4 3 4 6 7 specifies that bag number 4 contains the vertices 3, 4, 6, and 7 of the original graph. Bags may be empty. For every bag i, there must be exactly one line starting with b i. All remaining non-comment lines indicate an edge in the tree decomposition, so it must consist of two decimal integers from 1 and N where the first integer is smaller than the second, and the graph described this way must be a tree. For example, the following is a suboptimal tree decomposition of the path with four edges.

    c This file describes a tree decomposition with 4 bags, width 2, for a graph with 5 vertices
    s td 4 3 5
    b 1 1 2 3
    b 2 2 3 4
    b 3 3 4 5
    b 4
    1 2
    2 3
    2 4
