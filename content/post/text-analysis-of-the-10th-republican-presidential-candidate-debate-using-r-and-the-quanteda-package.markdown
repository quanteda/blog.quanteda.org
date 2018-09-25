---
title: "Text analysis of the 10th Republican Presidential candidate debate using R and the quanteda package"
author: "Kenneth Benoit"
date: "2016-02-26T02:00:00+02:00"
tags: ["blog"]
output: html_document
---



On 25 February 2016, the tenth debate among the Republican candidates for the 2016 Presidential election took place in Houston, Texas, moderated by CNN. In this demonstration of the [quanteda](https://github.com/kbenoit/quanteda) package, I will show how to download, import, clean, parse by speaker, and analyze the debate by speaker.

The first step involves loading the debate into R. I got the debate from the [New York Times publication of the transcript](http://www.nytimes.com/2016/02/26/us/politics/transcript-of-the-republican-presidential-debate-in-houston.html?action=click&contentCollection=Politics&module=RelatedCoverage&region=Marginalia&pgtype=article). It’s possible to copy and paste the text into a text editor, but because of headers, sidebar ads, and image captions, this necessitates a lot of cleaning afteward. To get around this, I printed the article to a pdf file, saved as `files/republican_debate_2016-02-25.pdf`.

To read this text, we can use the **xml2** package, which will automatically extract text from the [US Presidency Project website](http://www.presidency.ucsb.edu/ws/index.php?pid=111634).


```r
scraping <- xml2::read_html("http://www.presidency.ucsb.edu/ws/index.php?pid=111634")
transcriptText <- scraping %>% html_nodes("p") %>% html_text()
```

```
## Error in scraping %>% html_nodes("p") %>% html_text(): could not find function "%>%"
```

To get only the text spoken by each candidate, we still need to remove the non-text markers for events such as applause. We can do this using a substitution of the text we wish to remove for the null string "", using `gsub()`.


```r
# removes interjections
transcriptText <- gsub("\\s*\\[(APPLAUSE|BELL RINGS|BELL RINGING|THE STAR-SPANGLED BANNER|COMMERCIAL BREAK|CROSSTALK|inaudible|LAUGHTER|CHEERING)\\]\\s*", 
    "", ignore.case = TRUE, transcriptText)
```

```
## Error in gsub("\\s*\\[(APPLAUSE|BELL RINGS|BELL RINGING|THE STAR-SPANGLED BANNER|COMMERCIAL BREAK|CROSSTALK|inaudible|LAUGHTER|CHEERING)\\]\\s*", : object 'transcriptText' not found
```


```r
cat(paste(substring(transcriptText, 1, 1000), substring(transcriptText, 5001, 
    5655), collapse = "\n"))
```

```
## Error in substring(transcriptText, 1, 1000): object 'transcriptText' not found
```

Now that we have this as one "document", we need to load it into the **quanteda** package for processing and analysis.


```r
require(quanteda, warn.conflicts = FALSE, quietly = TRUE)
```

```
## Warning: package 'quanteda' was built under R version 3.4.4
```

```
## Package version: 1.3.4
```

```
## Parallel computing: 2 of 8 threads used.
```

```
## See https://quanteda.io for tutorials and examples.
```


```r
transcriptCorpus <- corpus(transcriptText, metacorpus = list(source = "http://nyti.ms/1QJs7R9", 
    notes = "10th Republican candidate debate, Houston TX 2016-02-25"))
```

```
## Error in corpus(transcriptText, metacorpus = list(source = "http://nyti.ms/1QJs7R9", : object 'transcriptText' not found
```


```r
summary(transcriptCorpus)
```

```
## Error in summary(transcriptCorpus): object 'transcriptCorpus' not found
```

Our goal in order to analyze this by speaker, is to redefine the corpus as a set of documents defined as a single speech acts, with a document variable identifying the speaker. We accomplish this through the `corpus_segment()` method:


```r
transcriptCorpus <- corpus_segment(transcriptCorpus, pattern = "\\s*[[:upper:]]+:\\s+", 
    valuetype = "regex")
```

```
## Error in corpus_segment(transcriptCorpus, pattern = "\\s*[[:upper:]]+:\\s+", : object 'transcriptCorpus' not found
```


```r
summary(transcriptCorpus, 10)
```

```
## Error in summary(transcriptCorpus, 10): object 'transcriptCorpus' not found
```

We can clean up the patterns:


```r
docvars(transcriptCorpus, "pattern") <- stri_trim_both(docvars(transcriptCorpus, 
    "pattern"))
```

```
## Error in stri_trim_both(docvars(transcriptCorpus, "pattern")): could not find function "stri_trim_both"
```

```r
docvars(transcriptCorpus, "pattern") <- gsub(":", "", docvars(transcriptCorpus, 
    "pattern"))
```

```
## Error in docvars(transcriptCorpus, "pattern"): object 'transcriptCorpus' not found
```

```r
docvars(transcriptCorpus, "pattern") <- gsub("ARRARAS", "ARRASAS", docvars(transcriptCorpus, 
    "pattern"))
```

```
## Error in docvars(transcriptCorpus, "pattern"): object 'transcriptCorpus' not found
```


```r
transcriptCorpus <- corpus_subset(transcriptCorpus, !(pattern %in% c("MALE", 
    "COOPER")))
```

```
## Error in corpus_subset(transcriptCorpus, !(pattern %in% c("MALE", "COOPER"))): object 'transcriptCorpus' not found
```


```r
summary(transcriptCorpus, 10)
```

```
## Error in summary(transcriptCorpus, 10): object 'transcriptCorpus' not found
```


```r
table(docvars(transcriptCorpus, "pattern"))
```

```
## Error in docvars(transcriptCorpus, "pattern"): object 'transcriptCorpus' not found
```

Now we can start to perfom some analysis on the text. Who spoke the most in the debate, in words?


```r
par(mar = c(5, 6, 0.5, 1))
barplot(sort(ntoken(texts(transcriptCorpus, groups = "pattern"), removePunct = TRUE)), 
    horiz = TRUE, las = 1, xlab = "Total Words Spoken")
```

```
## Error in texts(transcriptCorpus, groups = "pattern"): object 'transcriptCorpus' not found
```

The `ntoken()` function does the work here of counting the tokens in the vector of texts returned by the call to `transcriptCorpus, groups = "pattern"`, which extracts the texts from our segmented corpus and concatenates all texts by speaker. This results in a vector of the same 10 speakers as in our tabulation above. Passing through the `removePunct = TRUE` option in the `ntoken()` call sends this argument through to `tokenize()`, meaning we will not count punctutation characters as tokens. (See `?quanteda::tokenize` for details.)

If we wanted to go further, we convert the segmented corpus into a _document-feature matrix_ and apply one of many available psychological dictionaries to analyze the tone of each candidate’s remarks. Here I will demonstrate using the Regressive Imagery Dictionary, from Martindale, C. (1975) _Romantic progression: The psychology of literary history._ Washington, D.C.: Hemisphere. The code below automatically downloads a version of this dictionary in a format prepared for the WordStat software by Provalis, available from http://www.provalisresearch.com/Download/RID.ZIP. **quanteda** can import dictionaries formatted for WordStat, using the `dictionary()` function.

Here, we will apply the RID dictionary to find out who used what degree of “glory”-oriented language. (You might be able to guess the results already.)


```r
# add back code to download the file
data_dictionary_RID <- dictionary(file = "RID.CAT", format = "wordstat")
```

```
## Error in dictionary.default(file = "RID.CAT", format = "wordstat"): File does not exist: RID.CAT
```


```r
file.remove("RID.zip", "RID.CAT", "RID.exc")
```

```
## Warning in file.remove("RID.zip", "RID.CAT", "RID.exc"): cannot remove file
## 'RID.zip', reason 'No such file or directory'
```

```
## Warning in file.remove("RID.zip", "RID.CAT", "RID.exc"): cannot remove file
## 'RID.CAT', reason 'No such file or directory'
```

```
## Warning in file.remove("RID.zip", "RID.CAT", "RID.exc"): cannot remove file
## 'RID.exc', reason 'No such file or directory'
```

```
## [1] FALSE FALSE FALSE
```


```r
tail(data_dictionary_RID, 1)
```

```
## Error in tail(data_dictionary_RID, 1): object 'data_dictionary_RID' not found
```

Now we will extract just the candidates, using the `subset()` method for a corpus class object, and then create a document-feature matrix from this corpus, grouping the documents by speaker as we did before.


```r
transcriptCorpusCands <- corpus_subset(transcriptCorpus, pattern %in% c("TRUMP", 
    "CRUZ", "RUBIO", "KASICH", "CARSON"))
```

```
## Error in corpus_subset(transcriptCorpus, pattern %in% c("TRUMP", "CRUZ", : object 'transcriptCorpus' not found
```

```r
canddfm <- dfm(transcriptCorpusCands, groups = "pattern")
```

```
## Error in is(x, "dfm"): object 'transcriptCorpusCands' not found
```

Because the texts are of different lengths, we want to normalize them (by converting the feature counts into vectors of relative frequencies within document):


```r
canddfmRel <- dfm_weight(canddfm, "prop")
```

```
## Error in dfm_weight(canddfm, "prop"): object 'canddfm' not found
```

Now we are in a position to apply the RID to the dfm, which matches on the “glob” formatted wildcard expressions that form the values of the RID in our `data_dictionary_RID` object.


```r
canddfmRelRID <- dfm_lookup(canddfmRel, data_dictionary_RID)
```

```
## Error in dfm_lookup(canddfmRel, data_dictionary_RID): object 'canddfmRel' not found
```

```r
head(canddfmRelRID)
```

```
## Error in head(canddfmRelRID): object 'canddfmRelRID' not found
```


```r
topfeatures(canddfmRelRID, n = 20)
```

```
## Error in topfeatures(canddfmRelRID, n = 20): object 'canddfmRelRID' not found
```

We could probably spend a whole day analyzing this information, but here, let’s simply compare candidates on their relative use of language in the “Emotions: Glory” category of the RID. We do this by slicing out the feature with this label, converting this to a vector (as there is no ```drop = TRUE``` option for dfm indexing), and then reattaching the document labels to this vector so that it will be named vector. We then send it to the ```dotchart()``` for a simple plot, showing that Trump was by far the highest user of this type of language.


```r
canddfmRelRID[, "EMOTIONS.GLORY"]
```

```
## Error in eval(expr, envir, enclos): object 'canddfmRelRID' not found
```


```r
glory <- as.vector(canddfmRelRID[, "EMOTIONS.GLORY"])
```

```
## Error in as.vector(canddfmRelRID[, "EMOTIONS.GLORY"]): object 'canddfmRelRID' not found
```

```r
names(glory) <- docnames(canddfmRelRID)
```

```
## Error in docnames(canddfmRelRID): object 'canddfmRelRID' not found
```

```r
dotchart(sort(glory), xlab = "RID \"Glory\" terms used as a proportion of all terms", 
    pch = 19, xlim = c(0, 0.005))
```

```
## Error in sort(glory): object 'glory' not found
```

