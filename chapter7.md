# Clustering
- Clustering is the process of examining a collection of "points" and grouping the points into "clusters" according to some distance measure.
- Points in the same cluster have a small distance from one another, while points in different clusters are at a large distance from one another.

## 7.1 Introduction to Clustering Techniques
  - A dataset suitable for clustering is a collection of *points*, which are objects belonging to some *space*.
  - All spaces for which we can perform a clustering have a distance measure.
    1. Distances are always nonnegative, and only the distance between a point and itself is 0.
    2. Distance is symmetric; it doesn't matter is which you consider the points when computing their distance
    3. Distance measures obey the triangle inequality; the distance from x to y to z is never less than going from x to z directly.
### Clusting Strategies
  1. *Hierarchical* or *agglomerative* algorithms
    - Each point starts in its own cluster
    - Clusters are combined based on "closeness" (based on distance measures)
    - Combining stops when further combinations are undesireable (e.g., too few clusters, too spread out, etc.)
  2. Point assignment algorithms
    - Points considered in some order, and each one is assigned to the cluster into which it fits best
    - Initial cluster are estimated before each point is assigned.
Other distinguishing features are:
  1. Whether the algorithm assumes a Euclidean space, or arbitrary distance measure.
     - With euclidean spaces, we can summarize a collection of points by their centroid - the average of the points
     - No definition of a centroid in a non-Euclidean space, means we need another way of summarizing clusters
  2. Whether the algorithm assume the data is small enough to fit in main memory, or whether the data must reside in secondary memory, primarily.
    - Algorithms for large amounts of data must take shortcuts
    - For Large amounts of data we must also summarize the clusters in main memory.

### The Curse of Dimensionality
In high dimensions, almost all pairs of points are equally far away from one another, and any two vectors are almost orthogonal
...
Describe more

## 7.2 Hierarchical Clustering
  - Can only be used for relatively small datasets
  - Additional problems arise when data is non-Euclidean: we use "clustroids" instead of centroids in this case
  -

### Hierarchical Clustering in a Euclidean Space
Begin with a point in each cluster
  1. How will the clusters be represented?
  2. How will we choose which two clusters to merge?
  3. When will be stop combining clusters?
- In Euclidean space, we can represent clusters with centroids.
- A point is the centroid so initialization is easy
- Pick close clusters to merge by Euclidean distance

#### Algorithm Efficiency
  - Basic algorithm runs in O(n³) time. Each step we must compute the distance between each pairs of clusters. Each step takes n² + (n - 1)² + (n - 2)³ + ... time which sums to O(n³).
  - Alternative Algorithm O(n² log n)
    1. Compute the distance between all pairs of points. O(n²)
    2. Put the pairs and distances into a priority queue, so we can find smallest distance in one step. O(n²)
    3. When merging clusters C and D, remove all entries in the priority queue involving these clusters. O(n log n) because at most 2n deletions, and removing from a priority queue takes O(log n) time.
    4. Compute all the distances between new cluster and the remaining clusters. O(n log n), as there are at most n entries to insert into the priority queue, which takes O(log n) time.

#### Other Merging Methods
  - First method was merging clusters with smallest distances between centroids.
  - Alternative methods include
    1. Take the distance between any two clusters to be the minimum of the distances between any two points, one chosen from each cluster.
    2. Take the distance between two clusters to be the average distance of all pairs of points, one from each cluster.
    3. The *radius* of a cluster is the maximum distance between all the points and the centroid. Combine the two clusters whose resulting cluster has the lowest radius.
    4. The *diameter* of a cluster is the maximum distance between any two points of the cluster. (Not directly related to radius)

#### Stopping approaches
1. We know something about the data, e.g., that there should be 3 clusters. So we just stop when there are k clusters.
2. The best combination of existing clusters produces a cluster that is inadequate. Such as average distance between centroid and points is smaller than a certain distance.
3. Continue until there is only one cluster. This is useless.

Possibilities for second approach
- Stop if the diameter (or radius, or other variants) of the cluster that results from the best merger is above a certain threshold.
- Stop if the *density* of the cluster that results from the best merger is below some threshold. Density can be defined in many ways but should be roughly the number of cluster points per unit volume of the cluster.
- Stop when there is evidence that the next pair of clusters to be combined yields a bad cluster. This could be that a point added largely increases the average diameter.


### Non-Euclidean Spaces
  - When the space is non-euclidean we need to choose one of the points itself to represent the cluster.
  - This representative point is called the *clustroid*
  - We can set the point by minimizing
     1. The sum of the distances to the other points in the cluster
     2. The maximum distance to another point in the cluster
     3. The sum of squares of the distances to the other points in the cluster
  - Previous methods for measuring distance if we replace centroid with clustroid
    - E.g., merge two clusters whose clustroids are closest.
  - Radius and diameter still make sense in non-Euclidean space
  - No change in stopping rules for non-Euclidean space

## 7.3 K-means Clustering
  - point-assignment algorithm
  - Make assumptions that
    1. euclidean space
    2. k number of clusters, known in advance

Algorithm
```
Initially choose k points that are likely to be in
    different clusters;
Make these points the centroids of their clusters;
FOR each remaining point p DO
    find the centroid to which p is closest;
    Add p to the cluster of that centroid;
    Adjust the centroid of that cluster to account for p;
END;
```
- Optionally, fix the centroids of the clusters and to reassign each point, including the k initial points, to the k clusters.

#### Initializing Clusters for K-Means
Two different approaches to picking points
1. Pick points that are as far away from one another as possible
```
Pick the first point at random;
WHILE there are fewer than k points DO
    Add the point whose minimum distance from the selected
      points is as large as possible;
END;
```
2. Cluster a sample of the data so there are k clusters (perhaps hierarchically)
  - Pick a point from each cluster, perhaps that point closest to the centroid of the cluster.

#### Picking the right value of k
- if we can measure the quality of the clustering for various values of k, we can usually guess what the right value of k is.
- Can pick average radius or diameter
- Want to select k right before value starts to increase rapidly
[Imgur](http://i.imgur.com/PjUoTcB.png)
- If we have no idea what k is, we can try values k=1,2,4,8,... that will find a good k that grows logarithmically with the true number
- Eventually will have two values v and 2v between which there is very little change in the measure
- good k values lies between v/2 and v, can use binary search to find best value for k in log v clustering operations,
- Since true value of k is at least v/2, we have used a number of clustering that is logarithmical in k


### Bradley, Fayyad, and Reina (BFR) Algorithm
  - is designed to cluster data in a high-dimensional Euclidean space
  - the clusters must be normally distributed about a centroid
    - the BFR Algorithm assumes the axes of the cluster align with the axes of the space
  - The *discard set*: simple summaries of the clusters themselves
  - The *compressed set*: summaries, but for sets of points that have been found close to each other but not close to any cluster
  - The *retained set*: points that cannot be assigned to a cluster nor are they close to any other points that we can represent them by a compressed set.
  - The discard and compressed sets are represented by 2d + 1 values for a d dimensional data set
    a) The number of points represented, N
    b) The sum of the components of all the points in each dimension.
      - This data is a vector SUM of length d, and the component in the ith dimension is SUMᵢ
    c) The sum of the squares of the components of all the points in each dimension.
      - This data is a vector SUMSQ of length d, and its component in the ith dimension is SUMSQᵢ
  - Real goal is to represent a set of points by their count, their centroid and the standard deviation in each dimension.
  - The centroid's coordinate in the ith dimension is SUMᵢ / N
  - The variance in the ith dimension is SUMSQᵢ / N - (SUMᵢ / N)²
  - We represent a cluster with N, SUM, SUMSQ so we can recompute easier when we need to adjust the centroid
    1. All points that are sufficiently close to the centroid of a cluster are added to that cluster
    2. For the points that are not sufficiently close to any centroid, we cluster them, along with the points in the retained set.
      - Clusters of more than one point are summarized and added to the compressed set.
      - Singleton clusters become the retained set of points
    3. We now have miniclusters derived from our attempt to cluster new points and the old retained set, and we have the miniclusters from the old compressed set.
      - Although none of these miniclusters can be merged with one of the k clusters, they might merge with one another.
      - Use the same criterion that was previously mentioned in 7.2
    4. Points that are assigned to a cluster or a minicluster, i.e., those that are not in the retained set, are written out, with their assignment, to secondary memory.
      - Finally, if this is the last chunk of input data, we need to do something with the compressed and retained sets. We can treat them as outliers, and never cluster them at all. Or, we can assign each point in the retained set to the cluster of the nearest centroid.

An important decision that must be examined is how we decide whether a new point p is close enough to one of the k clusters that it makes sense to add p to the cluster. Two options
  1.  Add p to a cluster if it not only has the centroid closest to p, but it is very unlikely that, after all the points have been processed, some other cluster centroid will be found to be nearer to p.
  2. We can measure the probability that, if p belongs to a cluster, it would be found as far as it is from the centroid of that cluster.
    - This calculation makes use of the fact that we believe each cluster to consist of normally distributed points with the axes of the distribution aligned with the axes of the space.
    -  It allows us to make the calculation through the Mahalanobis distance of the point, which we shall describe next.
  - The *Mahalanobis distance* is essentially the distance between a point and the centroid of a cluster, normalized by the standard deviation of the cluster in each dimension.
  - To assign point p to a cluster, we compute the Mahalanobis distance between p and each of the cluster centroids.
  - We choose that cluster whose centroid has the least Mahalanobis distance, and we add p to that cluster provided the Mahalanobis distance is less than a threshold.

#### Exercises
Exercise 7.3.1
  - For the points of Fig. 7.8, if we select three starting points using the method of Section 7.3.2, and the first point we choose is (3,4), which other points are selected.

Exercise 7.3.3
  - Give an example of a dataset and a selection of k initial centroids such that when the points are reassigned to their nearest centroid at the end, at least one of the initial k points is reassigned to a different cluster.

Exercise 7.3.5
  - Suppose a cluster of three-dimensional points has standard deviations of 2, 3, and 5, in the three dimensions, in that order. Compute the Mahalanobis distance between the origin (0, 0, 0) and the point (1, −3, 4).
