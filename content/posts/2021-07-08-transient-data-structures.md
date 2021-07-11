---
title: "Transient Data Structures in Clojure"
date: 2021-07-08T00:03:16-09:00
tags:
  [
    Performance,
    Transient Data Structures,
    Transients,
    Clojure,
    Functional programming,
  ]
draft: false
---

Although in Clojure we strictly adhere to the idea of immutability we still have an option to work with mutable data structures.

Transient (mutable) data structures can be used when we are working with large data sets. It may give our application a significant performance boost.

Function `transient` is used to make immutable data structure mutable. We then perform the necessary calculations (operations) and convert it back to mutable structure with the function `persistent!` In detail, it is done by copying the data structure to the transient version of it and then copying it back to immutable. Clojure itself performs mutation on data structures; for example, when we call a function `assoc`. While the data structure is in a mutable state it is encapsulated from the outside application logic in the function.

Transient data structures are created from the existing persistent data structures. While Clojure `vectors`, `maps` and `sets` are supported, `lists` are excluded since there is no performance benefit of making them mutable.

`Transients` are supporting read-only functions of its immutable versions. For example, you can call `nth`, `get`, `count` on a transient vector. But we cannot use persistent functions of the source data structure. Calling `assoc` or `conj` on the transient vector will throw an exception. In their mutable state maps, sets and vectors have their own alternative functions to perform operations. Function names are similar to their persistent versions, only the `!` sign is added to the name to signify the mutation, for example `assoc!`, `conj!`

Let's write some code it in the REPL. The function `vrange` is used to generate some values and store them in an immutable vector. `vrange-t` has a similar functionality but uses the transient vector to store values.

We use macros `time` to check how long the function runs to perform our calculations. For cleaner result in the REPL we can wrap a function call with a `do` expression to prevent the returning of the generated vector.

```clojure

(defn vrange [n]
  (loop [i 0 v []]
    (if (< i n)
      (recur (inc i) (conj v i))
      v)))

(defn vrange-t [n]
  (loop [i 0 v (transient [])]
    (if (< i n)
      (recur (inc i) (conj! v i))
      (persistent! v))))

;; almost twice as fast with transient
(time (do (vrange 1000000) 0))    ; on average 46 ms
(time (do (vrange-t 1000000) 0))  ; on average 24 ms

```

What if we use a JavaScript array in the ClojureScript environment instead? The tagged literal `#js` will create a JS data structure (an array in our case). `.push` adds elements to our array. A function `js->clj` is used to convert JS array to the Clojure vector.
Correction: `js->clj` is not optimized for speed so for performance testing we should use `vec` function instead.

```clojure
;; ClojureScript
(defn vrange-j [n]
  (loop [i 0 v #js []]
    (if (< i n)
      (recur (inc i) (do (.push v i) v))
      (vec v))))

(time (do (vrange-j 1000000) 0))	; on average 55 ms, slowest result
```

As we can see from our experiments in the REPL, when used properly in the code, transient data structures give us a substantial performance improvement.
