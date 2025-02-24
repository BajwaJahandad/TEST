**<u>C++ Basics</u>**

Defining classes in C++:

```cpp
// interface

class MyNumber {
    private:
        int value;
    public:
        myNumber()
        myNumber(int); // just need the parameter types, not names!
        void increment();
        void print();
};

// implementation

MyNumber::MyNumber() { value = 0; }
MyNumber::MyNumber(int value) { this->value = value; }
void MyNumber::increment() { value++; }
void MyNumber::print() { cout << value << endl; }
```

**Note: typically, the file ins separated into header (interface) and cpp (implementation) files.**

Syntax for separate method definition:

```
[returnType] Class::method(params) { body }
```

**Separate Compilation**

We create 2 different files for each class.

-> **.h file** for the interface: - the access modifier sections (public, protected, private). - the instance variables. - the method headers.

-> **.cpp file** for the implementation: - the full method signatures. - the body of the methods.

**Pointer Operations**

C++ and C use explicit pointer, but C++ has tools that are easier to use than malloc/free. C++ has a high-level memory allocation facility similar to Java, but it is more explicit.

```cpp
Node* myList; // declaration of a pointer of type Node
myList = new Node; // calls null constructor, with no ()
```

We don't use () with the constructor call if no paramentes are sent.

**Memory Allocation in C++**

C++11 has the _nullptr_ keyword that can be used to check if a pointer is null.

```cpp
int* ptr = nullptr;

if (ptr) {
    cout << "true";
} else {
    cout << "false";
}

// nullptr is convertible to bool
```

We can also access addresses directly in C, but you rarely need to do this in C++.

```cpp
int x = 5;
int* intPtr = &x;
```

Mostly, just use pointers same as Java. Using new produces a pointer, and use pointers to arrays.

**Dereferencing in C++**

Dereferencing is done the same way as in C, using \*.

```cpp
int x = 1;
int* intptr = &x; // points to the location of x
*intPtr = 5; // assign 5 to what the pointer points to
```

If class Node has an int data member inside it, and myNode pointer to a Node,

```cpp
int value = myNode->data;
```

The arrow operator dereferences myNode, and then gets the data field.

To return the memory in C++ after you're done with an object, you delete it.

```cpp
delete myNode;
```

After a delete though, the memory is freed, but the pointer still points to the same address. Meaning, the data that was there is not erased, but it is free to eventually be overwritten. Deleting objects too early leads to undefined behaviour.

**Memory Leak**

If we don't properly reclaim memory, it is unusable for the rest of the program's life. This is called a **memory leak**.

In a linked structure, delete only frees the immediate memory pointed to. e.g.

```cpp
Node* newNode;
newNode = new Node;
... // build a list with many nodes
delete newNode;
```

The immediate node is freed, but the nodes linked to it are not.

This is why we need **destructors** to correctly free all the memory.

**Constructors in C++**

They can look exactly like methods in Java, but they also support a different syntax.

```cpp
MyNumber:MyNumber() : value(0) {}
```

The method has no body (it can have one), and the initialization is done in the header.

**Methods**

In C++, not everything is organized around a class like in Java. An executable has a _main function_, not a method belonging to a class, that sending messages to various objects and gets the work started.

We will often place the main function in its own .cpp file, no header needed.

```cpp
int main() {
    cout << "Hello world!" << endl;
    return 0; // can be omitted, 0 is returned by default
}
```

**Libraries**

C++ programs also need libraries, for example iostream library to use cout and cin.

```cpp
#include <iostream>
```

These special lines beginning with # are called **preprocessor commands**, and are interpreted before the compilation of the program begins.

**Namespaces**

C++ also has the ability to use different namespaces, and we have to say we want the standard one.

```cpp
#include <iostream>

using namespace std;
```

A using directive tells the compiler to bring identifiers from a specified namespace into the current scope.

```cpp
std::string myString;
```

can be written as

```cpp
string myString;
```

"using namespace std" is sometimes considered as bad practice, because of potential collosions that you could get from using other namespaces at the same time. Instead, you can do

```cpp
using std::cout;
using std::endl;
using std::string;

std::[somethingFromStd]
```

for identifiers you need the most.

The #include preprocessor command is also used in separate compilation. When one file needs to refer to another, we use it to copy the source from that file e.g., LinkedList.cpp needs to know about Node.h to access its methods, so you would use #include "Node.h" at the top of the LinkedList.cpp file.

We also need to use

```cpp
#pragma once
```

at the top of the .h file to include the file only once. This replaces the #ifndef statements. It stops you from including something more than once in one file compilation, but does not protect you from circular includes.

**Forward Reference**

A forward reference in C++ is a statement that does not include any code, but just says that the definition of the class is coming up in the future. If you can get away with a forward declare, you should, so you don't run into issues with circular includes.

```cpp
class C2;
```

**When to include**:

- When extending that class (.h): need to know about the members of the superclass.
- When implementing that class (.cpp): need to know about its own members.
- When making an instance of another: need to know the constructor of that class.
- When invoking any other method of another class: need to execute the code of that method.

**When to forward reference**:

- For a parameter type.
- For a return type.
- For an instance variable type.

You should also not include in a subclass of a subclass, when the first subclass is already including the parent class.

**Arrays in C++**

C++ does not do runtime bounds checking, so technically it is possible to do x[47] = 2 on a 4 element array. Like other languages, an array is just a collection of contiguous memory.

```cpp
char str[10]; // 10 chars
char* str; // pointer to a char
x = str; // legal, x points to the start of char array
```

Now, str[3] and \*(x + 3) refer to the same location in memory/array element (because each char is 1 byte long). Don't use pointer arithmetic unless speed is an issue, since you could write the wrong offset and subscripting is just more readable.

**Pointers and Arrays in C++**

If you declare an array parameter, it is converted internally to a pointer. You can declare a parameter of pointer type and pass it as an array.

```cpp
void fooBar(int* a) {
    for (int i = 0; i <= 10; i++) {
        cout << a[i];
    }
}
```

The variable a would BE a pointer internally even if you declared the parameter to be of type int a[10].

You can have an array of pointers as well, if you need to have an array of objects created with the _new_ keyboard.

```cpp
Thing* x[10]; // array of 10 pointers to Things
x[2]->print(); // x[2] is dereference with the '->' and the print method is called
```

**Self Reference**

When a class needs to refer to itself, we this the _this_ keyword.
