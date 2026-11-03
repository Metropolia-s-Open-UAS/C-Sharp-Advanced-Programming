#### Object-Oriented Programming in C#

#### 1. The Four Pillars of OOP

| Pillar | Definition | C# Mechanism |
|---|---|---|
| Encapsulation | Bundling data and methods together, hiding internal state | Access modifiers (`private`, `public`), properties |
| Abstraction | Exposing only essential features, hiding complexity | Abstract classes, interfaces |
| Inheritance | A class acquiring members from a base class | `class Derived : Base` |
| Polymorphism | Same method behaving differently depending on the object | `virtual`/`override`, interfaces |

#### 2. Classes and Objects

| Concept | Syntax | Notes |
|---|---|---|
| Define a class | `class Car { }` | Blueprint for objects |
| Create an object (instantiate) | `Car myCar = new Car();` | Allocates memory, calls constructor |
| Field | `public string Model;` | Variable belonging to the class |
| Method | `public void Drive() { }` | Function belonging to the class |
| Constructor | `public Car() { }` | Runs when object is created, same name as class |
| Parameterized constructor | `public Car(string model) { Model = model; }` | Initializes fields at creation |
| Destructor/Finalizer | `~Car() { }` | Rarely used — cleanup before garbage collection |

```csharp
class Car
{
    public string Model;
    public int Year;

    public Car(string model, int year)
    {
        Model = model;
        Year = year;
    }

    public void Drive()
    {
        Console.WriteLine(Model + " is driving.");
    }
}

Car myCar = new Car("Tesla", 2024);
myCar.Drive();
```

#### 3. Access Modifiers

| Modifier | Accessible from |
|---|---|
| `public` | Anywhere |
| `private` | Only within the same class (default for class members) |
| `protected` | Same class and derived classes |
| `internal` | Same assembly (project) only |
| `protected internal` | Same assembly OR derived classes anywhere |
| `private protected` | Same assembly AND derived classes only |

#### 4. Encapsulation — Properties

| Feature | Syntax | Notes |
|---|---|---|
| Full property | `private int age; public int Age { get { return age; } set { age = value; } }` | Explicit backing field |
| Auto-property | `public int Age { get; set; }` | Compiler generates backing field automatically |
| Read-only auto-property | `public int Age { get; }` | Settable only in constructor |
| Property with validation | `public int Age { get { return age; } set { if (value >= 0) age = value; } }` | Encapsulates validation logic |
| Init-only (C# 9+) | `public int Age { get; init; }` | Settable only at object initialization |

```csharp
class Person
{
    private int age;
    public int Age
    {
        get { return age; }
        set
        {
            if (value >= 0) age = value;
        }
    }
}
```

#### 5. Inheritance

| Feature | Syntax | Notes |
|---|---|---|
| Base class | `class Animal { }` | Parent/super class |
| Derived class | `class Dog : Animal { }` | Child/sub class, inherits public/protected members |
| Call base constructor | `public Dog() : base() { }` | Explicitly invokes base class constructor |
| Access base member | `base.Speak();` | Calls the base class version of a method |
| Sealed class | `sealed class Dog { }` | Prevents further inheritance |
| Single inheritance only | — | C# classes can inherit from only **one** base class (use interfaces for multiple) |

```csharp
class Animal
{
    public string Name;
    public void Eat()
    {
        Console.WriteLine(Name + " is eating.");
    }
}

class Dog : Animal
{
    public void Bark()
    {
        Console.WriteLine(Name + " says Woof!");
    }
}

Dog d = new Dog();
d.Name = "Rex";
d.Eat();   // inherited from Animal
d.Bark();  // defined in Dog
```

#### 6. Polymorphism

| Type | Syntax | Notes |
|---|---|---|
| Method overriding | `virtual` in base, `override` in derived | Runtime polymorphism — behavior determined by actual object type |
| Method hiding | `new` keyword | Hides base method instead of overriding (rarely desired) |
| Method overloading | Same name, different parameters | Compile-time polymorphism |
| Abstract method | `public abstract void Speak();` | Must be overridden by derived class, no body in base |

```csharp
class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Animal makes a sound");
    }
}

class Cat : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Meow");
    }
}

Animal a = new Cat();
a.Speak(); // Outputs "Meow" — resolved at runtime based on actual type
```

#### 7. Abstraction — Abstract Classes vs Interfaces

| Feature | Abstract Class | Interface |
|---|---|---|
| Keyword | `abstract class` | `interface` |
| Instantiable? | No | No |
| Can have fields | Yes | No |
| Can have constructors | Yes | No |
| Can have method bodies | Yes (mix of abstract and concrete) | Yes, as of C# 8 default implementations (rare use) |
| Multiple inheritance | A class can inherit only 1 abstract class | A class can implement multiple interfaces |
| Access modifiers on members | Yes | Members are implicitly `public` |
| Naming convention | `PascalCase` | `IPascalCase` (prefixed with `I`) |

```csharp
abstract class Shape
{
    public abstract double Area(); // no body — must be overridden
    public void Describe()
    {
        Console.WriteLine("Area is: " + Area());
    }
}

class Circle : Shape
{
    public double Radius;
    public Circle(double r) { Radius = r; }
    public override double Area()
    {
        return Math.PI * Radius * Radius;
    }
}
```

```csharp
interface IMovable
{
    void Move();
}

class Robot : IMovable
{
    public void Move()
    {
        Console.WriteLine("Robot is moving.");
    }
}
```

#### 8. Static Members

| Feature | Syntax | Notes |
|---|---|---|
| Static field | `public static int Count;` | Shared across all instances, not per-object |
| Static method | `public static void Reset() { }` | Called on the class, not an instance: `ClassName.Reset();` |
| Static class | `static class MathHelper { }` | Cannot be instantiated, all members must be static |
| Static constructor | `static Car() { }` | Runs once, before first use of the class |

#### 9. Other Key OOP Keywords

| Keyword | Purpose |
|---|---|
| `this` | Refers to the current instance |
| `base` | Refers to the parent class |
| `new` (on a member) | Hides an inherited member instead of overriding |
| `override` | Provides a new implementation of a virtual/abstract member |
| `sealed` | Prevents a class from being inherited, or a method from being overridden further |
| `partial` | Splits a class definition across multiple files |
| `readonly` | Field assignable only in declaration or constructor |
| `const` | Compile-time constant, must be assigned at declaration |

#### 10. Quick Decision Guide

| Situation | Use |
|---|---|
| Need to share a common base implementation | Abstract class |
| Need multiple unrelated capabilities (e.g., `IComparable`, `IDisposable`) | Interfaces |
| Want to hide internal data, control access | Properties + `private` fields |
| Behavior should vary by actual object type at runtime | `virtual` / `override` |
| Data/method belongs to the class itself, not instances | `static` |
| Want to prevent further inheritance | `sealed` |

