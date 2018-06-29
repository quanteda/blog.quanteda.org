+++
title = "Guide for contributors"
date = "2018-06-01T02:00:00+02:00"
tags = ["blog"]
author = "Ken Benoit, Akitaka Matsuo"
+++


## What to contribute

As we stated in the announcement, we would like to make this blog a forum for the people who are interested in natural language processing from the newbies to the guru, and to disseminate the information about what you can do about the se

For that purpose, we are particularly interested in the following types of contributions:

1. Tutorials for softwares and packages
1. Use case of NLP softwares
1. Benchmarking of different methods/softwares

## How to contribute

This blog is served by [blogdown](https://bookdown.org/yihui/blogdown/), and all contents are generated from [markdown](#) files. For our contributors, we would like to ask to prepare an `md` file. Due to the current setup of the blog, an `rmd` has to be pre-compiled to an `md` file before sending to us (regarding the difference between `md` and `rmd`, please refer [here](https://bookdown.org/yihui/blogdown/output-format.html)).

### Markdown syntax

If you are not familiar with the markdown syntax, please refer to online resources on it.

**examples**

- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
- https://help.github.com/articles/basic-writing-and-formatting-syntax/

### Sending us your blog post

At the moment, we provide two methods to sending us your contributions

1. Through a pull request (PR) to this repository (Recommended)
1. Sending us a markdown file as an email attachment

#### Method 1: PR method

The recommended way to contribute to our blog is to send your blog post as a pull request for our blog repository ([link](https://github.com/quanteda/blog.quanteda.org)). If you are not familiar with the git/github workflow, please read [this article](https://guides.github.com/introduction/flow/).

The followings are the steps:

1. Create a local copy of the repository
1. Create your own branch
1. Write your own post as a markdown file and save it under `contents/posts`. The template file is found [here](#)
1. Commit with your markdown file
1. Push your commit
1. Create a pull request [here](https://github.com/quanteda/blog.quanteda.org/pulls)

Once you create a pull request, our editors will check the contents and make some edits. This editorial process will be openly conducted within the pull request. If we think necessary, we may ask you to edit the post. In that case you pull the update to your branch, edit the file, commit the changes, and then push the update to the repository. When you and our editors agree the contents, the pull request is merged and your article is published.

#### Method 2: Sending us a markdown file

You can contribute to our blog with this way.

1. Download the template file [here](#)
1. Write your own post
1. Send the file to [us](mailto:***@quanteda.org)

When we receive the file, we create a pull request and notify you. From there, the editorial process will be the same as the pull request method

#### Inclusion of codes in your post

You, of course, include the code in your post. The syntax is something like:

````
    ```r
      x <- rnorm(100)
      y <- rnorm(100)
      plot(x, y)
    ```
````

We accept the post in the normal markdown format (not r-markdown), which means that if you want to include the output from your code, you need to put them in the markdown file. If the code is written in R, probably the easiest way is to `knit` the file (c.f. https://stackoverflow.com/questions/31914161/how-to-convert-rmd-into-md-in-r-studio).



#### Additional tips

1. Make your post accessible to NLP beginners (esp. if your post is a tutorial)
1. 
