**<u>OCCF</u>**

_Ortohdox Canonical Class Form_ means having standards to ensure that the low-level operations work the same for all objects.

The main guideline for this is having **four standard methods**, that you will need for almost every class automatically.

- a null constructor
- a destructor
- a copy constructor
- an assignment operator

**Destructors**

-> **Destructors**: are methods that perform tasks associated with the death of an object.

Neither constructors nor destructors actually create or destroy, they just do housekeeping when those events happen.

```cpp
~ClassName()

// This is called automatically when the object is deleted (heap-based object), or removed from the stack (stack-based objects). The latter happends when a methods returns or any other time a variable can go out of scope.
```

When an object goes out of scope, its destructor is called, followed by parent's, and so on. This happens automatically.

```cpp
~Grandparent()

~Parent()

~Child()

Child *c = new Child;
delete c;

// delete c calls the destructors of Child, Parent, and Grandparent
```

**Virtual Destructors**

If the appropriate subclass' destructor wasn't called, then we could potentially have memory leaks or other problems (anything missing from cleanup) – we need the destructor to be polymorphic. In C++, this means a virtual destructor, so that when a pointer is disposed of, the correct destructor for the type is called first (and then the others up the hierarchy).

```java
// In Java, we have an analogous feature, finalizers

void finalize(); // comes from Object class

// This is called when object is garbage collected
```

**Pass-by-value vs Pass-by-reference & Copy Constructors**

There are 2 ways to do an assignment when we are working with pointers.

1. We can assign a pointer to point to the same thing as something else - this is called a _shallow copy_.
2. We can make the memory pointed to by one pointer a copy of that pointed to by the other - this is called a _deep copy_, and means that there are 2 objects now.

In Java, this is not too big an issue, since the syntax is obvious. In C++, there are rules as to when a deep copy is done.

```cpp
Person q("Riley"); // this does not involve taking data from another object
Person p = q; // this is making a copy of another object
Person r(p); // so is this

// in both the ones where there is a copy being made, the copy constructor would be called
```

A **copy constructor** takes a _reference parameter_ instead of a value parameter.

```cpp
void dostuff(int& x) {...}

// The parameter x is now a reference parameter
// I do NOT need to dereference the pointer, C++ does it for me
```

```cpp
#include <iostream>
using namespace std;

void byReference(int &x) {
    cout << "ByReference recv: " << x << endl;
    x = 20;
    cout << "ByReference set to: " << x << endl;
}

void byValue(int x) {
    cout << "ByValue recv: " << x << endl;
    x = 20;
    cout << "ByValue set to: " << x << endl;
}

int main() {
    int x = 10;
    byValue(x);
    cout << "After ByValue: " << x << "\n" << endl;
    // This would just print 10, since internal changes to the value does not effect the actual value.

    x = 10;
    byReference(x);
    cout << "After ByReference: " << x << "\n" << endl;
    // This would print 20, since the actual value stored at the address is now changed to 20.
}
```

**Why reference parameters?**

It is just saying "allow the argument to be alterable". Also, pointers are small, objects are usually big, so it saves us time and space to use pointers instead.

There is a danger with reference parameters, that we MIGHT accidentally change them. To avoid this, use the `const` keyword.

```cpp
void dostuff(const int& x) {...}

// It is now illegal to change x
```

`const` can be put on a method name too, e.g. `void doIt() const`. This delcares that the method does not change any data members of the object it belongs to. You should use `const` wherever you can, especially with getter methods. It goes in both header and implementation files.

**Copy Constructors**

It looks like a constructor, except it takes a reference parameter to an object of the same type.

```cpp
Book::Book (const Book& source) { // const should be used since we're not modifying the object we're taking the copy from
    title = source.title;
    author = source.other;
}

// Notice that we don't need to dereference with -> when using a reference parameter

// If Book were organized as a subclass of Item, we would need to call the superclass copy constructor

Book::Book (const Book& source) : Item(source) {...}
```

-> **Default copy constructors**: do a recursive copy of the fields. Each simple field is copied directly, and every field that's an object has its copy constructor called recursively. Recursive does NOT mean deep: these are shallow copies.

A copy constructor is called when:

- We make an object that involves taking data from another object

- Passing the object as a value parameter

- Returning an object of this type as a result of a method

In Java, `Object` has a method named `clone` that you can call via `super` (`super.clone()`). To meet OCCF, your class MUST implement a cloneable interface and override the `clone()` method. `clone()` might throw `CloneNotSupportedException`, so you have to throw or handle it as well.

```java
class MyClass implements Cloneable {...
    public Object clone() throws CloneNotSupportedException {
        Object newThing = super.clone();
        return newThing;
    }
}

// This is a shallow copy. To make it deep, you need to add your own code
```

**Assignment Operator**

-> **Assignment**: is a message send to the object on the left of the =, with the object of the right as an argument.

```java
Person p,q;
p = q;

// is a message sent to p asking it to "set itself using q"
```

So assignment mostly looks like any other method when we override it:

```cpp
// Remember to make the parameter const to protect it from accidental modifications
MyObject& MyObject::operator = (const MyObject& right) {
    data = right.getData(); // data is this->data implicitly
    // copy over whatever else we want
    return (*this);
}

// Assumes that MyObject has a member data, and a method getData() to access it

// There is a reference in the return type, since it's actually modifying the "left" operand and then returning the new version
```

We can also overload other operators (e.g. +, ++, <<, ...).

Java does NOT let us change the behaviour of operators ourselves, but you could define a similar `assign()` method if you wanted.

**Q) What are the four standard methods required for OCCF?**

- a null constructor

- a destructor

- a copy constructor

- an assignment operator

**Q) Predict the output:**

```cpp
void byConstReference(const int &x) {
    cout << "byConstReference recv: " << x << endl;
    x = 20;
}

int main() {
    int x = 10;
    byConstReference(x);
    cout << "After byConstReference: " << x << endl;
}
```

This will throw an error since a constant reference can not be modified.

**Q) If we have an Item class, what is the header for a copy constructor?**

```cpp
Item(const Item &source)
```

**Q) How is the assignment operator different from the copy constructor?**

Copy constructor creates a new object as a copy of an existing object, while the assignment operator copies values between 2 already existing objects.
