# 🔤 Regular Expressions (Regex) - Theory & Java
#cs #regex #java #theory-of-computation #reference

---

## ⚡ The 3 Pure Mathematical Primitives
In formal language theory (Sedgewick 5.1), **every regular expression** is built entirely from just three operations over an alphabet $\Sigma$ (e.g., $\{a, b\}$):

| Operation | Notation | Meaning | Example | What matches? |
| :--- | :---: | :--- | :--- | :--- |
| **Concatenation** | `ab` | Followed immediately by | `ab` | `"ab"` only |
| **Alternation (OR)** | `a\|b` | Either one or the other | `a\|b` | `"a"` or `"b"` |
| **Kleene Star** | `a*` | **Zero or more** repetitions | `a*` | `""` ($\epsilon$ / empty), `"a"`, `"aa"`, `"aaa"`... |

> [!warning] Kleene Star Trap
> `a*` **ALWAYS** matches the empty string ($\epsilon$).  
> `(ab)*` matches: `""`, `"ab"`, `"abab"`, `"ababab"`...

---

## 🛠️ Extended Syntax Cheat Sheet (Practical Shortcuts)

These shortcuts add zero mathematical power—they are just practical shorthand:

| Symbol | Meaning | Example | Matches | Fails |
| :---: | :--- | :--- | :--- | :--- |
| **`.`** | Any single character | `a.b` | `"aab"`, `"abb"`, `"acb"` | `"ab"`, `"accb"` |
| **`+`** | **One or more** repetitions | `a+` | `"a"`, `"aa"` *(NOT empty)* | `""` |
| **`?`** | **Zero or one** (optional) | `a?b` | `"b"`, `"ab"` | `"aab"` |
| **`[ab]`** | Character class (either `a` or `b`) | `[ab]+` | `"a"`, `"b"`, `"abaabb"` | `"c"`, `""` |
| **`[^ab]`**| Negation (anything **except** `a` or `b`) | `[^ab]` | `"c"`, `"1"`, `"Z"` | `"a"`, `"b"` |
| **`[0-9]`**| Any digit from 0 to 9 (same as `\d`) | `[0-9]+` | `"5"`, `"42"`, `"999"` | `""`, `"abc"` |
| **`[a-z]`**| Any lowercase letter | `[a-z]+` | `"cat"`, `"java"` | `"Cat"`, `"123"` |
| **`{n}`** | Exactly $n$ occurrences | `a{3}` | `"aaa"` only | `"aa"`, `"aaaa"` |

---

## ☕ Java Implementation

### 1. The Big Three `String` Methods
```java
// 1. Check if ENTIRE string matches pattern (true/false)
boolean valid = s.matches(regex);

// 2. Cut a string into an array, split along the regex matches
String[] tokens = s.split(regex);

// 3. Replace all matches with a new string
String clean = s.replaceAll(regex, replacement);
```

> [!danger] The Java Gotcha: `s.matches()` is Greedy
> In Java, `s.matches(regex)` checks if the **entire string** matches from index `0` to the end.  
> It does **not** search for a substring inside `s`.

---

### 2. The Double-Backslash Rule (`\\`)
In Java string literals, a single backslash `\` is an escape character for the Java compiler itself (like `\n`). To pass an actual backslash to the regex engine, you must write **two**:

| What you want | Java String Regex |
| :--- | :--- |
| Literal period `.` | `"\\."` |
| Any digit (`[0-9]`) | `"\\d"` |
| Any whitespace (spaces, tabs) | `"\\s+"` |
| Literal parentheses `(` and `)` | `"\\("` and `"\\)"` |

---

## 🔬 Classic Textbook Examples (Sedgewick)

### DNA Sequence Validation
A valid DNA sequence only contains bases `A, C, T, G` and has at least one character:
```java
String dnaRegex = "[ACTG]+";
"ACGTTAG".matches(dnaRegex); // true
"ACXGT".matches(dnaRegex);   // false
```

### Whitespace Harvesting
Split input into tokens regardless of inconsistent spaces or tabs:
```java
String text = "field1    field2\tfield3";
String[] fields = text.split("\\s+");
// Result: ["field1", "field2", "field3"]
```

---

## 🚨 Flashcard / Mental Traps to Avoid

1. **"Can regex verify $a^n b^n$ or balanced parentheses?"**  
   $\rightarrow$ **NO.** Kleene's Theorem proves $\text{Regex} \equiv \text{DFA}$. DFAs have finite memory and cannot count to arbitrary depths.
2. **"What is `?` mathematically?"**  
   $\rightarrow$ `X?` is syntactic sugar for `(X | )` (where nothing means $\epsilon$ empty string).
3. **"Does `a*` require an `a`?"**  
   $\rightarrow$ **NO.** Zero is a valid repetition count. An empty string matches `a*`.
