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
If we would like to get a current namespace we can use the function *ns-name* and pass to it a `*ns*` var that keeps info about the current namespace.
```clojure
(ns-name *ns*) ; -> user
```
To switch to a specific namespace we use *in-ns* function.
```clojure
(in-ns 'clojure.string) ; -> #namespace[clojure.string]
```
To see all functions available in a specific namespace we can use *ns-publics* function. It returns a map data structure so to get only names we use *keys* function.
```clojure
(keys (ns-publics 'clojure.string)) ; -> (capitalize reverse join trim ...)
```