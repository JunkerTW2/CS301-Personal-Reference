# 🧠 Theory of Computing: DFA vs. NFA vs. Turing Machine
#cs #theory-of-computation #sedgewick #reference

---

## ⚡ Quick Comparison Matrix

| Feature | DFA | NFA | Turing Machine (TM) |
| :--- | :--- | :--- | :--- |
| **Memory** | Fixed, finite (only current state) | Fixed, finite (only current state) | **Infinite tape** (read/write scratchpad) |
| **Input Movement** | 1 step Right only | 1 step Right only | Left (**L**), Right (**R**), or Stay |
| **Branching / Guessing** | No (strictly 1 path) | **Yes** (multiple paths or $\epsilon$-jumps) | No (Deterministic standard model) |
| **Computational Power** | Regular Languages | Regular Languages | **Universal** (Equivalent to modern computers) |
| **Can check $a^n b^n$?** | ❌ No | ❌ No | ✅ **Yes** |
| **Can solve everything?** | ❌ No | ❌ No | ❌ **No** (Fails Halting Problem) |

---

## 1. Deterministic Finite Automaton (DFA)

> [!info] Core Idea
> A flowchart with **no storage memory/RAM**. It reads the input string left-to-right once, follows deterministic arrows, and checks if it lands in an **Accept State**.

### Components
* **States** ($\bigcirc$): Points of execution.
* **Start State** ($\rightarrow \bigcirc$): Where input reading begins.
* **Accept State** ($\bigodot$): Double circle. If string ends here $\rightarrow$ **ACCEPT**.
* **Transitions**: Arrows labeled with a symbol (e.g., $a$ or $b$).

### The Golden Rule of DFAs
* For **every state** and **every alphabet symbol**, there must be **exactly ONE** outgoing arrow. 
* No choices, no guessing.

### ⚠️ The Memory Trap
* A DFA has **no variables** (no `int count = 0`).
* Its only memory is *the state it is currently standing on*.
* **Can do:** Patterns, remainders, parity (e.g., "ends with $b$", "even number of $a$'s", "divisible by 3").
* **CANNOT do:** Unbounded counting (e.g., $a^n b^n$, matching arbitrary nested parentheses). 
  * *Why?* To count to infinity, it would require an infinite number of states drawn on paper.

---

## 2. Nondeterministic Finite Automaton (NFA)

> [!info] Core Idea
> A DFA with **superpowers**: It can guess the right branch, explore multiple paths simultaneously, or jump for free.

### The Two Superpowers
1. **Multiple Transitions:** A state can have multiple outgoing arrows for the *same* symbol (or zero arrows).
2. **$\epsilon$-Transitions (Epsilon Jumps):** Free transitions. The machine can hop from one state to another *without reading any input*.

### 🔑 Kleene's Theorem (Crucial Exam Concept)
$$\text{Regex} \iff \text{NFA} \iff \text{DFA}$$
* **All three have the EXACT SAME computational power.**
* Adding nondeterminism does **not** allow the machine to recognize anything a DFA cannot.
* Both recognize the class called **Regular Languages**.

> [!tip] Why do we use NFAs?
> * **Humans** easily write Regex or NFAs.
> * **Compilers** convert NFAs into equivalent DFAs.
> * **Computers** run the DFA in linear time $O(N)$ with no backtracking.

---

## 3. Turing Machine (TM)

> [!info] Core Idea
> A DFA attached to an **infinite tape** that can be read from and written to. It is the theoretical blueprint for all modern general-purpose computers.

### The 3 Physical Parts
1. **The Tape:** Infinite row of memory cells (holding symbols $a, b,$ or blank).
2. **The Read/Write Head:** Reads the current cell, can overwrite it, and steps Left ($L$) or Right ($R$).
3. **Finite State Control:** A finite state machine whose arrows contain **3-part instructions**:

$$\xrightarrow{\quad \text{READ} \;\to\; \text{WRITE},\; \text{MOVE} \quad}$$

*Example:* `a -> X, R` means: *"If head sees an `a`, replace it with `X`, move head 1 step Right, and transition to the next state."*

### How a TM "Searches and Skips" (The Self-Loop)
```text
           a → a, R  (skip 'a's)
           ┌─────┐
           ▼     │
      (( Search )) ─── b → Y, L ──► (( Next State ))
                      (found 'b'!)
```

### 🧠 The Big Theorems

* **Church-Turing Thesis:**  
  Any algorithm that can be computed by *any* computer (supercomputer, quantum computer, Java) can also be computed by a Turing Machine.
* **The Halting Problem (Turing's Proof):**  
  It is mathematically impossible to write a program that determines whether an arbitrary Turing machine will halt (finish) or loop forever on a given input. **The problem is Undecidable.**

---

## 🚨 Flashcard / Mental Traps to Avoid

1. **"Can an NFA compute things a DFA can't?"**  
   $\rightarrow$ **NO.** Kleene's theorem proves they are identical in expressive power.
2. **"Does a Turing machine have an infinite number of states?"**  
   $\rightarrow$ **NO.** The states are **finite**. The *tape* is infinite.
3. **"Does $A \le B$ mean $A$ is harder than $B$?"**  
   $\rightarrow$ **NO.** It means $A$ reduces to $B$, which means **$B$ is at least as hard as $A$**.
