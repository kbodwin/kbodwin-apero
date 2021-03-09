---
title: "flair"
subtitle: "Give your code some pizzaz"
excerpt: "Tools to highlight your source code in R Markdown."
date: 2021-01-25
author: "Kelly Bodwin"
draft: false
featured: true
tags:
  - r4edu
categories:
  - R
  - package
  - r4edu
images:
  - /software/flair/flair_hex_thumbnail.png
layout: single
links:
- icon: door-open
  icon_pack: fas
  name: website
  url: https://r-for-educators.github.io/flair/index.html
- icon: github
  icon_pack: fab
  name: code
  url: https://github.com/r-for-educators/flair
---

![flair hex](flair_hex.png)

## [flair](https://github.com/r-for-educators/flair) is a package for adding highlighting and annotation to the source code chunks produced by R Markdown.

---

### About

The goal of `flair` is to give users an easy and reproducible way to change
the colors and highlighting in source code chunks.  For example,

<pre><code class="language-r"><code>iris <span style="background-color:#ffff7f">%&gt;%</span><br>  group_by(Species) <span style="background-color:#ffff7f">%&gt;%</span><br>  summarize(mean(Sepal.Length))</code></code></pre>
<pre><code>
#&gt; # A tibble: 3 x 2
#&gt;   Species    `mean(Sepal.Length)`
#&gt;   &lt;fct&gt;                     &lt;dbl&gt;
#&gt; 1 setosa                     5.01
#&gt; 2 versicolor                 5.94
#&gt; 3 virginica                  6.59
</code></pre>


### Learn More

* Watch my [UseR 2020 talk](https://www.youtube.com/watch?v=sXFJ_AZ4jeA) or just
[check out the slides](https://kbod.win/talks/user_2020/#1)

* Read my [RStudio Education Blog Post](https://education.rstudio.com/blog/2020/05/flair/)

* [Read the vignette](https://cran.r-project.org/web/packages/flair/vignettes/how_to_flair.html)

* Contribute to [the development of the package](https://github.com/r-for-educators/flair)
