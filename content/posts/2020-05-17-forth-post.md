---
title: "Currying"
date: 2020-08-20T14:42:56-04:00
tags: [Currying, Functional programming]
draft: false
---

**Currying** is an act of taking one function that receives more than one argument and refactoring it so it becomes **HOF**(a higher order function) that returns series of functions each accepting only one argument and only evaluating once we receive our final argument.

The act of currying can be described as taking a multiary function and turning it into series of unary functions. Currying allows you to take an existing function that has one shape and **specialize** it in producing a new function that is more adapted to your needs and has a shape that you want.  
Functional programmers want everything to be currying because they like all functions to be **unary**, single input -> single output. All functional libraries methods that you will encounter are automatically doing currying under the hood.

**Partial application** and **currying** are accomplishing the same thing but doing it in a different way. Partial application **presets** some arguments now, receives the rest on the next call. Currying doesn't preset any arguments and receives each argument one at a time.

There are some cases when it would be more preferable to do a partial application. Ex. a function expects 5 inputs and you want to produce another function with 3 inputs, so you want to preset 2 inputs. If you want to curry it, you would have to call it twice and the function you will receive in return would not be a function that expects  3 inputs, it would be a currying function of three more chains and it might not be the shape you want. In this case a partial application would be more preferable.