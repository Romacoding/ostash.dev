---
title: "Destructuring in Clojure"
date: 2021-05-10T00:08:14-04:00
tags: [Destructuring, REPL, Clojure, Functional programming]
draft: false
---

Destructuring in Clojure can be confusing for someone coming from the language where this technique is not implemented. Hopefully when you are done reading this article, destructuring will make sense and you will be implementing this method in your own coding projects.

Destructuring is used to assign(extract) values from data structures to local bindings(variables). The end goal of destructuring is to make your code cleaner and more concise.
We can destructure when declaring a local `let` binding or directly in function parameters.

In the example below we are destructuring a vector with three elements into local variables then printing out their values with "println" function. The vector structure on the right is "reconstructured" with vector of variables on the left.
```clojure
(let [[fruit1 fruit2 fruit3]["Apples" "Bananas" "Mangos"]]
	(println fruit1 fruit2 fruit3))
=> Apples Bananas Mangos
```
Sometimes we don't know how many elements are in the data structure so we simply use `& args` placeholder for the rest of the elements. We have bound the first element of the vector to the variable `fruit1`. When we evaluate the expression in the REPL a value of the `args` is returned. It is a list with the second and third element.
```clojure
(let [[fruit1 & args]["Apples" "Bananas" "Mangos"]]
args)
=> ("Bananas" "Mangos")
```
Let's see how we can perform destructuring in the function parameters. We define a `fruit-basket` vector that is passed to the function `get-fruit`. Then we return the first element from the vector by assigning `first-fruit` variable to it. When we destructure the vector  we use square brackets. 
```clojure
(def fruit-basket 
	["Apples" "Bananas" "Mangos"])

(defn get-fruit 
	[[first-fruit]] 
	first-fruit)

(get-fruit fruit-basket) => "Apples"
```
We can substitute a value from the data structure that we are not going to use with an underscore `_`. Below we return a second element from the `fruit-basket`.
```clojure
(defn get-fruit 
	[[_ second-fruit]] 
	second-fruit)

(get-fruit fruit-basket) => "Bananas"
```
If we would need to use a whole vector for any reason in our function plus destructure some of its elements we would do it using a `let` binding as in the example below. We print values of the first and  third element of the vector structure then return the vector itself.
```clojure
(defn get-fruit 
	[fruits] 
	(let [[first-fruit _ third-fruit] fruits]
		(println first-fruit third-fruit) 
		fruits))

(get-fruit fruit-basket) => Apples Mangos 
							["Apples" "Bananas" "Mangos"]
```
What if elements of the vector are data structures themself? We can destructure them too! In the example below the vector has vectors as its elements so we would use extra square brackets to assign its values. A `str` function is used to concatenate(join) strings in Clojure.
```clojure
(def fruit-basket 
	[["Apples" 2] ["Bananas" 5] ["Mangos" 3]])

(defn get-fruit 
	[fruits] 
	(let [[[first-fruit quantity] _ third-fruit] fruits] 
		(str "We have " quantity " " first-fruit " in the basket")))

(get-fruit fruit-basket) => "We have 2 Apples in the basket"
```
Destructuring is super handy with Clojure maps. Let's redefine `fruit-basket` as a map data structure. To get a value of a key in the map we can use `get` function or simply use the key as the function(yes, it behaves as a function!). Keys in Clojure maps are special data types `:keywords`.
To destructure a map we use curly brackets where we specify variable name then provide a key we want to get a value from.
```clojure
(def fruit-basket 
	{:apples 2 :bananas 5 :mangos 3})

;;To get a value of the :apples we use a key (:keyword) as a function
(:apples fruit-basket) => 2

;;Map destructuring using a let binding 
(defn get-fruit-quantity 
	[fruits] 
	(let [{apples-quantity :apples} fruits]
		(str "We have " apples-quantity " apples in the basket")))

(get-fruit-quantity fruit-basket) => "We have 2 apples in the basket"
```
When we destructure a Clojure `map` we can set a default value for the key in case a data structure doesn't have it for any reason . We redefine `fruit-basket` so it doesn't have `:apples` anymore. In our function we have set a default value of `apples-quantity` local binding with `:or` keyword. It becomes very handy when working with APIs if the response map is missing some keys.
```clojure
(def fruit-basket 
	{:bananas 5 :mangos 3})

(defn get-fruit-quantity 
	[fruits] 
	(let [{apples-quantity :apples :or {apples-quantity 2}} fruits]
		(str "We have " apples-quantity " apples in the basket")))

(get-fruit-quantity fruit-basket) => "We have 2 apples in the basket"
```
Another way to destructure a map is to use `:keys` keyword followed by  map's keys in a vector. Since we are working with maps we wrap the expression in curly brackets. 
```clojure
(def fruit-basket 
	{:apples 2 :bananas 5 :mangos 3})

(defn get-fruit-quantity 
	[fruits] 
	(let [{:keys [apples bananas mangos]} fruits]
		(str "We have " (+ apples bananas mangos) " fruits total in the basket")))

(get-fruit-quantity fruit-basket) => "We have 10 fruits total in the basket"
```
If you happened to parse a json object you might have keys saved as `string` type not a `keyword`. It is still possible to get their values. We need to use `:strs` keyword instead of `:keys` in that case.
```clojure
(def fruit-basket 
	{"apples" 2 "bananas" 5 "mangos" 3})
	
(defn get-fruit-quantity 
	[fruits] 
	(let [{:strs [apples bananas mangos]} fruits]
		(str "We have " (+ apples bananas mangos) " fruits total in the basket")))

(get-fruit-quantity fruit-basket) => "We have 10 fruits total in the basket"
```
We can also bind a whole map to a variable using `:as` keyword while still destructuring its elements.
```clojure
(def fruit-basket 
	{:apples 2 :bananas 5 :mangos 3})

(defn get-fruit 
	[fruits] 
	(let [{:keys [apples bananas mangos] :as basket} fruits]
		basket))

(get-fruit fruit-basket) => {:apples 2 :bananas 5 :mangos 3}
```
Same as vectors maps can be destructured directly in function parameters.
```clojure
(defn get-fruit 
	[{:keys [apples bananas mangos] :as basket}]
	basket))
```
What if map keys have vectors as their values? As you would guess we can destructure them too. We simply provide names for the elements of the vector in square brackets followed by the key from the map and wrap it in curly brackets.
```clojure
(def fruit-types 
	{:apples ["Fuji" "Gala" "Cameo"] 
	 :bananas ["Cavendish" "Red" "Lady's Finger"] 
	 :mangos ["Honey" "Haden" "Francis"]})

(defn get-fruit-type
	[{[type1 type2 type3] :apples}]
	(str type1 " " type2 " " type3 ))

(get-fruit-type fruit-types) => "Fuji Gala Cameo"

```
Another practical example for destructuring is then we define a function with an optional second parameter. In the example below when the function `opt-args` is run with only one argument the value of the optional parameter is `nil`. If we provide the second argument its value is destructured to the variable `flag`.
```clojure
(defn opt-args 
	[first & [flag]]
	flag)

(opt-args 1) => nil
(opt-args 1 false) => false
```
We could also pass to the function an optional map of the parameters. Then we destructure it by providing a variable name for the map preceding by `:as` keyword and wrapping them in curly brackets.
```clojure
(defn opt-args-map 
	[first & {:as opts}]
	opts)

(opt-args-map 1) => nil
(opt-args-map 1 
	:flag false
	:debug true) => {:flag false, :debug true}
```
#Bonus

What about Clojure lists you might ask? We can destructure them too! As with vectors we use square brackets to rebind list elements to variables. Something to keep in mind, we need to define a list with apostrophe symbol or a list function: `'("Apples" "Bananas" "Mangos")` or `(list "Apples" "Bananas" "Mangos")`. If we don't do that we will get an error because Clojure reader expects a first element in the list to be the function.
```clojure
(let [[fruit1 fruit2 fruit3] '("Apples" "Bananas" "Mangos")]
	(println fruit1 fruit2 fruit3))
=> Apples Bananas Mangos
```
How about destructuring a string?! Yes, destructuring works on any sequential data structures. In the example below we destructure the first four elements of `name-string` and use underscore for the fifth unused element.
```clojure
(def name-string "Roman")

(let [[char1 char2 char3 char4 _] name-string]
	(str char1 char2 char3 char4)) => "Roma"
```