# Week 1 - Thursday 2019-04-04

#### Language Design Choices

1. **Orthogonality**
    * The choice of X does not affect Y
2. **Efficiency**
    * How fast is it?
    * How  much RAM does it use?
    * Power/Energy
    * Network throughput/bandwidth/latency
3. **Simplicity**
    * Easy to learn
    * Easy to use with intuitive syntax
    * Good and simple documentation
4. **Convenience**
    * Simple to use 
    * Potentially having lots of users is also a convenience
5. **Safety**
    * Static vs Dynamic
       * Static type checking is typically safer to use 
    * Type checking
6. **Abstraction**
    * Can your programs scale cleanly?
    * Use of classes is an attempt at abstraction
7. **Exceptions**
    * Proper exception handling
    * Proper indication of errors 
8. **Concurrency**
    * Must have proper support for parallel programmng 
    * Cannot just be a third party library that adds this functionality 
9. **Mutability/Extensibility**
    * Must have support for change in the future
    * Example of C with its macros that allow changes to the language without changing the compiler.


**Types of Languages**

| Imperative    | Functional    | Logic         |
| ------------- |:-------------:|:-------------:|
| C++, C, Java  | Lisp, ML, F#  | Prolog        |

1. Imperative Languages are composed of statements
    * Statements are listed in order: `S_1; S_2; S_3;`
2. Functional Languages are composed of functions
    * Functions are linked like:
        \begin{equation}
        y = F_1(F_2(x), F_3(x))
        \end{equation}
    * Functional languages <u>lack</u> I/O 
3. Logic Languages are composed of predicates
    * Predicates are linked together through the use of predicates:
        `(P_1 & P_2) | P_3` 

#### Functional Programming

1. Clarity: use notations from mathematics 
2. Parallelizability: performance is good, code structure incentivizes you to write parallel code.

* Function: mapping from a domain to a range
* Functional Form: function where either domain or the range is a function type 
    * Example of higher order function in functional form:

        \begin{equation}
        \int_{1}^{100} f(x) dx
        \end{equation}
    * Example in C++:

        ```cpp
        y = integral(f, 1, 100);
        ```

#### Ocaml Introduction

1. Static type checking is supported
    * Similar to Java in that all objects have a type 
2. Type Inference
    * type checking is done at compile time, types for variables 
        do not have to be declared
3. Automatic Storage Management
    * Garbage Collection is assumed inside of Ocaml system 
4. Good Support for Higher-Order Functions 
    * Really easy to implement these features (This led to creation of lambda expressions 


