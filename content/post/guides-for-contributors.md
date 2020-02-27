+++
title = "Guide for contributors"
date = "2018-06-01T02:00:00+02:00"
tags = ["blog"]
author = "Ken Benoit, Akitaka Matsuo"
+++


## What to contribute

As we stated in the announcement, we would like to make this blog a forum for people who are interested in natural language processing (NLP) from any level of contributor from newbies to guru, and to disseminate the information about the latest updates in NLP world and also how-to or how-not-to analyze text as data.

To serve this goal, we would like to ask those who have a say on NLP to get involved in our blog as authors of blog posts. In particular, we are particularly interested in the following types of contributions:

1. Tutorials for softwares and packages.
1. Use cases for NLP software.
1. Benchmarking of different methods or software implementations of NLP functions.

## How to contribute

This blog is served by [blogdown](https://bookdown.org/yihui/blogdown/), and all contents are generated from [markdown](#) files. For our contributors, we would like to ask you to prepare an `.md` or `.html` file. Due to the current setup of the blog, an `.Rmd` has to be pre-compiled to an html file using **blogdown** package (regarding the difference between `.md` and `.Rmd`, please refer [here](https://bookdown.org/yihui/blogdown/output-format.html)).

### Markdown syntax

If you are not familiar with the markdown syntax, please refer to, one of the many freely available online resources describing this format, such as:

- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
- https://help.github.com/articles/basic-writing-and-formatting-syntax/

### Sending us your blog post

At the moment, our blog offers two methods to submit a contribution:

1. Through a pull request (PR) to this repository (recommended).
2. Sending us a markdown file as an email attachment.

#### Method 1: PR method (with markdown)

The recommended way to contribute to our blog is to send your blog post as a pull request for our blog repository ([link](https://github.com/quanteda/blog.quanteda.org)). If you are not familiar with the git/GitHub workflow, please read [this article](https://guides.github.com/introduction/flow/).

The followings are the steps:

1. Create a local copy of the repository 
    - Fork the repository
    - Clone the forked repository in your local environment
1. Create your own branch
1. Write your own post as a markdown file using the template (found in `templates` folder.)
1. Save the file under `contents/posts`.
1. Make a commit to your branch with your markdown file
1. Push your commit
1. Create a pull request [here](https://github.com/quanteda/blog.quanteda.org/pulls)

Once you create a pull request, the editorial process will be openly conducted within the pull request. Our editors will check the contents and may make some edits.  If we think necessary, we may ask you to edit the post. In that case, you pull the update to your branch, edit the file, commit the changes, and then push the update to the repository. When you and our editors approve the final content, the pull request is merged and your article is published.

#### Method 1-b: PR method (with Rmarkdown)

The steps are similar but there are a few minor differences:

1. Create a local copy of the repository 
    - Fork the repository
    - Clone the forked repository in your local environment
1. Create your own branch
1. Write your own post as an rmarkdown file using the rmarkdown template (found in `templates` folder.)
1. Save the file under `contents/posts`.
1. Run `blogdown::build_sites()` 
    - This will generate an html file from an Rmarkdown file
1. Make a commit to your branch with both your Rmarkdown file and html file rendered from it
1. Push your commit 
1. Create a pull request [here](https://github.com/quanteda/blog.quanteda.org/pulls)

#### Method 2: Sending us a markdown file

You can contribute to our blog with this way.

1. Download the template file [here](https://github.com/quanteda/blog.quanteda.org/tree/master/templates/blogpost_template.md)
1. Write your own post using the template
1. Send the file to [us](mailto:amatsuo@quanteda.org)

When we receive the file, we will create a pull request and notify you. From there, the editorial process will be the same as the pull request method.  (For both methods, you will need to have a GitHub account.)

#### Inclusion of codes in your post

You, of course, include the code in your post. The syntax is something like:

````
    ```r
      x <- rnorm(100)
      y <- rnorm(100)
      plot(x, y)
    ```
````

We accept the post in the normal markdown format (not rmarkdown), which means that if you want to include the **output** from your code, you need to put them in the markdown or hmtl file. The easiest way to do that is to create an html file using **blogdown** (see above). 

#### Inclusion of images

You can show images saved in the `static/img` folder. The syntax to include image is found in the blog-post template.

#### Additional tips

1. Make your post accessible to NLP beginners (esp. if your post is a tutorial).
1. Be nice to other software developers or users of other software, even software that you are not particularly fond of.
