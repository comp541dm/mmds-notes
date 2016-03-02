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
