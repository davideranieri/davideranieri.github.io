# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 5 - Functions overload**

## Overloaded functions

Functions (methods) that have the *same name* but **different parameter** lists and that **appear** in the **same scope** are **overloaded**

*EXAMPLE:*

```c++
void print(const char *cp);
void print(const int ia[], size_t size);

int j[2] = {0,1);

print("Hello World");               // calls print(const char*)
print(j,2)                          // calls print(const int*, size_t)
```

Overloaded functions **must differ** in the **number** *or* the **type(s)** of their parameters

*EXAMPLE:*

```c++
Record lookup(const Account&);
bool lookup(const Account&);        // ERROR: only the return type is different
```

## Calling an overloaded function

**Function matching** (also known as **overload resolution**) is the process by which a particular function call is associated with a specific function from a set of overloaded functions

For any given call to an overloaded function, there are three possible outcomes:

- the compiler finds **exactly** one function that is a best match
- *Error*: **no match**
- more than one function that matches; *Error*: **ambiguous call**

1. **candidate functions**: identify the set of overloaded functions considered for the call
2. **viable functions**: select those that can be called with the arguments in the given call

Viable functions: a function must have the *same number of parameters* as there are arguments in the call; type of each argument must **match** or **be convertible to the type**

*EXAMPLE:*

- `f(int)` is viable because a conversion exists that can convert the argument of type double

- `f(double, double)` is viable because a default argument is provided for the function second parameter and its first parameter is of type double, which exactly matches the type of the parameter

## Finding the best match

- `f(int)` requires to convert the argument from double to int
- `f(double, double)` is an exact match for this argument

An exact match is better than a match that requires a conversion

## Overloading and `const` parameters

A parameter that has a top-level const is indistinguishable from one without a top-level const. We can overload based on whether the parameter is a *reference* or a *pointer* to the const or non-const version of a given type.

*EXAMPLE:*

```c++
Record lookup(Account &);           // takes a reference

Record lookup(const Account&)       // takes a const reference
```

## Overloading member functions of a class

As with non-member functions.

We can overload a member function based on whether it is `const`.

*EXAMPLE:* 

```c++
class C{

    f() const;

    f();
}
```

**We can only call `const` member functions on a `const` object**