# Cycle hunt

Collection of utilities and scripts for neutralizing cycles while performing the least amount of damage to the original structure (stealth).

Currently 2 powerful methods when used in conjunction enact the strongest firepower:

* Conversion of *Directed Cyclic Graph* (DCG) to *Directed Acyclic Graph* (DAG) through merges by keeping tracking of dependencies (`remove_cycles` inside `cycle_utils.py`)
* Greedy most-cyclic edge deletion (`greedy_remove_cycles` inside `Greedy Graph Simplifier.ipynb` and `cycle_removal.cpp`)

Currently, the best strategy is as follows:

1. Convert DCG to DAG. Keep track of ancestry when merging two nodes (~10mn to convert full English Wikipedia graph to DAG)

2. Recover ancestry for nodes that received most merges (most nodes with merges has 1-5 merges, but 3-4 have over a 1000, and contain an eclectic mix of nodes)

3. Form subgraph from recovered nodes in (2)

4. On each subgraph of (3) perform a greedy deletion of the edges that participate in the most cycles within those subgraphs. Only remove edges that do not disconnect nodes from the root.

5. Reconnect subgraph to merged graph from (1)

## Usage

### Running cycle_removal.cpp

To run greedy most-cyclic edge deletion do the following:
Select some number of cycles you want to search for at each step of the algorithms (say 10000),
and whether you want to update the most-cyclic edge count after deleting an edge (when cycles are broken the edges that participate in are no longer *cyclic*, however they were at some point) - for now let's not update (0). We run this algorithm on a graph that can be loaded from a binary file by storing edges in a binary file as a sequence of from-to pairs made of int32s (`graph_out.bin` contains such a graph):

```bash
make
./cycle_removal graph_out.bin 10000 0
```

