---
layout: post
title: "The HMM package"
description: ""
category: machine learning
tags: [hmm]
---
{% include JB/setup %}

Using the [HMM package](https://cran.r-project.org/web/packages/HMM/index.html). Generate sequence as previous post.

``` r
my_state <- c("AT-rich", "GC-rich")
at_tran_prob <- c(0.7, 0.3)
gc_tran_prob <- c(0.1, 0.9)
tran_matrix <- matrix(c(at_tran_prob, gc_tran_prob), 2, 2, byrow = TRUE)
rownames(tran_matrix) <- my_state
colnames(tran_matrix) <- my_state

my_nuc <- c("A", "C", "G", "T")
at_emi_prob <- c(0.39, 0.1, 0.1, 0.41)
gc_emi_prob <- c(0.1, 0.41, 0.39, 0.1)
emi_matrix <- matrix(c(at_emi_prob, gc_emi_prob), 2, 4, byrow = TRUE)
rownames(emi_matrix) <- my_state
colnames(emi_matrix) <- my_nuc

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

Create HMM object.

``` r
library(HMM)
my_hmm <- initHMM(my_state, my_nuc, transProbs = tran_matrix, emissionProbs = emi_matrix)
```

Best path using `viterbi()`.

``` r
my_most_prob_state <- viterbi(hmm = my_hmm, observation = my_seq$sequence)
data.frame(nuc = my_seq$sequence, real = my_seq$state, inferred = my_most_prob_state, match = my_seq$state == my_most_prob_state)
```

    ##    nuc    real inferred match
    ## 1    C AT-rich  GC-rich FALSE
    ## 2    A AT-rich  GC-rich FALSE
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
    ## 27   A AT-rich  AT-rich  TRUE
    ## 28   A AT-rich  AT-rich  TRUE
    ## 29   A AT-rich  AT-rich  TRUE
    ## 30   T AT-rich  AT-rich  TRUE
