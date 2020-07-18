# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 9 - Constructors**

Each class has to define how objects of its type can be initialized. Classes *control initialization* by defining **one or more** special **member functions** known as **constructors**; job of a constructor is to initialize the data members of a class object.

Constructor have;

- **same name of the class**.
- **no return type**.
- a (possible empty) **parameter list**.
- a (possible empty) **function body**.

Multiple constructor *must differ* from each other in the number or types of their parameters (function overloading).

Constructors may not be declared as const: when we create a const object of a class type, the object does not assume its constancy until after the constructor completes the object's initialization.

*EXAMPLE:* **In-class initializer**

```c++
class Sales_data {
public:
    std::string isbn() const { return bookNo; }    
    Sales_data& operator+=(const Sales_data&);
private:
    std::string bookNo;
    unsigned units_sold = 0;        // in-class initializer
    double revenue = 0.0;           // in-class initializer
};
```

## Default Constructors

Classes control *default initialization* by a special constructor, known as the **default constructor**: it takes **no arguments**.

If class does not *explicitly* define any constructors, it will be implicitly defined by the compiler. When you don't define a default constructor, the compiler will generate the default constructor called **synthesized default constructor**, in this way:

- if there is an in-class initializer, use it to initialize the member
- otherwise, default-initialise the member (*e.g.* for a string, it will default initialize to the empty string).


The compiler generates the default for us *only if we do not define any other constructors*: if we define any constructors, the class will not have a default constructor unless we define that constructor ourselves explicitly.

We can't always rely on the synthesized default constructor! 

1. For some classes, the synthesized default constructor does the wrong thing, *e.g.* objects of built-in or compound type (such as arrays and pointers) have undefined value. We should initialize those members inside the class or define our own version of the default constructor.
2. Sometimes the compiler is unable to synthesize one, *e.g.* if a class has a member that has a class type, and that class doesn't have a default constructor, then the compiler can't initialize that member.

In practice, it is almost always right to provide a default constructor if other constructors are being defined.

The default constructor is used **automatically** whenever an object is **default** or **value initialized**.

1. **Default initialization** happens:
   - when we define non-static variables or arrays at block scope without initializers
   - when a class that itself has members of class type uses the synthesized default constructor
   - when members of class type are not explicitly initialized in a constructor initializer list
2. **Value initialization** happens:
   - during array initialization when we provide fewer initializers than the size of the array
   - when we define a local static object without an initializer
   - when we explicitly request value initialization by writing an expressions of the form T( ) where T is the name of a type (*e.g.* vector)

*EXAMPLE:* `Sales_data` constructors

```c++
class Sales_data {
    public:
        Salesdata() = default;
        Sales_data(const std::string &s): bookNo(s) { }             // bookNo(s), units_sold(0), revenue(0) { }
        Sales_data(const std::string &s, unsigned n, double p):
                    bookNo(s), units_sold(n), revenue(p*n) { }

        std::string isbn() const { return bookNo; }
        Sales_data& operator+=(const Sales_data&);
        double avg_price() const;
    private:
        std::string bookNo;
        unsigned units_sold = 0;
        double revenue = 0.0;
};
```

`bookNo(s), units_sold(n), revenue(p*n)` are called **Constructor Initializer List**

## Constructor Initializer List

When we define variables, we typically initialize them immediately rather than defining them and then assigning to them.

Constructor Initializers are sometimes required; we can often, but not always, ignore the distinction between whether a member is initialized or assigned:

- members that are **const** or **references** **MUST BE initialized**
- members that are of a class type that does not define a default constructor also must be initialized

## Delegating Constructors

A delegating constructor uses another constructor from its own class to perform its initialization. Very used in inheritance framework (derived constructor delegates a part of initialization to base class constructor).

*EXAMPLE:*

```c++
class Sales_data {
    public:
    Sales_data(std::string s, unsigned cnt, double price):
               bookNo(s), units_sold(cnt), revenue(cnt*price) { }       // constructor 1  
    Sales_data(): Sales_data("", 0,0) { }                               // constructor 2, takes no arguments
    Sales_data(std::string s): Sales_data(s, 0, 0) {}                   // constructor 3
};
```

## Constructor and initialization order

How a constructor works to initialize an object:

1. Initializers lists are run first but members are initialized in order as they appear in the class declaration
2. (non-static) data members are initialized in order of declaration in the class definition according to in-class initializers
3. body of the constructor `{ }`

## Copy, Assignment and Destruction

- Objects **are copied** when:
  - **initialise a variable**
  - **pass or return** an object **by value**
  - use **assignment operator**
- Objects **are destroyed** when:
  - they **cease to exist**, such as when a local object is destroyed
  - **objects stored in a vector** (or an array) are destroyed when that vector (or array) is destroyed

If we do not define these operations (copying assigning, destroying), the compiler will sythesize them for us. But beware that some classes cannot rely on the synthesized versions.

## ***DEFINING A TYPE MEMBER***

## Type Aliases

A **type alias** is a name that is a *synonym for another type*. Two ways to define a type alias:

1. `typedef ORIGINAL_TYPE ALIAS_NAME`
2. `using ALIAS_NAME = ORIGINAL_TYPE` (**alias declaration**, C++ 11)

Members that define types must appear before they are used; *hint*: put on the top of the class definition