---
title: "Better learning with Clojure "
date: 2021-02-20T00:04:56-04:00
tags: [Functions, Macros, REPL, Clojure, ClojureScript, Functional programming]
draft: false
---
Clojure comes with many useful functions that are a great learning resource for those who are just starting to explore the language.  
Function *doc* gives us a documentation on a given *var*, *macro* or *special form*. In an example below we see the documentation on the *print* function.
```clojure
user=> (doc print)
-------------------------
clojure.core/print
([& more])
  Prints the object(s) to the output stream that is the current value
  of *out*.  print and println produce output for human consumption.
nil
```
Another very useful function is a *dir* macro. It prints out a sorted directory of public *vars* in a given namespace. The example below shows us how *dir* is used to get all functions from *clojure.string* namespace (a list was shortened for posting).    
```clojure
user=> (dir clojure.string)
blank?
capitalize
ends-with?
escape
includes?
...
```
Function *apropos* returns a sequence of all
public definitions in all loaded namespaces that match the provided string or Regex pattern. For example if we run *apropos* with an argument "println" we will get all functions with name matching this string (with their namespaces). 
```clojure
user=> (apropos "println")
(clojure.core/println clojure.core/println-str)
```
We can access a source code of the built-in function definition directly from the Clojure Repl with a help of *source* macro. Reading the source code, especially when you are new to the language, helps greatly to grow as a software developer.  
```clojure
user=> (source print)
(defn print
  "Prints the object(s) to the output stream that is the current value
  of *out*.  print and println produce output for human consumption."
  {:added "1.0"
   :static true}
  [& more]
    (binding [*print-readably* nil]
      (apply pr more)))
nil

```
We can also expand the macro and see what it is doing "under the hood". In the example below we use a macroexpand function on the *defn* macro. As we can see it is actually uses *def* special form to assign to a *var* sum an anonymos function.
```clojure
user=> (macroexpand '(defn sum [a b] (+ a b)))
(def sum (clojure.core/fn ([a b] (+ a b))))
```