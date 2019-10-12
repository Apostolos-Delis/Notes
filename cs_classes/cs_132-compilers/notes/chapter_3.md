# Chapter 3 - LL Parsing

### Scanning vs Parsing

* Regular expressions are used to classify:
    * identifiers, numbers, and keywords
    * REs are more concise and simpler for tokens than grammar
    * Can lead to more efficient scanners (DFAs) than grammars
* Context-free grammars are used to count:
    * Brackets: `(), begin ... end, if ... else`
    * Imparting structure: expression
* **Derivations:** at each step we choose a non-terminal to replace.
    * This can lead to different derivations depending on the choice we make
    * Leftmost derivation - always replace the leftmost non-terminal
    * Rightmost derivation - always replace the rightmost non-terminal

### Precedence

* Leftmost and Rightmost derivations have no notion of precedence
* The way to add precedence to context-free grammars is to add more non-terminal terms in
    the grammar, that allow for lower precedence
* For example: if you want to add higher precedence to multiplying/dividing:

    ```grammar
    <goal> ::= <expr>

    <expr> ::= <expr> + <term>
    <expr> ::= <expr> - <term>
    <expr> ::= <term>

    <term> ::= <term> / <term>
    <term> ::= <term> * <term>
    <term> ::= <factor>

    <factor> ::= <num>
    <factor> ::= <id>
    ```
* For multiple layers of precedence, grammars must be broken down into more and more
    seperate layers

### Ambiguity

* If a grammar has more than one derivation, for a single sentential form, then it is
    ambiguous, such as:

    ```grammar
    <stmt> ::= if <expr> then <stmt>
            |  if <expr> then <stmt> else <stmt>
            | other stmts
    ```
    * Consider deriving the sentential form: If $E_1$ then if $E_2$ then $S_1$ else
        $S_2$
    * It has two derivations. This ambiguity is purely grammatical. It is a context-free 
        ambiguity
    * May be able to eliminate ambiguities by rearranging the grammar:

        ```grammar
        <stmt> ::= <matched>
                | <unmatched>
        <matched> ::= if <expr> then <matched> else <matched>
                | other stmts
        <unmatched> ::= if <expr> then <stmt>
                | if <expr> then <matched> else <unmatched>
        ```
* ambiguity is often due to confusion in the context-free specification.

### Top-down Parsing

* A top-down parser starts with the root of the parse tree,
    labelled with the start of the grammar.
    To build a parse, it repeats the following steps until the fringe of
    the parse tree matches the input string
    1. At a node $A$, select a production $A\rightarrow \alpha$ and construct the
       appropriate child for $\alpha$
    2. When a terminal is added to the fringe that doesn’t match
        the input string, backtrack
    3. Find the next node to be expanded
* Top-down parsers cannot handle left-recursion in a grammar Formally, a grammar is 
    left-recursive if: $\exists A \in V_n$ such that $A\Rightarrow^+ A\alpha$ for some
    $\alpha$
* To remove left-recursion, we can transform the grammar, for example consider:

    ```grammar
    <foo> ::= <foo> a
           |   b
    ```
    * where a and b do not start with `<foo>`
    * We can rewrite this as:

    ```grammar
    <foor> ::= b <bar>
    <bar> ::= a <bar>
            | empty
    ```

### Lookahead when Parsing Grammars

* We saw that top-down parsers may need to backtrack when they select the wrong 
    production
* Do we need arbitrary lookahead to parse CFGs? In general yes, but there are shortcuts
* Large subclasses of CFGs can be parsed with limited lookahead
* Most programming language constructs can be expressed in a grammar that falls in 
   these subclasses
* Interesting subclasses:
    * **LL(1)**: left to right scan, left-most derivation, 1-token lookahead
    * **LR(1)**: left to right scan, right-most derivation, 1-token lookahead
* Top down parsing:
    * start with terminals, keep expanding non-terminals
    * Will have to backtrack, and can have infiniteloops for left-recursion
* Bottom-up parsing:
    * Start with non-terminals, reach start-state

### Constructing a Parser

1. Construct a grammar
2. Remove left-recursion
    * 
3. Left

