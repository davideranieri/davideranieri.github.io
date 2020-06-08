# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 2 - Pointers, References and Functions Parameters**

## ***POINTERS***

Declaring a variable means reserving a memory area including severel locations

Each location has a physical address and:

- the name of the variable indicates the contents of the memory location
- the operator `&` allows obtaining the memory address of the location associated with the variable to which the operator is applied

## Variables and memory

Suppose the declaration reserves the memory area at address 2

- `var` indicates the contents of the memory location
- `&var` indicates the memory address

When *declaring* a pointer you **must** also specify what type of object the pointer points to

Syntax:

    TYPE *NAME;

## Dereferencing a pointer

Using the **dreferencing `*` operator** to a pointer variable. `*p` indicates the content of the location pointed by `p`

*EXAMPLE:*

    int * p;
    int x;
    ...
    *p = 5;                             // OK: *p is an integer
    p = 5;                              // ERROR: p is a pointer
    
    x = 3;
    p = &x;

**Warning:** The symbol `*` is used both in the declaration and in the dereferencing

***FUNCTIONS PARAMETERS***

- **Declaration**
    RETURN_TYPE FUNCTION_NAME (PARAMETERS);

- **Definition**
    RETURN_TYPE FUNCTION_NAME (PARAMETERS) {
        ...
    }

Parameters are called *formal arguments*

If you don't want to return a value, give `void` as the return type

## Formal and Actual parameters

- In a function definition we use **formal parameters** representing a **symbolic reference** (*identifiers*)
- The initial value of formal parameters is defined when the function is called using the **actual parameters** specified by the caller

## Pass by **value**

At the time of function call, the **value** of the actual parameter is **copied** into the memory location of the corresponding formal parameter. In other words, **the formal parameter and the actual parameter refer to two different memory locations**

The function works in its environment and therefore on formal parameters

The actual parameters **are NOT changed**

## Pass by **reference**

At the time of the call the **address** of an actual parameter is associated with the formal parameter. In other words, **the actual parameter and the formal parameter share the same memory location**

The function works in its environment on the formal parameters (and consequently also on the actual parameters); each change on the formal parameter is reflected to the corresponding actual parameter

## Parameters passing in C

Parameters passing is always by value. To implement pass by reference we need to rely on pointers

## Pass by reference

Ingredients:

- A pointer for each formal parameter
- The dereference operator in the function body to access the actual parameter

**Warning:** Arrays are always passed by reference. Actually, the name of an array variable is an address, *i.e.* a pointer

*EXAMPLE:*

    double circ(double *radius) {
        double res;
        res = *radius * 3.14 * 2;
        *radius = 7;

        return res;
    }

    // somewhere in the main
    double c;
    double r = 5;
    c = circ(&r);                       // WARNING: now r is 7.0

## Comparison

- Pass by **value**:
  - lot of time to perform the copy if the parameter is large
  - actual parameter and formal parameter are different
  - no side effects risk
  - cannot return a value to the caller
- Pass by **reference**:
  - an address (which has fixed size) is copied. Fast!
  - actual parameter and formal parameter are the same
  - side effect risk
  - can return a value to the caller

## ***REFERENCES***

A reference is an **automatically dereferenced pointer** (or as "*an alternative name for an object*"). So a reference is a particular type of pointer

A reference is introduce though the `&` modifier in a variable declaration

A reference **MUST be initialized**, and the value of a reference **CAN'T be changed after initialization**. I can change the value of the variable linked to the reference but I can't change what is linked to the reference

*EXAMPLE:*

    int x = 9;
    int y = 8;

    int &r = x;
    r = 10;                             // OK: now x = 10
    r = &y;                             // ERROR: can't change what r refers to

- When we *initialize a variable*, the value of the initializer (right term, after assignment operator `=`) is *copied* into the object we are creating
- When we *define a reference*, instead of copying the initializer value, we **bind the reference to its initializer**

**There is no way to rebind a reference to refer to a different object**; because of this, references must be initialized

## Pointers and References

A **pointer** is a *compound type* that "points to" another type

**Like** reference, pointer is used to **indirect access**

**Unlike** reference, pointer **is an object**: pointer **can be assigned** and **copied; a single pointer can point to several different objects over its lifetime. Unlike a reference, a pointer does not need to be initialized at the time it's defined**

Like other built-in types, pointers defined at block scope have underfined value if they are not initialized!

`&` and `*` are both **operator in an expression** and **part of declaration**

*EXAMPLE:*

    int i = 42;
    int &r = i;                         // & follows a type; part of declaration; reference
    
    int *p;                             // * follows a type; part of declaration; pointer

    p = &i;                             // & in expression; address-of operator
    *p = i;                             // * in expression; dereference operator

## ***`const` QUALIFIER***

Aim: define a variable whose value cannot be changed

*EXAMPLE:*

    const int bufSize = 512;            // same as constexpr!

Because value can't be changed, **it must be initialized**

By default, `const` objects **local** to a file

## Reference to `const`

we can *bind a reference to an object of **const** type*, using a **reference to const**

Syntax: `const TYPE &NAME = OBJECT`

Unlike ordinary reference, a *reference to const can't be used to change the object to which the reference is bound*

*EXAMPLE:*

    const int ci = 1024;
    const int &r1 = ci;                 // OK: both reference and object are const

    r1 = 42;                            // ERROR: r1 is a reference to const
    
    int &r2 = ci                        // ERROR: r2 not const reference to a const object

## C++ pass by reference

- **C** relies on **pointers**
- **C++** relies on **references**
  
**Const references** can be used to pass **large objects** in **read only**, avoiding any side effect

*EXAMPLE:* swap of 2 integers - **C**

    void swap (int *p, int *q) {
        int temp;
        temp = *p;
        *p = *q;
        *q = temp;
    }

    // in main
    swap(&a,&b);

*EXAMPLE:* swap of 2 integers - **C++**

    void swap(int &p, int &q) {
        int temp;
        temp = p;
        p = q;
        q = temp;
    }

    // in main
    swap(a,b);

Reconsidering the example of computing circonference, we have

*EXAMPLE:*

    double circ(double &radius) {
        double res;
        res = radius * 3.14 * 2;
        radius = 7;                     // we get COMPILE ERROR, need to delete this line

        return res;
    }

    // somewhere in the main
    double c;
    double r = 5;
    c = circ(r);                       // r is 5.0

    // What about circ(22.0), i.e., of a value, not a object?
    // A TEMPORARY CONST OBJECT is created, initialized with 22.0 and this allows function to be executed

## Guidance for passing variables

- use **call-by-value** for *small objects*
- use **call-by-const-reference** for *large objects*
- use **call-by-reference** only *when you have to return a result rather than modify an object through a reference argument*

*EXAMPLE:*

    class Image {
        ...
    };

    void f(Image i);                    // YES copy; this could be slow

    void f(Image &i);                   // NO copy; f() CAN modify

    void f(const Image &i);             // NO copy; f() CAN'T modify