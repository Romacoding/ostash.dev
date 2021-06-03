---
title: "Clojure namespaces"
date: 2021-02-17T00:05:56-04:00
tags: [Functions, Namespaces, Clojure, ClojureScript, Functional programming]
draft: false
---

Let's talk about namespaces today. Below is a structure of the core.clj file that is located in /src/app/ directory. Function *ns* sets the namespace. We use `directory-name.file-name` for the name then `:require` to load external namespaces.

```clojure 
(ns app.core ; we use a file name dot directory name as a project namespace
  (:require ; loads the namespaces below
    [clojure.set :refer :all] ; imports all functions from clojure.set
    [clojure.string :as str] ; imports clojure.string with name "str"  
    [clojure.set :refer [union]])) ; imports only "union" function from clojure.set
```
If we would like to get a current namespace in the REPL we use the function `ns-name` and pass a `*ns*`(keeps info about the current namespace) var to it.
```clojure
(ns-name *ns*) ; => user
```
To switch to a specific namespace we use the `in-ns` function. It is important to remember to put a `quote '` sign before the namespace name to stop the Clojure reader from trying to resolve the namespace's value. We will receive a compiler exception error if we don't quote the namespace. 
```clojure
(in-ns 'clojure.string) ; => #namespace[clojure.string]
```
When loading a namespace in the REPL we would use the `require` function. 
```clojure
(require '[clojure.set :as cset])
```
To see all functions available in a specific namespace we can use *ns-publics* function. It returns a map data structure so to get only names we use *keys* function.
```clojure
(keys (ns-publics 'clojure.string)) ; => (capitalize reverse join trim ...)
```