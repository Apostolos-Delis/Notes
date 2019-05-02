# Midterm Review

### Chapter 1 - Programming Languages

* Programming Languages are grouped into four families
    1. Imperative
    2. Functional
    3. Logic
    4. Object-Oriented
* Hallmarks of imperative languages
    * Assignments
    * Iteration
    * Order of execution is critical
* Hallmarks of functional languages
    * Single-valued variables
    * Heavy use of recursion
* Hallmarks of logic languages
    * Program expressed as rules in formal logic
* Hallmarks of object-oriented languages
    * Usually imperative
    * Constructs to help programmers use "objects" to do things
* Language Standards
    * Documents that define the language standards
    * Can be a slow and rancorous process

### Chapter 2 - Defining Program Syntax

* Syntax and Semantics
    * Programming language syntax: how programs look, their form and structure, defined
        by a formal grammar
    * Programming language semantics: what programs do, their behavior and meaning
* How the Grammar Works
    * The grammar is a set of rules that say how to build a parse tree
    * You put \<S\> at the root of the tree
    * The grammar’s rules say how children can be added at any point in the tree
* A Parse Tree of the Gramar:

    ```language
    <S> ::= <NP> <V> <NP>
    <NP> ::= <A> <N>
    <V> ::= loves | hates|eats
    <A> ::= a | the
    <N> ::= dog | cat| rat
    ```

    ![Example English Parse Tree](images/parse_tree.png){ width=30% }

* Programming Language Grammar
    * Here is an example expesssion:

        ```language
        <exp> ::= <exp> + <exp> | <exp> * <exp> | ( <exp> )
                            | a | b | c
        ```
    * An expression can be one of the variables a, b, or c or it can be a two expressions
        along with an operator
    * The parse tree for this looks like:

        ![Parse tree for ((a+b)*c)](images/expr_tree.png){ width=30% }

* BNF Grammar Definition (4 parts)
    * Set of tokens
        * Tokens are smallest unit of syntax and are atomic - not treated as a
            composition of smaller pieces
    * Set of non-terminal symbols
        * They are strings enclosed in angle brackets, as in <NP>
        * They are not strings that occur literally in program text
        * Grammar specifies how these can be expanded out
    * Start symbol
        * the non-terminal that forms the root of any parse tree for the grammar
    * Set of productions
        * the tree-building rules
        * Each one has a non-terminal, the separator ::=, and a sequence of things which
            can be a tokens or a non-terminals
        * A production gives one possibility for building a parse tree, with each
            non-terminal having things to put as its children
        * Also possible in BNF grammar to seperate the possible right hand sides with |
* Empty
    * The special nonterminal <empty> is for places where you want the grammar to generate
        nothing
    * For example, this grammar defines a typical if-then construct with an optional else
        part

        ```language
        <if-stmt> ::= if <expr> then <stmt> <else-part>
        <else-part> ::= else <stmt> | <empty>
        ```
* Parse Trees
    * To build a parse tree, put the start symbol at the root
    * Add children to every non-terminal, following any one of the productions for that
        non-terminal in the grammar, finish when leaves are tokens
* Language Definition
    * The language defined by a grammar is the set of all strings that can be derived
        by some parse tree for the grammar
* Lexical Structure And Phrase Structure
    * Grammars so far have defined phrase structure: how a program is built from a
        sequence of tokens
    * We also need to define lexical structure: how a text file is divided into tokens
    * Usually this is dones with two seperate grammars - one on how to construct a
        sequence of tokens and one says how to construct the parse tree
* Seperate Compiler Passes
    * The scanner the input file and divides it into tokens according to the first
        grammar, discarding whitespace/comments
    * The parser then constructs the parse tree from the second grammar
* EBNF Variations
    * EBNF Adds Additional syntax to simplify some grammar chores
        - {x} to mean zero or more repetitions of x
        - `[x]` to mean x is optional (i.e. x | `<empty>`)
        - () for grouping
        - | anywhere to mean a choice among alternatives
        - Quotes around tokens, if necessary, to distinguish from all these meta-symbols
* EBNF Examples

    ```language
    <if-stmt> ::= if <expr> then <stmt> [else <stmt>]
    <stmt-list> ::= {<stmt> ;}
    <thing-list> ::= { (<stmt> | <declaration>) ;}
    <mystery1> ::= a[1]
    <mystery2> ::= ‘a[1]’

    ```
* Syntax Diagrams
    * Start with an EBNF grammar, a simple production is just a chain of boxes
    * For the grammar: `<if-stmt> ::= if <expr> then <stmt> else <stmt>`

        ![](images/simple_syntax_diagram.png){ width=50% }

    * Can also have Bypasses which are square-bracket pieces from the EBNF get
        paths that bypass them
    * So if you let the else be optional:
        `<if-stmt> ::= if <expr> then <stmt> [else <stmt>]` then

        ![](images/bypass.png){ width=50% }

    * Can also use branching for multiple productions, so for a grammar like

        ```language
        <exp> ::= <exp> + <exp> | <exp> * <exp> | ( <exp> )
                    | a | b | c
        ```
    * You can generate a parse tree:

        ![](images/branching.png){ width=50% }
    * Can also use Loops for EBNF curly brackets, like in the grammar

        ```grammar
        <exp> ::= <addend> {+ <addend>}
        ```
        ![](images/loops.png){ width=30% }
    * Syntax Diagram Pros and Cons
        * Easier to read casually, but harder precisely
        * Harder to make machine readable

### Chapter 3 - Where Syntax Meets Semantics

* Here are Three equivalent grammars

    ```
    <subexp> ::= a | b | c | <subexp> - <subexp>

    <subexp> ::= <var> - <subexp> | <var>
    <var> ::= a | b | c

    <subexp> ::= <subexp> - <var> | <var>
    <var> ::= a | b | c
    ```
* These grammars all define the same language but have different syntax trees

    ![](images/syntax_trees.png){ width=25% }
* We want the structure of the parse tree to correspond to the semantics
    of the string it generates
* Precedence of Operators
    * Each operator has a precedence level, and those with higher precedence are
        performed first
* Precedence In Grammar
    * To fix the precedence problem, modify the grammar so it is forced to put *
        below + in the parse tree, so transform grammar as follows:

        ```grammar
        <exp> ::= <exp> + <exp>
                    | <exp> * <exp>
                    | (<exp>)
                    | a | b | c

        To:
        <exp> ::= <exp> + <exp> | <mulexp>
        <mulexp> ::= <mulexp> * <mulexp>
                        | (<exp>)
                        | a | b | c
        ```
    * Now our grammar will not generate parse trees with bad precedence

        ![desc](images/precedence_tree.png){ width=30% }

* Operator Associativity
    * Left-associative operators group left to right: `a+b+c+d = ((a+b)+c)+d`
    * Right-associative operators group right to left: `a+b+c+d = a+(b+(c+d)`
    * Most operators in languages are left-associative
* Associativity in Grammar
    * To fix the associativity problem, we modify the grammar to make trees of `+s`
        grow down to the left (and likewise for `*s`)
    * Change the previous grammar to:

        ```
        <exp> ::= <exp> + <exp> | <mulexp>
        <mulexp> ::= <mulexp> * <rootexp> | <rootexp>
        <rootexp> ::= (<exp>)
                      | a | b | c
        ```
    * So the syntax tree is now:

        ![](images/associativity.png){ width=20% }

* EBNF and Parse Trees
    * We have that {x} means "zero or more repetitions of x" in EBNF
    * So `<exp> ::= <mulexp> {+ <mulexp>}` should mean a `<mulexp>` followed by
        zero or more repetitions of `+ <mulexp>`
    * But what is that associativity?
    * One approach to this is to use {} whenever possible
    * Another approach is to use the form for left associative operators:
        `<exp> ::= <mulexp> {+ <mulexp>}`, or use explicitly recursive rules for these
        unconventional situations: `<expa> ::= <expb> [ = <expa> ]`
* Full-Size Grammars
    * Any realistic language has many non-terminals
    * Extra non-terminals guide construction of unique parse tree
    * Once parse tree is found, such non-terminals are no longer of interest
* Abstract Syntax Tree
    * Language systems usually store an abreviated version of the parse tree, the AST
    * Usually, there is a node for every operation, with a subtree for every operand

        ![](images/ast_diagram.png){ width=50% }

* Parsing Revisited
    * When a language system parses a program, it goes through all the steps necessary to
        find the parse tree
    * But it usually does not construct an explicit representation of the parse tree in
        memory
    * Most systems construct an AST instead
* Grammars define not just a set of legal programs, but a parse tree for each program

### Chapter 4 - Language Systems

* Compiling
    * Compiler translates to assembly language (machine specific)
    * Each line represents either a piece of data, or a single machine-level instruction
* Assembling
    * Assembly language is still not directly executable, still in text format
    * Assembler converts each assembly-language instruction into the machine’s binary
        format that is machine language
* Linking
    * Object file still not directly executable - missing parts and has some names,
        mostly machine language at this point
    * Linker collects and combines all the different parts of the program

        ![](images/linker.png){ width=30% }
* Loading
    * Executable file still has some names even through it is mostly machine language,
        the final step is now when the program is run, the loader loads it into memory
        and replaces names with addresses
    * Loader finds an address for every piece and replaces names with addresses
* Running
    * After loading, the program is entirely in machine language, where all names have
        been replaced with memory addresses

    ![The Classical Sequence](images/full_compile.png){ width=50% }

* Optimization
    * Code generated by a compiler is usually optimized to make it faster, smaller, or both
    * Other optimizations may be done by the assembler, linker, and/or loader
    * Compilers can remove variables, remove code, add code, move code around, etc.
* Variation: Interpreters
    * To interpret a program is to carry out the steps it specifies, without first
        translating into a lower-level language
    * Interpreted languages like python are usually much slower, since compiling takes
        time up front, but fast at runtime
* Virtual Machines
    * A language system can produce code in a machine language for which there is no
        hardware: an intermediate code
    * Language system may do the whole classical sequence, but then interpret the
        resulting intermediate-code program
    * This allows for cross platform execution and heightened security
* Delayed linking
    * Delay linking step
    * Code for library functions is not included in the executable file of the calling program
    * In windows functions for delayed linking are stored in .dll files
        * Load-time dynamic linking - Loader finds .dll files and links the program to
            functions it needs, just before running
        * Run-time linking - Running program makes explicit system calls to find .dll files
            and load specific functions
    * In Unix, Libraries of functions for delayed linking are stored in .so files: shared
        object
        * Shared libraries - Loader links the program to functions it needs before running
        * Dynamically loaded libraries - Running program makes explicit system calls to
            find library files and load specific functions
    * In Java, the JVM automatically loads and links classes when a program uses them
    * The Advantages of Delayed Linking
        * Multiple programs can share one copy of library functions
        * Library functions can be updated independently of programs
        * Can avoid loading unused code
* Profiling
    * The classical sequence runs twice
    * First run of the program collects statistics: parts most frequently executed
    * Second compilation uses this information to help generate better code
* Dynamic Compilation
    * Some compiling takes place after the program starts running
    * Many variation like compile each function only when called, or roughly compile then
        spend more time on frequently used pieces of code
* Binding
    * Binding means associating two things—especially, associating some property with an
        identifier from the program
    * Ex: What set of values is associated with int?
    * Different bindings take place at different times
    * Language Definition Time
        * Some properties are bound when the language is defined like meanings of
            keywords like `void`
    * Language Implementation Time
        * Some properties are bound when the language system is written
        * Range of values of type int in C or implementation limitations like max
            identifier, max number of array dimensions
    * Compile Time
        * Some properties are bound when the program is compiled or prepared for
            interpretation
        * Types of variables, in languages like C and ML that use static typing
        * Declaration that goes with a given use of a variable, in languages that use
            static scoping
    * Link Time
        * Some properties are bound when separately-compiled program parts are combined
            into one executable file by the linker
        * Object code for external function names
    * Load Time
        * Some properties are bound when the program is loaded into the computer’s
            memory, but before it runs
        * Memory locations for code for functions and static variables
    * Run Time
        * Some properties are bound only when the code in question is executed:
        * Values of variables or types of variables in languages like lisp
* Runtime Support
    * Additional code the linker includes even if the program does not refer to it
        explicitly
    * Includes things like Exception handling, memory management, allocating memory, and
        an OS interface

### Chapter 5 - A First Look at ML

* Left Associative precesdence is  `{+,-} < {*,/,div,mod} < {~}`.
* String concatination: ^ operator
* Ordering comparisons: `<, >, <=, >=`, apply to string, char, int and real
* Boolean operators: andalso, orelse, not.

    ```ocaml
    - true orelse 1 div 0 = 0;
    val it = true : bool
    ```
* andalso and orelse are short-circuiting operators: if the first operand of orelse is true,
    the second is not evaluated and likewise for andalso

* Function Associativity is left-associative, so So `f a b` means `(f a) b`
* Tuples
    * ML gives the type of a tuple using * as a type constructor
    * For example, int * bool is the type of pairs (x,y) where x is an int and y is a
        bool
* Lists
    * All elements of the list must be of the same type
    * The @ operator concatenates lists
    * The :: operator is right-associative
    * The explode function converts a string to a list of characters, and the implode
        function does the reverse
* Functions
    * Function Definition syntax:

        ```grammar
        <fun-def> ::=  fun <function-name><parameter> = <expression> ;
        ```
    * ML gives the type of functions using -> as a type constructor
    * All ML functions take exactly one parameter To pass more than one thing, you can pass
        a tuple
* Combining Constructors
    * When combining constructors, list has higher precedence than *, and -> has lower
        precedence
    * Here is an example:

    ```ocaml
    (* These two are the same *)
    - int * bool list
    - int * (bool list)

    (* These two are the same *)
    - int * bool list -> real
    - (int * (bool list)) -> real
    ```
### Chapter 6 - Types

* A type is a set of values plus a low-level representation plus a collection of
    operations that can be applied to those values
* Primitive vs Contructed Types
    * Any type that a program can use but cannot define for itself is a primitive
        type in the language
    * Some primitive types in ML: `int, real, char`
* Primitive Types
    * Some languages define the primitive types more strictly than others
    * Java for example defines them exactly, whereas languages like C and ML leave a
        little wiggle room
* Constructed Types
    * Additional types defined using the language, like uples, arrays, strings, lists,
        unions, subtypes, and function types
    * Enum types are supported by many languages, where you can explicitly state all the
        different potential values
    * A common representation is to treat the values of an enumeration as small integers
    * Can also make sets by tupling and combining two different types of values
    * C lets you do this with structs while ML and other languages have explicit tuples
    * Many languages also support union types like in C:

        ```c
        union element {
            int i;
            float f;
        };
        ```
    * Or like in ML:

        ```ocaml
        datatype element =
            I of int |
            F of real;
        ```
* Strictly Typed Unions
    * In ML all you can do with a union is extract the contents and state what to do with
        each type of value in the union
* Making Subtypes
    * Some Languages support subtypes with more or less generality
    * Example of Lisp Types with predicates

        ```lisp
        (declare (type integer x))
        (declare (type (or null cons) x))
        (declare (type (and number (not integer)) x))
        (declare (type (and integer (satisfies evenp)) x))
        ```
* Representing Subtype Values
    * Usually, we just use the same representation for the subtype as for the supertype
* Operations on Subtype Values
    * Usually, supports all the same operations that are supported on the supertype
    * And perhaps additional operations that would not make sense on the supertype:
      like `function toDigit(X: Digit): Char`;
    * A subtype is a subset of values, but it can support a superset of operations.
* Classes
    * In class-based object-oriented languages, a class can be a type: data and operations
        on that data, bundled together
* Functions
    * Most languages have some notion of function types, what is the domain and codomain?
* Operations on Functions
    * We have taken it for granted that other types of values could be passed as
        parameters, bound to variables, and so on
    * Not all languages let you pass in functions as parameters
* Type Annotations
    * Many languages require, or at least allow, type annotations on variables, functions
    * This is used to let the prgrammer specify static type information
* Extreme Type Inference
    * ML takes type inference to extremes - infers a static type for every expression and
        for every function, without annotations
* Simple Type Inference
    * Most languages require some simple kinds of type inference
    * Expressions may have static types, inferred from operators and types of operands
* Static Type Checking
    * Static type checking determines a type for everything before running the program:
        variables, functions, expressions, everything
    * Most modern languages are statically typed
* Dynamic Typing
    * In some languages, programs are not statically type-checked before being run
    * They are still dynamically type checked at runtime, the language system checks
        that operands are of suitable types for operators
    * Although dynamic typing does not type everything at compile time, it still uses
        types, even moreso than static languages, since it must store type information
        along with values in memory
* Static And Dynamic Typing
    * Statically typed languages often use some dynamic typing
    * Everything is typed at compile time, but compile-time type may have subtypes
    * At runtime, it may be necessary to check a value’s membership in a subtype
* Type Equivalence
    * An important question for static and dynamic type checking is when types are the
        same?
    * Name equivalence: types are the same if and only if they have the same name
    * Structural equivalence: types are the same if and only if they are built from the
        same primitive types using the same type constructors in the same order
    * ML uses more structural equivalence, PASCAL more name equivalence

### Chapter 7 - A Second Look at ML

* Constants as Patterns
    * Underscore can be used as a pattern, it matches anything, but doesn't bind a
        variable
    * Can explicitly list the values you want to match, but if you don't cover everything
        you will get a non-exhaustive warning at runtime
* Local Variable Definitions
    * When you use val at the top level to define a variable, it is visible from that point
        forward
    * There is a way to restrict the scope of definitions: the let expression
        `<let-exp> ::= let <definitions> in <expression> end`
    * Here, the variable is only allowed to be used within the `in <expression>`
* Example: Merge Sort
    * The halve function divides a list into two nearly-equal parts of a tuple

        ```ocaml
        fun halve nil = (nil, nil)
        |   halve [a] = ([a], nil)
        |   halve (a::b::cs) =
                let
                    val (x, y) = halve cs
                in
                    (a::x, b::y)
                end;
        ```
    * Merge merges two sorted lists

        ```ocaml
        fun merge (nil, ys) = ys
        |   merge (xs, nil) = xs
        |   merge (x::xs, y::ys) =
                if (x < y) then x :: merge(xs, y::ys)
                else y :: merge(x::xs, ys);
        ```
    * And now the full merge sort:

        ```ocaml
        fun mergeSort nil = nil
        |   mergeSort [a] = [a]
        |   mergeSort theList =
                let
                    val (x, y) = halve theList
                in
                    merge(mergeSort x, mergeSort y)
                end;
        ```

### Chapter 8 - Polymorphism

* Functions with the flexibility to take in many different types are known as
    polymorphic, and this takes on many forms
* Overloading
    * An overloaded function name or operator is one that has at least two definitions,
        all of different types
    * Many languages have overloaded operators, while some even let you define your own,
        like C++
    * C++ lets virtually all operators to be overloaded including:
        ```
        +,-,*,/,%,^,&,|,~,!,=,<,>, +=,-=,=,*=,/=,%=,^=,&=,|=,
<<,>>,>>=,<<=,==, !=,<=,>=,&&,||,++,--,->*,,
        ```

    * Compilers usually implement overloading in the same way:
        * Create a set of monomorphic functions, one for each definition
        * Invent a mangled name for each, encoding the type information
        * Have each reference use the appropriate mangled name, depending on the
            parameter types
* Coercion
    * A coercion is an implicit type conversion, supplied automatically even if the
        programmer leaves it out
    * An example in Java would be:

        ```java
        double x;
        x = 3; // Casts 3 to double
        ```
* Parameter Coercion
    * When a language supports coercion of parameters on a function call, the
        resulting function (or operator) is polymorphic

        ```java
        void f(double x) {
            ...
        }
        // This f can be called with any type of parameter Java is willing to coerce to
        // type double

        f((byte) 1);
        f((short) 2);
        f('a');
        f(3);
        f(4L);
        f(5.6F);
        ```
* Defining Coercions
    * Languages usually have pages defining valid coercions, through languages like ML
        have none
    * This can get tricky however with complex function definitions
* Parametric Polymorphism
    * A function exhibits parametric polymorphism if it has a type that contains one or
        more type variables
    * A type with type variables is a polytype
    * In C++:

        ```cpp
        template<class X>
        X max(X a, X b) {   return a>b ? a : b; }
        ```
    * In ML:

        ```ocaml
        - fun identity x = x;
        val identity = fn : 'a -> 'a
        - identity 3;
        val it = 3 : int
        - identity "hello";
        val it = "hello" : string
        - fun reverse x =
        =   if null x then nil
        =   else (reverse (tl x)) @ [(hd x)];
        val reverse = fn : 'a list -> 'a list
        ```
* Subtype Polymorphism
    * A function or operator exhibits subtype polymorphism if one or more of its
        parameter types have subtypes

### Chapter 9 - A Third Look at ML

* Case expressions
    * The syntax for case expressions: `<case-expr> ::= case <expression> of <match>`
    * So in ml:

        ```ocaml
        - case 1+1 of
        =    3 => "three" |
        =    2 => "two" |
        =    _ => "hmm";
        ```
* Anonymous Functions
    * Functions in ML don't have names, rather you assign them to variables that do

        ```ocaml
        (* Named function: *)
        - fun f x = x + 2;
        val f = fn : int -> int

        (* Anonymous function: *)
        - fn x => x + 2;
        val it = fn : int -> int
        ```
* Higher-order functions
    * Every function has an order
    * A function that does not take any functions as parameters, and does not return
        a function value, has order 1
    * A function that takes in a function or returns a function has order n+1, where n is
        the highest-order parameter/returned val
* Currying
    * Functions in ML take one parameter, then for functions of multiple parameters, they
        are curried so a function that takes in two ints actually is a function that
        takes in an int and then that gives a function that takes in another int
    * Advantages is no tuples, and we get to specialize functions for particular initial
        parameters