# Week 1 - Tuesday (2019-04-04)

**Donald E. Knuth** "The Art of Computer Programming"

**TEX** Was written in Pascal

**Literate Programming**

- have comments that are written in LaTeX, allowing the code to process the source code as both a 
    piece of software and also as documentation
- main idea is to maintain documentation of source code inside of the source code so you don't 
    have to update two things at once
- example: Java, source code for the Java library is maintained in the documentation in the same file

**Quiz Solution:** 
```bash
    tr -c -s 'A-Za-z' '[ln*]' | sort | uniq -c | sort -rn
```

* tr -c means complement, so translate everything that's not a letter character
* tr -s means squeeze repeated outputs (generates a single newline between each word)
* uniq -c means count the duplicates
* sort -rn means sort by count (reverse and numeric sort)

#### Main Concepts of the Course

1. Principles and Limitations of programming models
2. Core Programming Paradigms 
3. Learn about the various tradoffs for different types of programming languages


| Theory          | Practice      | 
| --------------- |:-------------:|
| Language Design | Ocaml         | 
| Syntax          | Java          | 
| Semantics       | Prolog        | 
| Functions       | Scheme        | 
| Names           | Python        | 
| Types           | ? (Kotlin)    | 
| Control         |               | 
| Objects         |               | 
| Exceptions      |               | 



**BASIC Developed on the GE 225 Mainframe (1961)**

* 40 micro-seconds to add integers
* 500 micro-seconds to divide integers
* ~40 KB of RAM
* Timeshared with 20 other users

**C (C++) Developed on PDR11 (~1975)**

* 4 micro-seconds to add 
* 16 KB RAM
* 1.2 micro-seconds memory cycle time. (3x faster than adding)
    * This is the reason that C coding revolves around pointer arithmetic.
    * **Note:** that the assumption that following pointers is faster now is wrong

**Stable Languages Die, Successfull Languages Evolve!**

1. Syntax Evolution: Idea: Can we change the language and add new features without changing the compiler?
    * Example of how to change C language 

        ```c
        #define ELTS(a) (sizeof(a)/sizeof(a)[0])
        #define CALLMAN(f, args) (f)(ELTS(args), args)
        /* These are now equivalent: 
         * Foo(f, args); <=> CALLMAN(f, args);
         */
        #define CALLN(f, ...) CALLMAN(f, ((obj[]) { __VA_ARGS__ })) 
        /* This has now changed the language of C through the use of macros,
         * This is called metaprogramming
         */
        ```
    * Steve Bourne created the shell, and modified the C code using this:

        ```c
        #define IF if(
        #define THEN ) {
        #define ELSE } else {
        #define FI }
        ```

