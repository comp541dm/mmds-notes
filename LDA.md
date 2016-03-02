# Latent Dirichlet Allocation

- [Latent Dirichlet Allocation](https://www.cs.princeton.edu/~blei/papers/BleiNgJordan2003.pdf)
- [Singular Value Decomposition](https://www.ling.ohio-state.edu/~kbaker/pubs/Singular_Value_Decomposition_Tutorial.pdf)
- [Generative Learning Algorithms](http://cs229.stanford.edu/notes/cs229-notes2.pdf)
- [Dirichlet Distribution, Dirichlet Process and Dirichlet Process Mixture](http://www.cs.cmu.edu/~epxing/Class/10701-08s/recitation/dirichlet.pdf)

Need to look up:
  - generative probabilistic model
  - (Three-level) Hierarchical Bayesian Model (know vaguely from math 440b)
  - (Level 1?) finite mixture (for the underlying topics)
  - (Level 2?) topics then modeled as infinite mixture over an underlying set of topic probabilities
  - (Level 3?)
  - empirical Bayes parameter estimation
  - latent semantic indexing (LSI)
  - singular value decomposition
    - (This seems to come up everywhere, so good to read into)

## Overview
  - LDA is based on exchangeability of both documents and words
  - Exchangeability - conditionally (on underlying latent variables) independent and identically distributed, which is NOT equivalent that the r.v.s are i.i.d
  - "We aim to demonstrate in the current paper that, by taking
the de Finetti theorem seriously, we can capture significant intra-document statistical structure via
the mixing distribution."
  - the LDA model is not necessarily tied to text
  - A _word_ is the basic unit of discrete data, defined to be an item from a vocabulary indexed by
{1,...,V}. We represent words using unit-basis vectors that have a single component equal to
one and all other components equal to zero. Thus, using superscripts to denote components,
the vth word in the vocabulary is represented by a V-vector w such that wv = 1 and wu = 0 for
u 6= v.
  - A _document_ is a sequence of N words denoted by w = (w_1,w_2,...,w_N), where w_n is the nth
word in the sequence.
  - A _corpus_ is a collection of M documents denoted by D = {w_1,w_2,...,w_M}
  - "We wish to find a probabilistic model of a corpus that not only assigns high probability to
members of the corpus, but also assigns high probability to other "similar" documents."


## Model
  - "Latent Dirichlet allocation (LDA) is a generative probabilistic model of a corpus. The basic idea is
that documents are represented as random mixtures over latent topics, where each topic is characterized
by a distribution over words."
  - For each document w in a corpus D:
    1. Choose N ~ Poisson(\xi) (Where N is the number of words in the document)
    2. Choose \theta ~ Dir(\alpha)
    3. For each of the N words w_n:
      a) Choose a topic z_n ~ Multinomial(\theta)
      b) Choose a word w_n from p(w_n| z_n, \beta), a multinomial probability conditioned on the topic
z_n.
  -
