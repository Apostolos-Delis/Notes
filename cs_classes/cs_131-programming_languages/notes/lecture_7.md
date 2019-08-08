# Week 4 - Tuesday 2019-04-23

#### Paramentric Polymorphism

* Generics (without):

    ```cpp
    for (iterator i = c.iterator(); i.hasNext();) {
        if (((String) i.next()).length()) == 1) {
            i.remove();
        }
    }
    ```
* Generics (with):

    ```c
    for (Iterator<String> i = c.iterator(); i.hasNext();) {
        if (i.next().length() == 1) {
            i.remove();
        }
    }
    ```
* Problem with generics:

    ```java
    List<String> ls = _; // Ok
    List<Object> lo = ls; // reference assignment
    lo.add(new Thread()); // Ok
    String s = ls.get(); // Ok
    ```
* The problem here is that line 2 is an error in Java, since `List<String>` not is a
    subtype of `List<Object>`, even though `String` is a subtype of `Object`
    * Subtype is a **Superset of operations**

* Note that the collection of objects must not be a collection of strings

    ```java
    void printAll(Collection<Object> c) {
        for (Object o : c) {
            System.out.println(o);
        }
    }
    ```
* Java supports wildcards that let functions to take in any type for their operations, in
    printAll for example, can use the `?` wildcard which represents any type

    ```java
    void printAll(Collection<?> c) {
        for (Object o : c) {
            System.out.println(o);
        }
    ```
* If you want to copy an array of string to a collection of Objects, Java wont allow it
    unless T is the same type

    ```java
    static <T> void convert (T[] a, collection<T> c) {
        for (T o : a) {
            c.add(o);
        }
    }
    ```
* Can also add constraints for the generic types, such as U here is a supertype of T

    ```java
    static <T, U super T> void convert (T[] a, collection<U> c) {
        for (T o : a) {
            c.add(o);
        }
    }
    ```
#### Erasure

* During a cast, there is no real type checking when you do a cast

    ```java
    List<String>   -> List<Object>
    // Compiletime -> Runtime
    ```
* Should avoid writing code that uses dynamic casting of types during runtime, since the
    compiler will not do a check for these casts, and the error messages will be very
    difficult to decipher.
* The Java compiler does away with most types during compile time

#### Duck Typing

* Ask for methods of objects instead of types
* The compiler or interpreter does not care about type, only about functionality
    * This leads oftentimes to simpler languages
* Loss of readability
* Python is an example of a language that uses duck typing
