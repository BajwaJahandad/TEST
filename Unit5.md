**<u>Overriding, Shadowing and Refining</u>**

**What can subclasses do with methods?**

1. Leave methods from superclass unchanged.
2. Add new methods.
3. Replace methods from superclass.
   - Shadow, override, redefine.
4. Refine method from superclass.
   - Supply new method that uses the version of the superclass (e.g. calls `super.method()` in Java)

**Replacement vs Inheritance**

If a subclass receives a message for which it has no definition, the message is passed to the superclass i.e. an inherited method may be invoked.

If a class defines a matching method, the corresponding message will be intercepted and handled by it EVEN IF its superclass has a similar method. This means that the class hides the superclass' method name.

We replace a superclass' method anytime we define it in a subclass. There are different types of replacement.

**Shadowing & Overriding**

In _shadowing_, the signatures are the same as in the parent and child methods, but the method is NOT virtual/polymorphic.

```java
class Parent {
    public int x = 12;
    public void doIt() { System.out.println("In Parent!"); }
}

class Child extends Parent {
    public int x = 6;
    public void doIt() { System.out.println("In Child!"); }
}

public class Shadow {
    public static void main(String[] args) {
        Parent p = new Parent();
        Child c = new Child();
        System.out.println(c.x); // prints 6
        c.doIt(); // prints In Child!

        p = c;
        System.out.println(p.x); // prints 12
        p.doIt(); // prints In Child!
    }
}
```

In this example:

- Data members are shadowed: Child's x shadows the Parent's x.
- Can bypass child's shadowing data by referring to object through the parent type.
- This is done at compile time - compiler "knows" which x to refer to by pointer type (static type) being used.

Why does `p.doIt()` still print "in child"?

Because it's polymorphic. For methods, java is always looking at the dynamic type (the variable's actual data) and routing the message there.

Methods with the same signature in Java are not shadowed because _methods are always polymorphic in Java_, there is no option to turn it off.

In C++, shadowing of both methods and data members is possible. This is done at compile time because we only get polymorphism if we declare a method to be virtual.

```cpp
class Parent {
    public: //note no virtual on method below!
        void example() { cout << "Parent" << endl; }
};

class Child : public Parent {
    public:
        void example() { cout << "Child" << endl; }
};

// In some other method, invoking each directly:
Parent* p = new Parent();
p->example(); //output is Parent

Child* c = new Child();
c->example(); //output is Child

//and shadowing of methods is done at COMPILE TIME just like
//shadowing data members...
p = c;
p->example(); //output is Parent, even though it's a Child
```

If those methods were virtual, they would be polymorphic if we were using heap-based variables. Then we would print "child", even when accessing via parent variable `p`, as we saw in the Java example.

However, then we'd be doing _overriding_, where the type sugnatures in parent and child are the same, but the method IS virtual.

Java methods are always overridden rather than shadows, since there is always polymorphism.

Remember that `virtual` does NOT make an abstract class - it only means we can use polymorphism via pointer-based variables i.e., I can still make a direct instance of Parent that would use Parent's method.

**Forcing/Forbidding Overriding**

Virtual in C++ permits overriding (not shadowing). Unless it's pure virtual, it is NOT an abstract method and does NOT force the coder to override everything. But both C++ and Java have pure virtual and abstract to do this respectively. It is possible in BOTH to forbid overriding.

```java
class Parent {
    public final void aMethod(int) {....}
}

class Child extends Parent {
    public void aMethod (int) {....}
}

// Attempting to override aMethod is illegal here, since it is declared as final by Parent
```

This is possible to do to a class too, in that case, it is illegal to make a subclass of Parent.

```cpp
void myMethod() final;

class MyClass final { ... };

// This is the same in C++
```

**Redefinition**

If the type signature of a method name differs from a parent to a child, then we are doing _redefinition_.

```java
public boolean equals(Object obj) // This would be the parent
public boolean equals(String str) // Redefined with a new signature

// Technically, this is redefining, but it is closer to overriding since the intent is to override

// When a child's method has an argument that is more specific than the one in its parent's method, we say that it is a covariant argument

void method(Animal a)
void method(Object o)

// When a child's method has an argument that is more general than the parent's, it is a contravariant argument

// This is true for return types as well

class Parent {
    Mammal test() { return new Cat(); }
};

class Child extends Parent {
    Animal test() { return new Bird(); }
};

// The return type is contravariant, since the allowable values in the child (any animal) are larger than the parent (only mammals).
```

Many languages adopt a principle of non variance i.e., a child should NOT change the type signature in a method that is being overridden. Since co/contravariance is techinically redefinition, but what we really WANT is an override, it should be a true override with the same types in the method signature.

If a method is abstract in Java, it must be truly overridden in the subclass. So, if you're using a comparison function in an abstract class, it is desirable for versions in the subclass to take an appropriate subclass type to compare to as a parameter.

If you're calling the parent's method and using polymorphism, your subclass MUST take the superclass types - make it a true override.

In C++, return types ARE allowed to have covariant changes (not arguments though).

```cpp
class Parent {
    public:
        Parent* clone() { return new Parent(); }
};

class Child: public Parent {
    public:
        Child* clone() { return new Child(); }
};

// This will never give a type error, since the values produced by child are always a subset of the values produces by the parent
```

Java also allows covariant return types when overriding methods, but you should always try to follow conventions of non-variance.

**Refinement**

We can have a method executed in the subclass, but still have some work handled by the superclass. This is done in Java using the `super` keyword.

```java
public class B extends A {
    private int number;

    public B() {
        // Super calls the constructor of the superclass
        super([parameters of superclass constructor]);
        number = 0;
    }
}

class A {
    private double r;
    public void f() {.........}
    ....
}

class B extends A {
    ...
    public void f() {.......super.f(); .....}
}

// super refers to the f method in superclass
```

In C++, we do this using `class B : public A`.

```cpp
B::B() : A() { ... }

// A() calls the super constructor

B::B(int fieldA, int fieldB) : A(fieldA), fieldB(fieldB) { ... }

// Assuming fieldA is defined in class A and fieldB in class B

// This is another way to invoke a method from the superclass
class A {
    ...
    virtual void print;
}

class B : public A {
    ...
    void print override;
}

void B::print() { A::print(); ...}
```

The `super` keyword does not exist in C++, you just have to specify the name of the superclass, since multiple inheritance is possible in C++.

**Why use refinement?**

It let's the superclass handle its own data and reduces code duplication. The _responsibility_ of handling each method/data member should be left to each class, while providing some service to others.

**Q) Writing a method in a subclass that has a different signature from a method in the parent class of the same name is**

✅ C. Shadowing
Explanation: Shadowing occurs when a subclass defines a method with the same name as a method in the parent class but with a different signature (parameters). The method in the parent class is hidden, not overridden.

**Q) Writing a method in a subclass that has the same signature as a method in the parent class, when polymorphism is used is**

✅ D. Overriding
Explanation: Overriding occurs when a subclass provides a new implementation of a method that exists in the parent class with the same signature. This is used in polymorphism to allow dynamic method dispatch (virtual in C++).

**Q) Writing a method in a subclass that calls a method of the same name in the parent class, then possibly adds additional code is**

✅ A. Refinement
Explanation: Refinement occurs when a subclass method calls the parent class method (using `super.method()` in Java or `ParentClass::method()` in C++) and then adds more behavior.

**Q) Writing a method in a subclass that has the same signature as a method in the parent class, when polymorphism is not used is**

✅ B. Redefinition
Explanation: Redefinition happens when a method has the same signature as the parent class but is not marked for polymorphism (i.e., not virtual in C++). The method in the subclass simply hides the parent's method, and static binding is used.

**Q) Which of the following are reasons to use "final"?**

✅ B. To forbid overriding a method
✅ D. To forbid creation of a subclass
Explanation:

`final` on a method prevents overriding in a subclass.
`final` on a class prevents inheritance (no subclasses can be created).
`final` does not prevent adding instance variables (A ❌).
`final` does not forbid constructor usage (C ❌).

**Q) When a child's method has an argument where the set of allowable values is larger than the set of allowable values in the parent, it is**

✅ B. Contravariant
Explanation:

Contravariance occurs when a subclass method loosens or expands the type of its parameter(s) compared to the parent class method.
This means the child method accepts a broader range of arguments than the parent method.

**Static Members**

Static data members are similar to global variables, and static methods are similar to global functions. It is data and behaviours that belong to the whole class as a whole, and have no separate copy for each instance. Once you use the `static` modifier on a method/data, it belongs to the class and not to any one instance.

```java
public class MyThing {
    private static int thingCount = 0;
    public static void printCount() {
        System.out.println("You made " + thingCount +
        " things so far");
    }

    public MyThing() { thingCount++; }

    public static void main(String args[]) {
        MyThing thing1 = new MyThing();
        MyThing.printCount(); //outputs 1
        MyThing thing2 = new MyThing();
        thing1.printCount(); //outputs 2
    }
}

// Even though there are 2 different instances, the count goes up, since for the static keyword, instances don't matter. The count belongs to the class itself
```

In C++, the `static` data member exists outside all instances when compiled.

```cpp
private:
    static int count = 0;

// is illegal. The assignment NEEDS to be done outside the class definition

// Counter.h
class Counter {
    private:
        static int count; //lets class know of
    public:
        static void setcount (int); // count's existence
        static void printcount();
}; //end class

// Counter.cpp
int Counter::count = 0; //initialize count!
void Counter::setcount (int i) { count = i; }
void Counter::printcount() { cout << count << " "; }

// Here, the actual assignment of the value is NOT part of the interface of the class.
```

**Note: The declaration of any static data member inside a class does not actually allocate any memory for it!**

You need to make sure that this memory is allcoated through an assignment statement outside the class definition, as we initialized in the code previously. **If you do not do this, you will get _link editor errors_**.
