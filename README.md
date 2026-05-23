# NetworkX Betweenness Centrality Optimization

## Repository and Baseline

**Repository:** `networkx/networkx`  
**Baseline version:** `networkx-3.2.1`

I selected NetworkX because it is a widely used graph processing library with a permissive license, large user base, and computationally expensive graph algorithms suitable for performance optimization.

The workload chosen was exact betweenness centrality on a Barabási–Albert graph generated using:

```python
nx.barabasi_albert_graph(2500, 5, seed=42)
```
This workload consistently exceeds 10 seconds on a standard Colab CPU runtime and exercises a meaningful hot path inside NetworkX.

---

## Slow Path Identification

The hot path is the BFS traversal loop used repeatedly during betweenness centrality computation.

The loop performs many:

- `deque` method lookups
- adjacency dictionary lookups
- dictionary membership checks
- repeated attribute accesses

Because this code executes inside deeply nested Python loops, interpreter overhead becomes significant.

---

## Optimization

The optimization reduces Python interpreter overhead inside the BFS traversal loop by:

- locally binding deque methods (`append`, `popleft`)
- caching graph adjacency access (`G._adj`)
- caching dictionary lookup methods (`D.get`)
- reducing repeated dictionary and attribute lookups

The algorithm itself is unchanged.

---

## Correctness Verification

Correctness was verified by:

- comparing baseline and candidate outputs using a strict `1e-9` relative tolerance
- running the existing `pytest` regression suite for betweenness centrality

The regression validation uses the existing pytest suite for standard betweenness centrality, which is the subsystem modified by this optimization.

---

## Benchmark Methodology

Benchmarking was performed:

- on the same Colab CPU runtime
- using identical graph inputs
- using warmup runs before measurement
- using multiple measured runs
- reporting median runtime and IQR

Median was used instead of mean to reduce sensitivity to VM noise and outliers.

---

## Trade-offs

The optimization slightly reduces code readability due to additional local bindings and cached variables, but memory overhead is negligible and no external dependencies were introduced.

---

## What I Would Do With More Time

With additional time, I would:

- profile the implementation more formally
- explore algorithmic optimizations
- investigate Cython or Rust acceleration
- benchmark additional graph topologies and sizes

---

## Caveats

Colab CPU runtimes can exhibit variability due to shared infrastructure. To reduce noise, multiple measured runs and median/IQR reporting were used.