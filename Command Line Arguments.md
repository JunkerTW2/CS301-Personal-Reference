---
tags:
  - java/basics
  - java/cli
  - cheatsheet
aliases:
  - Command Line Arguments
  - CLI Arguments
  - String[] args
date_created: <% tp.file.creation_date() %>
---

# Java Command Line Arguments

Command line arguments allow passing parameters into your program at runtime before it executes. They are passed directly into the entry point method: `public static void main(String[] args)`.

---

## 1. How It Works

Arguments are passed as an array of Strings (`String[]`).

```java
public class App {
    public static void main(String[] args) {
        System.out.println("Total arguments: " + args.length);
        
        for (int i = 0; i < args.length; i++) {
            System.out.println("Argument " + i + ": " + args[i]);
        }
    }
}
```

> [!NOTE] Difference from C/C++
> In C/C++, `argv[0]` contains the program's executable name. 
> In **Java**, `args[0]` contains the **first user-supplied argument**.

---

## 2. Running from Terminal

```bash
# Compile
javac App.java

# Run with arguments
java App Alice 25 true
```

**Result inside Java:**
- `args[0]` $\rightarrow$ `"Alice"`
- `args[1]` $\rightarrow$ `"25"`
- `args[2]` $\rightarrow$ `"true"`

### Passing Arguments Containing Spaces
Wrap values containing spaces in quotes:
```bash
java App "New York City" "San Francisco"
```

---

## 3. Parsing Arguments

All arguments arrive as strings. Use wrapper classes to parse them to other types:

```java
public class ParserExample {
    public static void main(String[] args) {
        if (args.length < 3) {
            System.err.println("Usage: java ParserExample <name> <age> <isMember>");
            return;
        }

        String name = args[0];
        int age = Integer.parseInt(args[1]);
        boolean isMember = Boolean.parseBoolean(args[2]);
        
        System.out.printf("%s is %d years old. Member: %b%n", name, age, isMember);
    }
}
```

### Conversion Cheat Sheet:
| Target Type | Parse Method |
| :--- | :--- |
| `int` | `Integer.parseInt(args[i])` |
| `double` | `Double.parseDouble(args[i])` |
| `long` | `Long.parseLong(args[i])` |
| `boolean` | `Boolean.parseBoolean(args[i])` |
| `char` | `args[i].charAt(0)` |

---

## 4. Safety & Common Exceptions

> [!DANGER] ArrayIndexOutOfBoundsException
> Accessing `args[0]` when no arguments were passed crashes the program. Always check `args.length` first.

```java
if (args.length > 0) {
    String firstArg = args[0];
} else {
    System.out.println("No arguments provided.");
}
```

> [!WARNING] NumberFormatException
> Calling `Integer.parseInt()` on non-numeric input throws this exception.

```java
try {
    int port = Integer.parseInt(args[0]);
} catch (NumberFormatException e) {
    System.err.println("Error: Port must be an integer.");
}
```

---

## 5. Basic Flag Parsing

```java
boolean verbose = false;
String filename = null;

for (int i = 0; i < args.length; i++) {
    switch (args[i]) {
        case "-v", "--verbose" -> verbose = true;
        case "-f", "--file"    -> filename = args[++i];
        default                -> System.out.println("Unknown option: " + args[i]);
    }
}
```

---
## Related Notes
- [[Java Main Method]]
- [[Java Arrays]]
- [[Java String]]
- [[Java Wrapper Classes]]
- [[Java Exception Handling]]