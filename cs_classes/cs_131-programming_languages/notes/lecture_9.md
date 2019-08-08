# Week 6 - Tuesday 2019-05-07

#### Java Synchronization

* Race conditions
    * Lead to nondeterminism
    * Lead to Crashes
* `synchronized` keyword around critical sections/monitors
    * Prohibit races where two threads access the same object
    * Can be seen as too simple and leads to synchronization problems
    * Synchronized is used to be implemented by spinlocks, now it there are more
        sophisticated implementations that are faster
* High-level Synchronization Library
    1. Semaphores - allow for up to a set number of threads to access simultaneously
    2. Exchanger

        ```java
        Exchanger x = ... ;
        // Thread 1
        v = x.exchange(w);
        // Thread 2
        m = x.exchange(s);
        ```
    3. CountDownLatch
    4. Cyclic Barrier
* Volatile Keyword
    * Tells the compiler to not optimize accesses to the variable
    * When you access a volatile variable, you must do a load, and if you access two
        volatile variables, you must load them in the same order they are encountered
    * **Note**: volatile does not mean atomic

#### Language Implementation Optimization

* "As If" Rule: you can implement it any way you like, so long as its done as if the
    model were executed
    * Commonly invoked under the assumption of single-threaded code

* HW: Read Chapter 1-15 + Prolog

#### Scope

* Essentially allows you to break up your program into multiple self-contained modules
* Scope of a name: set of program locations where the name is visible
* **Block Scope**


    ```java
    {
        // The scope of n is this block, however that inside n is a different variable,
        // so inside the nested scope, the outer n is invisible
        int n = ...;
        { int n = ...; }
    }
    ```
    * Note that being invisible is different from not existing
    * The scope is usually static. The compiler can see when av variable is and isn't
        viable
    * Lifetime is dynamic, you need to run the code to see the lifetime of a variable
