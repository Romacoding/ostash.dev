---
title: "Arity"
date: 2020-07-08T14:42:56-04:00
tags: [Parameter, Argument, Functional programming, Arity]
draft: false
---

Before we explain what arity is, let’s make a clear distinction between parameters and arguments in a function.

The **parameter** is a variable declared in the function expression. It is a placeholder that is used to access function input (argument). The **argument**, on the other hand is the actual value passed to a function when the function is called. We can say that function defines parameters and it takes arguments.

**Arity** describes how many arguments the function receives.
The more inputs (parameters) the function has, the harder it is for the function to work with other functions.

Unary function takes one input and returns one output, binary  - two inputs, ternary – three, quaternary – four and so forth (multiary - more than two inputs). Functional programmers tent to prefer **unary functions** – single input, single output.