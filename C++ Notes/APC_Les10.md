# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 10 - Inheritance and Polymorphism**

## ***ENCAPSULATION***

**Encapsulation**: binds the data and function in one form known as *Class*. By thinking the system as composed of independent objects, we keep sub-parts really independent. They communicate only through well-defined method invocation.

Building the system as a group of interacting objects: modularity.

## ***INHERITANCE***

Provides a way to *create a new class from an existing class*; the new class is a **specialised version**.

Motivations: code reuse and evolution.

Advantages:

- **reuse**
- **extend**
- **modify**

## The **"is a"** Relationship

Inheritance establishes an "is a" relationship between classes.
Terminology: "the **child** (*derived* or *subclass*) type **inherits** (or is *derived from*) the **parent** (*base* or *superclass*) type".

A derived object has **all** of the characteristics of the base class.

The derived type is just the base type plus:

- added *specialisations*: change implementation (private part) without changing the base class interface (public)
- added *generalisations/extensions*: new operations and/or data

## What a derived class **inherits**:

- Every **data member** defined in the parent class (although such members *may not always be accessible in the derived class*)
- Every **ordinary member function** of the parent class (although such members *may not always be accessible in the derived class*)

## What a derived class **doesn't inherit**:

- The *base* class **constructor** and **destructor**
- The *base* class **assignment operator**
- The *base* class **friends**

Since all these functions are class-specific.

## What a derived class **can add**:

- New **data members**
- New **member functions** (also overwrite existing ones)
- New **constructors** and **destructor**

## Inheritance - C++ syntax

```c++
class DerivedClassName: access-level BaseClassName {

};
```

where **access-level** *specifies the type of derivation*: `private` by default, `public`, `protected`. We will use always and only public inheritance.

Note that any class can serve as a base class: a derived class can also be a base class.

## What does a Child have?

An object (an instance of the class) of the derived class has:

- all members *defined* in child class
- all members *declared* in parent class
  

An object of the derived class can use:

- all `public` members defined in child class
- all `public` members defined in parent class

## Members of class

- `private`: are accessible only in the class itself
- `public`: are accessible anywhere (outside the class)
- `protected`: are accessible in subclasses of the class and inside the class

## Modes of Inheritance:

- **public mode**: if we derive a sub class from a public base class, then the public members of the base class will become public in the derived class and protected members will become private in the derived class; note that outside a class, a protected member behaves like a private member (for base class objects).
- **protected mode**: if we derive a sub class from a protected base class, then both public member and protected members of the base class will become protected in derived class, so they will be seen as private outside the class.
-  **private mode**: if we derive a sub class from a private base class, then both public members of the base class will become private in the derived class.

*Note:* the private members in the base class can't be directly accessed in the derived class, while protected members can be directly accessed.

[***source***](https://www.geekforsgeeks.org/inheritance-in-c)

## `protected` members

- Like `private`, `protected` members are **inaccessible** (from outside the class) to users of the class (other objects belonging to other classes).
- Like `public`, `protected` members are **accessible** to members (and friends) of classes derived from this class.
- A derived class member may access the protected members of the base class **only through a derived object**. The derived class has *no special access to the protected members of base-class objects*. For example, class `Base` and `Derived`, method `Derived::get()` can access protected members of `Derived` (inherited from `Base`); not access to `Base`, infact `Derived::get(BaseObj)` gives error. It's not as a friend.

So:

- No class can access private variables, not even subclasses
- Only subclasses can access protected variables
- All classes can access public variables

## Construction of a derived object

`Derived` is really two parts: a `Base` part and a `Derived` part. When C++ constructs derived objects, it does so in phases: first, the most-base class (at the top of the inheritance tree) is constructed first; then each child class is constructed in order, until the most-child class (at the bottom of the inheritance tree) in constructed last.
So, when we instantiate an instance of `Derived`, first the `Base` portion of `Derived` is constructed (using the `Base` **default constructor**). At this point, there are no more derived classes, so we are done.

[***source***](https://www.learncpp.com/cpp-tutorial/115-inheritance-and-access-specifiers/)

![inheritance tree](inheritance-tree.png)

## Inheritance vs. Access sub-class methods perspective

*EXAMPLE:*

```c++
class Grade {
    private:
        char letter;
        float score;
        void calcGrade();
    public:
        void setScore(float);
        float getScore();
        char getLetter();
};

class Test : public Grade {
    private:
        int numQuestions;
        float pointsEach;
        int numMissed;
    public:
        Test(int, int);
};
```

When `Test` class inherits from `Grade` class using `public` class access (mode of inheritance), it looks like this:

```c++
class Test {
    private:
        int numQuestions;
        float pointsEach;
        int numMissed;
    public:
        Test(int, int);
        void setScore(float);
        float(getScore);
        char getLetter();
}
```

A `Test` object includes also a `letter` and `score`, **which can be accessed through public methods but not directly in the `Test` class code**. For example, if we define a new method for `Test`

```c++
void Test::printLetter() { cout << letter << endl; }
```

This return an error, because, even if `printLetter` is a method of `Test`, because member `letter` is private in the base class `Grade`. A fix could be

```c++
void printLetter(Test& t) { cout << t.getLetter() << endl; }
```

where, keep in mind, the method `getLetter` is inherited from the base class.

## When a derived-class object is created and destroyed

- The base class constructor is called to initialise the data members inherited from the base class.
- The derived class constructor is then called to initialise the data members added in the derived class.
- The derived class destructor is called on the object first.
- The base class destructor is called on the object.

## Class design principle

In the absence of inheritance, class have two different kinds of users:

- **Ordinary Users**: write code that uses objects of the that class type; such code can access only the public members of the class (interface).
- **Implementors**: write code contained in the members (and friends) of the class; these can access both the public and private sections (interface and implementation).

Under inheritance, there is a third kind of user:

- **Derived Classes**: a base class makes protected those parts of its implementation that it is willing to let its derived classes use; protected members remain inaccessible to ordinary user code; private members remain inaccessible to derived classes.

A class that is used as a base class makes its interface members public. An implementation member should be protected; if it provides an operation or data that a derived class will **need** to use in its own implementation (should be private otherwise).

### ***POLYMORPHISM AND VIRTUAL MEMBER FUNCTIONS***

**Polymorphism**: the ability of objects to respond *differently* to the *same message or function call*.

An object has *multiple identities* based on its class inheritance tree.

Two types:

- **Compile-time** polymorphism $\Rightarrow$ *Templates*
- **Run-time** polymorphism

## Overwriting methods

A **subclass can overwrite**, *i.e.*, change, a base class method behaviour. Three mechanisms:

- **Overloading**
- **Redefinition**
- **Overriding**

## Redefining Base class functions

**Redefining function**: function in a *derived class* has the **same name and parameter list** (*overloading function has different parameter list for each instance*).

In C++ a base class distinguishes functions that are type dependent from those that it expects its derived classes to inherit without change; the base class defines as **virtual** those functions it expects its derived classes to define for themselves.

Derived classes frequently, but not always, **override** (so polymorphism) the virtual functions that they inherit: if a derived class does not (redefine or) override a virtual from its base, then, like any their member, the derived class inherits the version defined in its base class.

## Polymorphism and Virtual Member Functions

**Virtual member function**: function in base class that expects to be redefined in derived class.

```c++
virtual void y() { ... }
```

**Dynamic binding**: functions bound at run time to function that they call.

Without `virtual`, C++ uses **static** (*compile time*) **binding** and it is only **function redefinition**.

*EXAMPLE:*

- Base class:

    ```c++
    class Quote {
        public:
            ...
            virtual double net_price(size_t cnt) const { retunr n*price; }
            virtual ~Quote() = default;
        private:
            ...
        protected:
            ...
    };
    ```

- Derived class:

    ```c++
    class Bulk_quote : public Quote {
        public:
        ...
        double net_price(size_t cnt) const override;
    };
    ```

## Dynamic Binding

Through dynamic binding we *can use the same code* to process objects of either the base or the derived class type interchangeably.

Considering the last example:
```c++
double print_total(const Quote& item, size_t n)
    {
        // depending on the type of the object bound to item, calls either Quote::net_price or Bulk_quote::net_price
    double ret = item.net_price(n);
    return ret;
}
```

**Polymorphism requires References or Pointers**.

## Summary:

- A base class specifies that a member function should be **dynamically bound** by preceding its declaration with keyword `virtual`.
- **Any non-static member function other than a constructor may be virtual (and the destructor should be)**.
- The `virtual` keyword appears **only on declaration** inside the class and may not be used on a definition that appears outside the class body.
- A function declared virtual in the base class is *implicitly virtual in the derived classes* as well
- Member functions that are **not declared** as virtual are resolved at *compile time*, while virtual (dynamic binding) are solved at *run time*

## Redefining vs Overriding

- **Redefining**: *statically bound*, *non-virtual*
- **Overriding**: *dynamically bound*, *virtual*

## ***DERIVED-TO-BASE CONVERSION***

A derived object contains multiple parts:

- a sub-object containing the (non-static) members **defined** in the derived class itself
- the sub-objects corresponding to each base class from which the derived class **inherits**

So, we **can use an object of a derived type as if it were an object of its base type(s)**. We can bind a base-class reference or pointer to the base-class *part* of a derived object; for example, if I have a pointer `Base *p` that points to `Derived d`, I can't `p->New_member`, where `New_member` has been defined in `Derived`

## Conversion and Inheritance

Classes related by inheritance are an important exception: we can bind a pointer or reference to a base-class type to an object of a type derived from that base class.

When we use a reference or pointer to a base-class type, we **don't know the actual type of the object** to which the pointer or reference is bound. That object can be an object of the base class or an object of a derived class.

## Static type and Dynamic type

- **Static type** of an expression is always known at the *compile time*; it is the type with which a variable is **declared** or that an expression yields.
- **Dynamic type** of an expression is the type of the object *in memory* that the variable or expression represents. The dynamic type may not be known until *run time*.

*EXAMPLE:*

In `print_total()` we have `double ret = item.net_price(n)`. Static type (declaration) of item is `Quote&`; dynamic type depends on the type of the argument to which item is bound, and that type cannot be known until a call is executed at run time.

The dynamic type of an *expression* that it **neither a reference, neither a pointer** is **always** the same as that expression static type

## Derived-Class Constructors

A derived object contains members that it inherits from its base but it *cannot directly initialise* those members. A derived class must **use a base-class constructor** to initialise its base-class part.

*EXAMPLE:*

```c++
Bulk_quote::Bulk_quote(const string& book, double p,
                        size_t qty, double disc) :
            Quote(book, p), min_qty(qty), discount(disc) { }
```

The base part of a derived object is default initialised. To use a different base-class constructor, we provide a constructor initializer using the name of the base class, followed by a parenthesized list of arguments.

## Inheritance and `static` Members

If a base class defines a static member, there is *only one such member defined for the entire hierarchy*.

Static members obey normal access control. Assuming the member is accessible, **we can use a static member through either the base or derived**.

## ***ABSTRACT CLASSES***

## Abstract Base Classes and Pure Virtual Functions

**Pure virtual function** is a virtual member function that **must be overridden** in a derived class that has objects; **must have no function definition in the base class**.

**Abstract base class** contains at least one pure virtual function.

Notation:

```c++
virtual void f() = 0;
```

**A class becomes an abstract base class when one or more of its member functions is a pure virtual function.**

Note that abstract base classes:

- **Can't have any objects** (for this reason is called abstract, because there won't be any object).
- Serve as a basis for derived classes that may/will have objects.

For example, given an inheritance tree of three levels, with abstract base class and a pure virtual method `foo`, it is possible that the second child don't define its own version of `foo`, but instead uses the version inherited by the first child.

## Refactoring

- Refactoring involves redesigning a class hierarchy to move operations and/or data from one class to another.
- When classes are refactored (or changed) we must recompile.

## Wrapping up

Inheritance is a mechanism for defining new class types to be a specialisation or an augmentation of existing types.

In principle, every member of a base class is inherited by a derived class with different access permissions, expect for the constructors.

The derived class **can**:

- inherit all members except constructor (but uses it for base part in the derived constructor)
- access directly all public and protected members of the base class
- define its private data member
- provide its own constructor
- define its public member functions
- override functions inherited from the base class