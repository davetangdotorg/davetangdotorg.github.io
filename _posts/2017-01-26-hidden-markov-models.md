---
layout: post
title: "Hidden Markov Models"
description: ""
category: machine learning
tags: [hmm]
---
{% include JB/setup %}

Example and code based on the brilliant tutorial: [Hidden Markov Models (HMMs) in R](http://a-little-book-of-r-for-bioinformatics.readthedocs.io/en/latest/src/chapter10.html). Using DNA sequence as an example, HMMs are used to assign states to nucleotides. We don't know the states (they are hidden) and we use HMMs to infer the most likely state. To get started, we will generate a sequence where we know the state. Then we can see how well the HMM performed by comparing the real states with the inferred states.

A HMM can be defined by (*A*, *B*, *π*), where *A* is a matrix of state transition probabilities, *B* is a vector of state emission probabilities and *π* is a vector of initial state distributions. Here's the state transition probabilities:

``` r
my_state <- c("AT-rich", "GC-rich")
at_tran_prob <- c(0.7, 0.3)
gc_tran_prob <- c(0.1, 0.9)
tran_matrix <- matrix(c(at_tran_prob, gc_tran_prob), 2, 2, byrow = TRUE)
rownames(tran_matrix) <- my_state
colnames(tran_matrix) <- my_state
tran_matrix
```

    ##         AT-rich GC-rich
    ## AT-rich     0.7     0.3
    ## GC-rich     0.1     0.9

Here's the state emission probabilities:

``` r
my_nuc <- c("A", "C", "G", "T")
at_emi_prob <- c(0.39, 0.1, 0.1, 0.41)
gc_emi_prob <- c(0.1, 0.41, 0.39, 0.1)
emi_matrix <- matrix(c(at_emi_prob, gc_emi_prob), 2, 4, byrow = TRUE)
rownames(emi_matrix) <- my_state
colnames(emi_matrix) <- my_nuc
emi_matrix
```

    ##            A    C    G    T
    ## AT-rich 0.39 0.10 0.10 0.41
    ## GC-rich 0.10 0.41 0.39 0.10

Now to generate a sequence based on our state transition and state emission probabilites.

``` r
generatehmmseq <- function(tran_matrix, emi_matrix, init_prob, seq_len){
  new_seq <- character()
  new_seq_state <- character()
  first_state <- sample(my_state, 1, prob=init_prob)
  my_prob <- emi_matrix[first_state,]
  first_nuc <- sample(my_nuc, 1, prob=my_prob)
  new_seq[1] <- first_nuc
  new_seq_state[1] <- first_state
  
  for (i in 2:seq_len){
    prev_state <- new_seq_state[i-1]
    state_probs <- tran_matrix[prev_state,]
    cur_state <- sample(my_state, 1, prob=state_probs)
    my_prob <- emi_matrix[cur_state,]
    nuc <- sample(my_nuc, 1, prob=my_prob)
    new_seq[i] <- nuc
    new_seq_state[i] <- cur_state
  }
  
  my_result <- list(sequence = new_seq, state = new_seq_state)
}

set.seed(31)
init_prob <- c(0.5, 0.5)
my_seq <- generatehmmseq(tran_matrix, emi_matrix, init_prob, 30)
my_seq
```

    ## $sequence
    ##  [1] "C" "A" "G" "C" "T" "G" "T" "C" "G" "C" "G" "G" "C" "T" "C" "G" "G"
    ## [18] "G" "C" "C" "G" "G" "G" "C" "C" "C" "A" "A" "A" "T"
    ## 
    ## $state
    ##  [1] "AT-rich" "AT-rich" "GC-rich" "GC-rich" "AT-rich" "AT-rich" "AT-rich"
    ##  [8] "GC-rich" "GC-rich" "GC-rich" "GC-rich" "GC-rich" "GC-rich" "GC-rich"
    ## [15] "GC-rich" "GC-rich" "GC-rich" "GC-rich" "GC-rich" "GC-rich" "GC-rich"
    ## [22] "GC-rich" "GC-rich" "GC-rich" "GC-rich" "GC-rich" "AT-rich" "AT-rich"
    ## [29] "AT-rich" "AT-rich"

Now that we have our sequence, we can see how well the HMM predicts the states.

``` r
viterbi <- function(sequence, transitionmatrix, emissionmatrix){
  most_prob_state <- character()
  v <- makeViterbimat(sequence, transitionmatrix, emissionmatrix)
  mostprobablestatepath <- apply(v, 1, function(x) which.max(x))
  prevnucleotide <- sequence[1]
  prevmostprobablestate <- mostprobablestatepath[1]
  prevmostprobablestatename <- my_state[prevmostprobablestate]
  most_prob_state[1] <- my_state[prevmostprobablestate]
  for (i in 2:length(sequence)){
    nucleotide <- sequence[i]
    mostprobablestate <- mostprobablestatepath[i]
    mostprobablestatename <- my_state[mostprobablestate]
    most_prob_state[i] <- my_state[mostprobablestate]
    prevnucleotide <- nucleotide
    prevmostprobablestatename <- mostprobablestatename
  }
  return(most_prob_state)
}

makeViterbimat <- function(sequence, transitionmatrix, emissionmatrix){
  sequence <- toupper(sequence)
  numstates <- dim(transitionmatrix)[1]
  v <- matrix(NA, nrow = length(sequence), ncol = dim(transitionmatrix)[1])
  v[1, ] <- 0
  v[1,1] <- 1
  for (i in 2:length(sequence)){
    for (l in 1:numstates){
      statelprobnucleotidei <- emissionmatrix[l,sequence[i]]
      v[i,l] <- statelprobnucleotidei * max(v[(i-1),] * transitionmatrix[,l])
    }
  }
  return(v)
}

my_most_prob_state <- viterbi(my_seq$sequence, tran_matrix, emi_matrix)
data.frame(nuc = my_seq$sequence, real = my_seq$state, inferred = my_most_prob_state, match = my_seq$state == my_most_prob_state)
```

    ##    nuc    real inferred match
    ## 1    C AT-rich  AT-rich  TRUE
    ## 2    A AT-rich  AT-rich  TRUE
    ## 3    G GC-rich  GC-rich  TRUE
    ## 4    C GC-rich  GC-rich  TRUE
    ## 5    T AT-rich  GC-rich FALSE
    ## 6    G AT-rich  GC-rich FALSE
    ## 7    T AT-rich  GC-rich FALSE
    ## 8    C GC-rich  GC-rich  TRUE
    ## 9    G GC-rich  GC-rich  TRUE
    ## 10   C GC-rich  GC-rich  TRUE
    ## 11   G GC-rich  GC-rich  TRUE
    ## 12   G GC-rich  GC-rich  TRUE
    ## 13   C GC-rich  GC-rich  TRUE
    ## 14   T GC-rich  GC-rich  TRUE
    ## 15   C GC-rich  GC-rich  TRUE
    ## 16   G GC-rich  GC-rich  TRUE
    ## 17   G GC-rich  GC-rich  TRUE
    ## 18   G GC-rich  GC-rich  TRUE
    ## 19   C GC-rich  GC-rich  TRUE
    ## 20   C GC-rich  GC-rich  TRUE
    ## 21   G GC-rich  GC-rich  TRUE
    ## 22   G GC-rich  GC-rich  TRUE
    ## 23   G GC-rich  GC-rich  TRUE
    ## 24   C GC-rich  GC-rich  TRUE
    ## 25   C GC-rich  GC-rich  TRUE
    ## 26   C GC-rich  GC-rich  TRUE
    ## 27   A AT-rich  GC-rich FALSE
    ## 28   A AT-rich  AT-rich  TRUE
    ## 29   A AT-rich  AT-rich  TRUE
    ## 30   T AT-rich  AT-rich  TRUE
