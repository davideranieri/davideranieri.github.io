# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 8 - Classes: part 2**

## ***`friend`***

- Members defined after a public specifier are accessible to all parts of the program
- Members defined after a private specifier are accessible to the member functions of the class but are not accessible to code that uses the class
- A class can *allow* **another class or function** to access its nonpublic members by making that class or function a `friend`

*EXAMPLE:*

    // friend declarations for nonmember added in the class
    class Sales_data {

        friend Sales_data operator+(const Sales_data&, const Sales_data&);
        public:
        private:
    };

    // declaration for nonmember parts of the Sales_data interface
    Sales_data operator+(const Sales_data&, const Sales_data&);

A friend declaration **only specifies access**. **IT IS NOT** a general declaration of the function. We **must** also declare the function separately from the friend declaration, usually in the same header as the class itself.

## **`static` Class Members**

Members that are *associated with the class, rather then with individual objects of the class type*.
From a memory efficiency standpoint, there'd be no reason for each object to store the rate.

For a Class:
- `friend` does not belong to the class
- `static` is a member of the Class

Static members can be `public` or `private`. Type can be whatever.

-  **not bound** to **any** object
-  **do not have** `this` pointer
-  **can access** through a **scope operator** `::`; member functions can use static members directly, without the scope operator

Even though static members are not part of the objects of its class, we can use an object, reference or pointer of the class type to access a static member.

*EXAMPLE:*

    Account ac1;
    Account *ac2 = &ac1;

    r = ac1.rate();                     // through object or reference
    r = ac2->rate();                    // through pointer

- **can define** static member function **inside or outside** of the class body. When we define a static member outside the class, we do not repeat the static keyword. The keyword `static` appears **only with the declaration inside the class body** (as `friend`).

They **are NOT defined** when we create objects of the class; this implies that:
- they are *not initialized* by the class constructor
- we may not initialize a static member inside the class
- we *must define and initialize each static data member outside* the class body

Like global objects, static data members are defined outside any function.

We define a static data member similarly to how we define class member functions outside the class. Put the definition of static data members in the same file that contains the definitions of the class non-inline member functions (.cpp file).

## ***CLASS SCOPE***

## Scope

It's a **region** of program text. It keeps things local.

1. Global scope: outside any language construct, *e.g.* before `main()`
2. Local scope: between `{}`
3. Statement scope: *e.g.* for statement
4. Class scope

A name in a scope can be seen from within its scope and within scope nested inside; **only after declaration** (*exception*: class members can be used within the class before they are declared)