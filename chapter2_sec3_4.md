# Chapter 2 Section 2.3 & 2.4 Notes

## 2.3 Algorithms Using MapReduce
- The entire distributed-file-system environment only makes sense when
  - Files are very large
  - Rarely updated in place
- Good for when relatively little changes with data that requires expensive calculations
  - E.g., large matrix-vector multiplications (PageRank)

### Matrix-Vector Multiplication
  - Assume an n x n matrix M
  - Vector v of length n
  - M times v is a vector x of length n
    - x_i = \sum_{j = 1}^{n} m_{ij} v_j
  - MapReduce good when n is very large (but not so large that a vector won't fit in memory)
  - Matrix M and vector v stored in a file of DFS
    - Matrix stored as (i,j, m_{ij})
    - Vector stored as (i, v_{i})
  - Map Function:
    - Input: chunk of matrix M. (m_{ij})
    - Output: (i, m_{ij} v_{j})
    - All components that makes up x_i will get the same key i
  - Reduce Function:
    - Sums all the values associated with a given key i.
    - (i, x_i)

### Relational-Algebra Operations
...

## Exercises
### Exercise 2.3.1
Design MapReduce algorithms to take a very large file of integers and produce as output:
a) The largest integer
  - Map function: val -> (1, val) (Identity function)
  - Reduce function: (1, X=[val_0, val_1, ..., val_n]) -> (1,max(X))
b) The average of all the integers
  - Map function: val -> (1, val)
  - Reduce function: (1, X=[v_0, v_1, ..., v_n]) -> (1, avg(X))
c) The same set of integers, but with each integer appearing only once.
  - Map function: val -> (val, val)
  - Reduce function (val, X=[val, val, ..., val]) -> (1, head(X))
d) The count of the number of distinct integers in the input.
  - Map function: val -> (1, val)
  - Reduce function: (1, X=[])
