# WTF: The Who to Follow Service at Twitter

Source: [paper](https://stanford.edu/~rezab/papers/wtf_overview.pdf)

WTF ("Who to Follow") is Twitter's user recommendation service.

In the current interface, the WTF box is prominently featured in the left rail of the web client as well as in many other contexts across multiple platforms. Beside powering user recommendation, WTF is also used for search relevance, discovery, promoted products, and other services as well.

## The Twitter Graph

The Twitter graph consists of vertices representing users, connected by directed edges representing the "follow" relationship. As of August 2012, the graph contains over 20 billion edges when considering only active users.

The Twitter graph is stored in a graph database called FlockDB, which uses MySQL as the underlying storage engine. Sharding and replication are handled by a framework called Gizzard. The entire system sustains hundreds of thousands of reads per second and tens of thousands of writes per second.

FlockDB and Gizzard are, unfortunately, not appropriate for the types of access patterns often seen in large-scale graph analytics and algorithms for computing recommendations of graphs.

## Design Considerations

### To Hadoop or not to Hadoop?

Although MapReduce excels at processing large amounts of data, iterative graph algorithms are a poor fit for the programming model. Each iteration corresponds to a MapReduce job. There are many shortcomings to this:
 * MapReduce jobs have relatively high startup cost.
 * Scale-free graphs, whose edge distributions follow power laws, create stragglers in the reduce phase.
 * At each iteration, the algorithm must shuffle the graph structure (i.e., adjacency lists) from mappers to the reducers. Since in most cases the graph structure is static, this represents wasted effort (sorting, network traffic, etc.).
 * The result is serialized to HDFS, along with the graph structure, at each iteration. This provides excellent fault tollerance, but at the cost of performance.
 
Finally, the WTF project required an online serving component for which Hadoop does not provide a solution. Although user recommendations, for the most part, can be computed as offline batch jobs, we still needed a robust, low-latency mechanism to serve results to users.
