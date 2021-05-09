---
title: "Working with ClojureScript at ease"
date: 2021-04-12T00:09:56-04:00
tags: [Functions, REPL, ClojureScript, JavaScript, Functional programming]
draft: false
---
Today we are going to talk about ClojureScript and review many useful functions and concepts you need to know when working with Clojure/JavaScript interoperability.

To get started you would need a ClojureScript Repl running. On Mac OS and Linux systems I recommend to install [Planck](https://planck-repl.org/guide-all.html).

You can even practice with ClojureScript on your mobile phone with Replete REP‪L app.  It is available for both [Apple](https://apps.apple.com/us/app/replete-repl/id1013465639) and [Android](https://play.google.com/store/apps/details?id=com.fikesfarm.Replete&hl=en_US&gl=US) platforms. 

With a ClojureScript Repl running we can now start to experiment. In ClojureScript to access JavaScript functions we have a special namespace `js`. For those coming from JavaScript `js` is the same as `window` object in a browser with all builtin functions(methods) attached to it. For ex.: `(js/alert "Hello world!")` or `(js/console.log "Hello world!")`.

In the example below we will print "Hello world" with a delay of 1000 ms using a setTimeout function:
```clojure
(js/setTimeout #(println "Hello world") 1000)
```
To create a JavaScript object we can use `js-obj` function:
```clojure
cljs.user=> (js-obj "zip" 10001 "city" "New York")
#js {:zip 10001 :city "New York"}
```
Alternatively we can use `#js` macros:
```clojure
cljs.user=> #js {:zip 10001 :city "New York"}
#js {:zip 10001 :city "New York"}
```
To delete a property from the object we use `js-delete` function. In the function call we need to provide an object name and the key. We need to remember that in JS world keys are strings not keywords:
```clojure
cljs.user=> (def location #js {:zip 10001 :city "New York"})
#'cljs.user/location
cljs.user=> (js-delete location "zip")
true
cljs.user=> location
#js {:city "New York"}
```
To create a JavaScript array we use `#js` macros again or an `array` function:

```clojure
cljs.user=> #js ["Apples", "Bananas", "Apricots"]
#js ["Apples" "Bananas" "Apricots"]
```
```clojure
cljs.user=> (array "Apples", "Bananas", "Apricots")
#js ["Apples" "Bananas" "Apricots"]
```
Also we could create an array with a desirable length with `make-array` function. In the example below we are creating array with five empty elements:
```clojure
cljs.user=> (make-array 5)
#js [nil nil nil nil nil]
```
We can convert data structures between JavaScript and Clojure using a `js->clj` function: 

```clojure
cljs.user=> (js->clj location)
{"city" "New York"}
```
Unfortunately keys are still strings after the conversion. To change them back to keywords we need to provide an optional parameter `:keywordize-keys` with value of `true`:
```clojure
cljs.user=> (js->clj location :keywordize-keys true)
{:city "New York"}
```
A `clj->js` function is used to convert Clojure data structures to JS ones:
```clojure
cljs.user=> (clj->js {:zip 10001 :city "New York"})
#js {:zip 10001, :city "New York"}
```
We can get a property value from the JS object with `.-key` syntax. A dot dash `.-` is used to distinguish a reading of the property from a calling of the method of the object with `.`: 
```clojure
cljs.user=> (.-city location)
"New York"
```
Another way to read the property of the object is by using `aget` function:
```clojure
(aget location "city")
"New York"
```
To write a value to the object property we use `aset` function:
```clojure
cljs.user=> (aset location "city" "Boston")
"Boston"
```
Reading a value of the nested properties is quite simple too. We use `..` to get to the nested structure:
```clojure
cljs.user=> (def location #js {:address #js {:zip 10001 :city "New York"}})
#'cljs.user/location
cljs.user=> (.. location -address -zip)
10001
```
Down below is an example of two ways of running `now` method on a JS `Date` object using a dot notation:
```clojure
cljs.user=> (js/Date.now)
1618189077370
cljs.user=> (.now js/Date)
1618189084328
```
To call a function constructor to create an instance of the JS object we use `Objectname.` dot notation. It is the same as calling `new Date` in JavaScript:
```clojure
cljs.user=> (js/Date.)
#inst "2021-04-12T01:13:57.709-00:00"
```