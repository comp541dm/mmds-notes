# Frequent Itemsets

## Market-Basket Model
  - Describes many to many relationships between two kinds of objects
  - Have items and baskets
  - Set of items in a basket (called the itemset), usually a small number
  - The *support* for an itemset I is the number of baskets for which I is a subset.
    - *support threshold* s: a number which represents our threshold for "frequent" items
    - I is *frequent* if it support is s or more

### Applications
  - Commonly used for stores shopping carts to see which items are frequently purchased together
  - *Related concepts*
    - items are words, and baskets are documents. Look for sets of words that are common amongst the documents to find similaries
  - *Plagiarism*
    - items are documents, and baskets are sentences.
    - item is in a basket if the sentence is in the document (seems backwards but correct)
    - if we find pairs of items that appear together, we have documents that share sentences
  - *Biomarkers*
    - items are biomarkers such as genes, or blood proteins, and diseases.
    - baskets are set of data about a patient

### Association Rules
  - *association rules* are if-then rules that have the from I -> j, where I is a set of items and j is an item.
    - If I appears in some basket, then j is "likely" to appear in the same basket.
    - the *confidence* of the rule I -> j to be the ratio of the support for I ∪ {j} to the support of I.
      - the fraction of all the baskets with all of I that also contain j
    - the *interest* of an I -> j is the difference between its confidence and the fraction of buckets that contain j
      - if I has no influence of j, then the interest should be around 0.
        - This happens when buckets that contain j are the same as buckets that contain j with I
      - I can have a negative influence on j
      - e.g.: {diapers} -> beers has high interest because the fraction of diaper-buyers who buy beer is significantly greater than the fraction of all customers that buy beer.

### Finding Association Rules with High Confidence
  - Looking for associate rules I -> j that apply to a high number of baskets
    - support for I must be resonably high (~1%)
    - confidence of the rule must be resonably high (~50%)
    - By the last two statements, I ∪ {j} will have fairly high support
  - Find all itemsets that meet a threshold of support, and calculate support for each.
    - find within them all the association rules with high support and high confidence
    - If J is a set of n items that are found to be frequent, there are only n possible association rules involving this set of items, namely J - {j} -> j for each j in J.
    - If J is frequent, J - {j} must be at least as frequent, so it is also a frequent itemset.
    - We have already computed the support for J and J - {j}
      - Their ratio is the confidence of the rule J - {j} -> j
    - Assumed that there are not too many frequent itemsets.
      - Adjust support threshold so we don't get too many items

## A-Priori Algorithm (6.2)
- As size of the subsets we want to generate gets larger, the time required grows larger; in fact takes approximately time nᵏ/k! to generate all the subsets of size k for a basket with n items.
- Often we only need small frequent itemsets so k is around 2 or 3.
- If we need large k, can often eliminate many of the items in each basket so the value of n drops as k increases.

### Representing Pairs
  - We can represent pairs of n items with integer pairs and translate using a hash table from integer to item.

### Triangluar-Matrix Method
  - Can easily store the pairs {i,j} in a two-dimensional array
  - Half of the array becomes useless since we only want to count a pair once
  - One solution is to use a one-dimensional triangular array, call this array A
    - Store in A[k] the count for the pair {i,j} for 1 <= i < j <= n, where
    - k = (i - 1)(n - i/2) + j - i
    - Pairs are stored in lexicographic order: {1,2}, {1,3},...,{1,n}, then {2,3}, {2,4},...,{2,n}, etc.

### Triples Method
  - Store count, c, of the pair {i,j} with i < j
  - Use hash table with i and j as the search key
  - Don't need to store counts with 0, but need to store three integers
  - Triangular matrix is better if at least 1/3 of the possible pairs actually appear in some basket
  - If significantly fewer than 1/3 of possible pairs occur, we should consider triples method

### Monotonicity of Itemsets
  - *Monotonicity*: If a set I of items is frequent, then so is every subset of I.
  - If we are given a support threshold s, then we say an itemset is *maximal* if no superset is frequent.
  - If we only list maximal itemsets, then we only all subsets of maximal itemsets are frequent, and no set that is not a subset of some maximal itemset can be frequent.

### A-Priori
  - The A-Priori Algorithm reduces the number of pairs that must be count but needs to make two passes over the data instead of one.

#### The First Pass of A-Priori
  - Create two tables
  - First table translates item names into integers from 1 to n.
  - Second table is an array of counts; the ith array element counts the occurences of the item numbered i.
  - Read each item in a basket and translate its name into an integer.
  - Use that integer to index into the array of counts, and we add 1 to the integer found there.
  - After this pass we can examine the counts to determine frequent singletons

#### Second Pass of A-Priori
  - Use new numbering from 1 to m for just the frequent items
  - Create a new table, the frequent-items table, indexed 1 to n, and the entry for i is either 0, if item i is not frequent, or a unique integer in the range 1 to m if the item i is frequent.
  - Count all pairs of two frequent items.
  - A pair cannot be frequent unless both its members are frequent so we find all frequent pairs.
  - Space used is 2m² bytes rather than 2n² bytes, if we use the triangluar-matrix method for counting
  - The algorithm for the second pass is described below
  1. For each basket, look at the frequent-items table to see which of its items are frequent.
  2. Generate all pairs of frequent items in that basket (double loop)
  3. For each pair, add one to its count in the data structure used to store counts
  - Examine the structure of counts to determine which pairs are frequent.

#### A-Priori for All Frequent Itemsets
  - Take one pass for each set-size k
  - If no frequent itemsets are a certain size are found, then monotonicity tells us there can be no larger frequent itemsets.
  - Moving from one size k to the next size k + 1 is as follow:
    1. C_{k} is the set of candidate itemsets of size k, every k - 1 of which is an itemset in L_{k - 1}:
      - The itemsets that we must count in order to determine whether they are in fact frequent.
    2. L_{k} is the set of truely frequent itemsets of size k.
      - Find this by making a pass through the baskets and counting all and only the itemsets of size k that are in C_{k}.
      - Those itemsets that have count at least s are in L_k
