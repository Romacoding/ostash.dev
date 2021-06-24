---
title: "Data notation in Clojure"
date: 2021-06-23T00:06:16-09:00
tags: [EDN, Extensible data notation, Clojure, Functional programming]
draft: false
---

Homoiconisity (code as a data structure) is one of the pillars of the Clojure language. It gives the extensibility and flexibility to the language.  For example we have a power to easily extend Clojure language by creating macros since in this process we can use all available functions for the data structures. 

Furthermore, we can use Clojure syntax notation for the data serialization/de-serialization, data transfers between the server and a client (Clojure => ClojureScript). A set of rules (a standard) of organizing the data is called EDN - extensible data notation.  A JSON format similarly was created with the syntax of the JavaScript language.

In order to serialize a data structure we would use a `pr-str` function. As the result we have a string that can be written in the file for example. 
```clojure
(pr-str {:id 1}) => "{:id 1}" 
```

For a conversion back to the data structure, in this example a map, we pass serialized string to a `read-string` function.
```clojure
(read-string "{:id 1}") => {:id 1}
```

When you work with EDN files using functions `spit` (to write to a file) and `slurp` (to read from the file) it is very convenient. Since `slurp` returns a string, we have to use `read-string` again to de-serialize data to the Clojure map.
```clojure
(spit "sample.edn" {:id 1}) => nil
(read-string (slurp "sample.edn")) => {:id 1}
```

For security reasons and as a good practice it is better to use the `read-string` function from the `clojure.edn` namespace. It only parses lists, maps and vectors and protects from accidental execution of a malicious code when working with EDN files from an "unsafe" source (clojure.core/read-string can execute code and should be used only with trusted sources).
```clojure
(require 'clojure.edn) => nil
(clojure.edn/read-string (slurp "sample.edn")) => {:id 1}
```

As it was mentioned before, one of the stronger sides of EDN is its extensibility. By using tagged elements (literals) we can serialize for a transfer some nontrivial values like a date for example. `#inst` (instant) tagged literal has a format `#inst "yyyy-mm-ddThh:mm:ss.fff+hh:mm"`. A Clojure reader will parse it to `java.util.Date` class.
```clojure
;;Clojure
(let [time #inst "2021-06-21"]
	(class time)) => java.util.Date
```
Why this is useful? For example, when we have ClojureScript on the front end and Clojure on the back end, they can easily exchange dates written in EDN format. On the server, the date record will be interpreted as the `java.util.Date` class and on the client as a `js/Date.` object.
```clojure
;;ClojureScript
(let [time #inst "2021-06-21"]
	(type time)) => #object[Date]
```
`#inst` and `#uuid` tags are already included in the EDN since Clojure version 1.4. A #uuid (universally unique identifier) tagged literal will be parsed into a java.util.UUID.
```clojure
(let [uuid #uuid "f81d4fae-7dec-11d0-a765-00a0c91e6bf6"]
	(class uuid)) => java.util.UUID
```

Tagged literals may not be something you need for your project, but when dealing with some unique value types it is extremely useful.