---
tags:
  - java
  - oop
  - computer-science
  - study-notes
aliases:
  - static
  - instance
  - class-vs-object
date: 2026-03-30
status: reviewed
---

# Java Core: Instance vs. Static

The fundamental difference between **Instance** and **Static** in Java comes down to **Ownership and Memory**:
* **Instance** = Owned by an **individual object** (a unique copy lives on the Heap).
* **Static** = Owned by the **Class itself** (a single shared copy lives in the Metaspace/Class area).

---

## 1. The Real-World Mental Model

Think of a **Class** as the **Blueprint of a House**, and an **Instance** as the **Actual House** built on a street.

| Concept | Instance | Static |
| :--- | :--- | :--- |
| **Analogy** | The paint color of house #42 | The city building code applied to all houses |
| **Belongs to** | One specific object (`myHouse`) | The category / blueprint (`House`) |
| **Creation** | Created when you use the `new` keyword | Loaded once when the JVM reads the `.class` file |
| **Quantity** | If you make 1,000 houses, there are 1,000 copies | Exactly **1 copy exists**, no matter how many houses are built |

---

## 2. Variables: Instance Variables vs. Static Variables

### Visualizing Memory

                HEAP MEMORY
 ┌────────────────────────────────────────┐
 │  Instance 1 (account1)                                     │
 │  ├── balance: $500                                          │
 │                                                                         │
 │  Instance 2 (account2)                                     │
 │  ├── balance: $20                                            │
 └────────────────────────────────────────┘

              METASPACE / METHOD AREA
 ┌────────────────────────────────────────┐
 │  BankAccount Class (Shared)                          │
 │  ├── bankName: "Chase"                                 │
 │  ├── interestRate: 0.05                                    │
 └────────────────────────────────────────┘

### Code Example

// BankAccount.java
public class BankAccount {
    // INSTANCE VARIABLE: Every customer has their own separate balance
    public double balance; 

    // STATIC VARIABLE: All accounts share the same bank name and interest rate
    public static String bankName = "Global Trust Bank";
    public static int totalAccountsCreated = 0;

    public BankAccount(double startingBalance) {
        this.balance = startingBalance;
        totalAccountsCreated++; // modifies the ONE shared counter
    }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        BankAccount acc1 = new BankAccount(100.0);
        BankAccount acc2 = new BankAccount(500.0);

        // Accessing INSTANCE variables -> Use lowerCamelCase object reference:
        System.out.println(acc1.balance); // 100.0
        System.out.println(acc2.balance); // 500.0

        // Accessing STATIC variables -> Use UpperCamelCase Class name directly:
        System.out.println(BankAccount.bankName);             // "Global Trust Bank"
        System.out.println(BankAccount.totalAccountsCreated); // 2
    }
}

> [!WARNING] The Antipattern to Avoid
> You *can* technically write `acc1.bankName`, but it is bad practice and confusing. It misleads other developers into thinking `bankName` belongs solely to `acc1`. Always access static members via the Class name: `BankAccount.bankName`.

---

## 3. Methods: Instance Methods vs. Static Methods

### Quick Comparison Rule
* Ask yourself: **"Does this operation depend on the internal state of a specific object?"**
  * **YES** -> **Instance Method** (e.g., `withdraw(50)`, `fly()`, `setName("Alice")`)
  * **NO** -> **Static Method** (e.g., `Math.max(a, b)`, `Integer.parseInt("123")`, `convertMilesToKm(10)`)

### The Golden Rule of Access Rights

   ┌────────────────────────────────────────────────────────┐
   │                                                                                                       |
   │   Instance Context       CAN access       Static Stuff                       │
   │   (Has a 'this' pointer)      ────────►    (Shared)                          │
   │                                                                                                       │
   │   Static Context       CANNOT access    Instance Stuff                  │
   │   (No 'this' pointer)         ───X───►    (Specific)                            │
   │                                                                                                      │
   └────────────────────────────────────────────────────────┘

> [!IMPORTANT] Why Static Cannot Call Instance Directly
> An instance method knows who `this` is (the object calling it). 
> A static method runs in a general vacuum. If you call `BankAccount.deposit(50)` without saying *which* account, the computer has no idea whose balance to increase!

// Example: Access rules in code
public class BankAccount {
    private double balance; // Instance field
    private static String bankName; // Static field

    // 1. INSTANCE METHOD
    public void deposit(double amount) {
        // ALLOWED: Instance methods can access their own fields...
        this.balance += amount; 
        // ...AND they can access static fields!
        System.out.println("Thank you for banking with " + bankName); 
    }

    // 2. STATIC METHOD
    public static void printTermsAndConditions() {
        // ALLOWED: Can access static variables
        System.out.println("Welcome to " + bankName);

        // ERROR! Compile-time failure:
        // System.out.println("Your balance is: " + balance);
        // Reason: Which balance? There might be 0 accounts or 10,000 accounts!
    }
}

---

## 4. Why is `public static void main` Static?

Have you ever wondered why Java's entry point is written like this: `public static void main(String[] args)`?

1. When you run a program, the Java Virtual Machine (JVM) starts up.
2. At this point, **no objects have been created yet**.
3. If `main` were an instance method, the JVM would have to create an instance of your class first. But it wouldn't know which constructor to call or what parameters to give it.
4. Making it `static` allows the JVM to execute `YourClass.main()` directly without needing to instantiate anything.

---

## 5. Cheat Sheet / Quiz Flashcards

| Question | Answer |
| :--- | :--- |
| How do you call a static method? | `ClassName.methodName()` |
| How do you call an instance method? | `objectReference.methodName()` |
| Can a static method use the `this` keyword? | **No.** `this` refers to an instance, which doesn't exist in static context. |
| Where do static variables live in memory? | Metaspace (formerly PermGen). One copy for the entire program lifecycle. |
| Where do instance variables live in memory? | On the Heap inside their specific object. Garbage-collected when the object is unreachable. |
| Can an instance method call a static method? | **Yes.** Instance methods have access to everything. |
| Can a static method call an instance method directly? | **No.** It must first explicitly instantiate an object: `MyClass obj = new MyClass(); obj.instanceMethod();` |

---

## Related Notes
* [[Java Memory Management - Stack vs Heap]]
* [[OOP Principles - Encapsulation]]
* [[Java Classes and Constructors]]