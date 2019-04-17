# Week 3 - Monday 2019-04-16

#### Grammars Continued

1. ISO EBNF Standard:

    | Item          | Specification                 |
    | ------------- |:-----------------------------:|
    | syntax        | syntax rule, {syntax rule}    |
    | syntax rule   | meta id, '=', defns list, ';' |
    | defns list    | defn, {'|', defn}             |
    | defn          |  term, {',', terms}           |
    | term          | factor ['-', exception]       |
    | exception     | factor                        |

2. Where the following syntax means::
    * [option]
    * {repetition}
    * (grouping)
    * (* comment *)

3. What can go wrong with grammars?
    * Nonterminal used but not defined
    * Nonterminals defined but not used
    * useless rules
    * extra constraints not easily formulated in BNF/EBNF
