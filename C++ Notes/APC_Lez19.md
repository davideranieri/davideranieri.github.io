# **Algorithms and Parallel Computing - C++: Notes**

# Lesson 19 - Templates

STL allows us to define and use collections of different types; for example, the element type is a "parameter" of the vector. 

We can define our own *parameterized types,*, called **templates**.

Templates are the basis for generic programming in C++. It is also called **parametric polymorphism**. It guarantees great flexibility and performance.

Templates are used to provide **generic definitions of functions or classes**, a **blueprint** that then is transformed in a specific function or class.

- **Function Templates**: *define logic* behind the algorithms that *work for multiple data types*.
- **Class Templates**: *define generic class patterns* into which *specific data types* can be plugged to produce new classes.

## Template definitions

- **Class Template**

        template<typename T, int N>
        class Buffer { };               // between < > goes the parameter list

- **Function Template**

        template<typename T, int N>
        void fill (Buffer<T,N> & b) { }

## What is a Template parameter?

It is a special kind of parameter that can be used to **pass a type as argument**.

Just like regular function parameters can be used to pass values to a function, template parameters allow to pass both **values** (`constexpr`) and also **types** to a function or a class.

## Template instantiations

When the compiler generates a class, function or static data members from a template is called **template instantiation**. Using the previous example of template definitions:

    Buffer<char, 1024> buf;

    fill(buf)

Templates are **not** normal functions or classes. At that moment, when an instantiation is required, the compiler generates a function or a class **specifically** for those arguments from the template.

## Templates compilation

When the compiler sees the definition of a template, it **does not** generate code; it generates code only when we instantiate a specific instance of the template.

*This affects how we organize our source code and when errors are detected.*

- **Ordinarily**, when we call a function, the compiler needs to see only a declaration for the function. Similarly, when we need use objects of class type, the class definition must be available, but the definitions of the member functions need not be present. Class definitions and function declarations in header files, definitions of ordinary and class-member functions in source files.
- When we define templates: to geneaate an instantiation, the compiler needs to have the code that defines a function template or class template member function. As a result, **headers for templates typically include definitions as well as declarations**.
- **Compilation errors** are mostly **reported during instantiation**. Three stages during which the **compiler** might flag an error:
  1. When we parse the code of the template itself: the compiler generally can't find many errors at this stage
  2. When the compiler sees a use of the template:
      - **function template**: the compiler will check that the number of the arguments is appropriate. It can also detect whether two arguments that are supposed to have the same type do so
      - **class template**: the compiler can check that the right number of template arguments are provided but not much more
  3. During instantiation: at this stage that **type-related errors** can be found. These errors may be reported at **link time**.

## ***FUNCTION TEMPLATES***

Special functions that can operate with generic types, whose implementation can be adapted to work on more than one type or class without repeating the entire code for each type. Same code is **re-used** for different types.

In C++ this is obtained with **template parameters**.

*EXAMPLE:*  `max` function

    template<typename T>
    T max (T a, T b) {
        if  (a>b)
            return a;
        else
            return b;
    }

*EXAMPLE:*  `compare` function

    template <typename T>
    int compare (const T & v1, const T & v2) {
        if (v1<v2) return -1;
        if (v2<v1) return 1;                                        // in this way, used only < operator, not >
        return 0;
    }

## Writing Type-Independent Code

The **function parameters** in the template are **references to const**. We ensure that our function can be used on types that can't be copied. Most types, *including* the built-in types and *except* for  `unique_ptr` and the I/P types (see lesson 16), **do allow copying**

## Template + STL Container 

    template< typename container_type>

## Functions and Function Templates

If a function template is not suitable for all data types, it is necessary to **overload** (specialize) function templates by using normal functions for specific data types.

The logic for comparing C-strings is different from comparing integer and floating point data types: it requires the normal function to be defined but not the function template.

We can use **both the normal function** and **function template in a same program**.

## ***CLASS TEMPLATES***

**Blueprint** for generating classes.

The compiler **can't** deduce the template parameter(s) type for a class template. We **must** supply additional information inside `< >`.

*EXAMPLE:*

    std::vector v;                      // ERROR

    // basically, vector<T> is:
    template<typename T> class vector {
        private
            int sz;
            T * elem;
        public:
            T & operator[ ] (int n) { return elem[n]; }
            int size() const { return sz; }
            void push_back(const T & d);
            ...
    };l

## Final Considerations

**Problems**:

- Poor error diagnostics
- Delayed error messages
- All templates must be fully defined in each translation unit, so **place template definitions in header files**

**Recommendation**:

- Use template-based libraries, such as the C++ standard library