---
title: "Pre and post conditions in Clojure"
date: 2021-07-01T00:03:16-09:00
tags: [Assertions, Function conditions, Clojure, Functional programming]
draft: false
---

Today we will discuss a topic of `pre` and `post` assertions (conditions) in Clojure. The language gives programmers an option to set certain conditions we want to constrain our function with. We can define the limitations on the input parameters and the return value of the function.

`Pre` and `post` assertions are defined in a vector and can be saved in a function's [metadata](/posts/2021-06-18-clojure-metadata). The expressions we define in `pre` and `post` condition vectors must return logical `true` for the constrains to pass. If any of them return `false` we receive an `AssertionError` with the specific constrain expression.
Lets try it in the REPL. 
```clojure
(defn func ^{:pre [(pos? x)]
             :post [(< % 100) (> % 1)]}
  [x]
  (+ 1 x))

;; Our function fails on pre condition: function argument should be a positive number. 
;; Post condition is not checked in this case.
(func -1) =>	Execution error (AssertionError) at user/func (REPL:1).
				Assert failed: (pos? x)

;; Our function fails on post condition: output should be less then 100.
(func 100) =>	Execution error (AssertionError) at user/func (REPL:1).
				Assert failed: (< % 100)
```
