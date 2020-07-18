# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 1 - C++ Introduction**

## Object Oriented Programming: Bottom up approach

- Identify the *main abstractions* that characterise the application domain and represents them in **classes**
- Assemble components by **identifying mechanisms** that allow different objects to work together

 A typical object oriented program instance:

 1. Code: set of classes definition, not a single huge `main`
 2. Run-time: set of cooperating objects; communication: invocation of object operations

## Structure of C++

- **Low-level** (most inherited from C): data types, flow control, functions, array, pointers
- **Advanced language features**: class, inheritance, polymorphism, template
- **Standard Library**: data structures and algorithms, *e.g.* containers, iterators

## First program

```c++
#include <iostream>
using namespace std;

int main()
{
    ...
    return 0;
}
```

`using namespace std;` allows to use `cout` instead of `std::cout`

- compiler
- development environment
- execution development

## Input and Output

```c++
cin >> ... ;
cout << ... << endl;

Strings are variable-length
```

A variable has a **type** which determines **what operations we can do** on it

`+` operator for strings means *concatenation* of them.
This is an esample of (**operator**) **overloading**: the type of a variable determines which operations are valid and what their meanings are for that type.

## istream

```c++
while (std::cin >> value)
...
```

When we use an istream as condition, the effect is to test the state of the stream:

- stream valid (NO errors encountered) then test succeeds
- stream invalid (hit *end-of-file* or encountered and invalid input), then test fails

## ***NAMESPACES***

A namespace is a named **scope**

All the names defined by the standard library are in the `std` namespace

a. `using` declaration

```c++
using std::cin;
using std::cout;
using std::endl;

cin >> ... ;
cout<< ... << endl;
```

b. `using` directive

```c++
using namespace std;
```

## ***BUILT-IN TYPES***

- **built-in** types, *e.g.* `bool`, `char`, `int`, `double`,`unsigned`
- **user-defined** types
- **standard library** types, *e.g.* `string`, `vector`, `complex`

1. ### Declaration and Initialisation

2. ### Assignment and increment

## Type checking

A variable must be used only after it has been initialised.

Not always possible to detect issues at *compile time*, sometimes they appear only at *run time*. For example:

- Implicit narrowing
- Uninitialised variables
*hint:* always initialise your variables

## Type conversion

Among the operations that many types support is the ability to convert a variable, **but** beware: some dangerous things might happen.

*EXAMPLE:* if we use both `unsigned` and `int` values in an arithmetic expression, the `int` value is **converted** to `unsigned`

## ***CONTROL STRUCTURES***

## Expressions

- boolean type: equality operators, logical operators, relational operators
- character type
- integer type
- floating-point type

## Statements

A statement is:

- a declaration
- expression followed by a semicolon
- a control statement determining flow of control

## Selection

## Iteration

- **`while` loop**

    ```c++
    INITIALIZE CONTROL VARIABLE ; 
    while ( TERMINATION CRITERION ) {
        DO SOMETHING ;
        UPDATE CONTROL VARIABLE ;
    }
    ```

- **`do-while` loop**

    ```c++
    INITIALIZE CONTROL VARIABLE ; 
    do {
        DO SOMETHING ;
        UPDATE CONTROL VARIABLE ;
    }
    while ( TERMINATION CRITERION )
    ```

- **`for` loop**
     Can collect all the control information in one place at the top

     

     ```c++
      for ( INITIALIZE CONTROL VARIABLE ; TERMINATION CRITERION ; UPDATE CONTROL VARIABLE )
     ```
     
- **`range-for` loop**: see Vectors, available also for built-in arrays

## ***ARRAYS AND STRUCTS***

## Built-in Arrays

- **Data structure** whose values are *homogeneous* (*i.e.* same type).
- Access: by **position**
- User defined type
- Size: **fixed**
  - dimension must be known at *compile time*, so dimension must be a **constant expression**
  - Addition of elements: NOT possible

## Declaration

```c++
base_type array_name[array_size];
```

Index range: from `0` to `array_size-1`

## Arrays and Memory allocation

- memory space is allocated contiguously
- an array is a *memory address*, *i.e.*, a **pointer**

## Definition and Initialisation

```c++
unsigned cnt = 42;
constexpr unsigned sz = 42;

int arr[10];                    // OK: 10 is a constant value

string bad[cnt];                // ERROR: cnt NOT a constant expression
string good[sz];                // OK: sz is constant expression

string strs[get_size()];        // OK IF get_size is constexpr, ERROR
```

A **constant expression**:

1. CAN NOT change the value of it
2. CAN be evaluated at *compile time*

The elements in an array are **default initialised**

```c++
const unsigned sz = 3;

int a1[sz] = {0,1,2};
int a2[] = {0,1,2};
int a3[5] = {0,1,2};            // equivalent to a3[] = {0,1,2,0,0}

string a4[3] = {"hi", "bye"};   // equivalent to a4[] = {"hi", "bye", ""}

int a5[2] = {0,1,2};            // ERROR: too many initializers
```

## NO Copy or Assignment

- NOT POSSIBLE initialise an array as copy of another array
- NOT POSSIBLE assign one array to another
- NOT POSSIBLE compare two arrays through `==`

    ```c++
    int a1[] = {0,1,2};
    int a2[] = a1;               // ERROR
    a2 = a;                      // ERROR
    ```

- Copy needs to be performed element by element

    ```c++
    int a2[3];
    for (unsigned i = 0; i < 3; ++i)
        a2[i] = a1[i];
    ```

## Multiple dimensional arrays: **Matrices**

An array element can be another array.

Declaration:

```c++
int matrix [SZ2][SZ1];              // SZ2 rows, SZ1 columns

matrix[i][j]                        // j-th element of i-th array
```

Elements are stored by rows

## Structs

- Allow to declare variables able to store *heterogeneous data*
- Global operator: **assignment**
    `student2 = student1`
- NOT POSSIBLE comparison through default `==`
- By default comparison is field by field

## ***DECLARATIONS AND DEFINITIONS***

## Declarations

- Introduce a name into a scope
- Specifies a type
- Sometimes includes an initializer

## Definitions

A declaration that (also) *fully* specifies the entity declared is called a definition

*EXAMPLE:* declarations that are not definitions

```c++
double sqrt(double);                // function body missing
struct Point;                       // members specified elsewhere
```

## Properties

- NOT POSSIBLE **define** something twice

- CAN **declare** something twice (*e.g.*, declaration and definition)
  
    ```c++
    double sqrt(double);				// declaration
    double sqrt(double d) { ... }		// definition (so declaration)
    ```

## Why both?

To refer to something, we need only its declaration. Often we want definition elsewhere.

**Declarations** **are used to specify interfaces**

Place all declarations in **header files**

## ***HEADER FILES***

Purpose of a header (*.hpp*) file is to propagate declarations to code files (*.cpp*)

## Source and header files

```c++
#include "MyFriendLibrary.h"
```

is a **preprocessor directive** that *adds declarations to the program*.
Header file holds declarations of functions, types, constants, and other program components.

## `MyFriendLibrary.h`

```c++
#ifndef MY_FRIEND_LIBRARY_H
#define MY_FRIEND_LIBRARY_H

...

#endif
```

## `MyFriendLibrary.cpp`

```c++
#include "MyFriendLibrary.h"
```

## ***CLASSES***

A class is a (**user-defined**) type that **specifies how objects of its type can be created and used**

A class directly **represents a concept** (if you can think of "it" as a *separate entity*, it's plausible that it could be a class or an object of a class)

Classes implements a very important concept: **Abstract Data Type**

## Members and member access

- *Data Members*: store information
- *Function Members*: do things, using the information

## ABSTRACT DATA TYPE

### **Type**

- Domain
- Set of operations

Distinguish:

- WHAT the software DOES: set of services provided (**interface**)
- HOW the software is built: module internals (**implementation**)

- Exports:
  - one type (only the name)
  - operations for **manipulating objects** (*i.e.* data) type
- Hides
  - type structure
  - operations implementation

The user can create objects (data) of the type specified by the module and manipulate these objects through the operations defined within the module

## Interface

Set of functions/procedures

It has a **state**, *i.e.* values of the hidden data structure

This implies that two subsequent calls to the same function can give different results

## Class vs Objects

- **Class** refers to a *blueprint*
  - **Encapsulation**
  - Can be seen as *the collection of similar types of objects*

## Definition of a CLASS

A **class** is an Abstract Data Type characterised by:

1. **Interface** (PUBLIC PART)

    - properties, variables or data members (to avoid, preferably)
    - procedures/functions (or methods)

2. **Implementation** (PRIVATE PART)

    - properties, variables or data members - **HIDDEN DATA STRUCTURE**
    - procedures/functions (or methods)

## Definition of an OBJECT

An **object** is a *computational entity* that:

1. Is an **instance** of a class
2. **Encapsulates** some state
3. Is able to perform actions, or *methods*, on this state
4. Communicates with other objects invoking their methods

## Classes - C++ general syntax

```c++
class X {
    public:                         // INTERFACE
    ...                             // functions, types, data
    ...                             // accessible by all

    private:                        // IMPLEMENTATION
    ...                             // functions, types, data
    ...                             // accessible by members of this class only
}
```

*EXAMPLE:* class Stack

```c++
const int max_size = 10;

class Stack {
    public:
    Stack() { top_index = -1; }     // CONSTRUCTOR
    void push(int x);
    int pop();
    int top() const;
    ~Stack() { std::cout << "Stack deallocated"; }

    private:
    bool isEmpty() const;
    bool isFull() connst;

    int top_index;
    int a[max_size];

};
```
***REMARK:*** `const` at the end of the declaration of a function means that the method execution **doesn't change the object status**

## Constructor

- Method used to **define the initial state of the object**
- Same name as the class
- Automatically invoked by the *run time* support of the language every time an object is created
- Can have parameters
- Can be defined more than one (function overloading)
- *Default constructor* (without any parameter)

## Destructor

- Automatically invoked every time an object goes out of scope
- Same name as the class, prefixed by a `~`
- No return value
- No parameters
- Only one destructor for class
- Do everything is necessary to free the resources used by an object
- *Default destructor* which does **nothing** when it has not been defined by developer

## Source files

The header file *.hpp* declares an interface between user code and implementation code (usually in a library)

## Classes - Graphical representation

- **+** : public
- **-** : private

This implies that the name of the claass and the set of methods and attributes marked with **+** are the **class interface**

## ***VECTORS***

- Vector *as* a **variable sized array**
- Referred as **containers**
- Most useful standard library data type

To use a vector, we **must include the appropriate header**

```c++
#include <vector>
using std::vector;
```

- A vector is a **class template**, provided by the *STL** (Standard Template Library)

C++ has both **class and function templates**

## What is a template?

Templates are not themselves functions or classes. Instead, can be thought as **instructions** to the compiler for *generating classes or functions*. This process is called **instantiation**.

When we use a template, we specify what kind of class or function we want the compiler to instantiate (*e.g.* `vector<double>`)

**A `vector<T>` holds a sequence of values of type `T`**

```c++
vector<int> v;                      // start off empty
v.push_back(1);                     // add element with value 1
```

**Initialization with a list**

​	```v = { 1,2,3,4,5,6 }```

## Range-For loop
```c++
for (TYPE ELEMENT : VECTOR)         // for each element in the vector
```

*EXAMPLE:*

```c++
vector<int> v = { 1,2,3,5,7 }
for (int x : v) cout << x << '\n';
```

*EXAMPLE:*

```c++
int main() {
unsigned scores[11] = {};
unsigned grade;

while (cin >> grade) {
    if (grade <= 100)
        ++scores[grades/10];
}

for (unsigned i : scores)
    cout << i << " ";
cout << endl;
}
```

## INITIALIZATION

- `vector<T> v1`
  
  Default initialisation, `v1` is **empty**

- `vector<T> v2(v1)` or `vector<T> v2 = v1`

    `v2` has a **copy** of each element of `v1`

- `vector<T> v3(n,val)`

  `v3` has `n` elements with value `val`

- `vector<T> v4(n)`

  `v4` has `n` copies of a *value-initialized object*

- `vector<T> v5{a,b,c,...}` or `vector<T> v5={a,b,c,...}`

  `v5` has as many elements as there are initializiers; elements are initialized by corresponding initializers

Vectors **grow efficiently**.

We must **ensure** that **any loops** are correct even if the loop **changes the size** of the vector.

## OPERATIONS

- `v.empty()`

  TRUE if `v` is empty, FALSE otherwise

- `v.size()`

- `v.push_back(t)`

- `v[n]`

  returns a **reference** to the element at *position* `n` in `v`

- `v1=v2`

  **replaces** the elements in `v1` with a **copy** of the elements in `v2`

- `v1 = {a,b,c,...}`

  **replaces** the elements in `v1` with a **copy** of the elements in the list

- `v1 == v2` or `v1 != v2`

  `v1` and `v2` are equal if they have the same number of elements and each element in `v1` is equal to the corresponding (*i.e.* same position) one in `v2`

We can fetch a given element using the **subscript operator** (**indexing starting from 0**)

*EXAMPLE:*

```c++
vector<int> ivec;                   // empty vector

for (int ix = 0; ix != 10; ++ix)
    ivec[ix] = ix;                  // DISASTER: ivec has no elements

for (int ix = 0; ix != 10; ++ix)
    ivec.push_back(ix)              // OK
```

A good way to ensure that subscripts are in range is to avoid subscripting altogether by using a **range fore**

## STL ARRAYS

Container similar to STL vector but of fixed size

Advantages:

- assignment operator

- NO comparison operators (available in future C++ 20)

     ```c++
#include <array>   
     int main() {
            array<int, 3> c = {1,2,3}
        }
     ```
     

## ***READINGS: STRINGS***

A **string** is a *variable-length* sequence of characters

Needed the string header. Because it is part of the library, string is defined in the standard library.

```c++
#include <string>
using std::string;
```

## Definition and Initialization

```c++
string s1;                          // default init. s1 empty string
string s2 = s1                      // s2 copy of s1
string s3 = 'hey'                   // s3 copy of string literal
string s4(10,'c')                   // s4 is "cccccccccc"
```

## **Direct** and **Copy** forms of **initialization**

When we initialize a variable using =, we are asking the compiler to **copy initialize** by copying the initializer on the right-hand side into the object being created. Otherwise, when we omit the =, we use **direct initialization**

## String comparison

- case sensitive
- relational operators `<,>,<=,=>` test use the same strategy as a case-sensitive dictionary:
  - if two strings have different lengths and if *every* character in the shorter string is equal to the correspinding character of the longer string, then the shortr string is less than longer one

  - if any characters at corresponding positions in the two strings differ, then the result of the string comparison is the result of comparing the first character at which the strings differ

## Adding literals and strings

    string s4 = s1 + ", ":              // OK: adding a string and a literal
    string s5 = "hello" + ", ";         // ERROR: no string operand
    
    string s7 = "hello" + ", " + s2;    // ERROR: can't add string literals

## Strings C++ vs. C

- There are two ways to access individual characters in a string: **subscript** or **iterator**
- The **subscript operator [ ]** takes a `string::size_type` value that denotes the position of the character we want to access. The operator returns a reference to the character at given position
- Subscripts for strings start at zero; last character is size - 1

## String operations

- `os << s`
  writes `s` onto output stream `os`, return `os`
- `is >> s`
  reads whitespace-separated string from `is` into `s`
- `getline(is,s)`
  reads a line into `s`, return `is`
- `s.empty()`
  TRUE if `s` is empty
- `s.size()`
  returns number of characters in `s`
- `s[n]`
  returns a reference to the char at position `n` in `s`

Processing every character? Use range-based for!

# **OPERATORS**

## ADDRESS-OF OPERATOR: `&`

## ARITHMETIC OPERATOR: `+`, `-`, `*`, `/`

## ASSIGNMENT OPERATOR: `=`

## BITWISE OPERATOR: `&`, `|`, `^`, `!`, `>>`, `<<`

## DEREFERENCING OPERATOR: `*`

## DYNAMIC MEMORY ALLOCATOR/DEALLOCATOR: `new`, `delete`

## EQUALITY/COMPARISON OPERATOR: `==`, `!=`

## EXTRACTION/INSERTION: `>>`, `<<`

## INCREMENT/DECREMENT OPERATOR: `++`, `--`

## LOGICAL OPERATOR: `&&`, `||`, `!`

## RELATIONAL OPERATOR: `<`, `>`, `<=`, `>=`

## SCOPE OPERATOR: `::`

## SPECIAL OPERATOR: `sizeof`
