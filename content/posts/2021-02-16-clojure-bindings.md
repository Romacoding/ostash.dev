---
title: "Bindings in Clojure"
date: 2021-02-16T14:42:56-04:00
tags: [Functions, Bindings, Clojure, ClojureScript, Functional programming]
draft: false
---

In Clojure we can bind a piece of the code/data to a `var` (variable). To declare a global var we use a `def` special form (similar to `let` keyword in JS world).

```clojure
(def my-list '(0 1 2))
```

We could also bind an expression to a `var`. Function `range` in our example returns numbers from 0 to 2 `(0 1 2)`.

```clojure
(def my-new-list (range 3))
```

We could also define a lexically scoped (local) bindings with a `let` special form. We declare them in square brackets by providing name and value pairs. `a` and `b` are only accessible in `let` scope wraped with parenthesis.

```clojure
(let [a 1
      b 2]
  (+ a b)) -> 3

(+ a b) -> Syntax error
```
