---
title: "Functions in Clojure"
date: 2021-02-12T14:42:56-04:00
tags: [Functions, Clojure, ClojureScript, Functional programming]
draft: false
---

**Functions** in Clojure are data structures since the language follows code as a data structure principle (homoiconicity).  
In Clojure those data structures are **lists** since it is a dialect of the **LISP** (LISt Processor) language, the second oldest language still in use today.  
A **list** data structure is used to store a collection of data. Items in the list are wrapped in parenthesis.  
```clojure
(1 2 3)
```
There is no need for commas in Clojure to separate values. We use a whitespace instead.  
Clojure interpreter expects the first item in a list (symbolic expression) to be a function (prefix notation). In this simple example we will add 3 numbers. + is actually a function from the clojure.core namespace (namespaces are like modules in JS world). When we evaluate the expression we will get 6.
```clojure
(+ 1 2 3) -> 6
```
If we try to evaluate a list `(1 2 3)` we will get an execution error. To prevent this we simply put an apostrophe before the data structure. When we evaluate the code a list itself is returned.
```clojure
'(1 2 3) -> (1 2 3)
```
Let's see how we would define a function in Clojure. We use a *defn* macro (a special type of function), specify the name (symbol)  followed by parameters in square brackets then the body (expression) of the function. We could have multiple expressions in the body, but only the last one is returned.
```clojure
(defn sum
  [a b]
  (+ a b))
```
To run the function we type the name and function arguments wrapped in parenthesis. As a result we get 7.
```clojure
(sum 2 5) -> 7
```
To understand what macro *defn* is doing under the hood let's try to define a *sum* function using a *def* special form. *Def* declares a global *Var* (similar to a variable in JS) with the name *sum* (symbol)  and we assign to it an anonymous function. So *defn* macro is a "syntactical sugar" to make our code more concise and clean.
```clojure
(def sum
  (fn [a b] (+ a b)))
```
Another way to write an anonymous function is to use a shorthand syntax (lambda function). We use a pound or hash symbol then our expression (body) inside parenthesis and arguments separated by whitespace. Finally we wrap our function in parenthesis. In the body we use *%* as a placeholder for parameters, %1 for the first %2 for the second and so on. We don't need to use a parameter number if only one is expected.
```clojure
(#(+ %1 %2) 2 5)
```