---
title: "What's new in quanteda version 3.0"
author: "Kenneth Benoit and Kohei Watanabe"
date: '2021-04-07'
tags: ["blog", "quanteda-latest", "r-bloggers"]
---



We are proud to announce the version 3.0 release of the **quanteda** package, just over a year following our last major release of v2.0. Version 3.0 is a significant update that makes **quanteda** and its growing family of modules more solid, more consistent, and more extensible.

## Main changes

### Modularisation

We have now separated the `textplot_*()` functions from the main package into a separate package [**quanteda.textplots**](https://github.com/quanteda/quanteda.textplots), and the `textstat_*()` functions from the main package into a separate package[**quanteda.textstats**](https://github.com/quanteda/quanteda.textstats).  This completes the modularisation begun in v2 with the move of the `textmodel_*()` functions to the separate package **quanteda.textmodels**.  **quanteda** now consists of core functions for textual data processing and management.

### Dependencies

v3 has a much lighter dependency footprint.  The package dependency structure is now greatly reduced, by eliminating some unnecessary package dependencies, through modularisation, and by addressing complex downstream dependencies in packages such as **stopwords**.  v3 should serve as a more lightweight and more consistent platform for other text analysis package developers to build on.

### Non-standard evaluation

v3 brings a new, consistent implementation of direct evaluation within docvars for `by` and `groups` arguments:  
* The `*_sample()` functions' argument `by`, and `groups` in the `*_group()` functions, now take unquoted document variable (docvar) names directly, similar to the way the `subset` argument works in the `*_subset()` functions.  
* Quoted docvar names no longer work, as these will be evaluated literally.  
* The `by = "document"` formerly sampled from `docid(x)`, but this functionality is now removed.  Instead, use `by = docid(x)` to replicate this functionality.  
* For `groups`, the default is now `docid(x)`, which is now documented more completely.  See `?groups` and `?docid`.

### Deprecation of previous "shortcut" functions

To enforce a more consistent workflow and one that users have to control more explicitly, we now require objects that operate on tokens to tokenize the inputs.  This mainly affects `dfm()` and `kwic()` -- see details below.

The main potentially breaking changes in version 3 relate to the deprecation or
elimination of shortcut steps that allowed functions that required tokens inputs
to skip the tokens creation step.  We did this to require users to take more
direct control of tokenization options, or to substitute the alternative
tokeniser of their choice (and then coercing it to tokens via [as.tokens()]).
This also allows our function behaviour to be more consistent, with each
function performing a single task, rather than combining functions (such as
tokenisation _and_ constructing a matrix).

The most common example involves constructing a dfm directly from a character
or corpus object.  Formerly, this would construct a tokens object internally
before creating the dfm, and allowed passing arguments to `tokens()` via `...`.
This is now deprecated, although still functional with a warning.

Why? Some processing steps are sequence-dependent, for instance when a user removes stopwords and also applies a stemmer.  




```r
txt <- "Because during the concert it's very loud."
tokens(txt, remove_punct = TRUE) %>%
    tokens_remove(stopwords("en")) %>%
    tokens_wordstem()
## Tokens consisting of 1 document.
## text1 :
## [1] "concert" "loud"
```
is different from:

```r
txt <- "Because during the concert it's very loud."
tokens(txt, remove_punct = TRUE) %>%
    tokens_wordstem() %>%
    tokens_remove(stopwords("en"))
## Tokens consisting of 1 document.
## text1 :
## [1] "Becaus"  "dure"    "concert" "veri"    "loud"
```
While the first is probably what nearly all users want, by removing these from options previously hard-wired via options to `dfm()` now requires the user to choose the sequence.  Now, users must either creating a tokens object first, or pipe the
tokens return to `dfm()` using `%>%`.

We have also deprecated direct character or corpus inputs to `kwic()`, since this also requires a tokenised input.

These are deprecations rather than removals, since in v3.x the deprecated arguments and methods still work, but with deprecation warnings.  We strongly encourage users to switch to the new workflow, as the deprecated arguments will be removed in the next major release.

## Other new features

The full list of new features is the following:

* `dfm()` has a new argument, `remove_padding`, for removing the "pads" left behind after removing tokens with `padding = TRUE`.  (For other extensive changes to `dfm()`, see "Deprecated" below.)

* `tokens_group()`, formerly internal-only, is now exported.

* `corpus_sample()`, `dfm_sample()`, and `tokens_sample()` now work consistently.

* The `kwic()` return object structure has been redefined, and built with an option to use a new function `index()` that returns token spans following a pattern search.  

* The punctuation regular expression and that for matching social media usernames has now been redefined so that the valid Twitter username `@_` is now counted as a "tag" rather than as "punctuation". 

* The data object `data_corpus_inaugural` has been updated to include the Biden 2021 inaugural address.

* A new system of validators for input types now provides better argument type and value checking, with more consistent error messages for invalid types or values.

* Upon startup, we now message the console with the Unicode and ICU version information.  Because we removed our redefinition of `View()` (see below), the former conflict warning is now gone.

* `as.character.corpus()` now has a `use.names = TRUE` argument, similar to `as.character.tokens()` (but with a different default value).


## Deprecations

In addition to the deprecation of convenience shortcuts noted above, the following usages are also deprecated in v3.

* `dfm.character()` and `dfm.corpus()` are deprecated.  Users should create a tokens object first, and input that to `dfm()`.

* `dfm()`: As of version 3, only tokens objects are supported as inputs to `dfm()`.  Calling `dfm()` for character or corpus objects is still functional, but issues a warning.  Convenience passing of arguments to `tokens()` via `...` for `dfm()` is also deprecated, but undocumented, and functions only with a warning.  Users should now create a tokens object (using `tokens()` from character or corpus inputs before calling `dfm()`.

* `kwic()`: As of version 3, only tokens objects are supported as inputs to `kwic()`.  Calling `kwic()` for character or corpus objects is still functional, but issues a warning.  Passing arguments to `tokens()` via `...` in `kwic()` is now disabled.  Users should now create a tokens object (using `tokens()` from character or corpus inputs before calling `kwic()`.

* (as noted above) Shortcut arguments to `dfm()` are now deprecated.  These are still active, with a warning, although they are no longer documented.  These are:
    - `stem` -- use `tokens_wordstem()` or `dfm_wordstem()` instead.
    - `select`, `remove` -- use `tokens_select()` / `dfm_select()` or `tokens_remove()` / `dfm_remove()` instead.
    - `dictionary`, `thesaurus` -- use `tokens_lookup()` or `dfm_lookup()` instead.
    - `valuetype`, `case_insensitive` -- these are disabled; for the deprecated arguments that take these qualifiers, they are fixed to the defaults `"glob"` and `TRUE`.
    - `groups` -- use `tokens_group()` or `dfm_group()` instead.
    
* `texts()` and `texts<-` are deprecated.
    - Use `as.character.corpus()` to turn a corpus into a simple named character vector.
    - Use `corpus_group()` instead of `texts(x, groups = ...)` to aggregate texts by a grouping variable.
    - Use `[<-` instead of `texts()<-` for replacing texts in a corpus object.  To replace all of the texts in a corpus, while keeping it a corpus, use `[]`, e.g., to replace all texts in a five-document corpus:
    
    ```r
    corp <- data_corpus_inaugural[1:5]
    # equivalent to old usage of texts(corp) <- LETTERS[1:5]
    corp[] <- LETTERS[1:5]
    ```
    

## Removals

Finally, we have removed some previously deprecated arguments and functions, and moved others to different packages.

* The `textplot_*()` and `textstat_*()` functions are now moved to **quanteda.textplots** and **quanteda.textstats**, respectively.

* The following functions have been removed:
    - all methods for defunct `corpuszip` objects.
    - `View()` functions -- so no more namespace conflict warnings on startup!
    - `as.wfm()` and `as.DocumentTermMatrix()` (the same functionality is available via `convert()`)
    - `metadoc()` and `metacorpus()`
    - `corpus_trimsentences()` (replaced by `corpus_trim()`)
    - all of the `tortl` functions.

* `dfm` objects can no longer be used as a `pattern` in `dfm_select()` (formerly deprecated).

* `dfm_sample()`:
    - no longer has a `margin` argument.  Instead, `dfm_sample()` now samples only on documents, the same as `corpus_sample()` and `tokens_sample()`; and
    - no longer works with `by = "document"` -- use `by = docid(x)` instead.

* `dictionary_edit()`, `char_edit()`, and `list_edit()` are removed.

* `dfm_weight()`: the formerly deprecated `"scheme"` options are now removed. 

* `tokens()`:  The formerly deprecated options `remove_hyphens` and `remove_twitter` are now removed.  (Use `split_hyphens` instead, and the default tokenizer always now preserves Twitter and other social media tags.)

* Special versions of `head()` and `tail()` for corpus, dfm, and fcm objects are now removed, since the base methods work fine for these objects. The main consequence was the removal of the `nf` option from the methods for dfm and fcm objects, which limited the number of features.  This can be accomplished using the index operator `[` instead, or for printing, by specifying `print(x, max_nfeat = 6L)` (for instance).
