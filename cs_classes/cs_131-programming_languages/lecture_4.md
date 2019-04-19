# Week 2 - Thursday 2019-04-11


#### Syntax

* A "solved" problem with classic solutions and theory
* Form is independent of meaning
* English lets you construct a sentence that can have two different meanings,
    for example "time flies" which could be interpreted as a (N,V) or (V,N)
    combination.
* Ambiguity needs to be avoided in programming languages

#### What Makes Good Syntax?

1. **Inertia**: Syntax that people are used to so that they are familiar with it
    * This is why a lot of programming syntax comes from math - something
        people are familiar with
2. **Simplicity**: Syntax that is simple and allows users to read it and follow
    along intuitively
3. **Readability**: Just because syntax is simple doesn't mean is readable
    * For example, say you are trying to do `i = j*k;` in lisp, this would be
        `(set! i (* j k))`
4. **Writability**: The language should not take forever to write code in.
5. **Its Redundant**: This is actually useful in catching human errors
6. **Unambiguous**: Want the syntax to be clear in exactly what a piece of
    code is trying to accomplish

#### Overall Program Structure

* In Java/C: You have lots of .c files that are then linked together
* Smalltalk: A large set of classes.
* Bottom up construction: start with tokents then process them
    \begin{equation}
    \textrm{tokens} \rightarrow \textrm{char streams} \rightarrow
    \textrm{lexeme streams} \rightarrow \textrm{token steam}
    \end{equation}

#### Tokenization

1. Tokenization is expensive - costs several machine cycles per byte
2. Most of the program is non-token white space like `\r \n \v \t \b ...`
3. Keywords (special tokens) and reserved words (keywords that exclude
    identifiers with the same name)
    * An example in C:

        ```c
            #defined FOO && FOO > 1
            // defined here is a keyword but not reserved, can do this:
            #define defined 27
        ```
    * Note that tokenizers are greedy, you cannot for example is assume that
        just because you have while right now, that it is the reserved keyword,
        it could just be the start of a variable name like whilex
    * Tokenizers once they finish their task, they return a token streams

#### Grammars

* Defined as by Backus Naur Form (BNF)
* The grammar structure is defined as

    ```
    grammar = finiteset of nonterminal symbols
            + finite set of terminal symbols
            + finite set of rules which each
            have a nonterminal symbol that points to another symbol
            + a starting nonterminal symbol
    ```
* Then the language is defined as a set of sentences where sentences
    are finite sequences of terminal symbols that follow the structure
    defined by the grammar.

#### Ocaml Review

1. Lambda Functions
    * Anonymous functions that are useful forms of syntactic sugar

        ```ocaml
        # (fun a -> a + 1) 5;;
        -: int=6
        ```
2. Functions with Multiple Arguments
    * The function that takes in multiple arguments will be converted
        into a functions that each take one argument

        ```ocaml
        # let sum a b = a + b
        (* This gets converted internally in Ocaml to *)
        # let sum = (fun a -> (fun b -> a + b));;
        val sum : int -> int -> int = <fun>

        # Can have subfunctions that fullfil some arguments
        # let add_one = sum 1
        val add_one : int -> int = <fun>
        ```
3. Function types
    * Ocaml will try to determine the input and output types automatically

        ```ocaml
        # let sum a b = a +. b
        val sum : float -> float -> float = <fun>
        ```
    * Sometimes the exact type is not known

        ```ocaml
        # let rec my_map func my_list = match my_list with
        | [] -> []
        | head::tail = (func head)::(my_map tail)
        val my_map : ('a -> 'b) -> 'a list -> 'b list = <fun>

        # my_map (fun x -> x+1);;
        -: int list -> int list = <fun>
        ```
4. Mutual Recursion
    * Useful when you want to alternate recursing with two functions

        ```ocaml
        let rec is_even = function
        | x when x = 0 -> true
        | x -> is_odd (x-1)

        and is_odd = function
        | x when x = 0 -> false
        | x -> is_even (x-1);;

        # is_even 10;;
        -: bool = true
        # is_odd 8;;
        -: bool = false
        ```
5. Infix Functions
    * Defining your own infix operators makes you code cleaner
    * Example for string concatination

        ```ocaml
        let (+) a b = a ^ b;;

        # "abc" + "def";;
        -: string = "abcdef"
        ```
    * Warning: function definitions always override existing functions - there
        is no overloading in Ocaml
    * You can also use infix operators as regular functions

        ```ocaml
        # (+) 1 2;;
        -: int 3
        ```
6. Pattern Matching
    * There are three ways to do pattern matching:

        ```ocaml
        # let first x = match x with
        | (left,_) -> left;;

        # let first = function
        | (left,_) -> left;;

        # let first (left, _) -> left;;

        (* All these functions will return: *)
        # first (1,2);;
        -: int = 1
        ```
7. Ocaml Types
    * Built in types include `int, float, char, string, bool, unit, tuple, list, function, ...`
    * Variant types: our types can be constructed using multiple types

        ```ocaml
        type ('nonterminal, 'terminal) symbol =
        | N of 'nonterminal
        | T of 'terminal;;

        type awksub_nonterminals = Expr | Lvalue | Incrop | Binop | Num;;

        # [T"("; N Expr; T")"]
        -: (awksub_nonterminals, string) symbol list = [T "("; N Expr; T ")"]
        ```
    * Option types
        * Sometimes functions cant come up with return values (ex: divide by 0 error)
        * Can come up with an alternative with the option type:

            ```ocaml
            let divide a b = match a,b with
            | x,y when y = 0.0 -> None
            | x,y -> Some ( x /. y );;

            # divide 1.0 0.0
            -: float option = None
            # divide 1.0 2.0;;
            -: float option = Some 0.5
            ```
        * Downside is you have to handle this in your following functions

            ```ocaml
            let print_division a b = match (divide a b) with
            | Some x -> print_float x
            | None -> print_string "Undefined";;
            ```
    * Recursive types - can use types to define recursive data structures like trees

        ```ocaml
        # type tree =
        | Leaf of int
        | Node of tree *tree;;

        # let my_tree = Node (Node (Leaf 1, Leaf 2), Node (Leaf 3, Leaf 4))

        # let rec first_leaf = function
        | Leaf x -> x
        | Node (left, right) -> first_leaf left;;

        #first_leaf my_tree;;
        -: int = 1
        ```
8. More On the List Module
    * List.fold_left
        * useful when you want to summarize a list to one value, (this method is called
            reduce in some other languages)

            ```ocaml
            # let sum my_list = List.fold_left (fun acc x _> acc + x) 0 my_list;;
            # sum [1; 2; 3];;
            -: int = 6
            ```
    * List.flatten
        * Function that removes all dimensions in the list

            ```ocaml
            # List.flatten [["a"; "b"]; ["c"; "d"]; ["e"; "f"]];;
            -: string list = ["a"; "b"; "c"; "d"; "e"; "f"]
            ```
    * List.Mapi
        * mapi is similar to map, except you can use the index of the element also

            ```ocaml
            # List.mapi (fun i x -> (i, x)) ["a"; "b"; "c"; "d"; "e"];;
            -: (int * string) list = [(0, "a"); (1, "b"); (2, "c"); (3, "d"); (4, "e")]
            ```
    * List.combine
        * Combines elements from two lists into a list of tuples (known as zip in python)

            ```ocaml
            # let grades = [70; 80; 90];;
            # let students = ["a", "b", "c"];;
            # let new_list = List.combine grades students
            val new_list: (int * string) list = [(70, "a"), (80, "b"), (90, "c")]
            ```
    * List.Split
        * Does the opposite of combine

            ```ocaml
            # List.split [(70, "a"), (80, "b"), (90, "c")]
            -: int list * string list = ([70; 80; 90], ["a", "b", "c"])
            ```
