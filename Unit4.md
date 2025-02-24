**<u>C++ Details, Inheritance and Polymorphism</u>**

**What is an activation record?**

An activation record is a data structure used by the _call stack_ to manage function calls. It starts from the main function and calls other functions in order.

**What is the heap?**

The heap is where all _dynamic memory_ comes from in any language. When you do a "new" in Java or C++, the object that is allocated is on the heap.

This memory can be reclaimed,
C: free
C++: delete
Java: garbage collected

The heap is unorganized - random memory allocation.

**What is the stack?**

Stack-based storage is what you get when variables are declared with a specific scope and lifetime according to the variable declaration rules of the language. It is in a stack frame and is allocated when routine is called, and removed when routine returns.

It has separate activation records for each call, it allows a routine to be called multiple times before returning (recursion), and each frame contains its own variable and parameter values.

| Stack    |
| -------- |
| Method 4 |
| Method 3 |
| Method 2 |
| Method 1 |

Each row in this table is a frame/activation record.

**Activation Records**

A pointer is normally a stack based variable, and the memory it points to is on the heap. We get memory leaks if we return without releasing what a pointer points to, because the stack-based pointer points to an instance in the heap, but the pointer is deleted and the heap is not, causing a _dangling pointer_.

Primitive types are stack-based, everything else is an object in Java. Object references can be stack-based, but the objects THEMSELVES are NOT on the stack.

In C++, it is NOT possible to declare object that are not heap-based.

```cpp
// heap-based
MyNumber* ptr;
ptr = new MyNumber;
ptr->print();
ptr->increment();
ptr->print();
cout << endl << "That's all, folks!";
```

If I have a stack-based object, it acts like and int or a float in Java: there is no pointer, and we work with the object directly.

```cpp
// stack-based
MyNumber theNumber; // No new
theNumber.print(); // No ->
theNumber.increment();
theNumber.print();
cout << endl;

// Note that there is no () on the constructor
// Putting () will turn it into a forward reference to a function
```

**Stack-based in C++**

- No memory disposal.
- `theNumber` will disappear when this function returns.
- There is no _obvious_ memory leak.
- If `theNumber` contains a pointer to something else as a member, we still need a destructor for the object.

```cpp
int main (void) {
    MyNumber theNumber; // stack-based
    theNumber.print();
    theNumber.increment();
    theNumber.print();
    cout << endl;

    MyNumber *ptr = new MyNumber; // heap-based
    ptr->print();
    ptr->increment();
    ptr->print();
    cout << endl <<"That's all, folks!" << endl;
    return 0;
}
```

In either case, when we make an object in C++, a constructor is called. We use one and not the other since pointers are NECESSARY for the dynamic aspects of OO.

In Java, some things are referenced by pointers, others are not, and you don't get to choose.

In C++, you can treat objects likes simple variables, but you don't want to most of the time. It also avoids requiring you to have a pointer if it's unnecessary for what you're doing.

1. myDog.bark();
   myDog is a stack-based object, since there is no dereference happening with ->.

2. studentA->getGPA();
   studentA is a heap-based object, since there is a dereference.

3. zooA->getFish()->getWeight();
   zooA is a heap-based object, and so is the fish (which has the getFish() method).

4. zooB.getFish()->getWeight;
   zooB is a stack-based object, and the fish is a heap-based object.

**Inheritance in Java**

A class can extend another class.

```java
public class Shape
{ ... }

public class Rectangle extends Shape
{ ... }

public class Circle extends Shape
{ ... }
```

**Inheritance in C++**

In C++ we add : and the name of the parent class.

```cpp
class Rectangle : public Shape
{ ... };
```

The colon (:) is equivalent of "extends" in Java. The access modifier (public) is necessary (we will just use public). It means the public members of the superclass are also public in the subclass.

We can also have _multiple inheritance_ in C++.

```cpp
class BlinkingSquare : public Rectangle, public BlinkingItem
{ ... };
```

When creating a subclass, you need to `#include` the header file of the superclass, forward reference is NOT enough.

```cpp
#include "Shape.h"

class Rectangle : public Shape
{ ... };
```

This is because the compiler needs to know the full definition of the base class to perform inheritance properly. A forward declaration only tells the compiler that `Shape` exists.

**Polymorphism in Java**

In Java, polymorphism is always "on". The version of the method that is executed depends on the _dynamic type_ of the object variable.

```java
ListItem item1 = new CharAtom();
item1.print(); // CharAtom's version of print is executed
```

**Polymorphism in C++**

In C++, polymorphism is "off" by default. You need to indicate that the method should be polymorphic by using the `virtual` keyword.

A virtual method can be redefined for each subclass where it appears and also in the class itself.

The `virtual` keyword is only used in the header file in the superclass.

```cpp
class Parent {
    public:
        virtual void myPolymorhpicMethod();
};
```

When a virtual method is called _through a pointer_ to an object, C++ determines which version of the method to call based on the type of the object that is being pointed to at runtime. This uses _dynamic class binding_ + _dynamic method message mapping_: polymorphism as we saw in Java. If we change a pointer so that it points to different objects, we will get calls to diffrent instances of the virtual method.

In C++, if there is no

- virtual method, there is no polymorphism.
- pointer, there is no polymorphism.

It is NOT automatic, as in Java.

**Override Keyword**

```cpp
class Base {
    public:
        virtual void print();
};

class Sub : public Base {
    public:
        void print() override; // override goes in the header file only
};
```

This is not necessary, but recommended. It tells the compiler that you intend to override a virtual method from the superclass. If you make typo or forgot to make the method virtual in the superclass, you will get a compilation error.

**Abstract in C++**

The `abstract` keyword doesn't exist in C++. Virtual method only handles polymorphism and does not force the subclasses to implement it. To make an abstract class in C++, we need to introduce a pure virtual method into the class.

```cpp
class Parent {
    public:
        virtual void myPolymorphicMethod() = 0;
};
```

This is now a pure virtual method, and it forces each concrete subclass to implement it. Same as `abstract` in Java.

Techincally, you can still compile your code without implementing a pure virtual method in the subclass, IF you don't create any instances of the subclass. You can also have an implementation of a pure virtual method, but typically you would not.

To make a class abstract, you need to have at least 1 pure virtual method, even if you want the class to have no methods.

```cpp
class X {
    virtual void dummyMethod() = 0;
};
```

Or, in more practical cases, you would have a destructor to clean up allocated memory.

Abstract classes with no implementation will only have a header file. If you need to have implementation, it will go into a cpp file.

```cpp
class ListItem {
    ...
    virtual void print() = 0;
};

class IntegerValue : public ListItem {
    ...
    public:
    ...
    // Pure virtual method print() MUST
    be overridden
    void print() { cout << value; }
};

class DoubleValue : public ListItem {
    ...
    public:
    ...
    void print() { cout << value; }
};

class Node {
    ...
    public:
    ...
    // This print prints itself,
    //and all linked nodes too
    void print() {
        value->print();
        if (next != nullptr) {
            cout << ", ";
            next->print();
        }
    }
    // Just print the value in the node
    // (pass the message to the value)
    void printValue() {
        value->print();
    }
};
```

```cpp
class List : public ListItem {
    ...
    public:
    ...
    // Don't forget the "*" here!
    void add(ListItem *item) {
        Node *newNode = new Node(item);
        newNode->setNext(top);
        top = newNode;
    }

    // The recursive version
    void print() {
        cout << "[List: ";
        if (top != nullptr)
        top->print();
        cout << "]";
    }


    // Non-recursive traversal version
    // (it still uses recursion
    // indirectly to print sub-lists)
    void print() {
        Node *current = top;
        cout << "[List: ";
        while (current != nullptr) {
            current->printValue();
            current = current->getNext();
            // Print commas except
            // after the last item
            if (current != nullptr)
            cout << ", ";
        }
    cout << "]";
    }
};
```

**Downcasting**

**Consequences of Polymorphism**

When we use polymorphism, we'll be using superclass variables holding instances of subclasses. This means that we can't access methods within subclasses if the reference defined is of the superclass, since the supercalss does not have that method. In this case, we cast the superclass into the base class explicitly.

e.g.

```cpp
class ListItem {
    public:
        virtual void print() { std::cout << "ListItem\n"; }
};

class CharAtom : public ListItem {
    public:
        void print() override { std::cout << "CharAtom\n"; }
        void specialCharMethod() { std::cout << "Special method!\n"; }
};

int main() {
    ListItem* x = new CharAtom(); // Base class pointer holding derived class object
    x->print();  // ✅ Calls CharAtom's print() due to polymorphism
    x->specialCharMethod();  // ❌ ERROR: ListItem doesn't have this method

    delete x;
    return 0;
}
```

This is called _downcasting_, or _reverse polymorphism_. The problem here is that we have to be sure that the Object really contains what we are casting it to. In a list, if you knew you put an `IntAtom` into it, you can safely cast it back to an `IntAtom`, but if you are mixing and matching types, you don't know.

To combat this, we will use dynamic casting. This way, I can safely cast `aPet` back to a `Dog` variable.

```cpp
Animal* aPet;
aPet = new Dog; // Dog extends animal

Dog* d = dynamic_cast<Dog*>(aPet);

if (d == nullptr) {
    cout << "aPet was not a Dog!" << endl;
}
```

The <> contains the pointer type I am trying to get i.e. what I am downcasting to. If unsuccessful, `nullptr` is returned. You must ALWAYS use `dynamic_cast` in C++ when doing reverse polymorphism - same as an `instanceof` error check in Java, and check for `nullptr` with an appropriate error message/reaction.

**Dynamic Class Binding**

Since we have a declared type for any variable, that is called the static class of the variable i.e. the variable itself will ALWAYS be this type.

But, we can assign any subclass to it and talk about the type of the value the variable currently holds. This is the _dynamic class_ of the variable.

```cpp
Animal* aPet = new Dog;

// Animal is the static class/type
// Dog is the dynamic class/type
```
