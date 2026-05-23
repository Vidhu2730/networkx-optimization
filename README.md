# Networkx-optimization

The optimization reduces Python interpreter overhead in the BFS traversal hot path used by betweenness centrality.

Changes include:
- local binding of deque methods,
- adjacency dictionary caching,
- cached dictionary lookup methods,
- reduced repeated hash lookups,
- reduced repeated arithmetic and indexing.

These changes preserve algorithm semantics while improving execution efficiency in the innermost traversal loop.
