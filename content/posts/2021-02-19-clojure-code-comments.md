---
title: "Clojure code comments"
date: 2021-02-19T00:06:56-04:00
tags: [Functions, Comments, Clojure, ClojureScript, Functional programming]
draft: false
---

If we would like to comment out a line of the code we simply use a semicolon at the beginning. To comment out a block of the code we use hash underscore `#_` (`reader macro`).

```clojure
;(1 2 3)

#_(defn sum
  [a b]
  (+ a b))
```

If an entire line is dedicated to the comment we conventionally use a double semicolon.
When a comment is in line with the code we just use a single semicolon. We could also put a comment inside of the function (`Docstring`), after its name but before parameters.

```clojure
;; Our full line comment
(defn sum
  "Returns sum of a and b"
  [a b] ; our inline comment
  (+ a b))
```

Finally we can use a `comment` macro (`Rich Comment`) for the code we are only going to use for development/debugging. For example we could have two versions of the same function but with different implementations. It returns `nil` when evaluated.

```clojure
(comment
  (println "Test string")
  (* 2 3))
```
