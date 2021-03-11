---
title: "Latent Association Mining in Binary Data"
subtitle: ""
excerpt: "When information is continuous, but you only observe binary data, it can be difficult to detect association between variables.  This method proposes a new model based on thresholding, and implements a search procedure that identifies groups of latently associated variables."
date: 2021-01-25
author: "Kelly Bodwin"
draft: false
featured: true
show_post_thumbnail: true
categories:
  - association mining
  - R
  - methodology
images:
 - /projects/lamb/lamb-thumbnail.png
layout: single
links:
- icon: book-open
  icon_pack: fas
  name: arxiv paper
  url: https://arxiv.org/pdf/1711.10427.pdf
- icon: github
  icon_pack: fab
  name: code
  url: https://github.com/kbodwin/LAMB
---

### Co-Authors: [Carson Mosso](https://www.med.unc.edu/microimm/dittmerlab/directory/carson-nicholas-mosso/), [Suman Chakraborty](https://sites.google.com/view/sumanswebpage/), [Andrew Nobel](https://nobel.web.unc.edu/) and [Kai Zhang](https://zhangk.web.unc.edu/)

## Paper Abstract:

We consider the problem of identifying stable sets of mutually associated features in moderate or high-dimensional binary data. In this context we develop and investigate a method
called Latent Association Mining for Binary Data (LAMB). The LAMB method is based on
a simple threshold model in which the observed binary values represent a random thresholding of a latent continuous vector that may have a complex association structure. We
consider a measure of latent association that quantifies association in the latent continuous
vector without bias due to the random thresholding. The LAMB method uses an iterative
testing based search procedure to identify stable sets of mutually associated features. We
compare the LAMB method with several competing methods on artificial binary-valued
datasets and two real count-valued datasets. The LAMB method detects meaningful associations in these datasets. In the case of the count-valued datasets, associations detected
by the LAMB method are based only on information about whether the counts are zero or
non-zero, and is competitive with methods that have access to the full count data.

