---
title: "What's new in quanteda version 2.0"
author: "Kenneth Benoit and Kohei Watanabe"
date: '2020-02-27'
tags: ["blog", "quanteda-latest", "r-bloggers"]
---



We are proud to announce the version 2.0 release of the **quanteda** package, following over a year of planning, discussion, design, and -- most significantly -- programming and testing. **quanteda** version 2.0 is a major update introducing some major changes and new features detailed below.

## Major changes

### 1. Object structure

All included data objects (corpus, tokens, dfm and dictionary) have the new formats. These are all updated to work with the existing extractor and replacement functions.  Your old objects are automatically upgraded to the version 2.0 structure by **quanteda**'s functions but you can also do it manually using `as.corpus()`, `as.tokens()`, `as.dfm()` or `as.dictionary()`.

One of the biggest headaches we faced in making the structural change was that other package authors -- including the (as of today) 20 packages that have strong reverse dependencies on **quanteda** had written functions that bypassed the accessor and replacement functions, despite our explicit plea not to do this in the documentation.  By working with these package authors, however, we were able to correct almost all of this.

#### New corpus object  

Changes are most dramatic for the corpus object. The corpus object have been redesigned extending the character vector with meta- and system-data in attributes; document variables (docvars) are now handled separately from the texts, in the same way that docvars are handled for tokens objects.


```r
library(quanteda, warn.conflicts = FALSE)
## Package version: 2.0.0
## Parallel computing: 2 of 6 threads used.
## See https://quanteda.io for tutorials and examples.
is.character(data_corpus_inaugural)
## [1] TRUE
```

This means that all functions that operate on character vectors will also operate on a corpus, although it might be necessary to drop the special attributes using `as.character()`.  This is how `spacyr::spacy_parse()` for instance now works with a **quanteda** corpus, by using the fact that it's just a souped-up, specially classed character vector. Corpus objects also store default summary statistics for efficiency.  When these are present, `summary.corpus()` retrieves them rather than computing them on the fly.

#### Document variables

New index operators have been defined for core objects.  The main change here is to redefine the `$` operator for corpus, tokens, and dfm objects (all objects that retain docvars) to allow this operator to access single docvars by name.  Some other index operators have been redefined as well, such as `[.corpus` returning a slice of a corpus, and `[[.corpus` returning the texts from a corpus.


```r
# view a docvar
tail(data_corpus_inaugural$Party)
## [1] Democratic Republican Republican Democratic Democratic Republican
## Levels: Democratic Democratic-Republican Federalist none Republican Whig

# or reassign one
data_corpus_inaugural$Party <- "hidden"
head(docvars(data_corpus_inaugural), 4)
##   Year  President FirstName  Party
## 1 1789 Washington    George hidden
## 2 1793 Washington    George hidden
## 3 1797      Adams      John hidden
## 4 1801  Jefferson    Thomas hidden

# or delete one
data_corpus_inaugural$Party <- NULL
tail(docvars(data_corpus_inaugural), 4)
##    Year President FirstName
## 55 2005      Bush George W.
## 56 2009     Obama    Barack
## 57 2013     Obama    Barack
## 58 2017     Trump Donald J.
```

See the full details at https://github.com/quanteda/quanteda/wiki/indexing_core_objects.

#### Metadata

Corpus-level metadata is now inserted in a user metadata list via `meta()` and `meta<-()`.  `metacorpus()` is kept as a synonym for `meta()`, for backwards compatibility.  Additional system-level corpus information is also recorded, but automatically when an object is created.  

Document-level metadata is deprecated, and now all document-level information is simply a docvar.  For backward compatibility, `metadoc()` is kept and will insert docvars with the name prefixed by an underscore.

The settings functions (and related slots and object attributes) are gone.  These are now replaced by a new `meta(x, type = "object")` that records object-specific meta-data, including settings such as the `n` for tokens (to record the `ngrams`).

Under the hood, this enabled us to design a vessel to contain three types of medata: user metadata, which can be controlled entirely by the user and can contain anything (since it's a list), and is accessed by `meta()` and `meta()<-`; system metadata which is set when an object is created; and object metadata, which is automatically set when an object is modified or created, and includes options that the user controls.

Example:

```r
corp <- corpus("This is my one-document corpus.")
meta(corp) <- list(source = "My blog post.",
   mymatrix = matrix(1:6, nrow = 2))
meta(corp)
## $source
## [1] "My blog post."
## 
## $mymatrix
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6

meta(corp, type = "system")
## $`package-version`
## [1] '2.0.0'
## 
## $`r-version`
## [1] '3.6.2'
## 
## $system
##  sysname  machine     user 
##  "Linux" "x86_64"  "kohei" 
## 
## $directory
## [1] "/home/kohei/repo/blog.quanteda.org/content/post"
## 
## $created
## [1] "2020-02-27"
```


### 2. Tokens constructor

**quanteda** version 2.0 implements major changes to the `tokens()` constructor.  These are designed to simplify the code and its maintenance in **quanteda**, to allow users to work with other (external) tokenizers, and to improve consistency across the tokens processing options.  Changes include:

A new method `tokens.list(x, ...)` constructs a `tokens` object from named list of characters, allowing users to tokenize texts using some other function (or package) such as `tokenize_words()`, `tokenize_sentences()`, or `tokenize_tweets()` from the **tokenizers** package, or the list returned by `spacyr::spacy_tokenize()`.  This allows users to use their choice of tokenizer, as long as it returns a named list of characters.  With `tokens.list()`, all tokens processing (`remove_*`) options can be applied, or the list can be converted directly to a `tokens` object without processing using `as.tokens.list()`.  For instance:


```r
txt <- c(doc1 = "I live in the United Kingdom.", 
 doc2 = "Excellent document two.")
tokenizers::tokenize_words(txt) %>%
tokens()
## Tokens consisting of 2 documents.
## doc1 :
## [1] "i"       "live"    "in"      "the"     "united"  "kingdom"
## 
## doc2 :
## [1] "excellent" "document"  "two"
```

as well as:


```r
spacyr::spacy_parse(txt) %>%
spacyr::entity_consolidate() %>%
as.tokens(include_pos = "pos")
## Found 'spacy_condaenv'. spacyr will use this environment
## successfully initialized (spaCy Version: 2.0.11, language model: en)
## (python options: type = "condaenv", value = "spacy_condaenv")
## Tokens consisting of 2 documents.
## doc1 :
## [1] "I/PRON"                    "live/VERB"                
## [3] "in/ADP"                    "the_United_Kingdom/ENTITY"
## [5] "./PUNCT"                  
## 
## doc2 :
## [1] "Excellent/ADJ" "document/NOUN" "two/ENTITY"    "./PUNCT"
```

All tokens options are now _intervention_ options, to split or remove things that by default are not split or removed.  All `remove_*` options to `tokens()` now remove them from tokens objects by calling `tokens.tokens()`, after constructing the object.  "Pre-processing" is now  actually post-processing using `tokens_*()` methods internally, after a conservative tokenization on token boundaries. This both improves performance and improves consistency in handling special characters (e.g. Twitter characters) across different tokenizer engines.

Note that `tokens.tokens()` will remove what is found, but cannot "undo" a removal -- for instance it cannot replace missing punctuation characters if these have already been removed.

The option `remove_hyphens` is removed and deprecated, but replaced by `split_hyphens`.  This preserves infix (internal) hyphens rather than splitting them.  This behaviour is implemented in both the `what = "word"` and `what = "word2"` tokenizer options.  This option is `FALSE` by default.

The option `remove_twitter` has been removed.  The new `what = "word"` is a smarter tokenizer that preserves social media tags, URLs, and email-addresses.  "Tags" are defined as valid social media hashtags and usernames (using Twitter rules for validity) rather than removing the `#` and `@` punctuation characters, even if `remove_punct = TRUE`.

While we have made it easier than ever to use any tokenizer you wish, the default **quanteda** tokenizer is smarter than ever. By default, it preserves social media usernames and hashtags, preserves URLs, and keeps infix hyphens.  With the `remove_` options however, a user can change this at any time, even after having formed tokens using the (conservative) defaults. 

### 3. Print methods

New print methods for core objects (corpus, tokens, dfm, dictionary) now exist, each with new global options to control the number of documents shown, as well as the length of a text snippet (corpus), the tokens (tokens), dfm cells (dfm), or keys and values (dictionary).  Similar to the extended printing options for dfm objects, printing of corpus objects now allows for brief summaries of the texts to be printed, and for the number of documents and the length of the previews to be controlled by new global options.

The printing of objects is smarter and provides more user control, but looks better by default.

Example:


```r
data_corpus_inaugural
## Corpus consisting of 58 documents and 3 docvars.
## 1789-Washington :
## "Fellow-Citizens of the Senate and of the House of Representa..."
## 
## 1793-Washington :
## "Fellow citizens, I am again called upon by the voice of my c..."
## 
## 1797-Adams :
## "When it was first perceived, in early times, that no middle ..."
## 
## 1801-Jefferson :
## "Friends and Fellow Citizens: Called upon to undertake the du..."
## 
## 1805-Jefferson :
## "Proceeding, fellow citizens, to that qualification which the..."
## 
## 1809-Madison :
## "Unwilling to depart from examples of the most revered author..."
## 
## [ reached max_ndoc ... 52 more documents ]
print(data_corpus_inaugural, max_ndoc = 1, max_nchar = 20)
## Corpus consisting of 58 documents and 3 docvars.
## 1789-Washington :
## "Fellow-Citizens of t..."
## 
## [ reached max_ndoc ... 57 more documents ]

print(tokens(data_corpus_inaugural), max_ndoc = 3, max_ntok = 6)
## Tokens consisting of 58 documents and 3 docvars.
## 1789-Washington :
## [1] "Fellow-Citizens" "of"              "the"             "Senate"         
## [5] "and"             "of"             
## [ ... and 1,531 more ]
## 
## 1793-Washington :
## [1] "Fellow"   "citizens" ","        "I"        "am"       "again"   
## [ ... and 141 more ]
## 
## 1797-Adams :
## [1] "When"      "it"        "was"       "first"     "perceived" ","        
## [ ... and 2,571 more ]
## 
## [ reached max_ndoc ... 55 more documents ]

dfm(data_corpus_inaugural)
## Document-feature matrix of: 58 documents, 9,399 features (91.8% sparse) and 3 docvars.
##                  features
## docs              fellow-citizens  of the senate and house representatives :
##   1789-Washington               1  71 116      1  48     2               2 1
##   1793-Washington               0  11  13      0   2     0               0 1
##   1797-Adams                    3 140 163      1 130     0               2 0
##   1801-Jefferson                2 104 130      0  81     0               0 1
##   1805-Jefferson                0 101 143      0  93     0               0 0
##   1809-Madison                  1  69 104      0  43     0               0 0
##                  features
## docs              among vicissitudes
##   1789-Washington     1            1
##   1793-Washington     0            0
##   1797-Adams          4            0
##   1801-Jefferson      1            0
##   1805-Jefferson      7            0
##   1809-Madison        0            0
## [ reached max_ndoc ... 52 more documents, reached max_nfeat ... 9,389 more features ]
```

The defaults for printing are all user-controllable via `quanteda_options()` as global settings, including whether to print summary information, or whether to print any section of a preview object at all.  For instance:


```r
quanteda_options(print_tokens_max_ndoc = 0)
tokens(data_corpus_inaugural)
## Tokens consisting of 58 documents and 3 docvars.
```

### 4. textstat_simil and textstat_dist

Return format from `textstat_simil()` and `textstat_dist()`, now defaults to a sparse matrix from the **Matrix** package, but coercion methods are provided for `as.data.frame()`, to make these functions return a data.frame just like the other textstat functions.  With the conversion to `data.frame`, all documents (or features) are returned as pairwise sets.

Additional coercion methods are provided for `as.dist()`, `as.simil()`, and `as.matrix()`.

Example:


```r
dfmat <- corpus_subset(data_corpus_inaugural, Year > 1990) %>%
dfm(remove_punct = TRUE)
tstat <- textstat_simil(dfmat, method = "cosine")
tstat
## textstat_simil object; method = "cosine"
##              1993-Clinton 1997-Clinton 2001-Bush 2005-Bush 2009-Obama
## 1993-Clinton        1.000        0.915     0.894     0.888      0.934
## 1997-Clinton        0.915        1.000     0.899     0.920      0.945
## 2001-Bush           0.894        0.899     1.000     0.878      0.920
## 2005-Bush           0.888        0.920     0.878     1.000      0.921
## 2009-Obama          0.934        0.945     0.920     0.921      1.000
## 2013-Obama          0.931        0.928     0.920     0.890      0.960
## 2017-Trump          0.902        0.894     0.899     0.884      0.911
##              2013-Obama 2017-Trump
## 1993-Clinton      0.931      0.902
## 1997-Clinton      0.928      0.894
## 2001-Bush         0.920      0.899
## 2005-Bush         0.890      0.884
## 2009-Obama        0.960      0.911
## 2013-Obama        1.000      0.902
## 2017-Trump        0.902      1.000
```

This now easily becomes a data.frame for "tidy"ing:


```r
# see the top most similar speeches
as.data.frame(tstat) %>% 
dplyr::arrange(-cosine)
##       document1    document2    cosine
## 1    2009-Obama   2013-Obama 0.9604997
## 2  1997-Clinton   2009-Obama 0.9454959
## 3  1993-Clinton   2009-Obama 0.9335386
## 4  1993-Clinton   2013-Obama 0.9310702
## 5  1997-Clinton   2013-Obama 0.9275654
## 6     2005-Bush   2009-Obama 0.9209563
## 7     2001-Bush   2013-Obama 0.9202483
## 8  1997-Clinton    2005-Bush 0.9201660
## 9     2001-Bush   2009-Obama 0.9196979
## 10 1993-Clinton 1997-Clinton 0.9149276
## 11   2009-Obama   2017-Trump 0.9112379
## 12   2013-Obama   2017-Trump 0.9024086
## 13 1993-Clinton   2017-Trump 0.9020775
## 14    2001-Bush   2017-Trump 0.8994070
## 15 1997-Clinton    2001-Bush 0.8987704
## 16 1997-Clinton   2017-Trump 0.8935932
## 17 1993-Clinton    2001-Bush 0.8935107
## 18    2005-Bush   2013-Obama 0.8897259
## 19 1993-Clinton    2005-Bush 0.8884613
## 20    2005-Bush   2017-Trump 0.8836008
## 21    2001-Bush    2005-Bush 0.8781827
```

Or a list, similar to `tm::findAssocs()`:


```r
textstat_simil(dfmat, margin = "features", method = "cosine") %>%
as.list(n = 6) %>%
head()
## $my
##   democracy        ever         see      fellow       world opportunity 
##   0.9739642   0.9547033   0.9515026   0.9500692   0.9354143   0.9321432 
## 
## $fellow
##      give      home        my      ever democracy      eyes 
## 0.9675032 0.9589667 0.9500692 0.9477582 0.9453623 0.9431191 
## 
## $citizens
##  interests       live   deserves        not conscience       life 
##  0.9472096  0.9467715  0.9375093  0.9348714  0.9233518  0.9077395 
## 
## $today
## challenges      young      world        way         so     people 
##  0.9815037  0.9694358  0.9621451  0.9540410  0.9401619  0.9351331 
## 
## $we
##       our       are        no        to       but       for 
## 0.9928250 0.9918359 0.9886128 0.9841328 0.9754735 0.9720326 
## 
## $celebrate
##   ceremony   millions      world   congress themselves       down 
##  0.9428090  0.9365858  0.9365696  0.8944272  0.8728716  0.8728716
```

### 5. quanteda.textmodel package

All textmodels and related functions have been moved to a new package **quanteda.textmodels**.  This makes them easier to maintain and update, and keeps the size of the core package down.

This new package also includes the data objects that are primarily used as examples for scaling (which will be removed from **quanteda** in the next minor update), and also includes some new data objects as well.  See more about this package at https://github.com/quanteda/quanteda.textmodels.


## Other changes

### New features

* We changed the default value of the `size` argument in `dfm_sample()` to the number of features, not the number of documents.
* We fixed a few CRAN-related issues (compiler warnings on Solaris and encoding warnings on r-devel-linux-x86_64-debian-clang.)
* Added `startpos` and `endpos` arguments to `tokens_select()`, for selecting on token positions relative to the start or end of the tokens in each document.
* We added a `convert()` method for corpus objects, to convert them into data.frame or json formats.
* We added a `spacy_tokenize()` method for corpus objects, to provide direct access via the **spacyr** package.

### Behaviour changes

*  The `subset` argument now must be logical, and the `select` argument has been removed.  (This is part of `base::subset()` but has never made sense, either in **quanteda** or **base**.)
* We added a `force = TRUE` option and error checking for the situations of applying `dfm_weight()` or `dfm_group()` to a dfm that has already been weighted.  The function `textstat_frequency()` now allows passing this argument to `dfm_group()` via `...`.
* `textstat_frequency()` now has a new argument for resolving ties when ranking term frequencies, defaulting to the "min" method.
* New docvars accessor and replacement functions are available for corpus, tokens, and dfm objects via `$`.  (See Index Operators for Core Objects above.)
* `textstat_entropy()` now produces a data.frame that is more consistent with other `textstat_*` methods.

### Bug fixes and stability enhancements

*  docnames are now enforced to be character (formerly, could be numeric for some objects).
*  docnames are now enforced to be strictly unique for all object classes.
*  Grouping operations in `tokens_group()` and `dfm_group()` are more robust to using multiple grouping variables, and preserve these correctly as docvars in the new dfm.
*  We made some fixes to documented ... objects in two functions that were previously causing CRAN check failures on the release of version 1.5.2.
