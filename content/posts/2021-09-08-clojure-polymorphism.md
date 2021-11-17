---
title: "A concept of polymorphism in Clojure"
date: 2021-09-08T00:10:16-09:00
tags: [Polymorphism, Clojure, Functional programming]
draft: false
---

Working with larger Clojure projects or libraries requires us to use some concepts that we all know from the Object Oriented Programming (OOP) world. Today's topic is about a use of the polymorphic behavior while programming in Clojure.  
Why polymorphism is important? Why do we need to use OOP concepts at all in the functional programming? While not requiring a prototypal inheritance,  polymorphism becomes a very useful feature in Clojure. It gives the language flexibility and extensibility in design of the system architecture.  
So what does the term polymorphism mean in the functional world? Very often we need the function to behave differently based on the type, value or number of arguments that are passed to it. The concept of polymorphism is called to help with this task.
Clojure language has three types of the polymorphic behavior: depending on an arity (number of arguments), value and type.

#Arity  
Multi-arity functions are used in the situation when we have some optional arguments, that may or may not be available. In case of no arguments provided a zero-arity body usually calls the unary one.
```clojure
;; multi-arity function definition
(defn greet
  ([]     (greet "World")) ;zero arity function body
  ([name] (str "Hello " name "!")) ;unary function body
  ([name1 name2] (str "Hello " name1 " and " name2 "!"))) ;binary function body
```
The above function declares three arities: with zero, one and two parameters. The zero-parameter arity calls the one-parameter arity with a predefined (default) argument and returns a string "Hello World!". When function is called with one or two arguments, the proper arity "body" is used.
```clojure
(greet) => "Hello World!"
(greet "Roman") => "Hello Roman!"
(greet "Roman" "Anna") => "Hello Roman and Anna!"
```
 Clojure provides two other mechanisms for a conditional (polymorphic) behavior: multimethods (by value) and protocols (by type).

#Value  
A Clojure multimethod is a combination of a dispatching function and one or more methods. The dispatching function at the execution time will produce the value that will be used to match the one we set when we defined our methods.
```clojure
;; multimethod definition
(defmulti voice :type)
;;        ^____________ multimethod name
;;              ^______ dispatching function
```
In the example above a keyword will serve as a function to acess the value in the map.  
Multimethod processing logic: first it applies arguments to a dispatching-function to get the dispatching-value; then finds a method associated with the dispatching value; finnaly, applies arguments to the method. If there's no such a method, calls a `:default` one. If there's no `:default` method, throws an error.  

A `defmethod` macro creates and installs a new method of multimethod associated with a dispatch-value.
```clojure
;; method definition
(defmethod voice "cat" [animal] ;; method arguments
  ;;       ^_____________________ multimethod name
  ;;             ^_______________ dispatching value
  ;;                   ^_________ dispatching function argument 
  (println  "Cat goes: Meow!")) ;; method body

(defmethod voice "dog" [animal]
  (println  "Dog goes: Woof!"))

(defmethod voice :default [animal]
  (println (str "Sorry, no voice type for a " (:type animal)"!")))

(voice {:type "cat"}) ;; Cat goes: Meow!
(voice {:type "goat"}) ;; Sorry, no voice specified for a goat!
```
#Important  
Methods don't have to be defined in the same namespace as their dispatching function. This gives us an ability to extend the functionality of multimethods even if they are defined in the third party library that we use in our project.

#Type  
Dispatching performed by the class (type) of the first argument is the most common use of multimethods. Protocols are designed to implement this type of behavior.  Multimethods are slower than protocols, therefore, if we only need polymorphism by type it is better to utilize protocols. 

Protocols provide performant and dynamic polymorphisms without a need of prototypal inheritance. They are similar to the interfaces from OOP languages (for ex. Java). The protocols group multiple functions together. Each function has parameters and doc strings, but no implementation. Each protocol function must have at least one argument (it is used for dispatching). The object implementing the protocol is passed as the first parameter somewhat like the implicit "this" parameter in object oriented languages. Macros `defprotocol` creates protocol function like macros `defn` genetic one.

```clojure
;REPL
(require '[clojure.string :as str])
(defprotocol Calendar
  (month [_ base-month])
  (day   [_])
  (year  [_]))

(defn format-cal [cal]
  (str (month cal 1) "." (day cal) "." (year cal)))
```

Clojure allows extending any protocol to any type, even if the type was not originally designed to support it. In the example below we are extending Java class Date with our protocol Calendar. Macros `extend-type` is used to extend an existing type. The class Date is not modified in any way.
```clojure
;REPL
(import '[java.util Date])
(extend-type Date ; 1 type, many protocols
  Calendar 
  (month [this base-month] (+ base-month (.getMonth this)))
  (day   [this] (.getDate this))
  (year  [this] (+ 1900 (.getYear this))))
```
If we need to extend protocol on more than one type we can use `extend-protocol` macros. 
```clojure
;REPL
(extend-protocol Calendar ; 1 protocol, many types
  Date
  (month [this base-month] (+ base-month (.getMonth this)))
  (day [this] (.getDate this))
  (year [this] (+ 1900 (.getYear this)))
  ;Footype
  ;(day...)
  ;...
  )

(format-cal (Date.)) => "9.8.2021"
```
#reify
```clojure
;REPL
(defn create-cal [m d y]
  (reify Calendar
    (month [_ b] (+ b m))
    (day   [_] d)
    (year  [_] y)))

(format-cal (create-cal 7 7 2021)) => "8.7.2021"  
```

When we run above function `create-cal` with month day and year arguments, we have created a closure. The macros `reify` builds a temporary (anonymous) type similar to anonymous functions that extends the protocol Calendar with its methods. The function `format-cal` returns a formatted calendar string.  

#Conclusion  
A conditional behavior based on the arity is probably the simplest and most used form of polymorphism in Clojure. Multimethods and protocols on the other hand, although more complex, provide a very powerful way to design and architect modern systems without a need of prototypal inheritance.
