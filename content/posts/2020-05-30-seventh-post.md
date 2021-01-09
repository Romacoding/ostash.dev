---
title: "Immutability"
date: 2020-11-10T14:42:56-04:00
tags: [Immutability, Mutability, Functional programming]
draft: false
---

**Mutable** data structure can be changed after creation **immutable** cannot.  
Paradoxically, when you need to mutate a data structure, is a time then you should use an immutable data structure because it allows a structured mutation. Immutable data structure creates a controlled mechanism for the mutation to occur. **Immutability** means, that you cannot change a data structure, you can only create a new data structure with changes applied to it.  
How that possibly be performant to create a whole new copy of the array or object every singe time we need to make changes? The immutable data structures are designed with the optimization in mind to mitigate that cost (a garbage collection and CPU load). When we use immutable data structures, the process of changing a property of the structure (mutation) is not to creature a whole new copy of that structure but to make a new structure with a new information in it and internally have a pointer to the previous one. It is similar to the git data structure: every time you commit something you just storing a difference compering to what it was before.  
We don’t have immutable data structures at the present time in JavaScript. There are libraries that provide us with them like Immutable.js.