# Week 4 - Thursday 2019-04-25

#### Lanugage Environments

1. In Java, order of evaluation is always left to right
    + less undefined behavior
    + code is more portable
    - lower performance
2. There are several tiers to compiling good code, initially, the compiler will write
   first do type checking and optimize for any machine, after that, there is another
   tier of optimizations that occurs that are architecture specific

# Week 5 - Tuesday 2019-04-30

#### Prolog

1. Prolog Lets you define your own operators

    ```prolog
    :-op(700, xfx, [==,\=,=<])
    :-op(500, yfx, [+,-])
    :-op(400, yfx, [*,/])
    :-op(200, yfx, [**])
    :-op(200, fy, [+,-])
    ```
2. Suppose we want to create a Prolog parser, parsing in general has two main problems
   that need to be solved:
    1. Disjunction - try different rules with same NonTerminal
    2. Concatenation - Handle rules with multiple pieces
3. Here is an example parser for regular expressions using functional programming:

    ```ocaml
    let rec mm = function
    | Or (a, b) -> let ma = mm a and
                   let mb = mm b
                   in (fun acc frag ->
                    match ma acc frag with
                    | Some x -> Some x
                    | None -> mb acc frag)
    | Concat (a,b) -> let ma = mm a and
                      let mb = mm b
                      in (fun acc -> ma (mb acc))
    ```
    * Note that here: Or and Concat represent data structures that represent the text
        fragment

#### Java Abstract Classes

1. Java allows the definition of abstract classes

    ```java
    abstract class X {
        int i;
        public void incr() { i++; }
        public void decr();
    }
    ```
2. Note in the previous operation, cannot run `new X` since X does not fully implement
   the interface
3. Must create a child class that inherits from X and finishes the interface

    ```java
    public class SX extends X {
        public void decr() { i--; }
    }
    ```
4. Abstract classes can be replaced with an interface and regular classes
5. Final class: cannot overwrite methods or extend, this prevents child class from making
   mistakes
    * Final allows the compiler to inline calls to final methods since you cannot
        override the method and thus can make optimizations
    * Final methods don't have to deal with generics and worrying about being overwritten
        by subclasses - essentially trades flexibility for speed

#### Java Library Definition of the Object Class

1. The Object abstract class requires the following from all the inherited objects:

    ```java
    public class Object {
        public Object();

        // Note that '==' will compare addresses while equals can be defined to do
        // something else
        public boolean equals(Object obj); //default ==
        public int hashCode(); // default is int(this)
        public String toString(); // Note that yes, String is a subclass of Object, this
                                  // is allowed in Java
        public final Class getClass(); // Note this actual declaration is more
                                       // complicated than this. (Note that Class is
                                       // capitalized, this is a description of the class)
        protected void finalize() throws Throwable; // For garbage collection
        protected Object clone() throws CloneNotSupportedException; // For copying
    }
    ```
2. Note that for two objects, $o_1$ and $o_2$, we have the following implication:

    ```java
    o1.equals(o2); // This implies that:
    o1.hashCode() == o2.hashCode();
    ```
3. This means that if you plan on overwriting `equals()` you are probably going to
    have to overwrite 'hashCode()'

#### Java Multithreading

1. Thread's life cycle

    ```java
    Thread t = new Thread(...); // First it is created
    t.start();  // Tells the OS to allocate resources (pthread_create() invocation)
    t.run() // you can either pass a runnable object or implement the Runnable interface
            // or subclass Thread

    public interface Runnable {
        void run();
    }
    sleep()    // ->
    wait()     // ->
    // during I/O ->   BLOCKED (waiting for I/O)
               //      WAITING
               //      TIMEDWAITING
               //      Threads in these states are frozen and do not take up CPU power
    exit() // exit the run method resulting in status TERMINATED, here it still exists
           // but can't do anything
    // Garbage collector cleans it up
    ```

2. Synchronization
    * Synchronization methods:
        * Before calling, lock an object level spin lock
        * After calling, unlock the object level spin lock
    * wait methof of an 
