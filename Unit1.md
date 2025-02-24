**<u>BASIC OO CONCEPTS</u>**

**Records, Classes, and Objects**

-> **Records (structures/structs)**: are several types of data stored together. They are a collection of fields,
or attributes.
When we declare a new record, we assign a value to each field in the record.

-> **State**: is the set of values at any given time.

```c
// C-style example

struct date {
    int day;
    int month;
    int year;
};
```

In the struct/record, we treat the 3 variables as a unit. The set of values at any given time dictate the state
of this structure.

-> **Classes**: are templates or models to _construct_ objects. They define methods too.

-> **Objects**: are instances of classes.

-> **Methods**: are general behaviours used by any instance of the class.

-> **Instance Variables**: are new copies of variables in each specific object that is created. They define the state
of the object that is created.

-> **Class variables and Methods**: are variables and methods shared by all instances of the class.

**Differences:**

|              Question              | Classes | Records |
| :--------------------------------: | :-----: | :-----: |
|       Support encapsulation?       |   Yes   |   No    |
| Class-level variables and methods? |   Yes   |   No    |
|    Hierarchies and inheritance?    |   Yes   |   No    |

**Abstract Data Types (ADTs)**

ADTs: are data structures that use abstraction to hide their implementation, and provide an interface to the user,
for abstraction purposes.

-> **Implementation**: is how the operations and structuring are done.

-> **Interface**: is how we interact with the data structure.

-> **Abstraction**: is to remove the burden of understanding how things are being handled from the user.

**Problems with ADTs Implemented in Non-OO Languages**

For example, we have a stack ADT implemented in C using an array.

1. There is nothing to stop outside code from directly interacting with the array (no encapsulation).
2. The code is not as generic as it should be - the type of elements within the stack are to be defined at
   compile time.
3. There is no independence of the implementation and interface (in C, the separation of header and
   implementation gives the appearance of that).

An object oriented approach can make this ADT into an active data structure, combining valid operations with
the data. An object can take requests from outside code, but the operations changing the state of the data
structure are controlled by the object itself. These objects will also be encapsulated - hide data and protect
the internals.

**What is a maessage?**

-> **Messages**: are sent to an object by calling its methods (with or without parameters), and then the object responds.

Sender of the message (client), does not:

1. know the actions to be done internally,
2. know what methods will be used by the receiver to satisfy the requirements of the message.
3. need to know/care/worry about this.

**Methods**

-> **Methods**: define how the object will respond to a message.

In imperative programming languages (C++, Java), a method is a function that defines a sequence of actions.

Methods should:

1. manipulate data within their own class.
2. send messages to other objects for things that they are responsible for.

**Class Relationships**

1. Classes group together data and function members (methods).
2. All objects belong to some class.
3. Classes define:
   1. the state information of each object of that class (instance variables).
   2. the methods used by each object of that class.
4. The class itself might have state and behaviours as well.
5. Classes are hierarchal.
6. Child classes will inherit instance variables and methods in parent classes.
7. Child classes can add additional states and behaviours.

-> **Containment**: one object contains another.

**Constructors**

Every class has a default no-parameters constructor, which does nothing until you define your own.
Subclasses will automatically use their parent's constructor along with their own, but we can call any other superclass constructor if we choose to.

```java
public Student(String n, int newNum, double initGpa) {
    super(n);
    stNum = newNum;
    gpa = initGpa;
}
```

**Visibility**

1. A child class can not use a _private_ field in its parent class, but it has the field.
2. Mutators like getters/setters should be used to access private fields, or they should be declared as protected instead.

-> **Private**: Only visible in this class.

-> **Protected**: Visible in this class and in subclasses, and anywhere in the same package.

-> **Public**: Visible anywhere.

Protected would only be useful if there are multiple packages being used, otherwise mutators should be used.

**Type Compatibility**

A variable of type T can hold a reference to an object of, or any subclass of type T, but not the reverse.

```java
Person x = new Student(); // works
Student y = new Person(); // doesn't work
```

A subclass can be used in the same way as its parent, but the reverse is not true.

**Casting**

```java
Person p;

p.setName(); // works
p.getGpa(); // doesn't work
```

It doesn't work since not every person has a GPA, even though the person might be a student. This can work if you _downcast_ p to a Student class using

```java
((Student)p).getGpa();
```

but if p is not pointing to a Student object, there will be a ClassCastException thrown at runtime.

**Instance-of Operator**

You can use the instanceof operator to make sure you're safely downcasting.

```
object-reference instanceof class-name
```

This is not a method, instead it is an operator like + or &&.
It gives a boolean result: true if the object being referred to is of that class, or any sub-class of that class.

**Example Usage**

```java
if (p instanceof Student) {
    System.out.println((Student)p).getGpa();
} else {
    System.out.println("This is not a student.");
}
```

1. Every variable has a type that was assigned to it during variable declaration. The variable will ALWAYS be of this type, this is called the _static class_ of the variable.
2. The variable can hold a reference to either that specific type or any subclass, this is called the _dynamic class_ of variable.

**Superclass Array Example**

In a Person[] array, we can store references to a mix of Person, Student, Employee, Undergraduate, Graduate, Professor, Instructor, and TeachingAssistant objects, since they are all instances of Person at the end of the day.

e.g.

```java
Person[] people = new Person[10];
people[0] = new Person("Peter");
people[1] = new Student("Paul", 2345, 0.0);
people[2] = new Professor();
people[3] = new Student("Mary", 3456, 0.0);

// Count the students

int numStudents = 0;

for (int i = 0, i < people.length, i++) {
    if (people[i] instanceof Student) {
        numStudents++;
    }
}
```

**The Ultimate Superclass**

1. There is a built-in class in Java -- Object

**Object Methods**

The Object class has a few basic methods, and therefore, by _inheritance_,, every object has these (possibly overridden).

1. Object clone()
2. String toString()

**Multiple Inheritance**

-> **Multiple Inheritance**: is the fact that in some languages, a class can have more than 1 superclass.
We need to deal with conflicts in behaviour and state information that can cause inconsistencies. Many OO languages don't support MI for this reason, but have other maintainable ways,
e.g. Java - interfaces
JavaScript, Ruby - Mixins

**Matching Messages to Methods**

Messages can be sent to an instance or to a class itself. The sender doesn't know which method is activated, or whether it is a method in the superclass or in the class itself.

e.g.

```java
AButton myObject;
myObject.mouseDown();
```

If myObject has a mouseDown() method, that would be used, if NOT, a superclass MUST have it implemented. A method just stops a message from going up the hierarchy and works on it as soon as possible (the first superclass that has that method).

**Bindings**

A variable must be bound to a value (x = 3)
A type is bound to a variable (int x)

-> **Dynamic Binding**: happens at runtime.

OO languages support dynamic class binding.

e.g.

```java
GraphicalObject go = new Button();
```

Static type of go: Graphical Object. (set at compile time)
Dynamic type of go: Button. (set at runtime)

We also need _dynamic dispatch_, which is dynamic method-message mapping.

**Polymorphism**

A message could cause one of several different methods to be invoked, depending on the type of object being referenced (current contents of the variable and inheritance tree). This is decided at runtime.

e.g. We have different toString() methods in the Person, Student and Employee classes, and a variable of type Person can refer to any of those objects. So, when you use p.toString(), the method to be used is dynamically chosen at runtime.

All methods in the different classes MUST have the _same signature_, AND the superclass MUST have that method, not just the subclasses. It is common to have a dummy method in the superclass to allow polymorphism.

**Advantages of OOP**

1. Often matches the problem domain nicely, e.g. GUIs, mimicry of physical objects.
2. Software and code reusability - Inheritance allows multiple common behaviours to be implemented once. A common set of classes for standard tasks can be used by many programs.
3. Understanability and maintenance.
4. Ease of design - subdividing the problem.
