---
tags:
  - java/syntax
  - java/control-flow
  - cheatsheet
aliases:
  - Java Switch
  - Switch Statement
  - Switch Expression
date_created: <% tp.file.creation_date() %>
---

# Java `switch`

The `switch` construct allows multi-way branching based on the value of an expression. 

Modern Java (Java 14+) supports both **Switch Statements** (traditional control flow) and **Switch Expressions** (which return a value).

---

## 1. Supported Data Types

A switch selector expression can evaluate to:
- **Primitives**: `byte`, `short`, `char`, `int` (and their wrapper classes: `Byte`, `Short`, `Character`, `Integer`)
- **Strings**: `String` (Java 7+)
- **Enums**: `enum` values
- **Objects**: Any type when using *Pattern Matching* (Java 21+)

> [!WARNING] Unsupported Types
> You **cannot** switch directly on `long`, `float`, `double`, or `boolean` (unless using Java 21+ type patterns for boxed versions).

---

## 2. Traditional `switch` (Statement)

Uses colons (`:`) and requires explicit `break` statements. Without `break`, execution falls through to the next case.

```java
int dayOfWeek = 3;
String dayName;

switch (dayOfWeek) {
    case 1:
        dayName = "Monday";
        break;
    case 2:
        dayName = "Tuesday";
        break;
    case 3:
        dayName = "Wednesday";
        break;
    default:
        dayName = "Invalid day";
        break;
}
```

> [!CAUTION] Fall-Through Pitfall
> If you omit `break;`, Java executes all subsequent `case` blocks regardless of whether they match, until a `break` or the end of the block is reached.

---

## 3. Enhanced `switch` (Arrow Syntax `->`) *(Java 14+)*

Uses arrows (`->`). **No fall-through** occurs, eliminating the need for `break`. Multiple labels can be separated by commas.

```java
int dayOfWeek = 6;

switch (dayOfWeek) {
    case 1, 2, 3, 4, 5 -> System.out.println("Weekday");
    case 6, 7          -> System.out.println("Weekend");
    default            -> System.out.println("Invalid day");
}
```

---

## 4. `switch` as an Expression (Returning Values)

Switch can evaluate directly to a value assigned to a variable.

### Single-line return:
```java
int day = 2;
String typeOfDay = switch (day) {
    case 1, 2, 3, 4, 5 -> "Weekday";
    case 6, 7          -> "Weekend";
    default            -> "Unknown";
}; // Semicolon required
```

### Multi-line blocks using `yield`:
When a branch requires multiple lines of logic inside an expression, use `{}` and `yield` to return the value.

```java
String status = switch (statusCode) {
    case 200 -> "OK";
    case 404 -> {
        System.out.println("Resource missing");
        yield "Not Found"; // yield acts like 'return' for this branch
    }
    default -> "Unknown Code";
};
```

> [!NOTE] Exhaustiveness
> When using `switch` as an expression, it **must be exhaustive** (every possible value must be handled, usually via a `default` branch).

---

## 5. Modern Features *(Java 21 LTS)*

### A. Pattern Matching & `instanceof`
You can switch on an object's type and cast it automatically:

```java
Object obj = "Hello, World!";

String result = switch (obj) {
    case Integer i -> "Integer of value: " + i;
    case String s  -> "String of length: " + s.length();
    case null      -> "It was null!";
    default        -> "Unknown type";
};
```

### B. Guard Clauses (`when`)
Add secondary conditions directly to the `case`:

```java
Object obj = 42;

switch (obj) {
    case Integer i when i > 0 -> System.out.println("Positive integer");
    case Integer i            -> System.out.println("Zero or negative integer");
    default                   -> System.out.println("Something else");
}
```

---

## Quick Reference

| Feature | Traditional (`:`) | Enhanced (`->`) |
| :--- | :--- | :--- |
| **Fall-through?** | Yes (must use `break`) | No |
| **Returns a value?** | No (only statements) | Yes (can be expression) |
| **Comma-separated labels?** | No (`case 1: case 2:`) | Yes (`case 1, 2 ->`) |
| **Return keyword** | N/A | `yield` (for blocks) |

---
## Related Notes
- [[Java Control Flow]]
- [[Java Enums]]
- [[Java Pattern Matching]]
