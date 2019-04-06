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

**Ocaml Basics**

* Variables

    ```ocaml
    # let my_value = 5;;
    val my_value = 5
    ```
* List

    ```ocaml
    # let numbers = [ 1; 2; 3; 4; 5 ]
    ```
    * All elements must have the same type
    * Under the hood, lists are immutable singly-linked lists
    * List Operations: 
        * Adding new elements is right-associative

            ```ocaml
            0::[1;2;3] -> [0;1;2;3]
            ```
        * Cannod append to the end
    * Lists are immutable
* Functions
    * Functions in Ocaml can be relatively similar to other languages

        ```ocaml
        # let average a b = 
            (a + b) / 2;;;
        val average : int -> int -> int = <fun>

        (* float version *)
        # let average a b = 
            (a +. b) /2.0;;
        val average : float -> float -> float = <fun>
        ```

    * `let` binds a function with parameters a and b to name average
* Recursive Functions
    * Recursive functions must be specified explicitly (`let rec`), otherwise the 
        compiler will give an error about an undefined function
    
        ```ocaml
        # let rec factorial a = 
            if a = 1 then 1 else a * factorial (a-1);;
        val factorial : int -> int = <fun> 
        ```
* Defining Local Variables in Functions
    * add keyword `in` after the let statement to make the value available in the following 
        statement

        ```ocaml
        # let average a b = 
            let sum = a +.b in 
            sum /. 2.0;;
        val average : float -> float -> float = <fun>
        ```
* Useful List Operations - map
    * Map transforms a list by applying a function on each element 

        ```ocaml
        # List.map(fun x -> x*x) [1; 2; 3; 4; 5];;
        - : int list = [1; 4; 9; 16; 25]
        ```

* Useful list operations - rev 
    * rev reverses the list: 

        ```ocaml
        # List.rev [1; 2; 3; 4] 
        - : int list = [4; 3; 2; 1]
        ```

* Useful list operations - filter 
    * filter filters elements of the list that do not fit a certain critera

        ```ocaml
        # List.filter(fun x -> x = 2) [1; 2; 3; 4] 
        - : int list = [2]
        ```

* Useful list operations - for_all
    * Returns true if all elements match the condition

        ```ocaml
        # List.for_all(fun x -> x = 2) [1; 2; 3; 4; 5];;
        - : bool = false
        # List.for_all(fun x -> x = 2) [2; 2; 2];;
        - : bool = true
        ```

* Useful list operations - exists
    * Exists checks if any elemnt in the list matches a condition 

        ```ocaml
        # List.exists(fun x -> x = 3) [1; 2; 3; 4; 5];;
        - : bool = true
        # List.exists(fun x -> x = 6) [1; 2; 3; 4; 5];;
        - : bool = false
        ```

* Pattern Matching 
    * More powerful version of the switch statement used in some other languages
    * Pattern matching allows you to list all the different cases in a clean way

        ```ocaml
        # let is_zero x = 
            0 -> true 
          | _ -> false;;
        ```
    * Patterns can also include conditions using when statement
        
        ```ocaml
        # let rec factorial a = match a with 
            x when x < 2 -> 1
          | x -> x * factorial (x-1);;
        ```

* Data Types - Native Types 
    * More native types: 
        - `list([1; 2; 3; 4; 5], ["foo"; "bar"])`
            * Elements are tuples
* Data Types - Our Own Data Types 
    * The most simple use case is to wrap an existing type 
    
        ```ocaml
        type age = int;;
        type name = string;;
        type person = age * name;;
        
        let print_name(p: person) = match p with
        | (p_age, p_name) -> print_string p_name;;

        let my_person = (111, "Bilbo": person);;
        print_name my_person;;
        ```

* Data types - Variants
    * Used when there are multiple subtypes of one main type

        ```ocaml
        type ccle_user = 
            Student of string 
            | TA of string 
            | Professor of string;;

        type my_type = 
        | A of string 
        | B of int;;

        let my_print x = match x with 
        | A a -> print_string a 
        | B b -> print_int b;;

        my_print(A "some string");;
        "some string"
        my_print(B 5);;
        5
        ``` 

