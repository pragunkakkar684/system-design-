# UML Diagrams: Class Diagrams & Sequence Diagrams in System Design

> **TL;DR:** Unified Modeling Language (UML) standardizes system architecture communication. Rather than wading through ambiguous paragraphs of text, engineers use **Class Diagrams** (Structural/Static) to model entities, attributes, methods, and relationships, and **Sequence Diagrams** (Behavioral/Dynamic) to visualize how these objects interact over time via synchronous and asynchronous messages.

---

## 🗺️ UML Taxonomy: Structural vs. Behavioral

In software architecture and system design interviews, UML is split into two primary categories:

```mermaid
flowchart TD
    UML["📐 Unified Modeling Language (UML)"]
    
    UML --> Structural["🧱 Structural (Static) Diagrams<br/><i>What is the system made of?</i>"]
    UML --> Behavioral["⚡ Behavioral (Dynamic) Diagrams<br/><i>How does the system act over time?</i>"]
    
    Structural --> ClassDiag["⭐ <b>Class Diagram</b><br/>(Core LLD Focus)<br/>Classes, attributes, methods, relationships"]
    Structural --> CompDiag["Component / Package Diagram<br/>Subsystems & dependencies"]
    Structural --> DeployDiag["Deployment Diagram<br/>Nodes, containers, cloud infra"]
    
    Behavioral --> SeqDiag["⭐ <b>Sequence Diagram</b><br/>(Core HLD & LLD Focus)<br/>Time-ordered object interactions"]
    Behavioral --> StateDiag["State Machine Diagram<br/>Lifecycle transitions (e.g., Order status)"]
    Behavioral --> ActDiag["Activity Diagram<br/>Workflows & parallel flows"]

    style UML fill:#1e293b,stroke:#0f172a,color:#fff
    style Structural fill:#2563eb,stroke:#1d4ed8,color:#fff
    style Behavioral fill:#059669,stroke:#047857,color:#fff
    style ClassDiag fill:#dbeafe,stroke:#2563eb,color:#1e3a8a
    style SeqDiag fill:#d1fae5,stroke:#059669,color:#064e3b
```

---

## 🧱 1. Class Diagrams (Structural Modeling)

A **Class Diagram** provides a static blueprint of a system by showing the classes, their data members (state), member functions (behavior), and how they are associated with one another.

### Anatomical Structure of a Class Box

Every class box consists of three stacked compartments:

```mermaid
classDiagram
    class ClassName {
        +publicAttribute: Type
        -privateAttribute: Type
        #protectedAttribute: Type
        ~packageAttribute: Type
        +publicMethod(param: Type) ReturnType
        -privateMethod() void
        #protectedMethod() int
    }
```

1. **Top Compartment (Name):** Class identifier. 
   - Abstract classes use italics or stereotype: `<<abstract>> ClassName`.
   - Interfaces use: `<<interface>> InterfaceName`.
2. **Middle Compartment (Attributes / State):** Format: `visibility name: type`.
3. **Bottom Compartment (Methods / Behavior):** Format: `visibility methodName(parameter: type): returnType`.

### Access Modifier Legend

| Symbol | Modifier | Visibility Scope |
| :---: | :--- | :--- |
| `+` | **Public** | Accessible by any outside class or package. |
| `-` | **Private** | Accessible *only* within the declaring class. |
| `#` | **Protected** | Accessible within the class, its package, and subclasses. |
| `~` | **Package / Default** | Accessible only within classes of the same package. |

---

### Class & Object Relationships Spectrum

Understanding relationships is the difference between passing and failing a Low-Level Design (LLD) interview:

```mermaid
flowchart LR
    Dep["Dependency<br/>(uses-a)<br/><i>..></i>"] --> Assoc["Simple Association<br/>(has-a / knows-a)<br/><i>---</i>"]
    Assoc --> Agg["Aggregation<br/>(weak has-a)<br/><i>o--</i>"]
    Agg --> Comp["Composition<br/>(strong has-a)<br/><i>*--</i>"]
    Comp --> Inh["Inheritance<br/>(is-a)<br/><i>--|></i>"]
```

```mermaid
classDiagram
    %% Inheritance (Class Association)
    Person <|-- Student : Inheritance (is-a)
    Person <|-- Professor : Inheritance (is-a)
    
    %% Aggregation (Weak Object Association)
    Department o-- Student : Aggregation (Unfilled Diamond)
    
    %% Composition (Strong Object Association)
    Car *-- Engine : Composition (Filled Diamond)
    
    %% Simple Association
    Student --> Course : Association (knows / enrolled)
    
    %% Dependency
    Car ..> GasStation : Dependency (uses temporarily)

    class Person {
        -String name
        -int age
        +getName(): String
    }
    class Student {
        -String studentId
        +study(): void
    }
    class Professor {
        -String department
        +gradePapers(): void
    }
    class Department {
        -String deptName
        -List~Student~ students
        +addStudent(Student s): void
    }
    class Car {
        -String vin
        -Engine engine
        +startCar(): void
    }
    class Engine {
        -int horsepower
        +ignite(): void
    }
    class Course {
        -String courseCode
        +getSyllabus(): String
    }
    class GasStation {
        +refuel(Car c): void
    }
```

#### Detailed Breakdown of Relationships:

1. **Inheritance (`is-a`):**
   - **Symbol:** Solid line with hollow triangular arrowhead (`<|--`).
   - **Nature:** Class-level association. Subclass inherits attributes and methods of superclass.
   - **Example:** `Student` **is-a** `Person`.

2. **Composition (`has-a`, Strong Ownership / Lifetime Bound):**
   - **Symbol:** Solid line with a **filled black diamond** (`*--`).
   - **Lifecycle Rule:** The child **cannot exist** without the parent. If the parent object is destroyed, the child object is destroyed alongside it.
   - **Example:** A `Car` has an `Engine`. An `Engine` has no independent existence outside a car.

3. **Aggregation (`has-a`, Weak Ownership / Independent Lifetime):**
   - **Symbol:** Solid line with an **unfilled / hollow diamond** (`o--`).
   - **Lifecycle Rule:** The child **can exist** without the parent. If the parent object is destroyed, the child object continues to exist.
   - **Example:** A `Department` has a list of `Student`s. If the `Department` shuts down, the students still exist at the university.

4. **Simple Association (`knows-a` / `associated-with`):**
   - **Symbol:** Plain solid line (`---`) or directed arrow (`-->`).
   - **Lifecycle Rule:** Loose relationship where one class maintains a reference to another.
   - **Example:** `Student` enrolls in `Course`.

5. **Dependency (`uses-a`):**
   - **Symbol:** Dashed arrow line (`..>`).
   - **Lifecycle Rule:** Transient usage (e.g., passed as a method argument or local variable).
   - **Example:** `Car` temporarily uses `GasStation` during `refuel()`.

---

## ⚡ 2. Sequence Diagrams (Behavioral Modeling)

A **Sequence Diagram** models dynamic behavior by illustrating how processes and objects exchange messages chronologically along a vertical timeline.

### Core Visual Elements

```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 User
    participant Web as 🖥️ Web App
    participant Auth as 🛡️ Auth Service
    participant DB as 🗄️ Database

    User->>Web: 1. Click "Login" (lat, lon, credentials)
    activate Web
    Note over Web: Web is active (Activation Bar)
    
    Web->>Auth: 2. authenticate(username, hash)
    activate Auth
    
    Auth->>DB: 3. Sync SQL Query: findByUsername()
    activate DB
    DB-->>Auth: 4. Reply / Return: UserRecord
    deactivate DB
    
    Auth-->>Web: 5. Reply / Return: JWT Token
    deactivate Auth
    
    Web-->>User: 6. Login Success Page
    deactivate Web
```

### Key Components Explained:

1. **Lifeline (Vertical Dashed Line):**
   - Represents the continuous existence of an object over time.
   - Time moves **top to bottom**.
2. **Focus of Control / Activation Bar (Vertical Rectangle):**
   - Indicates the period during which an object is actively performing an operation or waiting for a delegated subroutine.
3. **Synchronous Call (`->>`):**
   - Solid line with filled arrow. Sender halts execution and blocks until a reply is received.
4. **Reply / Return Message (`-->>`):**
   - Dashed line with arrowhead. Carries return value or execution control back to caller.
5. **Asynchronous Call (`-)`):**
   - Solid line with open arrowhead. Non-blocking fire-and-forget. Sender does **not** wait for response before proceeding.
6. **Object Creation (`create`):**
   - Directed arrow leading directly to a new object rectangle box being instantiated.
7. **Object Destruction (`destroy` / `X`):**
   - Large `X` placed at the bottom of a lifeline indicating that the object has been garbage collected or deallocated.
8. **Lost & Found Messages:**
   - **Lost Message (`●--x`):** Message was transmitted into network but dropped before reaching the receiver (network packet drop, timeout).
   - **Found Message (`o->`):** Message arrives at an actor from an outside/unknown boundary outside the modeled system.

---

## 🔍 Deep-Dive: Sync vs. Async & Message Topologies

```mermaid
sequenceDiagram
    autonumber
    actor Client
    participant OrderService
    participant PaymentGateway
    participant KafkaQueue
    participant Worker

    %% Synchronous Request-Reply
    rect rgb(240, 249, 255)
    Note over Client, PaymentGateway: Synchronous Flow (Blocking)
    Client->>OrderService: POST /checkout (Sync)
    OrderService->>PaymentGateway: chargeCard(token) (Sync)
    PaymentGateway-->>OrderService: 200 OK (Payment Receipt)
    end

    %% Asynchronous Event Publishing
    rect rgb(240, 253, 244)
    Note over OrderService, Worker: Asynchronous Flow (Non-Blocking)
    OrderService-)KafkaQueue: publish(OrderPlacedEvent) (Async Fire & Forget)
    OrderService-->>Client: 201 Created (Order confirmed)
    KafkaQueue-)Worker: deliverMessage(OrderPlacedEvent)
    activate Worker
    Worker->>Worker: processInventoryAllocation()
    deactivate Worker
    end

    %% Lost Message Example
    rect rgb(254, 242, 242)
    Note over Client, OrderService: Lost Message (Network Failure)
    Client->>OrderService: POST /retry-request
    Note right of Client: ❌ Packet lost in network (Never reaches OrderService)
    end
```

---

## ⚖️ Architectural Comparison Matrices

### Composition vs. Aggregation vs. Simple Association

| Criterion | 🔒 Composition (`*--`) | 🔓 Aggregation (`o--`) | 🔗 Simple Association (`---`) |
| :--- | :--- | :--- | :--- |
| **Relationship** | Whole-Part (Strict) | Whole-Part (Loose) | Peer-to-Peer / User-Resource |
| **Ownership** | **Strong ownership** | **Weak ownership** | No direct ownership |
| **Lifecycle Coupling**| **Coupled**. Child destroyed with parent | **Independent**. Child survives parent | **Independent**. Both survive |
| **UML Symbol** | Filled / Solid Black Diamond | Hollow / Unfilled Diamond | Plain Line or Directed Arrow |
| **Code Representation**| Initialized inside constructor | Passed via setter or method injection | Local reference / foreign key |
| **Example** | `Order` $\to$ `OrderLineItem`<br/>`House` $\to$ `Room` | `Department` $\to$ `Teacher`<br/>`Playlist` $\to$ `Song` | `Driver` $\to$ `Passenger`<br/>`Student` $\to$ `Course` |

### Synchronous vs. Asynchronous Messages

| Dimension | ⏱️ Synchronous Message (`->>`) | ⚡ Asynchronous Message (`-)`) |
| :--- | :--- | :--- |
| **Thread Behavior** | Sender blocks waiting for reply | Sender continues immediately without blocking |
| **Coupling** | High temporal coupling | Low temporal coupling (Decoupled) |
| **Error Handling** | Direct error codes / exceptions | Dead letter queues, retries, idempotency tokens |
| **Latency** | Cumulative sum of downstream calls | Minimum latency (Enqueue operation only) |
| **Typical Protocols**| HTTP/1.1, gRPC, REST, JDBC | Kafka, RabbitMQ, AWS SQS, Webhooks |

---

## 🏭 Real-World Production Example: Ride-Hailing Booking

Let's observe both diagrams working together to model a ride-hailing checkout:

### Class Model (LLD)
```mermaid
classDiagram
    class Ride {
        -String rideId
        -RideStatus status
        -Fare fare
        +assignDriver(Driver d)
        +completeRide()
    }
    class Fare {
        -double baseAmount
        -double surgeMultiplier
        +calculateTotal(): double
    }
    class Driver {
        -String driverId
        -Vehicle vehicle
        +acceptRide(String rideId)
    }
    class Vehicle {
        -String plateNumber
        -Engine engine
    }
    class Engine {
        -int horsepower
    }

    Ride *-- Fare : Composition (Fare lifecycle bound to Ride)
    Ride o-- Driver : Aggregation (Driver exists independently)
    Driver *-- Vehicle : Composition (Registered vehicle bound to profile)
    Vehicle *-- Engine : Composition (Engine cannot exist without car)
```

---

## 💡 System Design Interview Tips & Common Pitfalls

### 🚩 Red Flags Interviewers Watch For
1. **Inverting Composition and Aggregation:**
   - *Trap:* Claiming a `Room` has an aggregation relationship with a `Building`. If the building is demolished, the rooms cease to exist—it is strict **Composition**.
   - *Trap:* Claiming an `Author` is composed within a `Book`. If a book goes out of print, the author still lives—it is **Aggregation** or **Association**.
2. **Missing Activation Bars in Sequence Diagrams:**
   - Drawing messages connecting directly to lifelines without activation bars makes it impossible to see concurrency bottlenecks and deadlocks.
3. **Using Synchronous Arrows for Event-Driven Systems:**
   - In Kafka/RabbitMQ architectures, always use open-arrow asynchronous notations (`-)`) rather than blocking request-response arrows.

### 🏆 Quick Interview Sketching Rules
- **Class Diagrams:** When asked for LLD, start with 3–5 core nouns (Classes), write 2 attributes and 2 methods each, and immediately define their relationship diamonds (`*--` vs `o--`).
- **Sequence Diagrams:** Focus on the "Happy Path" first with step numbers. Use an `alt` or `opt` box for error/fallback scenarios.

