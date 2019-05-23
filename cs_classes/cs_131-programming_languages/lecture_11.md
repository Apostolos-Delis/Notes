# Week 6 - Thursday 2019-05-16

#### Scheme Introduction

* Scheme is a functional programming language that is part of the LISP language family
* Pioneered many new concepts such as garbage collection, recursion, dynamic typing, and
    program code as a data structure
* Scheme is a dialect of lisp that was created by the MIT AI lab, and historically very
    popular in academia
* Hello world in scheme can be done with the following: create a helloworld.ss file:

    ```scheme
    #lang racket

    (display "Hello, world!\n")
    ```
* Then to run this file on the command line:

    ```scheme
    $ racket helloworld.ss
    "Hello, world!"
    ```

#### Basic Syntax

* **Comments**
    * Semi-colon `;` starts a line comment
    * Block comments are done with `#|` as the start, and `|#` as the end
* **Numbers**: similar to most languages:

    ```scheme
    1, 1/2, 3.14, 6.02e+23
    ```
* **Strings**: done with double quotes

    ```scheme
    "Hello, World!"
    ```
* **Booleans**: represented with `#t` and `#f`
* **Function Calls**
    * In scheme, the function name always comes first in function calls

    ```scheme
    > (display "Hello")
    Hello
    > (+ 1 2)
    3
    > (+ 1 2 (- 4 3))
    4
    > (/ (+ 1/3 1/6) 2)
    1/4
    ```
* **Definitions**: defining variables and functions have similar syntax:

    ```scheme
    (define pi 3.14)
    > pi
    3.14

    (define (print-name name)
            (display (string-append "Hello, " name)))
    > (print-name "Steve")
    Hello, Steve
    ```
* **Lambda Functions**: anonymous functions that can be defined with `(lambda (args*) expr)`

    ```scheme
    > (lambda (a b c) (+ a b c))
    #<procedure>

    > ((lambda (a b c) (+ a b c)) 1 2 3)
    6
    ```
* **Local Bindings**: Let keyword defines a new variable inside an expression

    ```scheme
    (define (say-hello)
        (let ([name "John"]
              [greeting "Hello, "])
            (display (string-append greeting name))))
    > (say-hello)
    Hello, John
    ```
* **Identifiers**
    * Scheme is very liberal with identifiers, and allows all the following:

        ```scheme
        +
        Hfuhruhurr
        integer?
        pass/fail
        john-jacob-jingleheimer-schmidt
        a-b-c+1-2-3
        ; The following are forbidden:  ) [ ] { } " , ' ` ; # | \
        ```
* **Checking Types**
    * Scheme provides functions to check types

    ```scheme
    > (number? 5)
    #t
    > (string? "My string")
    #t
    > (list? (list 1 2 3 4))
    #t
    > (pair? (cons 1 2))
    #t
    ```
* **Lists**
    * Scheme internally uses linked lists similar to Ocaml and Prolog
    * To access head you can use `(car my-list)` or `(first my-list)`
    * To access tail, use `(cdr my-list)` or `(rest my-list)`
    * Empty list represented by `'()`, can check with

        ```scheme
        (empty? '()) -> #t
        ```
    * Can define lists as follows:

        ```scheme
        > (define my-list (list 1 2 3))
        > (car my-list)
        1
        > (first my-list)
        1
        > (rest my-list)
        '(2 3)
        ```
    * Other list operations:

        ```scheme
        > (map (lambda (x) (+ x 1)) '(1 2 3))
        '(2 3 4)
        > (filter (lambda (x) (> x 2)) '(1 2 3 4))
        '(3 4)
        > (foldl (lambda (a b) (+ a b)) 0 '(1 2 3 4))
        10
        > (sort '(5 4 3 2 1) <)
        '(1 2 3 4 5)
        > (length ‘(1 2 3 4 5))
        5
        > (reverse ‘(1 2 3 4 5))
        '(5 4 3 2 1 )
        ```
