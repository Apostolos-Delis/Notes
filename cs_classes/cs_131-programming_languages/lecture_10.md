# Week 6 - Prolog - Thursday 2019-05-09

#### Prolog

* Prolog has no traditional functions - it has predicates, which can be thought ofas just
    functions that return a boolean. This is the hallmark of a logic programming language
* In Prolog, you declare what a correct solution looks like and Prolog searches for it.
    This makes Prolog adeclarative programming language
* Prolog attempts to separate an Algorithm into logic and control, where logic dictates
    correctness and control dictates efficiency. The programmer writes logic, and the
    Prolog compiler figures out how to achieve that. Contrast this with Java, where the
    two are combined.

#### Example: Sorting

* When atempting to create `sort`, first define what it means for something to  be
    sorted:

    ```Prolog
    sort(L, S) :- sorted(S), perm(L, S).
    ```
* So Here, `S` is a sorted version of `L` if `S` is sorted and `L` and `S` are
    permutations
* In a way, Prolog "if" is reversed compared to other programming languages; the
    predicate for "if" comes after the implication

    ```Prolog
    sorted([]). /* empty list is sorted */
    sorted([_]). /* singleton list is sorted */
    sorted([X, Y | L]) :- X =< Y, sorted([Y, L]). /* x,y | L means x :: y :: L in OCaml */
    ```
* Now define `perm`

    ```Prolog
    perm([], []).
    perm([X], [X]).
    perm([X | L], R) :-
    append(P1, [X | P2], R),
    append(P1, P2, P),
    perm(L, P)
    ```
* Now define `append`

    ```Prolog
    append([], L, L).
    append([X | L], M, [X | LM]) :- append(L, M, LM).
    ```
* **Executing code**: To run the code, Prolog has a prompt:

    ```Prolog
    | ?- sort([3, -5, 7], R).
    R = [-5, 3, 7].
    ```
* **Reordering predicates**: However, Prolog has a determistic behavior, going from left
    to right. Consider `sort(L, S)`; Prolog first considers `sorted(S)`, trying all
    possible `S` (starting with the empty list) so that it fits `perm(L, S)`
* A way to fix that is to just reverse the order of the predicates, note that only
    changed the control - not logic, as the "and" operation is commutative

    ```Prolog
    sort(L, S) :- perm(L, S), sorted(S).
    ```
* Note, this algorithm first looks at all possible permutations of `L`, making this
    algorithm  $\mathcal{O}(n!)$ time
* Prolog is used to see if an algorithm is logically correct; if proven to be so then
    people would rewrite the code in someother language

#### Second example: list member

* Consider the following clauses

    ```Prolog
    member(X, [X | _])
    member(X, [_ | L]) :- member(X, L).

    | ?- member(Q, [5, 3, 1]).
    Q = 5 ?
    ```
* Here, we have the option of letting Prolog give us a new valid answer (;), or stop
    (ret), if we input `;`, thenm we get

    ```Prolog
    | ?- member(Q, [5, 3, 1]).
    Q = 5 ? ;
    Q = 3 ? ;
    Q = 1
    yes
    ```
* This feature could be described as automatic backtracking, what happens if we try a
    list containing 9?

    ```Prolog
    | ?- member(9, L).
    L = [9|_] ? ;/* derived directly from the first rule */
    L = [_,9|_] ? ;/* derived first from the second rule *
    L = [_,_,9|_] ? ;
    L = [_,_,_,9|_] ?
    ...
    ```
* This program enters an infinite loop
* What if we try to find a three-element list that contains 9?3

    ```Prolog
    | ?- member(9, [A, B, C]).
    A = 9 ? ;
    B = 9 ? ;
    C = 9 ? ;
    ```
* If we reject all of the possible predicates, the interpreter returns no. Now consider
    how `append` works

    ```Prolog
    | ?- append([3, 1], [9], R).
        X1 = 3, L1 = [1], M1 = [9], R1 = [3, LM1]
            append([1], [9], LM1)
                X2 = 1, L2 = [], M2 = [9], R2 = [1| LM2] = LM1
                    append([], [9], LM2)
                        L3 = [9] = LM2
    R = [3, 1, 9]
    ```
* We could also run append backwards:

    ```Prolog
    | ?- append(L, M, [3, 1, 9])
    L = [], M = [3, 1, 9] /* due to first rule */? ;
        [3 | L1] = L, M1 = M, [X1 | LM1] = [3, 1, 9] => X1 = 3, LM1 = [1, 9]
            append(L1, M1, [1, 9])
                L1 = [], M1 = [1, 9] /* due to first rule */
    L = [3], M = [1, 9] ? ...
    ```

#### Prolog Arithmetic

* Consider the following expression: 

    ```Prolog
    | ?- X = 2+3.
    X = 2+3
    ```
* The `+` constructs a tree data structure, with a root node as `+`, to actually do
    arithmetic in Prolog, use the `is` primitive

    ```Prolog
    | ?- X is 2+3.
    X = 5

    /* This is also equivalent */
    | ?- is(X, 2+3).
    ```
* 
