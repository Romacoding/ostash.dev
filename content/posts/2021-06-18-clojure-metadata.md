---
title: "Clojure metadata"
date: 2021-06-18T00:06:16-09:00
tags: [Type hints, Metadata, Clojure, Functional programming]
draft: false
---
Conceptually metadata is an ability for the symbol or the collection to have some additional information for the Clojure compiler. It is implemented as a map data structure and is often used to convey the type information to the compiler, documentation and compilation warnings.
Let's experiment with metadata in the REPL. Function `meta` is used to read metadata, `with-meta` to write metadata.
```clojure
(def m-data {:first "Roman"}) => #'user/m-data
(meta (with-meta m-data {:doc "Test"})) => {:doc "Test"}
```

In Clojure we can define some custom properties that will be saved in object's metadata. For example, if we want to make global Var `xx` private (not accessible outside of the current namespace) we can add `^:private` in the form definition (^ is the metadata marker). Now Var `xx` has a metadata property :private with a value of `true`.
```clojure
(def ^:private xx 0) => #'user/xx
(meta #'xx) => 	{:private true,
				:line 1, 
				:column 1, 
				:file "NO_SOURCE_PATH", 
				:name xx, 
				:ns #object[clojure.lang.Namespace 0x6e5bfdfc "user"]}
```
We use `#'` Var quote to pass the Var reference to the meta function (and not the value it resolves to).
A `^:private` directive also works for functions. Alternatively we can use the `defn-` macro to make the function private.

Another useful metadata property is `^:const`. It tells the Clojure compiler that the Var should not be redefined later in the code and remains constant.

A `^dynamic` property gives the ability to redefine Var only for the current form (local binding) with `binding` macro. Without this metadata property we will get an execution error.
```clojure
(def ^:dynamic xy 10) => #'user/xy
(:dynamic (meta #'xy)) => true
(binding [xy 11]
	xy) => 11
```

Function's metadata additionally has an `arglists` property.
```clojure
(defn func [a b c]) => #'user/func
(meta #'func) => 	{:arglists ([a b c]), 
					:line 1, ...}
```

A function's `Docstring` comment is conveniently saved in Var's metadata under which the function is defined. We can retrive it with the `:doc` keyword.
```clojure
(defn f "docstring" [a b]) => #'user/f
(:doc (meta #'f)) => "docstring"
```

Additionally we can add type hints for the Clojure compiler.
For example we can define that our function should always return `String` type with `^String` directive. Or implicitly set one argument to be a `Long` type. Let's check it in the REPL.
```clojure
(defn f ^String [^Long a b]) => #'user/f
(meta (first (:arglists (meta #'f)))) => {:tag java.lang.String}
(meta (first (first (:arglists (meta #'f))))) => {:tag Long}
```

It is important to mention that type hints are not a substitution for the static typing in Clojure. They are just hints for the compiler. In some cases, like usage of Java methods and classes or Clojure `records`, type hints help to optimize the code and to improve the application performance. It is done by minimizing the compiler lookup time for the methods in Java classes when we specify the class type in our code.

Hopefully now you have more knowledge about Clojure metadata and will use it efficiently in your day to day work.