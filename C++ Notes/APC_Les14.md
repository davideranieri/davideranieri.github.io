# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 14 - Inheritance, part 2**

## Derived-to-Base Conversion

1. **No implicit conversion from Base to Derived**.

Every derived object contains a base-class part to which a pointer or reference of the base-class type can be bound.

There is no similar guarantee for base-class objects: a base-class object can exist either as an independent object or as part of a derived object. A base object that is not part of a derived object has only members defined by the base class and not the members defined by the derived class. There is **no automatic conversion** from base class to derived class.

*EXAMPLE:*

```c++
Bulk_quote * bulkPnt = &base;        // ERROR: can't convert base to derived
Bulk_quote & bulkRef = base;         // ERROR: can't convert base to derived
```

We **can't convert from base to derived even when a base pointer or reference is bound to a derived object**.

2. **No conversion between objects**

The automatic derived-to-base conversion applies only for **conversions to a reference or pointer type**. It is still possible to convert an object of a derived class to its base-class type.

When we initialize or assign:

- when we *initialize*, we're calling a *copy constructor*
- when we *assign*, we're calling an *assignment operator*
- these members normally have a parameter that is a *r2eference to the const version of the class type*

Because these members take references, the derived-to-base conversion lets  pass a derived object to a base-class copy operation.

**These operations are not virtual** (which is related to polymorphism):
when we pass a derived object to a base-class *constructor*, the constructor that is run is defined in the base class. If we *assign* a derived object to a base object, the assignment operator that is run is the one defined in the base class.

*EXAMPLE:*

```c++
Bulk_quote bulk;
Quote item(bulk);
item = bulk;

// because the Bulk_quote part is ignored, we say that
// the Bulk_quote portion is sliced down
```

## ***CONTAINERS AND INHERITANCE***

We generally must store **indirectly** objects from an inheritance hierarchy. In fact, we can't put objectrs of types related by inheritance directly into a container, **because there is no way to define a container that holds elements of differing types**.

For example, consider to declare a vector that tries to store `Quote` and `Bulk_quote` objects:

- we cant' convert `Quote` objects to `Bulk_quote`, so we wouldn't be able to put `Quote` objects into a `vector<Bulk_quote>`.
- we can't use a `vector<Quote>` to store also `Bulk_quote`, because the derived objects would be converted into base class objects, *i.e.*, **ignoring the derived part**.

**Solution:** put (smart) **pointers**, not objects, in the containers. The dynamic type of the object to which those pointers point might be the base-class type or a type derived from that base

*EXAMPLE:*

```c++
vector<Quote*> basket;
basket.push_back(new Quote("0-201-88888-1", 50));
basket.push_back(new Bulk_quote("0-201-99999-2", 50, 10, 0.25));

// here remind to delete objects before you exit!

vector<shared_ptr<Quote>> basket;
basket.push_back(make_shared<Quote>("0-201-88888-1", 50));
basket.push_back(make_shared<Bulk_quote>("0-201-99999-2", 50, 10, 0.25));

// here you can forget to delete objects before you exit
```

## ***VIRTUAL FUNCTIONS***

**Dynamic binding** happens when a virtual member function is called *through a reference or a pointer* to a base-class type.

Calls to virtual functions **may** be resolved at *run time*. When a virtual function is called through a reference or pointer, the compiler generates code to decide at run time which function to call. the function that is called is te one that corresponds to the **dynamic type** of the object bound to pointer or reference.

*EXAMPLE:*

```c++
// Dynamic (run time) binding
Quote base("0-201-82470-1", 50);
print_total(cout, base, 10);                        // calls Quote::net_price
Bulk_quote derived("0-201-82470-1", 50, 5, .19);
print_total(cout, derived, 10);                     // calls Bulk_quote::net_price

// Static (compile time) binding
base = derived;
base.net_price(20);                                 // calls Quote::net_price
```

## Virtual functions in a derived class return type

When a derived class overrides a virtual function, it may, but is not required to, repeat the virtual keyword. Once a function is declared as virtual, it remains virtual in all the derived classes.

A derived-class function that overrides an inherited virtual function **must** have exactly the **same parameter type(s)** as the base-class function that it overrides.

With one exception, the return type of a virtual in the derived class also **must match** the return type of the function from the base class; the *exception* applies to virtuals that return a reference or a pointer to types that are themselves related by inheritance: if `D` is derived from `B`, then a base class virtual can return a `B*` and the version in the deried can return a `D*`.

## ***CONSTRUCTORS AND DESTRUCTORS***

A derived class constructor initializes its **direct** base class only, *i.e.* the direct father-base class, not necessarily the original base class, root of the inheritance tree.

## Synthesized Default Constructor

## Virtual Destructors

A **base class** generally should define a **virtual destructor**.

Destructor needs to be virtual to allow objects in the inheritance hierarchy to be **dynamically allocated**.

It's a good idea to make destructors virtual if the class could ever become a base class; otherwise, the compiler will perform static binding on the destructor if the class ever is derived from.

The destructor is run when we delete a pointer to a dynamically allocated object.

If that pointer points to a type in an inheritance hierarchy, it's possible that the static type of the pointer might differ from the dynamic type of the object being destroyed.

*EXAMPLE:*

```c++
class Quote {
    ...
    public:
        virtual ~Quote() = default;             // dynamic binding for destructor
};

Quote * itemP = new Quote;                      // same static and dynamic type
delete itemP;                                   // destructor for Quote called

itemP = new Bulk_quote;                         // static and dynamic types differ
delete itemP;                                   // destructor for Bulk_quote called
```

## Derived-Class Destructor

Base-class parts of an object are also implicitly destroyed. As a result a derived destructor is responsible only for **destroying the resources allocated by the derived class**.

*EXAMPLE:*

```c++
class Derived : public Base {
    public:
        ~Derived() {
            // do what it takes to clean up derived members
        }   // Base::~Base invoked automatically
}
```

## ***CLASSES (C++) VS. STRUCTS & FUNCTIONS (C)***

## Object-Oriented goals

- Encapsulation: if we don't change the Class interface we can change internals and existing code continues to work properly.
- Inheritance and Polymorphism: new derived classes and methods override to extend functionalities.

*EXAMPLE:* CAD program

```c++
// C version - Structs and functions

Enum ShapeType {circle, square};

struct Shape {
    ShapeType itsType;
};

struct Circle {
    ShapeType itsType;
    double itsRadius;
    Point itsCenter;
};

struct Square {
    ShapeType itsType;
    double itsSide;
    Point itsTopLeft;
};

void drawSquare(struct Square *);
void drawCircle(struct Circle *);
void drawAllShapes(ShapePointer list[], int n) {
    for (int i=0; i<n; ++i) {
        struct shape* s = list[i];
        switch (s->itsType) {
            case square:
                drawSquare((struct Square *) s);
                break;
            case circle:
                drawSquare((struct Circle *) s);
                break;
        }
    }
}

// To add new shapes we need to change this function implementation

// C++ version - Classes

class Shape {
    public:
        virtual draw() const = 0;               // pure virtual function
};

struct Circle : public Shape {
    double itsRadius;
    Point itsCenter;

    void draw() const override;
};

struct Square : public Shape {
    double itsSide;
    Point itsTopLeft;

    void draw() const override;
};
```
