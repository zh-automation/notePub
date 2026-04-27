---
title: Datatype
draft: false
tags:
  - PLCBasics
  - Terminology
---
## Datatype
This is a descriptor for how a value is organized and manipulated in a programming language. For instance, if you want a variable that can handle only a true or false value, you would use a [[Boolean]] datatype. 
A datatype is, often, a class within the programming language designed to handle specific types of data based on the size and how it is interpreted. So, in C an int datatype is a whole number of *at least* 16-bits in value (-32768 to 32767) while a float is a single precision 32-bit floating point value of ± 3.4 x 10³⁸. It's also important that in many programming languages, a datatype also holds the methods by which that data is identified, manipulated and set. For instance, a bool datatype in python is defined as [this](https://github.com/python/cpython/blob/main/Objects/boolobject.c).