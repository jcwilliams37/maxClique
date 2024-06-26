# maxClique Project

## Details
Author: Jonathan Williams 

Created: Friday, April 19th 2024 at 10:12:08

## Purpose
To discover a good max clique solution to a given graph constructed from an adjaceny list file through various methods.

## Use
Run maxClique.py and use the file dialog that pops up to select a **graph adjaceny list** *adjaceny matrices won't function properly*
```python
python maxClique.py
```
The program then allows the user to select a chosen algorithm to run on the loaded graph:

1. Brute Force
2. Vertex Cover Transformation
3. Original Algorithm (Genetic Algorithm)

Then it returns the list of nodes in the maximal clique found by the chosen algorithm, and the size of the found clique. It also appends some statistics about the execution to a data.txt file.

The key of the output data is shown below:
``` txt
Algorithm Name | FileName | Num of Nodes in Graph | List of Clique Nodes | Size of Clique | Run Time in Seconds
```

The program continues to run on the chosen graph until the user quits by clicking the "quit" button. This allows the user to continually execute the same or different algorithms on the same graph quickly without reloading the program and graph into memory.

# Algorithms

## Brute Force

This method is not recommended for graphs much larger than ~25 nodes as it's time becomes exponentially longer.

If it runs to completion, this method guarantees the best, or one of the best if there are multiple, maximal cliques in the graph.

It operates by finding all possible combination of nodes, checking if they are a clique, and returning the largest found clique.

## Vertex Cover Transformation
This method will utilize the properties of a graph's Vertex Cover, and the relationship between Clique's and Independent Sets.

C = IS(G') = G' - VC(G')

Because a clique is also an Independent Set (IS) of the clique graph's (G) complement graph (G'), we can just find the IS of G'.

An independent set of a graph G is equivalent to G with one of it's Vertex Cover's (VC) removed. This new graph is a set of nodes with no edge connections, thus making it's complemented form a clique (fully connected).

This method _heavily_ depends on the effectiveness of the VC found, so this version of the algorithm uses a VC that guarantees no worse than 2 times the optimal vertex cover.

**Proof of No More than 2 Times Optimal Vertex Cover** 
> This Algorithm finds in the end is a matching (a set of edges no two of which share an endpoint) that is “maximal” (meaning that you can’t add any more edges to it and keep it a matching). This means if we take both endpoints of those edges, we must have a vertex cover. In particular, if the algorithm picked k edges, the vertex cover found has size 2k. But, any vertex cover must have size at least k since it needs to have at least one endpoint of each of these edges, and since these edges don’t touch, these are k different vertices.So the algorithm is a 2-approximation 

_From: https://www.cs.cmu.edu/~avrim/451f13/lectures/lect1105.pdf_

## Original Genetic Algorithm

This method draws inspiration from Genetic Algorithms and makes use of a population of lists representing cliques that we call _chromosomes_. We perform various operations on these chromosomes in several _generations_ of the _population_, which is a collection of chromosomes.

1. We initialize the population.
    - First by randomly creating chromosomes which may or may not be valid cliques
    - Then we optimize the entire population by extracting a maximum clique and optimizing the chromosome's clique
    - We determine the _fitness_ of every chromosome in the population by checking the clique's size
2. We Continually create new generations of the population until we stop improving. We make new generations as follows:
    1. First we take two parents from the population and use crossover to create two new children chromosomes.
    2. Then each child is mutated, randomly changing some of its genes according to a pre-defined probability to introduce diversity.
    3. Each child is then optimized to ensure it is a clique, and potentially expanding that clique.
    4. If the stronger child is better than either parent, it replaces it, or the weakest member in the population if not.
    5. This cycle continues until the algorithm grows _stagnant_. Stagnancy is when the total fitness of the generation stops increasing for a certain number of generations, I have currently defined at 50.
3. Once the algorithm grows stagnant, it exits and find the strongest (or largest) clique in the population. Returning it to the driver program.