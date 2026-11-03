#### Association, Aggregation, Composition, and Inheritance in C#

#### 1. Quick Overview

| Relationship | Type | Meaning | Real-World Example |
|---|---|---|---|
| Association | "Uses-a" | A general relationship where objects interact, no ownership | `Teacher` teaches `Student` |
| Aggregation | "Has-a" (weak) | One object contains another, but the contained object can exist independently | `Department` has `Professors` (professor can exist without the department) |
| Composition | "Has-a" (strong) | One object owns another completely; the contained object cannot exist independently | `House` has `Room` (room ceases to exist without the house) |
| Inheritance | "Is-a" | A class derives from another, inheriting its members | `Dog` is an `Animal` |

#### 2. Association

| Feature | Detail |
|---|---|
| Nature | Objects reference each other, but have independent lifecycles |
| Direction | Can be one-way (unidirectional) or two-way (bidirectional) |
| Ownership | None — neither object owns the other |
| Multiplicity | Can be one-to-one, one-to-many, many-to-many |

```csharp
class Teacher
{
    public string Name;
}

class Student
{
    public string Name;
    public Teacher AssignedTeacher; // association — Student references a Teacher

    public void ShowTeacher()
    {
        Console.WriteLine(Name + "'s teacher is " + AssignedTeacher.Name);
    }
}
```
Both `Teacher` and `Student` can exist and be created independently of each other — the link is just a reference.

#### 3. Aggregation ("Has-a", weak ownership)

| Feature | Detail |
|---|---|
| Nature | Whole/part relationship, but part can outlive the whole |
| Lifecycle | Independent — deleting the container doesn't delete the contained objects |
| Typical implementation | The contained object is created **outside** and passed in (e.g., via constructor or property) |
| UML symbol | Hollow diamond |

```csharp
class Professor
{
    public string Name;
}

class Department
{
    public string Name;
    public List<Professor> Professors = new List<Professor>();

    public void AddProfessor(Professor p) // professor created elsewhere, just added here
    {
        Professors.Add(p);
    }
}

// Usage:
Professor prof = new Professor { Name = "Dr. Amir" }; // exists independently
Department dept = new Department { Name = "CS" };
dept.AddProfessor(prof);
// If dept is destroyed, prof still exists — it wasn't owned by dept
```

#### 4. Composition ("Has-a", strong ownership)

| Feature | Detail |
|---|---|
| Nature | Whole/part relationship where the part cannot exist without the whole |
| Lifecycle | Dependent — contained object is created and destroyed with the container |
| Typical implementation | The contained object is created **inside** the container's constructor, no external reference given |
| UML symbol | Filled/solid diamond |

```csharp
class Room
{
    public string Type;
    public Room(string type) { Type = type; }
}

class House
{
    private List<Room> rooms;

    public House()
    {
        // Rooms are created INSIDE House — they don't exist independently
        rooms = new List<Room>
        {
            new Room("Bedroom"),
            new Room("Kitchen")
        };
    }
}

// Usage:
House h = new House();
// There's no way to create a "Room" outside of a House in this design —
// if House is destroyed/garbage collected, its Rooms go with it
```

#### 5. Aggregation vs Composition — Side-by-Side

| Aspect | Aggregation | Composition |
|---|---|---|
| Ownership strength | Weak | Strong |
| Object creation | Created outside, passed in | Created inside the container |
| Lifecycle dependency | Independent | Dependent |
| Can part belong to multiple wholes? | Yes (e.g., a `Professor` in multiple `Department` committees) | No, typically exclusive to one owner |
| Example | `Car` has `Driver` (driver exists without the car) | `Car` has `Engine` (engine is built specifically for that car) |

#### 6. Inheritance ("Is-a")

| Feature | Detail |
|---|---|
| Nature | A derived class extends/specializes a base class |
| Keyword | `class Derived : Base` |
| Relationship test | "Is-a" — `Dog` **is an** `Animal` (valid) vs `Car` **is an** `Engine` (invalid — should be composition) |
| Access | Derived class inherits `public`/`protected` members |

```csharp
class Animal
{
    public string Name;
    public void Eat() { Console.WriteLine(Name + " is eating."); }
}

class Dog : Animal // Dog IS-A Animal
{
    public void Bark() { Console.WriteLine(Name + " says Woof!"); }
}
```

#### 7. Choosing the Right Relationship — Decision Guide

| Question | If "Yes" → Use |
|---|---|
| Does the child object make sense without the parent existing? | Aggregation |
| Would the child object be meaningless/orphaned if the parent is destroyed? | Composition |
| Are the two objects just interacting/referencing each other, with no ownership at all? | Association |
| Is the relationship "type X **is a** type Y" (specialization)? | Inheritance |
| Do you want to reuse behavior without an "is-a" relationship (avoid misusing inheritance)? | Composition/Aggregation ("favor composition over inheritance") |

#### 8. Common Mistake: Misusing Inheritance Instead of Composition

| Bad Design (inheritance misuse) | Better Design (composition) |
|---|---|
| `class Car : Engine` — a Car "is an" Engine? Doesn't make logical sense | `class Car { private Engine engine; }` — a Car **has an** Engine |
| `class Stack : List<T>` — inherits ALL of List's methods, even ones that break stack rules (e.g., `Insert` at arbitrary index) | `class Stack<T> { private List<T> items; }` — exposes only `Push`/`Pop`, hides unwanted `List` behavior |

**Rule of thumb:** *"Favor composition over inheritance"* — use inheritance only for genuine is-a relationships; use composition/aggregation when you just want to reuse or organize functionality.

#### 9. Summary Table

| | Association | Aggregation | Composition | Inheritance |
|---|---|---|---|---|
| Relationship phrase | "uses-a" | "has-a" (weak) | "has-a" (strong) | "is-a" |
| Ownership | None | Shared/independent | Exclusive/dependent | N/A (structural) |
| Lifecycle tied? | No | No | Yes | N/A |
| C# mechanism | Field/property reference | Field holding externally-created object | Field created within constructor | `class Derived : Base` |

