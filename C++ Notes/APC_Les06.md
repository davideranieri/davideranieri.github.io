# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 6 - Pointers Advanced**

## Pointers and references

A reference **is not** an object; hence, **no pointer to a reference**.
A pointer **is** an object; hence, it's possible a **reference to a pointer**.

*EXAMPLE:*

```c++
int i = 42;
int * p;
int * & r = p;                        // r is a reference to pointer p

r = &i;                               // p points to i
*r = 0;                               // i, the object pointed by p, is 0
```

## References **can't** be stored in a vector

*EXAMPLE:*

```c++
std::vector<int> hello;
std::vector<int &> hello;           // ERROR
```

$\Rightarrow$ Containers values **must be assignable**; references are *not assignable*. Other not assignable types are not allowed as components of containers, *e.g.* `vector<const int>` not allowed.

## A reference to `const` may refer to an object that is not `const`

A reference to const restricts only what we can do through that reference

## ***`auto` Specifier***

Aim: store the value of an expression in a variable; to declare the variable, we have to know the type of that expression, and sometimes it can be difficult to determine it. With **C++ 11**, we can use `auto` *type* specifier.

**It deduces the type from the initializer** $\Rightarrow$ an initializer must be provided.

## Traversing a vector

*EXAMPLE:*

```c++
vector<int> v{1,2,3,4,5,6,7,8,9};

for (auto &i : v)                   // for each element in v; note, i is a reference
    i *= i;
```

## Useful use of references

- `for (string s : v)` : s is a *copy* of all v[i]
- `for (string & s: v)` : *no copy*
- `for (const string& s : v)` : *no modify* `v`

## Range-for to access multiple dimensional arrays

*EXAMPLE:*

```c++
int ia[3][4];

size_t cnt = 0;
for (auto &row: ia)
{
    for (auto &col : row)
    {
        col = cnt;                  // give this element the next value
        ++cnt;
    }
}
```

## Pointers and Arrays

In C++ pointers and arrays are closely intertwined; when we use an array the compiler ordinarily converts the array to a pointer to the first element.

*EXAMPLE:*

```c++
string nums[] = {"one", "two", "three"};
string * p = &nums[0];
string * q = nums;                   // equivalent to q = &nums[0]
```

This fact implies various things; one of the most important is that *operations on arrays are often really operations on pointers*

*EXAMPLE:*

```c++
int ia[] = {0,1,2,3,4,5,6,7,8,9};
auto ia2(ia);                       // ia2 is an int* that points to first element of ia
ia2 = 42;                           // ERROR
```

## Pointers arithmetic

Subtracting two pointers gives us the distance between those pointers

## `begin` and `end`

*EXAMPLE:*

```c++
int arr[] = {0,1,2,3,4,5,6,7,8,9};
int * beg = begin(arr);
int * last = end(arr);
```

We can use the **subscript operator** on any pointer, as long as that pointer points to an element (or one past the element) in an array

*EXAMPLE:*

```c++
int ia[] = {0,1,2,3,4,5,6,7,8,9}
int * p = &ia[2];
int j = p[1];                       // p[1] equivalent to *(p+1) aka ia[3]
int k = p[-2]                       // p[-2]equivalent to *(p-2) aka ia[0]
```

**NB:** this last example points out an important **difference** between **arrays** and **library types** that *have subscript operators*: the library types forces the index used with a subscript to be an **unsigned value**. The built-in subscript **does not**

## ***ITERATORS***

*Purpose:* **Access**

We can use subscripts `[]` to access the characters of a string or the elements in a vector. But there is a more general mechanism: iterators.

*The **STL** library defines several other kinds of containers. All the library containers have iterators, but **only a few support subscript operator**.*

*Hint:* think to an iterator as a pointer to access any container.

If we use iterators instead of subscripts, we can change easily he container type.

**Like** pointers, iterator give **indirect access** to an object.
**Unlike** pointers, the **address-of operator** `&` is not used to obtain an iterator; instead, types that have iterators have member functions that return iterator: `begin()` and `end()`.

If a container is empty, the iterators returned by begin and end are equal, both off-the-end iterator.

*EXAMPLE:*

```c++
if (s.begin() != s.end()) { ... };

for (auto it = s.begin(); it != s.end(); ++it) { ... };
```

## Standard container iterator operations

- `*iter` : returns a reference to the element denoted by the iterator `iter`
- `iter->memb` or `(*iter).memb` : dereferences `iter` and fetches the member `memb` from the underlying element
- `++iter` or `--iter`
- `iter1 == iter2` or `iter1 != iter2` : comparison; two iterators are equal if they denote the same element or if they are the off-the-end iterator for the same container

## Operations supported only by **vector** and **string iterators**

- `iter + n` or `iter - n`
- `iter += n` or `iter -= n`
- `iter1 - iter2`
- `<,>,>=,<=`

## Iterator Types

The library types that have iterators define types named `iterator` and `const_iterator` that represent actual iterator types

*EXAMPLE:*

```c++
vector<int>::iterator it1;          // can read and write

vector<int>::const_iterator it2;    // can read but not write
```

## `cbegin` and `cend`

*EXAMPLE:*

```c++
vector<int> v;
const vector<int> cv;

auto it1 = v.begin();               // it1 has type vector<int>::iterator
auto it2 = cv.begin();              // it2 has type vector<int>::const_iterator
```

It's usually best to use a const type when we need to read but do not need to write to an object. In C++ 11 there have been introduced `cbegin` and `cend`

```c++
auto it3 = v.cbegin();              // it3 has type vector<int>::const_iterator
```

