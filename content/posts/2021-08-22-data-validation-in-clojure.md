---
title: "Data validation in Clojure"
date: 2021-08-22T00:09:16-09:00
tags: [Data validation, Malli, Clojure, Functional programming]
draft: false
---

As programmers we work with data every day. It has different forms: data may come from the database, an API endpoint or from the user input. Juggling information coming from different resources may be a hassle especially then we needed it to be "shaped" in a certain way for our application or service to consume. Fortunately when developers face the problem they usually try to fix it.  
A library called Malli (fin. a "model") from the finish company Metosin was developed to help with the data validation and specification in the Clojure universe.  
Malli uses familiar for the Clojure developers hiccup-like syntax. It is data driven in its core and very flexible.  
Let's define our first schema (a blueprint of our data). A function `validate` validates a value against the given schema. Maps are open by default, but with a `{:closed true}` property we can disallow adding new values to the data structure. Keys are required by default and are ordered.  
There are about 100 default schema types in the Malli registry already and we can create our own as well.  

```clojure
;; REPL
(require '[malli.core :as m])

(def address-schema 
[:map
 [:id string?]
 [:tags {:optional true}[:set keyword?]]
 [:address
  [:map {:closed true}
   [:street string?]
   [:city string?]
   [:zip {:default 10016} int?]
   [:coords [:tuple double? double?]]]]])

(def address
{:id "US123"
:tags #{:home :kids :family}
:address {:street "100 Madison Ave"
          :city "New York"
          :zip 12000
          :coords [ 40.744807, -73.985255 ]}})

(m/validate address-schema address)
; => true

(def invalid-address
  {:id 123
    :tags #{:home :family}
    :address {:stret "100 Madison Ave"
          :city "New York"
          :zip 12000}})

(m/validate address-schema invalid-address)
; => false
```

We can use our own predicate function in the schema definition with a `:fn` keyword:

```clojure
;; REPL
(def fn-schema
  [:and
   [:vector
    [:map]]
   [:fn
    (fn [vec] 
      (pos? (count vec)))]])
   
(m/validate fn-schema (vector (hash-map :a 1 :b 2)))
=> true

(m/validate fn-schema [])
=> false
```

For detailed error messages we need to require `malli.error` namespace to our project. A function `explain` will give us a detailed error description. Results of running the `explain` can be made more concise and readable with the `humanize` function. We can even add spellchecking with a handy `with-spell-checking` function. The real power comes when we combile all three of them with a thread first macros `->`.

```clojure
;; REPL
(require '[malli.error :as me])

(-> address-schema
    (m/explain invalid-address)
    (me/with-spell-checking)
    (me/humanize))             => {:id ["should be a string"], 
                                   :address {:coords ["missing required key"], 
                                   :stret ["should be spelled :street"]}}
```
We can add custom error messages to our predicate function from the previous example.

```clojure
;; REPL
(def fn-schema
  [:and
   [:vector
    [:map]]
   [:fn
    {:error/message "vector should have at least 1 element"}
    (fn [vec] 
      (pos? (count vec)))]])

(-> fn-schema
    (m/explain [])
    (me/humanize))
; => ["vector should have at least 1 element"]
```

It is possible to generate a sample data with the `generate` function from the `malli.generator` namespace.

```clojure
;; REPL
(require '[malli.generator :as mg])

;; we can specify size and seed
(mg/generate address-schema {:size 10, :seed 10}) 
; => 
{:id "0e3r",
 :address {:street "5", :city "NxC", :zip -1, :coords [2.0 0.625]},
 :tags #{:-?d/DPL :Y*/*k5 :.43/Gm8p :L/A0s :+t/O! :*?uB/H}}

;; generated values are valid
(m/validate address-schema (mg/generate address-schema {:size 10, :seed 10})) 
; => true
```

`malli.util` namespace contains adapted functions to work with the schema. Many of them we know from the `clojure.core` namespace: `select-keys, dissoc, get, assoc, update, get-in, assoc-in, update-in`.

```clojure
;; REPL
(require '[malli.util :as mu])
(mu/get-in address-schema [:address :coords])
; => [:tuple double? double?]
```

We can also make all keys optional with the `optional-keys` utility function.
```clojure
;; REPL
(mu/optional-keys address-schema) ; all keys are now made optional
```

Malli supports data transformations with `decode` and `encode` functions. A `decode` will transform a value with a given decoding transformer against the schema. In the example below, `default-value-transformer` from `malli.transform`, namespace will apply the default value according to the schema if key is missing in the data structure we are transforming. 

```clojure
;; REPL
(require '[malli.transform :as mt])

(def address-no-zip
{:id "US123"
:tags #{:home :kids :family}
:address {:street "100 Madison Ave"
          :city "New York"
          :coords [ 40.744807, -73.985255 ]}})

(m/decode address-schema address-no-zip mt/default-value-transformer)
; =>
{:id "US123",
 :tags #{:home :family :kids},
 :address {:street "100 Madison Ave",
           :city "New York",
           :coords [40.744807 -73.985255], 
           :zip 10016}} ; default zip value was applied
```

This article is not a comprehensive guide to Malli schemas but rather a short overview of the library and it's useful functions for the data validation.  
If you would like to learn more, Malli [GitHub page](https://github.com/metosin/malli) has a very good documentation and series of video presentations that would help you start using it in your project. 