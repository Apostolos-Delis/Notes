# Week 2 - Tuesday 2019-04-09

#### Ocaml Pattern Matching

1. Patterns types

    | Type          | What they match |
    | ------------- |:---------------:|
    | 0, 19, []     | constants only match themselves |
    | $P_1$::$P_2$  | identifier matches anything but y is bound |

    * Examples of pattern matching

        ```ocaml
        match 3+9 in
        | x -> x+1

        match [3; -5; 12;] with
        | [] -> 0
        | [x] -> x+3
        | x::y -> x
        ```
    * Note that tuples in Ocaml must have a length known at compile time.
2. Recursion
    * Say that you want to sum up all the elements of a list
        \begin{equation}
        \sum_{i \in \{\}} a_i = 0
        \end{equation}
    * Why does it start of 0? This is because 0 is the identity element of min on positive nums
    * We expect this behavior to be consistent
        \begin{equation}
        \sum_{i \in A \cup B} a_i = \sum_{i \in A} a_i + \sum_{i \in B} a_i
        \end{equation}
    * Suppose now that B = {}, then we expect that
        \begin{equation}
        \sum_{i \in B} a_i = 0
        \end{equation}
    * Example of Recursion in Ocaml (reverse)

        ```ocaml
        # let rec reverse l = function
        | [] -> []
        | h::t -> (rev t) @ [h];;
        reverse : 'a list -> a' list = <fun>

        (*Alternatively:*)
        let rec revapp a = function
        | [] -> a
        | h::t -> revapp (h::a) t;;

        let reverse = revapp []
        ```
    * Note that in the previous example, the second method is preferred and follows functional
        programming as a paradigm better

