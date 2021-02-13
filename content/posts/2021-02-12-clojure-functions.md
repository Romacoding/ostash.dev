---
title: "Functions in Clojure"
date: 2021-02-12T14:42:56-04:00
tags: [Functions, Clojure, ClojureScript, Functional programming]
draft: false
---

**Functions** in Clojure are data structures since the language follows **code as a data structure** principle (homoiconicity).  
In Clojure those data structures are **lists** since it is a dialect of the **LISP** (LISt Processor) language, the second oldest language still in use today.  
A **list** is a data structure used to store a collection of data. Items in a list are wrapped in parenthesis.  
```clojure
(1 2 3)
```
There is no need for commas in Clojure to separate values. We use a space instead.  
Clojure interpreter expects the first item in a list to be a function. In this simple example we will add 3 numbers. + is actually a function from clojure.core namespace (module in JS world). When we evaluate the expression we will get 6.
```clojure
(+ 1 2 3) -> 6
```
If we try to evaluate a list `(1 2 3)` we will get an execution error. To prevent this we simply put an apostrophe before the data structure. When we evaluate the code a list itself is returned.
```clojure
'(1 2 3) -> (1 2 3)
```
Let's see how we would define a function in Clojure. We use a *defn* macros (a type of function) followed by parameters in square brackets then the body of the function. We could have multiple calculations in the body, but only the last one is returned.
```clojure
(defn sum
  [a b]
  (+ a b))
```
To run the function type the name and function arguments wrapped in parenthesis. When we evaluate it we get 7.
```clojure
(sum 2 5) -> 7
```
To understand what macros *defn* is doing under the hood let's try to define a *sum* function using a *def* special form. *Def* declares a global *Var* *sum* (variable in JS) and we assign to it an anonymous function. So *defn* macros is a "syntactical sugar" to make our code more concise and clean.
```clojure
(def sum
  (fn [a b] (+ a b)))
```
Another way to write an anonymous function is to use a shorthand syntax. We use a pound or hash symbol then our evaluation(body) inside parenthesis and the arguments separated by comma. Finally we wrap our function in parenthesis. In the body we use *%* as a placeholder for parameters, %1 for the first %2 for the second and so on. We don't need to use a parameter number if only one is expected.
```clojure
(#(+ %1 %2) 2 5)
```
If we would like to comment a line of code we simply use a semicolon at the beginning. To comment a block of the code we use *#_* a reader macro.
```clojure
;(1 2 3)

#_(defn sum
  [a b]
  (+ a b))
```
You could put a comment inside of the function, after its name before the parameters.
```clojure
(defn sum
  "Returns sum of a and b"
  [a b]
  (+ a b))
```
Here is a video for this section.
{{< youtube FoDj-DTISes >}}