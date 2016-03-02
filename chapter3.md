# Chapter 3

## Minhashing
Convert large sets to short signatures, while preserving similarity.
  - Signatures - short integer vectors that represent the sets, and reflect their similarity



## Locality-Sensitive Hashing for Documents
  - Minhashing documents may still be too big.
  - locality-sensitive hashing only looks at pairs that are likely to be similar, without looking at every pair.
  - We only want to focus on pairs that are likely to be similar.
  - The only pairs that are likely to be similar are those in which are hashed to the same bucket, for any of the buckets from the minhash similarity matrix. These are called the candidate pairs
  - Dissimilar pairs that do hash to the same bucket are called false positives.
  - Truly similar pairs will hopefully hash to the same bucket under at least one of the buckets. Those that do not are called false negatives.

### Choosing the hashings
One effective way to choose the minhashing is to
  1. Divide the minhash signature matrix into b bands with r rows.
  2. For each band there is a hash function that takes vectors of r integers and hashes them to some large number of buckets
  3. Use the same hash function for all the bands, but use a separate bucket array for each band so columns with same vectors in different bands will not hash to the same bucket.

### Analysis of Banding Technique
Using b bands and r rows each with a particular pair of documents that have Jaccard similarity s.
  1. The probability that the signatures agree in all rows of one particular band is sʳ.
  2. The probability that the signatures disagree in at least one row of a particular band is 1 - sʳ
  3. The probability that the signatures agree in all the rows of at least one band, and therefore become a candidate pair, is 1 - (1 - sʳ)ᵇ
  - This function is called an S-curve, and the threshold, the value of similarity s at which the probability of becoming a candidate is 1/2, is a function of b and r.

### Combining Techniques
  1. Pick a value of k and construct from each document the set of k-shingles.
Optionally, hash the k-shingles to shorter bucket numbers.
  2. Sort the document-shingle pairs to order them by shingle.
  3. Pick a length n for the minhash signatures. Feed the sorted list to the algorithm of Section 3.3.5 to compute the minhash signatures for all the documents.
  4. Choose a threshold t that defines how similar documents have to be in order for them to be regarded as a desired "similar pair." Pick a number of bands b and a number of rows r such that br = n, and the threshold t is approximately (1/b)^{1/r}. If avoidance of false negatives is important, you may wish to select b and r to produce a threshold lower than t; if speed is important and you wish to limit false positives, select b and r to produce a higher threshold.
  5. Construct candidate pairs by applying the LSH technique of Section 3.4.1.
  6. Examine each candidate pair’s signatures and determine whether the fraction of components in which they agree is at least t.
7. Optionally, if the signatures are sufficiently similar, go to the documents themselves and check that they are truly s


## Exercises 3.4
```
import numpy as np
def s_curve(r, b, s):
    return 1 - (1 - s**r)**b
s = np.arange(.1, 1, .1)
print "{:>15} {:>15} {:>15} {:>15}".format("s", "r=3 and b=10", "r=6 and b=20", "r=5 and b=50")
for i,x,y,z in np.array([s, s_curve(3, 10, s), s_curve(6,20, s), s_curve(5, 20, s)]).T:
    print "{:15.1f} {:15.4f} {:15.4f} {:15.4f}".format(i,x,y,z)
```

## Exercises 3.5
```
def findthreshold(r, b):
    for i in np.arange(.1, .9, .0001):
        if np.abs(s_curve(r, b, i) - 0.5) <= .001:
            return i

def thresholdestimate(r, b):
    return (1/float(b))**(1/float(r))

r = 3
b = 10
print "r={}, b={}, threshold={:4f}, estimate={:4f}".format(r, b, findthreshold(r, b), thresholdestimate(r,b))
r = 6
b = 20
print "r={}, b={}, threshold={:4f}, estimate={:4f}".format(r, b, findthreshold(r, b), thresholdestimate(r,b))
r = 5
b = 50
print "r={}, b={}, threshold={:4f}, estimate={:4f}".format(r, b, findthreshold(r, b), thresholdestimate(r,b))
```

## Exercise 3.6
Estimate 1 - (1 - sʳ)ᵇ when sʳ is very small.
Estimate with e^{−ab} which is e^{-sʳb}
So final estimate is 1 - e^{-sʳb}
