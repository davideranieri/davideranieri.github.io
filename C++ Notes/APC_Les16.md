# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 16 - Streams and I/O**

Interact with the user via its input/output features. This section will present a short introduction to some of the most useful.

C++ uses a convenient *abstraction* called **streams** to perform input and output operations in sequential media such as the *screen*, the *keyboard* or a *file*. A **stream** is an entity **where a program can either insert or extract characters to/from**. There is *no need* to know details about the media associated to the stream or any of its internal specifications.

All we need to know is that **streams are a source/destination of characters**, and that these characters are provided/accepted sequentially (i.e., one after another).

The **standard library** defines a handful of stream objects that can be used to access what are considered the standard sources and destinations of characters by the environment where the program runs.

## Standard Output (`cout`)

On most program environments, the **standard output by default is the screen**, and the C++ stream object defined to access it is `cout`.

For formatted output operations, `cout` is used together with the **insertion operator** `<<`.

The `<<` operator **inserts the data that follows it into the stream that precedes it**. When the text is enclosed between `" " `, the text is printed literally; when they are not, the text is interpreted as the identifier of a variable, and its value is printed instead.

## Standard input (`cin`)

In most program environments, the **standard input by default is the keyboard**, and the C++ stream object defined to access it is `cin`.

For formatted input operations, `cin` is used together with the **extraction operator**, which is written as `>>`. This operator is then **followed by the variable where the extracted data is stored.**

This operation makes the program wait for input from `cin`; generally, this means that the program will wait for the user to enter some sequence with the keyboard. Note that the characters introduced using the keyboard are only transmitted to the program when the ENTER (or RETURN) key is pressed. Once the statement with the extraction operation on `cin` is reached, the program will wait for as long as needed until some input is introduced.

The extraction operation on `cin` uses the type of the variable after the `>>` operator to determine how it interprets the characters read from the input; if it is an integer, the format expected is a series of digits, if a string a sequence of characters, etc.

So, to summarize:

- `cout << arg` means insert (`<<`) to variable `cout`, which turns to be the *standard output*, the *screen*, the content of `arg`.
- `cin >> var` means extract (`>>`) from the content `cin`, which turns to be the *standard input*, the *keyboard*, and put into `var`

## Files

Contents of computer main memory is *transient*. Data needs to be preserved: it must be stored on disks and similar permanent storage device. A **file** is a sequence of bytes stored in *permanent* storage.

At fundamental level, a file is a **sequence of bytes** numbered from 0 upwards. Other notions can be supplied by programs that interpret a *"file format"*.

- To **read** a file: we must know its name, we must open it and, once finished, we must close it.
- To **write** a file: we must name it, we must open it and, once finished, we must close it.

## Opening a file for reading

*EXAMPLE:*

```c++
int main()
{
    cout << "Please enter input file name: ";
    string iname;
    cin >> iname;

    ifstream ist {iname};                       // ifstream is an "input stream from a file";
                                                // opens the file of that name for reading
                                                // i.e., create ist and open the file
}
```

## Opening a file for writing

*EXAMPLE:*

```c++
int main()
{
    cout << "Please enter name of output file: ";
    string oname;
    cin >> oname;

    ofstream ofs {oname}                        // ostream is an "output stream from a file"
                                                // opens the file with that name for writing
}
```

## Implicit close

When a **fstream** object goes out of scope, the file it is bound to is automatically closed.

Termination: reaching the end-of-file terminates the read.

## NO Copy or Assign for I/O objects

We **can't** copy or assign objects of the I/O types.

*EXAMPLE:*

```c++
ofstream out1, out 2;
out1 = out2;                                    // ERROR: no assign stream objects ofstream
ofstream print(ofstream);                       // ERROR: no initialize the ofstream param
out2 = print(out2);                             // ERROR: no copy stream objects
```

Because we can't copy the I/O types, we **can't have a parameter or return type that is one of the stream types**.

- Functions that do I/O typically pass and return the stream through references.
- Reading or writing an I/O object changes its state, so the reference must not be const.

## User-defined output: **insertion operator** `operator<<()`

*EXAMPLE:*

```c++
ostream& operator<< ( ostream& os, const Date& d )
{
    return os << '(' << d.year() 
              << ;,; << d.month()
              << ',' << d.day() << ')';  
}

...

void do_some_printing (Date d1, Date d2)
{
    cout << d1;                                 // means: operator<<(cout, d1);

    cout << d1 << d2;                           // means: (cout<<d1)<<d2
                                                // or     (operator<<((operator<<(cout, d1)), d2);
}
```

## User-defined input: **extraction operator** `operator>>()`

*EXAMPLE:*

```c++
istream& operator>> ( istream& is, Date& d)
{
    int yy, mm, dd;
    if (is >> yy >> mm >> dd){
        d = Date{yy,mm,dd};
    }
    return is;
}
```

## ***FILE MODES AND BINARY I/O***

## File Open Modes

By default,

- an **ifstream** opens its file for **reading**.
- an **ofstream** opens its file for **writing**

There are some alternatives, thanks to file modes:

- `ios_base::**app**`: append
- `ios_base::**ate**`: "at end", open and seek to end
- `ios_base::**binary**`: binary mode
- `ios_base::**in**`: for *reading*
- `ios_base::**out**`: for *writing*
- `ios_base::**trunt**`: truncate file to 0-length

A file mode is optionally specified after the name of the file:

*EXAMPLE:*

```c++
ofstream of1 {name1};                                   // default to ios_base::out
ifstream if1 {name2};                                   // default to ios_base::in
ofstream of1 {name, ios_base::app};                     // append rather than overwrite
fstream fs {"mylife", ios_base::in | ios_base::out};    // both in and out
```

## Text vs. Binary files:

- In binary files, we use sizes to delimit values
- In text files, we use separation/termination characters

**Use text** when you *can*:

- can read it (without a fancy program)
- can debug programs more easily
- text is portable across different systems
- most inforation can be represented reasonably as text

**Use binary** when you *must*:

- image, sound files

## ***STRING STREAMS***

A **stringstream** reads/writes from/to a string rather than a file or a a keyboard/screen

```c++
istring is {s}          // make a stream so that we can read from s
```

## Type vs. Line

- Read a string
- Read a line: `getline(cin, name)` (reads a line into `name`and returns `cin` )

*EXAMPLE:*

```c++
ifstream data {"data.txt"};         // read from file
while (getline(data, line))
{
    istringstream record(line);     // read from a string, bind record to the line just read
    ...
}
```

*EXAMPLE:* word transformation map

Write a program that given one string, transforms it into another.

```c++
void word_transform(ifstream &map_file, ifstream &input)
{
    auto trans_map = buildMap(map_file);
    string text;
    while (getline(input, text)) {
        istringstream stream(text);
        string word;
        bool firstword = true;
        while (stream >> word)        // extract content in stream and put in word
        {
            if (firstword)
                firstword = false;
            else
                cout << " ";
        }
        cout << endl;
    }
}

map<string, string> buildMap (ifstream &map_file)
{
    map<string, string> trans_map;
    string key;
    string value;
    while (map_file >> key && getline(map_file, value)))
        if (value.size() > 1)
            trans_map[key] = value.substr(1);
        else
            cout << "no rule for" + key << endl;
    return trans_map
}

const string& transform (const string &s, const map<string, string> &m)
{
    auto map_it = m.find(s);
    if (map_it != m.cend())             // if word is in the transformation map
        return map_it -> second;
    else
        return s;
}
```

## ***READINGS***

## Positioning in a filestream

To **support random access**, the system maintains a *marker* that determines where the next read or write will happen.

We also have two functions:

1. One **repositions the marker** by seeking to a given position
2. One **tells the current position** of the marker

The library actually defines two pairs of **seek and tell functions**:

- one pair is used by input streams, the other by output streams
- the input and output versions are distinguished by a suffix that is either a **g**, as *getting*, reading data, or **p**, as *putting*, writing data.

*EXAMPLE:*

```c++
fstream fs {name};              // open for input and output
fs.seekg(5);                    // move reading position to 5, the 6th character
fs >> ch;                       // read value at 5 and increment the reading position to 6
fs.seekp(1);                    // move writing position to 1, the 2nd character
fs << 'y';                      // write at 1 and increment writing position to 2
```

- We can use only the **g** versions on a `istream` and on the types that inherit from it, `ifstream` and `istringstream`.
- We can use only the **p** versions on a `ostream` and on the types that inherit from it, `ofstream` and `ostrinstream`.

## There is only one marker

Even though the library makes distinction between the **putting** and **getting** versions of the **seek** and **tell**, it mantains **only a single marker** in a stream - no distinction between **read** and **write** markers.

## Repositioning the Marker

```c++
seekg(new_position);                // set the read marker to the given location
seekp(new_position);                // set the write marker to the given location
                                    // offset some distance ahead of or behind the given starting point
seekg(offset, from);                 // set the read marker offset distance from
seekp(offset, from);
```

## Positioning

Write most of your code in terms of "plain" istream and ostream.

## Using `ostringstream`s

An ostringstream is useful when we need to build up our output a little at a time but do not want to print the output until later.