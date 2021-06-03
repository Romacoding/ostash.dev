---
title: "Clojure atoms"
date: 2021-06-02T00:06:56-04:00
tags: [Functions, atoms, Clojure, state, Functional programming]
draft: false
---

All data structures in Clojure are immutable. Any changes we do will result in a new data structure leaving an original one untouched. When we write code, quite often we need to make parts of our application aware of changes and behave accordingly to those muttations. Situation is even more complicated if we have two functions that are trying to make changes to the same data structure.

One of the methods to coordinate and control changes in Clojure is to use Atoms, designed specifically to deal with those problems. Atoms are a reference data types and are used for a shared state in applications.

An atom function is used to create `atoms`. Below we define a `bank-account` `atom`. In this example it is a map with the `:balance` key that has an initial value of `0`.
```clojure
(def bank-account (atom {:balance 0}))
```
To read the current value of the atom we use a `deref` function or simply add `@` sign (deref macros) to its name.
```clojure
(deref bank-account) => {:balance 0}

@bank-account => {:balance 0}
```
There are two ways to update values in the atom. If we need a previous state to calculate the new value we can use `swap!` function. The `!` symbol after the name indicates that the function is performing a mutation. Function `swap!` takes the name of the atom and the function to perform the changes. In our example we use the `update` function that receives a key of the map value we want to update and the function to perform it. We use a lambda function that receives a previous value as an argument to calculate the new account balance.

```clojure
(defn update-balance [credit]
  (swap! bank-account update :balance (fn [prev-balance] (+ prev-balance credit))))
```

If we don't need a previous value of the atom we can use `reset!` function to completely replace it with a new "state".

```clojure
  (reset! bank-account {:balance 0}) => {:balance 0}
```

Another useful function when working with atoms is `compare-and-set!`. It sets the value of the atom to a new value if the current value of the atom is identical to the value we provided to compare. Returns `true` if change happened, otherwise `false`.

```clojure
(compare-and-set! atom-name value-to-compare new-value)
```

##Watchers and validators

We can monitor our state changes by adding a watcher to the atom. Everytime the atom is changed a watcher function is run. It is useful for making your application reactive to changes of its state.
For those who are familiar with the JavaScript library React, this behavior is not new. A change to the state in React triggers the re-rendering of the component that uses its any of the state's values.

```clojure
(add-watch bank-account :logger (fn [key state old-value new-value]
                                 (println "Balance was changed to:" (:balance new-value))))

(update-balance 10) =>
Balance was changed to: 10

(reset! bank-account {:balance 0})
Balance was changed to: 0
```

To remove the watcher we use the function `remove-watch`.

```clojure
(remove-watch bank-account :logger)
```

The function `set-validator!` sets a validator for the atom to check if a new state is acceptable (meets our criteria). The validator function has one argument, the new state. If the new state is unacceptable, the validator returns false or throws an exception. Let's try it in practice. We set the validator to the `bank-account` atom that prevents a negative balance of the account. If we try to withdraw an amount that will result in the negative balance, the validator will reject the change to the state.

```clojure
(set-validator! bank-account (fn [new-state]
                                (>= (:balance new-state) 0)))

(defn withdraw-balance [credit]
  (swap! bank-account update :balance (fn [prev-balance] 
                                        (- prev-balance credit))))

(update-balance 10) => Balance was changed to: 10

(withdraw-balance 10) => Validator rejected reference state
```
