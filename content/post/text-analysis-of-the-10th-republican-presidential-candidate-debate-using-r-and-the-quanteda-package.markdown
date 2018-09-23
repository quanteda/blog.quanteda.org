---
title: "Text analysis of the 10th Republican Presidential candidate debate using R and the quanteda package"
author: "Kenneth Benoit"
date: "2016-02-26T02:00:00+02:00"
tags: ["blog"]
output: html_document
---





On 25 February 2016, the tenth debate among the Republican candidates for the 2016 Presidential election took place in Houston, Texas, moderated by CNN. In this demonstration of the [quanteda](https://github.com/kbenoit/quanteda) package, I will show how to download, import, clean, parse by speaker, and analyze the debate by speaker.

The first step involves loading the debate into R. I got the debate from the [New York Times publication of the transcript](http://www.nytimes.com/2016/02/26/us/politics/transcript-of-the-republican-presidential-debate-in-houston.html?action=click&contentCollection=Politics&module=RelatedCoverage&region=Marginalia&pgtype=article). It’s possible to copy and paste the text into a text editor, but because of headers, sidebar ads, and image captions, this necessitates a lot of cleaning afteward. To get around this, I printed the article to a pdf file, saved as ```republican_debate_2016-02-25.pdf```.

To read this text, I then called the utility ```pdftotext```, described in this [post](http://www.kenbenoit.net/how-to-batch-convert-pdf-files-to-text/).



```r
transcriptText <- system2("pdftotext", args = "-layout ~/Desktop/republican_debate_2016-02-25.pdf -", stdout = TRUE)
```


```r
Encoding(transcriptText) <- "UTF-8"
```

The first command performs the conversion using the ```system2()``` call, sends this to “stdout”, which is assigned in R to ```transcriptText```, a character vector consisting of one element per line of the text. The ```-layout``` argument to ```pdftotext``` preserves the formatting as printed in the original text. If we inspect the first 17 lines, we see the text, which includes tags for each speaker’s name in capitals, and parenthetical remarks such as “(APPLAUSE)” denoting non-text events.


```r
cat(paste(transcriptText[6:22], collapse = "\n"))
```

To make this a single text object, we will paste the lines together, joining them with the \n characters that separated them originally.


```r
transcriptText <- paste(transcriptText, collapse = "\n")
```

To get only the text spoken by each candidate, we still need to remove some of the extra text at the beginning and the end of the article, and we need to remove the non-text markers for events such as applause. We can do this using a substitution of the text we wish to remove for the null string "", using ```gsub()```.


```r
transcriptText <- gsub("\\fTranscript of the Republican(.*?)08\\)18", "", transcriptText)
transcriptText <- gsub("\\s*\\((APPLAUSE|BELL RINGS|BELL RINGING|THE STAR-SPANGLED BANNER|COMMERCIAL BREAK|CROSSTALK|inaudible|LAUGHTER|CHEERING)\\)\\s*", "", transcriptText)
transcriptText <- gsub("https:(.*?)\\d\\sof\\s90", "", transcriptText)
```


```r
cat(paste(substring(transcriptText, 1, 1000), substring(transcriptText, 5000, 5655), collapse = "\n"))
```

Now that we have this as one “document”, we need to load it into the **quanteda** package for processing and analysis.


```r
require(quanteda, warn.conflicts = FALSE, quietly = TRUE)
```


```r
transcriptCorpus <- corpus(transcriptText, metacorpus = list(
  source = "http://nyti.ms/1QJs7R9",
  notes = "10th Republican candidate debate, Houston TX 2016-02-25"))
```


```r
summary(transcriptCorpus)
```

Our goal in order to analyze this by speaker, is to redefine the corpus as a set of documents defined as a single speech acts, with a document variable identifying the speaker. We accomplish this through the ```corpus_segment()``` method:


```r
transcriptCorpus <- corpus_segment(transcriptCorpus, pattern = "\\s*[[:upper:]]+:\\s+", valuetype = "regex")
```


```r
summary(transcriptCorpus, 10)
```

We can clean up the patterns:


```r
docvars(transcriptCorpus, "pattern") <- stri_trim_both(docvars(transcriptCorpus, "pattern"))
docvars(transcriptCorpus, "pattern") <- gsub(":", "", docvars(transcriptCorpus, "pattern"))
docvars(transcriptCorpus, "pattern") <- gsub("ARRARAS", "ARRASAS", docvars(transcriptCorpus, "pattern"))
```


```r
transcriptCorpus <- corpus_subset(transcriptCorpus, !(pattern %in% c("MALE", "COOPER")))
```


```r
summary(transcriptCorpus, 10)
```


```r
table(docvars(transcriptCorpus, "pattern"))
```

Now we can start to perfom some analysis on the text. Who spoke the most in the debate, in words?


```r
par(mar = c(5, 6, .5, 1))
barplot(sort(ntoken(texts(transcriptCorpus, groups = "pattern"), removePunct = TRUE)), horiz = TRUE, las = 1, xlab = "Total Words Spoken")
```

The ```ntoken()``` function does the work here of counting the tokens in the vector of texts returned by the call to ```transcriptCorpus, groups = "pattern"```, which extracts the texts from our segmented corpus and concatenates all texts by speaker. This results in a vector of the same 10 speakers as in our tabulation above. Passing through the ```removePunct = TRUE``` option in the ```ntoken()``` call sends this argument through to ```tokenize()```, meaning we will not count punctutation characters as tokens. (See ```?quanteda::tokenize``` for details.)

If we wanted to go further, we convert the segmented corpus into a _document-feature matrix_ and apply one of many available psychological dictionaries to analyze the tone of each candidate’s remarks. Here I will demonstrate using the Regressive Imagery Dictionary, from Martindale, C. (1975) _Romantic progression: The psychology of literary history._ Washington, D.C.: Hemisphere. The code below automatically downloads a version of this dictionary in a format prepared for the WordStat software by Provalis, available from http://www.provalisresearch.com/Download/RID.ZIP. **quanteda** can import dictionaries formatted for WordStat, using the ```dictionary()``` function.

Here, we will apply the RID dictionary to find out who used what degree of “glory”-oriented language. (You might be able to guess the results already.)


```r
RIDdict <- dictionary(file = "RID.CAT", format = "wordstat")
```


```r
file.remove("RID.zip", "RID.CAT", "RID.exc")
```


```r
tail(RIDdict, 1)
```

Now we will extract just the candidates, using the ```subset()``` method for a corpus class object, and then create a document-feature matrix from this corpus, grouping the documents by speaker as we did before.


```r
transcriptCorpusCands <- corpus_subset(transcriptCorpus, pattern %in% c("TRUMP", "CRUZ", "RUBIO", "KASICH", "CARSON"))
canddfm <- dfm(transcriptCorpusCands, groups = "pattern")
```

Because the texts are of different lengths, we want to normalize them (by converting the feature counts into vectors of relative frequencies within document):


```r
canddfmRel <- dfm_weight(canddfm, "prop")
```

Now we are in a position to apply the RID to the dfm, which matches on the “glob” formatted wildcard expressions that form the values of the RID in our ```RIDdict``` object.


```r
canddfmRelRID <- dfm_lookup(canddfmRel, RIDdict)
head(canddfmRelRID)
```


```r
topfeatures(canddfmRelRID, n = 20)
```

We could probably spend a whole day analyzing this information, but here, let’s simply compare candidates on their relative use of language in the “Emotions: Glory” category of the RID. We do this by slicing out the feature with this label, converting this to a vector (as there is no ```drop = TRUE``` option for dfm indexing), and then reattaching the document labels to this vector so that it will be named vector. We then send it to the ```dotchart()``` for a simple plot, showing that Trump was by far the highest user of this type of language.


```r
canddfmRelRID[, "EMOTIONS.GLORY"]
```


```r
glory <- as.vector(canddfmRelRID[, "EMOTIONS.GLORY"])
names(glory) <- docnames(canddfmRelRID)
dotchart(sort(glory), xlab = "RID \"Glory\" terms used as a proportion of all terms",
         pch = 19, xlim = c(0, .005))
```

