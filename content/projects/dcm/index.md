---
title: "Differential Correlation Mining"
subtitle: ""
excerpt: "This method was developed to identify sets of genes that are more inter-correlated under one condition than under another; genes that are correlated among patients with a certain disease, but uncorrelated among those without the disease.  The DCM method relies on statistical testing principles to iteratively search until a significantly differential set is discovered."
date: 2021-01-25
author: "Kelly Bodwin"
draft: false
featured: true
categories:
  - association mining
  - methodology
  - R
images:
  - /projects/dcm/genes.jpeg
layout: single
links:
- icon: book-open
  icon_pack: fas
  name: Annals of Applied Statistics paper
  url: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6927674/
- icon: github
  icon_pack: fab
  name: code
  url: https://github.com/kbodwin/differential-correlation-mining
---

## Paper Abstract

Given data obtained under two sampling conditions, it is often of interest to identify variables that behave differently in one condition than in the other. We introduce a method for differential analysis of second-order behavior called Differential Correlation Mining (DCM). The DCM method identifies differentially correlated sets of variables, with the property that the average pairwise correlation between variables in a set is higher under one sample condition than the other. DCM is based on an iterative search procedure that adaptively updates the size and elements of a candidate variable set. Updates are performed via hypothesis testing of individual variables, based on the asymptotic distribution of their average differential correlation. We investigate the performance of DCM by applying it to simulated data as well as to recent experimental datasets in genomics and brain imaging.