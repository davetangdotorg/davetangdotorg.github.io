---
layout: post
title: "Text mining"
description: "Using the tm package in R"
category: text mining
tags: [tm]
---
{% include JB/setup %}

Using the tm package
--------------------

Using the corpus already prepared for us.

``` r
library(tm)
```

    ## Loading required package: NLP

``` r
data(crude)
crude
```

    ## <<VCorpus>>
    ## Metadata:  corpus specific: 0, document level (indexed): 0
    ## Content:  documents: 20

``` r
inspect(crude[1:3])
```

    ## <<VCorpus>>
    ## Metadata:  corpus specific: 0, document level (indexed): 0
    ## Content:  documents: 3
    ## 
    ## $`reut-00001.xml`
    ## <<PlainTextDocument>>
    ## Metadata:  15
    ## Content:  chars: 527
    ## 
    ## $`reut-00002.xml`
    ## <<PlainTextDocument>>
    ## Metadata:  15
    ## Content:  chars: 2634
    ## 
    ## $`reut-00004.xml`
    ## <<PlainTextDocument>>
    ## Metadata:  15
    ## Content:  chars: 330

Create a Term Document Matrix

``` r
tdm <- TermDocumentMatrix(crude)
tdm
```

    ## <<TermDocumentMatrix (terms: 1266, documents: 20)>>
    ## Non-/sparse entries: 2255/23065
    ## Sparsity           : 91%
    ## Maximal term length: 17
    ## Weighting          : term frequency (tf)

``` r
my_matrix <- as.matrix(tdm)
dim(my_matrix)
```

    ## [1] 1266   20

``` r
table(my_matrix == 0)
```

    ## 
    ## FALSE  TRUE 
    ##  2255 23065

``` r
# sparsity is the number of zeros
# i.e. words that are not present in documents
23065/(23065+2255)
```

    ## [1] 0.91094

Some functions that can be used on `tdm`.

``` r
class(tdm)
```

    ## [1] "TermDocumentMatrix"    "simple_triplet_matrix"

``` r
methods(class = "TermDocumentMatrix")
```

    ##  [1] as.DocumentTermMatrix as.TermDocumentMatrix c                    
    ##  [4] dimnames<-            Docs                  findAssocs           
    ##  [7] inspect               nDocs                 nTerms               
    ## [10] plot                  print                 [                    
    ## [13] Terms                 tm_term_score         t                    
    ## see '?methods' for accessing help and source code

``` r
methods(class = "simple_triplet_matrix")
```

    ##  [1] aperm      as.array   as.matrix  as.vector  cbind      c         
    ##  [7] dimnames<- dimnames   dim<-      dim        duplicated is.numeric
    ## [13] Math       mean       Ops        print      rbind      [<-       
    ## [19] [          split      Summary    t          unique    
    ## see '?methods' for accessing help and source code

``` r
findFreqTerms(x = tdm, lowfreq = 10)
```

    ##  [1] "about"      "and"        "are"        "bpd"        "but"       
    ##  [6] "crude"      "dlrs"       "for"        "from"       "government"
    ## [11] "has"        "its"        "kuwait"     "last"       "market"    
    ## [16] "mln"        "new"        "not"        "official"   "oil"       
    ## [21] "one"        "opec"       "pct"        "price"      "prices"    
    ## [26] "reuter"     "said"       "said."      "saudi"      "sheikh"    
    ## [31] "that"       "the"        "they"       "u.s."       "was"       
    ## [36] "were"       "will"       "with"       "would"

``` r
# limit matrix to specific words
inspect(DocumentTermMatrix(crude,
                           list(dictionary = c("government", "market", "official"))))
```

    ## <<DocumentTermMatrix (documents: 20, terms: 3)>>
    ## Non-/sparse entries: 15/45
    ## Sparsity           : 75%
    ## Maximal term length: 10
    ## Weighting          : term frequency (tf)
    ## 
    ##      Terms
    ## Docs  government market official
    ##   127          0      0        0
    ##   144          0      3        0
    ##   191          0      0        0
    ##   194          0      0        0
    ##   211          0      0        0
    ##   236          0      0        5
    ##   237          5      0        0
    ##   242          0      1        1
    ##   246          6      0        0
    ##   248          0      4        1
    ##   273          0      1        4
    ##   349          0      1        2
    ##   352          0      1        1
    ##   353          0      0        0
    ##   368          0      0        0
    ##   489          0      0        0
    ##   502          0      0        0
    ##   543          0      0        0
    ##   704          0      1        0
    ##   708          0      0        0

``` r
findAssocs(x = tdm, terms = 'government', corlimit = 0.8)
```

    ## $government
    ##    early     pct. positive     been      say    since 
    ##     1.00     1.00     1.00     0.94     0.91     0.82

Simple analysis on the matrix.

``` r
head(sort(rowSums(my_matrix), decreasing = TRUE))
```

    ##  the  oil  and said  for  its 
    ##  229   80   77   52   50   40

``` r
cor(my_matrix[,1], my_matrix[,2])
```

    ## [1] 0.4910128

``` r
set.seed(31)
my_cluster <- kmeans(x = t(my_matrix), centers = 3)
my_cluster$cluster
```

    ## 127 144 191 194 211 236 237 242 246 248 273 349 352 353 368 489 502 543 
    ##   3   1   3   3   3   1   2   3   2   1   1   3   3   3   3   3   3   3 
    ## 704 708 
    ##   2   3

``` r
meta(crude, 'heading')[my_cluster$cluster == 1]
```

    ## $`144`
    ## [1] "OPEC MAY HAVE TO MEET TO FIRM PRICES - ANALYSTS"
    ## 
    ## $`236`
    ## [1] "KUWAIT SAYS NO PLANS FOR EMERGENCY OPEC TALKS"
    ## 
    ## $`248`
    ## [1] "SAUDI ARABIA REITERATES COMMITMENT TO OPEC PACT"
    ## 
    ## $`273`
    ## [1] "SAUDI FEBRUARY CRUDE OUTPUT PUT AT 3.5 MLN BPD"

``` r
meta(crude, 'heading')[my_cluster$cluster == 2]
```

    ## $`237`
    ## [1] "INDONESIA SEEN AT CROSSROADS OVER ECONOMIC CHANGE"
    ## 
    ## $`246`
    ## [1] "QATAR UNVEILS BUDGET FOR FISCAL 1987/88"
    ## 
    ## $`704`
    ## [1] "NYMEX WILL EXPAND OFF-HOUR TRADING APRIL ONE"
