# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 12 - Copy Constructors and Destructors**

**Container elements are copies:** when we use an object to *initialize* a container, or *insert* an object into a container, a copy of that object value is placed in the container, not the object itself. There is no relationship between the element in the container and the object from which that value originated.

## Copy Control

Classes can control what happens when objects of the class type are **copied, assigned, destroyed** through *special member functions*, called collectively as **copy control**:

- Copy *constructor*
- Assignment *operator*
- Destructor

If a class does not define all the copy-control members, the compiler will automatically define the missing operations. For some classes, relying on the compiler (and so the default definitions made by it) leads to disaster. The hardest part of implementing copy-control operations is recognising when we need to define them.

## Copy initialization

*EXAMPLE:*

```c++
string dots(10,'.');                // direct initialization
string s(dots);                     // direct initialization
string s2 = dots;                   // copy initialization
```

- When we use **direct initialization**, we are asking the compiler to **use ordinary function matching to select the constructor that best matches the arguments we provide**.
- When we use **copy initialization**, we are asking the compiler to **copy the right-hand operand into the object being created, converting that operand if necessary**.

Copy initialization ordinarily uses the **copy constructor**. Copy initialization happens not only when we define variables using an `=`, but also when we:
- pass an object as an argument to a parameter of non-reference type
- return an object from a function that has a non-reference return type
- brace initialise the elements in an array or the members of an aggregate class

The library containers copy initialise their elements wen we initialize the container, or when we call an insert or push member.

## **Copy Constructor**

A constructor is the copy constructor (so copy constructor is a particular constructor) **if its first parameter is a reference** (always a **reference to const**) **to the class type** and any additional parameters have **default values**.

*EXAMPLE:*

```c++
class Foo {
    public:
        Foo();                      // default constructor
        Foo(const Foo&);            // copy constructor
};
```

## The Synthesized Copy Constructor

When we don't define a copy constructor for a class, the compiler tries to synthesise one for us; unlike the synthesized default constructor, **a copy constructor is synthesized even if we define other constructors**.

- (*if*) All the members *can be copied*: the synthesized copy constructor **member-wise copies** the members of its argument into the object being created.
- (*if*) Some members *can't be copied*: the synthesized copy constructor is **unavailable** (implicitly deleted).

The type of each member determines how that member is copied:

- Members of class type are copied by the copy constructor for that class.
- Members of built-in type are copied directly.
- Array members are copied by copying elements one by one.

*EXAMPLE:*

```c++
Sales_data::Sales_data(const Sales_data &orig):
    bookNo(orig.bookNo),
    units_sold(orig.units_sold),
    revenue(orig.revenue)
    { }
```

## `vector` Synthesized Copy Constructor

By default "copy" means "copy the data members".

*EXAMPLE:*

```c++
class vector {
    private:
        unsigned sz;
        double* elem;               // pointer to elements
    public:
        vector(unsigned s): sz{s}, elem{new double[s]} {}
        ~vector() { delete[] elem; }

        /* Synthesized copy constructor is like:
        vector(const vector& other) : sz{other.sz}, elem{other.elem} { }
        */
};

...

void f(int n)
{
    vector v1(n);
    vector v2 = v1;
}
```

Disaster when we leave `f()`: `v1`'s elements are deleted twice by the destructor.

Solution:

```c++
class vector {
    private:
        ...
    public:
        ...

        vector(const vector& other) : sz{other.sz}, elem{new double[other.sz]} {
            for (int i = 0; i < sz; ++i)
                elem[i] = other.elem[i];
        }
};

...

void f(int n)
{
    vector v1(n);
    vector v2 = v1;
}
```

The destructor correctly deletes all elements (once for each vector).

## **Copy-Assignment Operator**

Just as a class controls how objects of that class are initialised (via constructor), it also controls how objects of its class are assigned.

 As with the copy constructor, the compiler synthesizes a copy-assignment operator if the class does not define its own.

 All the members can be copy-assigned: each **non-static member** of the right-hand object is assigned to the corresponding member of the left-hand object using the copy-assignment operator for the type of that member.

Some members can't be copy-assigned: the synthesized copy-assignment is unavailable (implicitly deleted).

The synthesized copy-assignment operator **returns a reference to its left-hand object**.

*EXAMPLE:*

```c++
Sales_data& Sales_data::operator= (const Sales_data& rhs) {
    bookNo = rhs.bookNo;
    units_sold = rhs.units_sold;
    revenue = rhs.revenue;

    return *this;
}
```

## Overloading Copy-Assignment Operator

Copy-assignment operator takes an argument of the same type as the class. To be consistent with assignment for the built-in types, assignment operators usually **return a reference to their left-hand operand**.

## `vector` copy-Assignment Operator

```c++
vector& vector::operator=(const vector& rhs) {
    double* p = new double[rhs.size]            // allocate new space
    for (unsigned i = 0; i < rhs.sz; ++i)
        p[i] = rhs.elem[i];
    delete[] elem;                              //deallocate old space
    sz = rhs.sz;
    elem = p;
    
    return *this;
}
```

Let's optimise:

```c++
vector& vector::operator=(const vector& rhs) {
    if(this==&rhs)
        return *this;                           // self-assignment, no work needed
    if(rhs.sz<=space)                           // enough space, no need new allocation
    {
        for (int i=0; i < rhs.sz; ++i)
            elem[i] = rhs.elem[i];
        sz = rhs.sz;

        return *this;
    }
    double* p = new double[rhs.sz];
    for (unsigned i=0; i<rhs.sz; ++i)
        p[i] = rhs.elem[i];
    delete[] elem;
    sz = rhs.sz;
    space = rhs.sz;
    elem = p;
    return *this;
}
```

## **Destructor**

The destructor does *whatever* operations the class designer wishes to have executed after the last use of an object. Typically, the destructor frees resources.

Just as a constructor has an initialization part and a function body, a destructor has a function body and a destruction part.

What happens when a member is destroyed depends on the type of the member: the built-in types do not have destructors, so nothing is done to destroy members of built-in type.

## Constructor vs. Destructor

- **Constructors** initialize the non-static data members of an object and may do other work (usually in function body); members are initialized before the function body is executed, in the same order as they appear in the class.
- **Destructors** do whatever work is needed to free the resources used by an object and destroy the non-static data members of the object; the function body is executed first and then the members are destroyed, in reverse order from the order in which they were initialized.

## Destructor: Syntax

No return value and takes no parameters. For this reason, it **can't be overloaded**: there's **only one destructor for a given class.**

## When a destructor is called:

It's called automatically whenever an object of its type is destroyed:

- variables are destroyed when they go out of scope (objects belong to the scope where they are declared)
- elements in a container - whether a library container or an array - are destroyed when the container is destroyed
- dynamically allocated objects are destroyed when the delete operator is applied to a pointer to the object
- temporary objects are destroyed at the end of the full expression in which the temporary was created

Because destructors are run automatically, our programs can allocate resources and (usually) not worry about when those resources are released.

The destructor is **NOT** run when a **reference** or a **pointer** to an object goes out of scope.

## The Synthesized Destructor

The compiler defines a synthesized destructor for any class that does not define its own destructor. As with the copy constructor and the copy-assignment operator, for some classes, the synthesized destructor can't be synthesized.

The members are automatically destroyed **after** the (empty) destructor body is run.

## ***COPY CONTROL DEFAULT, DEFINE, DELETE***

## The Rule of Three

we have seen that three basic operations to control copies of class objects:

- copy constructor
- copy-assignment operator
- destructor

**No requirement** that we define all these operations: we can define one or two of them without having to define all of them. But in general, it is unusual to need one without needing to define them all.

**Classes that need *destructors* need *copy and assignment***: decide first whether the class needs a destructor. 

Often, the need for a destructor is more obvious than the need for the copy constructor or assignment operator: .

**Classes that need *copy constructor* need *assignment* and viceversa**. 

Consider, for example, a class that gives each object its own, unique, serial number:

- such class would need a copy constructor to generate a new, distinct serial number for the object being created
- that constructor would copy all the other data members from the given object
- this class would also need its own copy-assignment operator to avoid assigning the serial number of the right-hand side to the one of the left-hand object
- this class would have no need for a destructor

This example gives rise to a second rule of thumb:

**If a class needs a *copy constructor*, it almost surely needs a *copy-assignment* operator and viceversa**.

## Using `= default;`

For **default constructor**,**copy constructor**, **copy-assignment** and **destructor**. Explicitly requires synthesis of default special method, preventing custom implementation.

*EXAMPLE:*

```c++
class Sales_data {
    public:
        Sales_data() = default;                                 // constructor
        Sales_data(const Sales_data&) = default;                // copy constructor
        Sales_data operator=(const Sales_data&) = default;      // copy-assignment operator
        ~Sales_data() = default                                 // destructor
}
```

**Preventing copies**. For some classes, there is no sensible meaning for create a copy, so *copies or assignments must be denied*.

For example, the `iostream` classes prevent copying to avoid letting multiple objects write to or read from the same IO buffer.

Prevent copies by not defining the copy-control members is not the right solution, because the compiler will synthesise them.

## Defining a function as `deleted`

Under C++ 11, we can **prevent copies** by defining the **copy constructor** and **copy-assignment operator** as **deleted functions**, by adding `= deleted`. In this way, synthesized versions of deleted functions are not generated.

## ***COPY CONTROL AND RESOURCE MANAGEMENT***

Classes that **manage resources that do not reside in the class** must define the copy-control members. In order to define these members, we first have to decide what copying an object of our type will mean. Two choices:

- **like-a-value**: the class behaves like a value
- **like-a-pointer**: the class behaves like a pointer

**Like-a-value** classes have their **own state**. The copy and the original are independent of each other; changes made to the copy have no effect on the original and viceversa.

**Like-a-pointer** classes that act like pointers share part of the state. The copy and the original use the same underlying data; changes made to the copy also change the original and viceversa.

## Copy + like-a-pointer/like-a-value semantic

*Shallow copy* $\Rightarrow$ Like-a-pointer: copy only a pointer so that the two pointers now refer to the same object.

*Deep copy* $\Rightarrow$ Like-a-value: requires copy constructors and copy assignments for container classes.

Ordinarily, classes copy members of built-in type (other than pointers) directly.

*EXAMPLE:*

```c++
class HasPtr {
    private:
        std::string * ps;
        int i;
    public:
        HasPtr(const std::string &s):
            ps(new std::string(s)), i(0) { }            // constructor self made
        HasPtr(const HasPtr &p):
            ps(new std::string(*p.ps)), i(p.i) { }      // copy constructor
        HasPtr& operator=(const HasPtr&);               // copy-assignment operator
        ~HasPtr() { delete ps; }
};
```

## Like-a-pointer implementation

Remember: if a class need copy constructor, it needs copy-assignment; and viceversa.

We need the copy constructor and copy-assignment operator to **copy the pointer member**. 

Our class will still need its own destructor to free the memory allocated by the constructor that takes a string; in this case, the *destructor can't unilaterally free* its associated string.

## **Reference count**

In addition to initialise the **object**, each constructor (other than the copy constructor) creates a **counter**; this counter will keep track of **how many objects *share state* with the object we are creating**. When we **create** an object, there is only one such object, so we initialize the **counter to 1**.

The **copy constructor** does not allocate a new counter; instead, it copies the data members of its given object and **increments this counter** (that becomes shared, like the state).

The **destructor** decrements the counter, indicating that there is one less user of the shared state. **If the count goes to zero, the destructor decrements the state**.

The **copy-assignment operator**:

- increments the right-hand operand 's counter;
- decrements the counter of the left-hand operand;
- if the counter for the left-hand operand goes to zero, destroys also the state of the left-hand operand;

We have to decide  *where to put the reference count*. In the example before, `HasPtr`, the counter **can't be a direct member** of a `HasPtr` **object**.

$\Rightarrow$ Store the counter in dynamic memory!

*EXAMPLE:*

```C++
class HasPtr {
    private:
        std::string * ps;
        int i;

        // pointer (dynamic memory) to keep track of how many objects share *ps;
        std::size_t * use;

    public:
        // constructor allocates a new string and a new counter set to 1
        HasPtr(const std::string& s):
            ps(new std::string(s)), i(0), 
            use(new std::size_t(1)) {}  

        // copy constructor increments the counter    
        HasPtr(const HasPtr & p):
            ps(p.ps), i(p.i),
            use(p.use) { ++*use; }

        ~HasPtr();               
};

HasPtr::~HasPtr() {
    if(--*use == 0) {
        delete ps;
        delete use;                 // counter deleted
    }
}

HasPtr& HasPtr::operator=(const HasPtr &rhs) {
    ++*rhs.use;                     // increment count of rhs
    if(--*use == 0) {               // then decrement this object's counter
        delete ps;                  // delete if no other users
        delete use;                 // free this object's allocated members
    }
    ps = rhs.ps;
    i = rhs.i;
    use = rhs.use;

    return *this;
}
```

## **Implicit** class-type **conversions**

C++ defines several automatic conversions among the built-in types. Classes can define implicit conversions as well.

For example, *every constructor that can be called with a single argument defines an implicit conversion to a class type* (**converting constructors**).

Class-type conversions are not always useful. Sometimes we need to suppress the implicit conversion defined by the constructor. We can prevent the use of a constructor by **declaring the constructor as `explicit`**.

This because `explicit` constructors can be used only for **direct initialization**, while one context in which implicit conversions happen is when we use the copy form of initialization.

## ***SWAP***

Classes that **manage resources** often also define a function named `swap`.

Important for classes to use with algorithms that reorder elements: such algorithms call `swap` whenever they need to exchange two elements (*e.g.* containers).

*EXAMPLE:*

```c++
class HasPtr {
    friend void swap(HasPtr&, HasPtr&);

    ...
};
```

With the following definition, allocating new memory and using copy constructor:

```c++
inline void swap(HasPtr& lhs, Hasptr& rhs) {
    auto temp_ps = rhs.ps;
    rhs.ps = lhs.ps;
    lhs.ps = temp_ps;
    
    auto temp_i = rhs.i;
    rhs.i = lhs.i;
    lhs.i = temp_i;
}
```

Better resource management:

```c++
inline void swap(HasPtr& lhs, HasPtr& rhs) {
    using std::swap;
    swap(lhs.ps, rhs.ps);
    swap(lhs.i, rhs.i);
}
```