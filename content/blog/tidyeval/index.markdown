---
title: "Tidy Eval is Confusing Sometimes"
subtitle: "An experiment"
excerpt: "The purpose of this document is to once and for all figure out how to use tidy eval in my functions.  I keep thinking I understand it, and then messing up the next time. "
author: "Kelly Bodwin"
date: '2010-07-01'
draft: false
# layout options: single, single-sidebar
layout: single
categories:
- R
- random
---


ggplot2ggplot2::

```r
library(tidyverse)
library(rlang)
```


# Introduction

The purpose of this document is to once and for all figure out how to use tidy eval in my functions.  I keep thinking I understand it, and then messing up the next time.  The last time I wrote decent working complex tidy eval code, I had Joe Motherflipping Cheng coaching me... can't count on that every time.  Let's figure this ish out.

I am hoping `curly-curly` or `doublestache` or whatever we are calling `{{` is going to help it finally click.

# In which I do it wrong

Every time I try to write a function with tidy eval, I do something like this:


```r
mean_by <- function(.data, group_var){
  
  .data %>% 
    group_by(sym(group_var)) %>%
    summarize_all(mean)
  
}
```

The correct code instead should be:


```r
mean_by <- function(.data, group_var){
  
  .data %>% 
    group_by(!!enquo(group_var)) %>%
    summarize_all(mean)
  
}
```

`!!enquo` instead of `sym`.  This was easy to Google and fix.  But why wasn't it my instinct?  More on that in a second.

## Doublestache to the rescue


```r
mean_by <- function(.data, group_var){
  
  .data %>% 
    group_by({{group_var}}) %>%
    summarize_all(mean)
  
}

mean_by(iris, Species)
```

```
## # A tibble: 3 x 5
##   Species    Sepal.Length Sepal.Width Petal.Length Petal.Width
## * <fct>             <dbl>       <dbl>        <dbl>       <dbl>
## 1 setosa             5.01        3.43         1.46       0.246
## 2 versicolor         5.94        2.77         4.26       1.33 
## 3 virginica          6.59        2.97         5.55       2.03
```

Hooray!!!  Okay, this is much easier to remember.  Problem mostly solved.  Also I officially now support the name `doublestache` over `curly-curly` because it makes me laugh.

![](https://external-preview.redd.it/-ZmAri-tn-Vtan3PCx6FzuFFuCJFUWXcbdNimW4e_vc.jpg?width=800&auto=webp&s=60fb6d5b42f43a259044939822402e1228c180e0)

## Tidy eval is still hard.

Alright, so doublestache is gonna do the trick most of the time, but I still want to understand what's going on here and why this feels unnatural.

### Object types

I think my main confusion is to do with what object types exist in NSE and where they appear.  

Here is my naive, non-programmer, non-developer thought process on the types.

#### strings: 

Strings make sense to me.  Storing code as a string makes sense to me.

* Variable names are passable as strings to `[[` and certain functions.  This feels clunky and inelegant to me, because a string is a string, not a name of an object in the environment/dataset.  
    
* Code can be passed as a string and then parsed.  But `eval(parse())` is what sent me down this rabbit hole in the first place.  To be avoided.


```r
thing <- "Sepal.Length"
mean(iris[[thing]])
```

```
## [1] 5.843333
```

```r
thing <- "mean(iris$Sepal.Length)"
eval(parse(text = thing))
```

```
## [1] 5.843333
```

    
### expressions: 

I can also wrap my head around an expression as an object type.  This is just the result of `parse()`-ing a string.  No problemo.  I like that `expr` lets you create these directly.


```r
thing <- expr(mean(iris$Sepal.Length))
eval(thing)
```

```
## [1] 5.843333
```

#### symbols:

To me, it feels like the next logical "meta" object type we might need would be the names of objects in an environment or list, so we can reference them dynamically.

My brain wants this to be what `symbol` means, but that is wrong.


```r
# I really want this to work :(

thing <- sym("Sepal.Length")

mean(iris$thing)
```

```
## Warning in mean.default(iris$thing): argument is not numeric or logical:
## returning NA
```

```
## [1] NA
```

```r
iris %>% summarize(mean(thing))
```

```
## Warning in mean.default(thing): argument is not numeric or logical: returning NA
```

```
##   mean(thing)
## 1          NA
```

Incidentally - and I did not expect this - they are both warnings rather than errors.

##### Code sanitizing.

I do like `sym()` as a way of sanitizing input code.


```r
thing <- "rm(list = ls())"

sym(thing)
```

```
## `rm(list = ls())`
```

```r
# Nice try, hacker!  Taste my backticks!!!
```

But surely this is not it's only purpose?

And also the backticks make me think, again, of variable or object names... which is not what a symbol is, or at least, not directly.

So.... what exactly is the purpose of a symbol?

## Bang-Bang

So I think to myself, okay, this makes sense.  We don't want to `mean` the symbol, we want the thing the symbol refers to.

This seems to be bang-bang's job:


```r
thing <- sym("Sepal.Length")
iris %>% summarize(mean(!!thing))
```

```
##   mean(Sepal.Length)
## 1           5.843333
```

Great!  That worked!  


### Important (maybe):  BangBang stalls evaluation!!!

Instinctively, all we are doing is evaluating an expression/symbol.  But we are saving the moment of evaluation until the parent function needs it.


```r
my_sym <- sym("Sepal.Length")

# It's mad because it can't find Sepal.Length
iris %>% select(eval(my_sym)) %>% head()
```

```
## Error: object 'Sepal.Length' not found
```

```r
# It's happy because it doesn't look for Sepal.Length until select says so
iris %>% select(!!my_sym) %>% head()
```

```
##   Sepal.Length
## 1          5.1
## 2          4.9
## 3          4.7
## 4          4.6
## 5          5.0
## 6          5.4
```

```r
# This is fine for some reason?
iris %>% summarize(mean(eval(my_sym)))
```

```
##   mean(eval(my_sym))
## 1           5.843333
```


## Quosures  


Uhhh but what about quosures???  If I can get quosures I think I can get the whole shebang.


```r
my_str <- "Sepal.Length"
typeof(my_str)
```

```
## [1] "character"
```

```r
my_sym <- sym(my_str)
typeof(my_sym)
```

```
## [1] "symbol"
```

```r
# so far so good...

thing <- quo(my_sym)
typeof(thing)
```

```
## [1] "language"
```

Okay.  The object type is "language".  To me this says "here's some code".  But that was already how I understood expressions.

What's it look like?


```r
thing
```

```
## <quosure>
## expr: ^my_sym
## env:  global
```

It's a `quosure`... but that's not it's object type... and it has an `expr` and a `env`.  These are clearly not variables...


```r
thing$expr  #nope
```

```
## NULL
```

... so what are they?  Attributes?


```r
attributes(thing)
```

```
## $class
## [1] "quosure" "formula"
## 
## $.Environment
## <environment: R_GlobalEnv>
```

Gah, okay.  `thing` is a formula.  I can deal with that.  And it's a special formula that is also a quosure.  Getting warmer.


*[Author's note:  At this point I tried to read the source code for `aov()` and `lm()` to understand how formulas get processed.  Then I got stressed out, ate an entire bag of popcorn, read *Advanced R* for a bit, and decided to work on formulas some other time]*


Alright.  A quosure is technically a formula.  Whatever.  That's fine.

Now, what needs to happen to "activate" a quosure.


```r
!!thing
```

```
## Error: Quosures can only be unquoted within a quasiquotation context.
## 
##   # Bad:
##   list(!!myquosure)
## 
##   # Good:
##   dplyr::mutate(data, !!myquosure)
```

That's annoying.


```r
iris %>% summarize(mean((!!thing)))
```

```
## Warning in mean.default(~my_sym): argument is not numeric or logical: returning
## NA
```

```
##   mean(my_sym)
## 1           NA
```

Whoa okay I expected that one to work and now I am definitely confused.  It wanted a column called `my_sym`. Did I do too many steps in the creation?


```r
# before:
## my_str <- "Sepal.Length"
## my_sym <- sym(my_str)
## thing <- quo(my_sym)

thing2 <- quo("Sepal.Length")

iris %>% summarize(mean(!!thing2))
```

```
## Warning in mean.default(~"Sepal.Length"): argument is not numeric or logical:
## returning NA
```

```
##   mean("Sepal.Length")
## 1                   NA
```

Oops.  Mean of a string is NA.


```r
thing2 <- quo(Sepal.Length)

iris %>% summarize(mean(!!thing2))
```

```
##   mean(Sepal.Length)
## 1           5.843333
```


![](http://33.media.tumblr.com/d549e8332bf7f41e1f20f8a731b1ee70/tumblr_naw1kaSfcy1r8jjn6o1_500.gif)


I'm curious what errors this will give:


```r
thing <- quo("mean(iris$Sepal.Length)")

eval(thing)
```

```
## <quosure>
## expr: ^"mean(iris$Sepal.Length)"
## env:  empty
```

```r
eval(!!thing)
```

```
## Error: Quosures can only be unquoted within a quasiquotation context.
## 
##   # Bad:
##   list(!!myquosure)
## 
##   # Good:
##   dplyr::mutate(data, !!myquosure)
```


## Final Conclusion

* `expr` turns my code under a name, ready to be `eval`-ed later.
* `sym` turns my string into R code but also slaps some backticks on that bad boy if need be.
* `quo` (or `enquo` in function) turns my code into a... a quosure.  Why?
* `eval` evaluates the expression right the heck now
* `!!` takes a symbol or a quosure and prepares a pre-result, let's say, for a later function to evaluate
* `{{` mushes `!!` and `quo`/`enquo` into one function.


## Thoughts on teaching tidy eval

*Everything* centers around the idea of lazy vs eager eval.  I did not understand this until recently, but it's actually not too terribly complicated.

Here is my analogy, because it is the task I am avoiding by doing this writeup:  Suppose you are doing laundry.  

**Eager Eval**: Fold one pair of pants, put pants in drawer.  Fold one shirt, put shirt in drawer.  Fold another pair of pants, put pants in drawer.

**Lazy Eval**: Fold all laundry.  Put pants in pants drawer.  Put shirts in shirt drawer.


The second is more efficient, but it requires you to keep track of multiple stacks of clothes as you work.


`expr` and `sym` fold the pants.  `eval` puts the pants in the drawer.

`!!sym`, `!!quo` or `{{` folds the pants and puts them on the pants pile.  When the parent function is called, the pants pile gets put in the drawer.

![](https://media1.giphy.com/media/QSLtvOL02ggyA/giphy.gif)

*and here we have parse-eval*




## Remaining questions

* When might we actually want `!!` and `quo` to be separate steps?

* Why are quosures necessary, if you can `!!` symbols?

* Why can't you `eval` a quosure?  Why can't you `!!` an expression?  Why can you do both to a symbol?  *(More generally, I could use a clearer understanding of the role of these three object types.)*


