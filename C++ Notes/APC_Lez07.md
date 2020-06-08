# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 7 - Classes: part 1**

## Classes

**User-defined type**, specifies a blueprint for objects.

- data members
- member functions (methods)

Member functions can define the meaning of:

1. creation (constructor)
2. initialization
3. assignment
4. copy
5. cleanup (destructor)

## C++ general syntax

    class X {
    public:                             // interface to users, accessible by all
        ...
    private:                            // implementation details, accessible by members of this class only
        ...
    }:

Members are accessed using `.` for objects and `->` for pointers.

Operators such as `+`,`!`,`[]` can be defined

Class members are **private by default**.

Struct is a class where (all) members are public by default.

## Public/Private benefits

- To provide a clean interface, *i.e.* the tools that can be used from the user, the outer world
- To mantain an **invariant**
- To allow a change of representation
- To support better code evolution, thanks to internal representation hidden (**information hiding principle**)

## Invariants

A rule for what constitutes a valid value is called an **invariant**.

*EXAMPLE:* the invariant for Date ("a Date must represent a date in the past, present or future") is unusually hard to state precisely.

If we **can't think of a good invariante**, we are probably dealing with **plain data**; if so, **use a Struct**.


## Structs - C version

*EXAMPLE:*

    struct Date {
        int y, m, d;
    };
    
    Date my_birthday;

    // helper functions

    void init_day(Date& dd, int y, int m ,int d);

    void add_day(Date& dd, int n);

    init_day(my_birthday, 6, 8, 1973);                      // RUN TIME ERROR

## Structs - C++ v.0.1

    struct Date {
        int y, m, d;

        Date(int y, int m, int d);      // constructor

        void add_day(int n)
    };

## Structs - C++ v.0.2

    class Date {
    private:
        int y, m, d;
    public:
        Date(int y, int m, int d);      // constructor
        void add_day(int n);
        int month() { return m; }
        int day() { return d; }
        int year() { return y; }
    };

More refined:

- file Date.hpp

        class Date {
            private:
            int y, m, d;
            public:
            Date(int yy, int mm, int dd);
            void add_day(int n);
            int month()
        };

- file Date.cpp

        Date::Date(int yy, int mm, int dd) : y{yy}, m{mm}, d{dd} { ... };
        void Date::add_day(int n) { ... }
        int Date::month() { return m; }

If `Date::` is forgotten, the function will be seen as a global nonmember function, so not access for example.

## ***`this`***

With the exception of `static` members, **when we call a member function we do so on behalf of an object**.

Member functions **access the object** on which they were called through an *extra, implicit parameter* named `this`

When we call a member function, `this` **is initialized with the *address* of the object on which the function was invoked.**

For example, when we call `my_birthday.month();` the compiler passes the address of `my_birthday` to the implicit `this` parameter. It is as if the compiler rewrites this call as

    Date::month(&my_birthday)

which calls the `month` member of `Date` passing the address of `my_birthday`

Inside a member function (in both declaration, definition and so execution), we can refer directly to the members of the object on which the function has been called. We do not have to use a member access operator (`.` or `->`) to use the members of the object to which  `this`points. Any direct use of a member of the class is assumed to be an implicit reference through `this`; When `month()` uses `m`, it is implicitly using the member to which this points: it is as we had written `this->m`.

It is illegal for us to define a parameter or variable named `this`. 

Inside the body of a member function, we can use `this`. As seen above, it would be legal, although unnecessary. to define `month()` as `int month() { retun this->m; }`.

`this` is a **const pointer**: we *CAN'T* change the address that `this` holds.

## Const member functions

Distinguish between functions that can modify (mutate) objects and those that cannot (const member functions).

*EXAMPLE:*

    class Date {
        public:

        int day() const;
        void add_day(int n);
    };

    const Date dx{2008, 11, 4}
    int d = dx.day();                   // OK
    dx.add_day(4);                      // ERROR

## Interfaces and "Helper functions"

Keep a class interface (set of public functions) minimal.

When we keep the class interface simple and minimal, we need extra **helper functions** *outside* the class (so **helper functions are non-member functions**).

## ***OPERATORS***

## Operator overloading

- You can define only existing operators
- You can define operators only with their **conventional number** of operands, *e.g.* NO unary `<=`, NO binary `!`
- An overloaded operator must have **at least** *one user-defined type* as operand

*EXAMPLE:*

    int operator+ (int, int);                           // ERROR: can't overload built in +
    Vector operator+(const Vector&, const Vector&);     //OK

## Advices

- Overload operators only with their conventional meaning
- Avoid overload `*`, `&&`, `||`, `!`

## Operators non-member functions

- Same number of parameters as the operator
- They need to access to all data members of type: typically must be declared as **friend**

## Operators member functions

- First operand (left hand) is **bounded to `this`**

*EXAMPLE:*

    // non-member:
    Sales_data operator+(const Sales_data& lhs, const Sales_data& rhs)
    {
        Sales_data ret;
        ret.units_sold = lhs.units_sold + rhs.units_sold;
        ret.revenue = lhs.revenue + rhs.revenue;
        return ret;
    }
    // member:

    class Sale_data{
        ...
        public:
        Sales_data operator+(const Sales_data &rhs);
    };

    Sales_data Sales_data::operator+(const Sales_data)
    {
        Sales_data ret;
        ret.units_sold = units_sold + rhs.units_sold;
        ret.revenue = revenue + rhs.revenue;
        return ret;
    }

## Member or Non-Member?

- **MUST** be *member*: **`= [] () ->`**
- **SHOULD** be *member*:
  - Compound assignments: **`+= -=`** `/= %= ^= *=`
  - Modify operator `**++ -- ***`
- **BETTER** be *non-member*:
  - Arithmetic operators `+ - * %`
  - Bitwise operators `& | ^`
  - Equality operators `< > <= => != ==`
  - Relational operators `! && ||` 
- **BETTER** *not overloaded*: `* && || !`
- **CAN'T** be *overloaded*: **`::`** `.* . ?:`

## Defining a function to *return `this` object*

*EXAMPLE:*

    class Sales_data {
        public:
        ...
        Sales_data& operator+=(const Sales_data&)
        // different from above! Here no rhs: because takes as input this and return this (modified)
    }

`+=`, etc. **must return a reference**.

The object on which `+=` operator is called represents the left-hand operand of the assignment; the right-hand operand is passed as an *explicit* argument

    Sales_data& Sales_data::operator+=(const Sales_data& rhs)
    {
        units_sold += rhs.units_sold;
        revenue += rhs.revenue;

        return *this;                   // return the object on which the function was called
    }

`total += (trans)`: the address of `total` is bound to the implicit `this` parameter and right-hand side is bound to `trans`.

Note the return type and return statement.

The built-in assignment operators return their left-hand operand as an lvalue. To return an lvalue, our operator **must** return a *reference*, because the left-hand operand is a `Sales_data` object, the return type is `Sales_data&`.

We *do not need to use the implicit* `this` pointer to access the members.