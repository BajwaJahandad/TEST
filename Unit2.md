**<u>Pointers, Inheritance and Polymorphism</u>**

**Pointers**

OO languages are based on pointers since many things must occur at runtime, e.g. dynamic binding, method-message mapping, polymorphism. In Java and Javascript, pointers are implicit, in C++, we need to handle pointers explicitly.

In Java, an object variable holds a _reference_ to an object, this is a pointer abstraction. Most operations related to pointers are not explicit, e.g. using 'new' for dynamic memory allocation, not expicitly dereferencing pointers, and don't free memory when done with it (automatic garbage collection).

**Abstract Classes vs Interfaces**

**Containment**

-> **Containment**: is when an object contains and instance of another one of its data members, e.g. a Car has an Engine.

This is also called:

- Composition
- "has-a" relationship, e.g.
  - a Car has an Engine
  - an Employee has a Supervisor
  - a Person has a Spouse (another Person)

**ListItem**

ListItem is a class that only exists to group subclasses together. It is an abstract class to constrain the features on other classes, not to make instances.

Classes can be either _concrete_ or _abstract_.

-> **Concrete**: classes are fully implemented and meant to be instantiated.

-> **Abstract**: classes provide elements to be inherited by subclasses, not meant to be instantiated.

**Abstract Classes**

Abstract classes are defined in java by adding the _abstract_ keyword, e.g.

```java
public abstract class ListItem {...
```

It is an error to try and make an instance of abstract classes. In our case, the only thing ListItem needs to do is print(), not even have a constructor.

Constructors CAN be used in abstract classes if you want those fields to be initialized in subclasses.

We won't have any code for printing in the LisItem either, but any subclass of ListItems NEEDS to have a print method for this to work.

```java
public abstract class ListItem {
    public abstract void print();
}
```

It is now illegal to make a concrete subclass of ListItem that does not have a print method implemented.

An abstract class can have methods that are not abstract, and you should define something here that is common to all subclasses.

A class defining data has the responsibility of defining methods to manipulate that data. Other than public constants, the user should only know how to use the data, but not allowed to use it directly.

A concrete class can NOT have an abstract method.

**Dynamic Class Binding**

The **Liskov Substitution Principle** means that if _Child_ is a subclass of _Parent_, then instances of _Parent_ can be substituted by instances of _Child_ with no observable effect. This means that the general behaviour of the object remains the same, regardless of whether the instance is of a parent class or a subclass.

Substitution allows the assignment of subclass instances to superclass variables. DCB is what allows the language to look up what the variable really contains at runtime.

**Polymorphism**

Polymorphism delegates the responsibility of implementing simpler methods to subclasses instead of one complex method in the parent class.

e.g. If we need to create an ordered list, ListItem does not cut it. We will need a compareTo() method in the subclasses to compare the values to the other ListItems. This will be an abstract class that the subclasses will implement.
