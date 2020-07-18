# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 4 - Pointers**

## **Raw** pointers and **Smart** pointers

- **Raw** pointers:

C pointers with `new` and `delete`

- **Smart** pointers

  - Managed by the compiler, no garbage collector
  - Allocated objects have a counter associated
  - When the counter becomes equal to 0, heap memory is automatically released

## Vector

- simple and most useful container
- compactly stores elements of a given type
- efficient access
- expands to hold any number of elements

How is that done?

Vector is the default container.

## Raw pointers

The hardware provides memory and addresses: low level, untyped, fixed-sized chunks of memory

The application builder needs something like a vector: type checked, size varies

A vector can hold an arbitrary number of elements

```c++
class vector {
    private:
        int sz;
        double* elem;               // pointer to first element
    public:
        vector(int s);              // constructor

        int size() const { return sz;}
}
```

## The computer's memory

- Executable code is in the "code section"
- Global variables are "static data"
- "Free store" is managed by `new` and `delete`
- Local variables "live on the stack"
  
## The free store or the **heap**

You request memory "to be allocated" on the *free store* by the `new` operator

`new` operator **returns a pointer to the allocated memory**

A pointer is the address of the first byte of the memory

A pointer does not know how many elements it points to

## Pointer states

The value (*i.e.*, the address) stored in a pointer can be in one of four states:

1. It can point **to an object**
2. It can point **to the location just immediately past the end of an object**
3. It can be a **null pointer**, **not bound to any object**
4. It can be **invalid**

It is an **error** to copy or to try to access the value of an invalid pointer; this error the compiler unlikely detect. The result of accessing an invalid pointer is *undefined*

## Null pointers (C++ 11)

```c++
int * p1 = nullptr;
```

## Vector (constructor)

```c++
vector::vector(int s)
    sz(s),
    elem(new double[s])
{ }
```

## Access

- Individual elements

    ```c++
    int * p1 = new int;              // allocate a new uninitialized int
    int * p2 = new int(5);           // allocate a new initialized to 5
    
    int x = *p2;                     // get/read the value pointed to by p2
    
    int y = *p1;                     // y gets an undefined value; AVOID THIS
    ```

- Arrays (sequences of elements)

    ```c++
int * p3 = new int[5];               // allocate 5 ints
    
    p3[0] = 7;                           // set/write 1st of p3
p3[1] = 9;
    
    int x2 = p3[1]                       // get value of the 2nd of p3
    int x3 = *p3;                        // means p3[0]
    ```

**For an array we can also use the dereference operator `*`**

*Why use free store?* To allocate objects that have to outlive the function that creates them.

1. A pointer **does not know** the *number of elements* that it's pointing to, **only the address** of the first element

    *EXAMPLE:*

    ```c++
    double * p1 = new double;            // definition of pointer
    *p1 = 7.3;
    p1[0] = 8.2;                         // pointer seen as array of length 1
    p1[17] = 9.4                         // ERROR
    p1[-4] = 2.4                         // ERROR
    
    double * p2 = new double[100];       // pointer as array of n, where n is given in new
    *p2 = 7.3;
    p2[17] = 9.4;                        // OK
    p2[-4] = 2.4;                        // ERROR
    
    p1 = p2;							 // note: this is assignment of pointers, not arrays
    p1[17] = 9.4                         // OK
    ```

2. A pointer **does know** the *type* of the object that it's pointing to

    *EXAMPLE:*

    ```c++
    int * pi1 = new int(7);              // pi1 allocates a new initialized to 7
    int pi2 = pi1;
    double * pd = pi1;                   // ERROR: can't assign int* to a double*
char * pc = pi1;                     // ERROR: can't assign int* to char*
    ```

    **There are no implicit conversions between a pointer to one value type to a pointer to another value type.** However, there are implicit conversions between value types
    
    ```c++
    *pc = 8;                        // OK
    *pc = *pi1;                     // OK
    ```

## Pointers, arrays and vectors

With pointers and arrays we are touching hardware *directly* with only the most minimal help from the language. Here is where serious programming errors can most easily be made.

`vector` is one way of getting almost all of the flexibility and performance of arrays with greater support from the language.

## Vector (construction and primitive access)

```c++
class vector {
private:
    int sz;
    double* elem;
public:
    // CONSTRUCTOR
    vector(int s) : sz(s), elem(new double[s]) { }
    // ACCESS: READ
    double get(int n) const { return elem[n]; }
    // ACCESS: WRITE
    void set(int n, double v) { elem[n] = v; }
    // get current size
    int size() const { return sz; }
}
```

## A problem: **memory leak** (lack of de-allocation)

*EXAMPLE:*

```c++
double * calc(int result_size, int max)
{
    double * p = new double[max];
    double * result = new double[result_size];

    return result;
}

double * r = calc(200,100);          // OPS: forgot to give the memory allocated for p back to the free store
```

Solution:

```c++
double * calc(int result_size, int max)
{
    double * p = new double[max];
    double * result = new double[result_size];

    delete[] p;                     // OK: de-allocate, back to the free store

    return result;
}

double * r = calc(200,100);
delete[] r;
```

All memory is returned to the system at the end of the program.

*EXAMPLE:*

```c++
void f()
{
    double * p = new double[27];

    p = new double[42];

    delete[] p;                     // BUT: first array (of 27 doubles) leaked
}
```

*How do we systematically and simply avoid memory leaks?*

- **Don't** mess directly with `new` and `delete`; use vector or
- **Use garbage collector**: a program that keeps track of all the memory has been allocated dynamically
- In C++ there are **Smart Pointers**: allocate and return unused free-store allocated memory to the free store

Unfortunately, even a garbage collector and Smart Pointers do not prevent all leaks

## Vector (destructor)

```c++
class Vector {
private:
    int sz;
    double * elem;
public:
    // COSTRUCTOR
    vector(int s)
    : sz(s), elem(new double[s]) { };
    
    // DESTRUCTOR
    ~vector()
    { delete[] elem; }
};
```

- Acquire resources in a constructor
- Release them in the destructor; prefer `delete`s in destructors

## Free store summary

- Allocate using `new`
- De-allocate using `delete` and `delete[]`
- Delete of the null pointer does nothing